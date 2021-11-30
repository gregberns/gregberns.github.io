---
layout: post
title: Patterns For Clarity and Reusability
date: '2017-09-11T07:28:01.567Z'
categories: []
keywords: [software]
---

Recently I’ve been working with quite a few very small to small applications (very small: 100–200 lines, small: under 1000 lines) and I’ve noticed a problem. Especially on very small applications, our best practices, like separation of concerns, seem to get thrown out the window. The code is just ‘hacked together real quick’ because its ‘such a small application’.

There are several pretty significant issues that crop up. Here’s the workflow:

1.  The code doesn’t work at first, and so we sit in the debugger to make it work
2.  Because the code was written through debugging, its poorly structured
3.  Because its poorly structured it’s incredibly difficult for the next developer to pick up

I don’t want to go into depth with why this is bad, but if you want more details add a comment and we can dive into it.

Instead, I’ll focus on how we can prevent this with some very simple patterns. These are NOT ‘Gang of Four’ style patterns. They are much simpler, and very easy to implement.

The pattern I’ve been using is composed of three simple parts:

*   Some type of ‘interface’ to start a ‘transaction’. This may be a command line, Http Api, or a server-less function.
*   Next there is some type of ‘domain’ code. This is the core of your application and does ‘logic stuff’.
*   And last, external calls to services. I’ve been calling this layer the ‘client’ layer because it creates a ‘client’ to contact an external service.

These three pieces: an interface, a domain, and clients, if separated properly, can make code much easier to work with, understand, test, debug, and hand off.

(Side note: I’d like to re-enforce that you should be taking the time to write a small program with the same care you write a 10,000 line program. Taking the time to focus on separation of concerns will pay off faster than you’d imagine.)

To illustrate this pattern, I’ll use a piece of production code that was written, deployed, and eventually required a change. But the change was difficult because the interface, domain, and client code was all mushed together.

The code below is used in an Azure Function, Azure’s server-less framework (similar to Amazon’s Lambda functions). It is written in C#, but any Java and most JS devs should be able to find some value.

Lets look at how it can be improved.

Here’s a quick outline of what it does:

*   accepts a message and extracts the contents
*   deserializes the contents to an object
*   calls a method, passing both the message and deserialized object
*   gets several environment variables
*   extracts a value from the original message
*   gets an http client
*   creates an http request
*   executes an http request
*   checks to see if the request was successful

So nothing really all that complicated. But lets take a look at the code:

(Note: The code has been a bit simplified for readability and conciseness. Apologies for any syntactic/naming issues.)

```csharp
Task Run(EventData msg, TraceWriter log) {  
    string body = Encoding.UTF8.GetString(msg.GetBytes());  
    if(body == "")  
        throw new Exception("No body in the Event.");  
    EventInfo evnt = JsonConvert.DeserializeObject<EventInfo>(body);  
    await SendEvent(evnt, log, msg);  
}

Task SendEvent(EventInfo evnt, TraceWriter log, EventData msg) {  
    var _serviceUri = GetEnvironmentVariable("ServiceUri");  
    object appToken;  
    if (!msg.Properties.TryGetValue("AppToken", out appToken))  
        throw new Exception("No app token in the Event.");  
    string appTokenString = (string)appToken;  
    string subscriberKey = GetEnvironmentVariable($"SubscriberKey:{appTokenString}:{evnt.AccountId}");  
    var client = HttpCaller.GetGpsHttpClient(subscriberKey);  
    var json = JsonConvert.SerializeObject(evnt);  
    var stringContent = new StringContent(json, Encoding.UTF8, "application/json");  
    var response = await client.PostAsync(_serviceUri, stringContent);  
    if(!response.IsSuccessStatusCode)  
        await LogError($"Error ocurred while calling service: {response.ToString()}");  
}
```

As you can see, there’s a bit going on. Lets try and break this down.

How can we use the interface, domain, and client pattern:

*   Interface — Extract all incoming data (message data and environmental variables) into a verifiable domain object
*   Domain — Transform the domain object into an Http request
*   Client — Send the Http request and interpret the results

Within the OOP paradigm, we’re supposed to think about ‘objects’ and the ‘responsibility’ of those objects, then attach data and functionality to those objects.

I’d like to suggest, a slightly different tact. Let’s treat each of our high level objectives as steps in a ‘pipeline’, where each ‘pipe section’ changes or transforms the data flowing through it.

In this case, the first section of the ‘pipe’ (the interface) will take the data from the ‘outside world’, and convert it into something that makes sense in our world (the domain).

Another way to think about it: to get from interface to domain, the data must go through a ‘decontamination’ process, where the data gets sanitized, cleaned, and structured properly before it can reach our cleanly structured domain environment.

So lets think about the code above, what external data/logic can we group together:

*   Environmental variables can be retrieved
*   Retrieve and deserialize the message
*   Extract the deserialized message’s ‘AppToken’ and fail fast if it doesn’t exist
*   The ‘SendEvent’ method isn’t really doing much for us either, its just a useless divider between two intertwined logic sets. Let’s just get rid of that separation for now.

We now have all the ‘dirty’ external data/logic in one place, here’s our new code:

```csharp
Task Run(EventData msg, TraceWriter log) {  
    // — -Start Extract Data      
    string body = Encoding.UTF8.GetString(msg.GetBytes());  
    if(body == "")  
        throw new Exception("No body in the Event.");  
    //Move this above the serialization, since this will alow us to fail fast  
    object appToken;  
    if (!msg.Properties.TryGetValue("AppToken", out appToken))  
        throw new Exception("No app token in the Event.");  
    string appTokenString = (string)appToken;  
    EventInfo evnt = JsonConvert.DeserializeObject<EventInfo>(body);  
    var _serviceRequestUri = GetEnvironmentVariable("ServiceRequestUri");  
    string subscriberKey = GetEnvironmentVariable($"SubscriberKey:{appTokenString}:{evnt.AccountId}");  
    // — -End Extract Data  
    var client = HttpCaller.GetGpsHttpClient(subscriberKey);  
    var json = JsonConvert.SerializeObject(evnt);  
    var stringContent = new StringContent(json, Encoding.UTF8, "application/json");  
    var response = await client.PostAsync(_serviceRequestUri, stringContent);  
    if(!response.IsSuccessStatusCode)  
        await LogError($"Error ocurred while calling service: {response.ToString()}");  
}
```

Let’s now do two critical things:

*   pull that nasty work into a new ‘decontamination’ function. This will prevent the polution of our domain code.
*   return a new domain object with **only** the data the domain requires

Here’s the code now.

```csharp
Task Run(EventData msg, TraceWriter log) {  
    var domainData = Validate(msg);  
    var client = HttpCaller.GetHttpClient(domainData.SubscriberKey);  
    var stringContent = new StringContent(domainData.Body, Encoding.UTF8, "application/json");  
    var response = await client.PostAsync(domainData.ServiceUri, stringContent);  
    if(!response.IsSuccessStatusCode)  
        LogError($"Error ocurred while calling service: {response.ToString()}");  
}

DomainData Validate(EventData msg){  
   string body = Encoding.UTF8.GetString(msg.GetBytes());  
   //… other code: envvars, deserialization, etc

   return new DomainData(){  
      ServiceUri = _serviceUri;  
      SubscriberKey = subscriberKey;  
      Body = body;  
   };  
}

class DomainData {  
    string ServiceUri;  
    string SubscriberKey;  
    string Body;  
}
```

Dang! Look at the change, its almost understandable now.

*   Our `Run` method is so much simpler now!
*   All the gross Validation logic is all in one place. There’s now a nice dividing line between unvalidated and validated data.
*   Our new domain object is really simple. We won’t do it here, but we **should** do further validation to ensure the domain object is **completely** valid (so null checks, data validation, etc.)
*   We also got rid of `JsonConvert.SerializeObject` since we already had a serialized body. That optimization was easy to see because we had a clearer domain!

Beautiful!

Lets now improve on building the request and executing it.

```csharp
var client = HttpCaller.GetHttpClient(domainData.SubscriberKey);var stringContent = new StringContent(domainData.Body, Encoding.UTF8, "application/json");  
var response = await client.PostAsync(domainData.ServiceUri, stringContent);
```

To clean this up, we’re going to look for a ‘seam’, or a dividing line between the ‘creation’ of a request and the actual execution of the request.

When writing tests, we use the ‘Arrange, Act, Assert’ model. **There is no reason that only needs to occur in tests.** We can (and should) use the same concept when writing our code!

Steps:

*   Arrange — Setup our request data structure
*   Act — Execute the request
*   Assert — Check the response for a result

Or something like this:

```csharp
ServiceRequest BuildRequest(DomainData data){  
    return new ServiceRequest(){  
       Client = HttpCaller.GetHttpClient(data.SubscriberKey),  
       Content = new StringContent(data.Body, Encoding.UTF8, "application/json");  
       SerivceUrl = data.ServiceUri  
    };  
}

HttpResponse ExecuteRequest(ServiceRequest req) {  
    return req.Client.PostAsync(req.ServiceUri, req.Content);  
}

class ServiceRequest {  
    HttpClient Client;  
    StringContent Content;  
    Uri ServiceUrl;  
}
```

Building a request is now clear, simple, and straight forward.

The execution of the request is also very explicit. It is also separated from everything else. This is **very** important because the execution is the most likely thing to fail. Separation allows us to easily make the code more defensive (add try/catch and logging).

With that change, here is our new `Run` method:

```csharp
Task Run(EventData msg, TraceWriter log) {  
   var domainData = Validate(msg);  
   var request = BuildRequest(domainData);  
   var response = ExecuteRequest(request);  
   if(!response.IsSuccessStatusCode)  
      LogError($"Error ocurred while calling service: {response.ToString()}");  
}
```

Notice how the `Run` method creates a chain or pipeline of logic: `Validate` data flows into `BuildRequest`, which flows into `ExecuteRequest`. The data is nicely flowing through the pipeline we’ve created.

Once we have a pipeline like this, there are other methods to clarify it, but thats for another post.

(Note: See full implementation below)

**Conclusion**

Not only is this code easier to pick up and understand, but it is very simple to test the individual pieces.

There are a couple things to notice with this new code that may be different than you’re used to:

*   functions **only** take in the data they need through their parameters
*   functions could easily be moved into different modules/classes
*   the data ‘flows’ through the functions as if the chained functions were a pipeline

Hopefully you now have a couple new tools to use in your tool belt:

*   separation of concerns: incoming data (interface), business logic (domain), external resources (clients)
*   clean interfaces **are** **valuable** because they are more understandable
*   chaining functions together can make for clean, modular code

Though this is a nice simple example, it doesn’t apply to just small code bases. Its actually even more important to keep large code bases flexible, understandable and testable.

Thats it for now. Any thoughts? Hopefully you can pull a couple of these ideas into your daily practice.

(Note: Please disregard code differences/issues, especially with regard to async usage. Code is for illustration purposes, not prod usage.)

```csharp
Task Run(EventData msg, TraceWriter log) {  
   var domainData = Validate(msg);  
   var request = BuildRequest(domainData);  
   var response = ExecuteRequest(request);  
   if(!response.IsSuccessStatusCode)  
      LogError($"Error ocurred while calling service: {response.ToString()}");  
}

DomainData Validate(EventData msg){  
   string body = Encoding.UTF8.GetString(msg.GetBytes());  
   //… other code: envvars, deserialization, etc

   return new DomainData(){  
      ServiceUri = _serviceUri;  
      SubscriberKey = subscriberKey;  
      Body = body;  
   };  
}

ServiceRequest BuildRequest(DomainData data){  
    return new ServiceRequest(){  
       Client = HttpCaller.GetHttpClient(data.SubscriberKey),  
       Content = new StringContent(data.Body, Encoding.UTF8, "application/json");  
       SerivceUrl = data.ServiceUri  
    };  
}

HttpResponse ExecuteRequest(ServiceRequest req){  
    return await req.Client.PostAsync(req.ServiceUri, req.Content);  
}

class DomainData {  
    string ServiceUri;  
    string SubscriberKey;  
    string Body;  
}

class ServiceRequest {  
    HttpClient Client;  
    StringContent Content;  
    Uri ServiceUrl;  
}
```