# T3: Respect REST
REST is an acronym standing for _REpresentational State Transfer_ and a way of architecturing web services. It was defined in 2000 by Roy Fielding, one of the authors of the HTTP specification and ex-chairman of the Apache Software Foundation, in his dissertation entitled [_Architectural Styles and the Design of Network-based Software Architectures_](http://www.ics.uci.edu/~fielding/pubs/dissertation/fielding_dissertation.pdf).

Later on, Fielding described REST as the result of a long discussion of the HTTP specification:

> Throughout the HTTP standardization process, I was called on to defend the design choices of the Web. That is an extremely difficult thing to do within a process that accepts proposals from anyone on a topic that was rapidly becoming the center of an entire industry. I had comments from well over 500 developers, many of whom were distinguished engineers with decades of experience, and I had to explain everything from the most abstract notions of Web interaction to the finest details of HTTP syntax. That process honed my model down to a core set of principles, properties, and constraints that are now called REST.

During the last decade, REST has quickly become the de-facto standard for the implementation of public-facing web APIs, even though a few alternatives exist. A comparison of REST with the two most popular web service architectures, SOAP and RPC, can be found in the last paragraph and will help clarify why REST has become so popular.

## Concepts
These are the key concepts of a RESTful web service. We'll be expanding on what constraints REST imposes on each one of them in the next paragraph.

<dl data-markdown>
  <dt>Resource</dt>
  <dd>
    A resource is a concept in your application (user, blog post etc.) which can be uniquely identified and operated on. Nouns are preferred over verbs for resource names. When REST is implemented over HTTP, resources are identified through a URI (e.g. `/users/1`).
  </dd>

  <dt>Representation</dt>
  <dd>
    A representation is a document which contains the state of a resource. Representations might be created both by the server (when it sends a resource to the client) and the client (e.g. when     describing what updates to apply to a resource). For instance, the same user could be served in JSON and XML.
  </dd>

  <dt>Server</dt>
  <dd data-markdown>
    A server stores information about the resources accessible through the web service and allows clients to operate on them. Note that a logical server might consist of several physical servers operating transparently under the same identity. In fact, this is usually the case in large web services, for redundancy and performance. Servers do not expose a UI (User Interface).
  </dd>

  <dt>Client</dt>
  <dd>
    A client calls a server to operate a resources. Clients do not store any authoritative information about the resources, but might store context about them and/or how the web service i being used (e.g. authentication information) to improve the UX. Clients also provide a UI which can be graphical, textual or both.
  </dd>

  <dt>Request/response</dt>
  <dd>
    RESTful web services operate in request/response cycles. The client sends a request to the server, which handles it and returns a response.
  </dd>
</dl>

## Architectural Constraints
In order to implement REST, a web service has to implement the following architectural constraints.

<dl>
  <dt>Client-Server</dt>
  <dd>
    Client-server refers to an architecture where the server stores all authoritative information about the resources of the web service. The client does not operate on the resources directly: it merely asks the server to perform operations on the resources.
  </dd>

  <dt>Stateless</dt>
  <dd>
    The server does not store any information about the state of the clients interacting with it. This makes a RESTful service to scale more easily as the number of clients grows. Additionally, it makes implementation and testing much easier.
  </dd>

  <dt>Cache</dt>
  <dd>
    All the responses provided by a server should contain explicit or implicit caching indications (i.e. whether the resource can be cached or not and, if yes, for how long).
  </dd>

  <dt>Uniform Interface</dt>
  <dd>
    All resources on the server should be accessed through the same interface. This minimizes the effort required to learn and use a RESTful service (it arguably makes the implementation easier as well). Over HTTP, this constraint is implemented by using HTTP methods and media types.
  </dd>

  <dt>Layered System</dt>
  <dd>
    A RESTful service is made of one or more layers that can only "see" the next one. These layers can be anything from load balancers to authentication proxies. The client does not know anything about any layers past the most external ones.
  </dd>

  <dt>Code-On-Demand</dt>
  <dd>
    This is an optional constraint of REST: a server can provide code that extends the functionality of and offload certain computations to the client.
  </dd>
</dl>

If any of the above constraints is not respected, a web service cannot be considered strictly RESTful. The only exception is the **Code-On-Demand** constraint, which is optional.

## The Richardson Maturity Model
RESTfulness is a binary feature: your web service is either RESTful, when it implements all the requirements, or non-RESTful. However, not all non-RESTful services are created alike: some of them are very non-RESTful, some are a little bit non-RESTful, some are five minutes of work away from RESTfulness.

Leonard Richardson devised a scale comprised of four levels of (non-)RESTfulness, where each level builds upon and expands the previous one. It is called the [Richardson Maturity Model](https://www.crummy.com/writing/speaking/2008-QCon/act3.html) and [Martin Fowler](http://martinfowler.com/articles/richardsonMaturityModel.html) wrote a blog post that summarizes it pretty effectively, but we're also going to give our description of it:

<dl data-markdown>
  <dt>Level 0: Remote procedures over HTTP</dt>
  <dd>
    This level is the most basic one: at this point, the web service is simply using HTTP as a tunnel to accept requests for a remote procedure and deliver their responses, all through the same endpoint. There is no notion of resource or HTTP method.
  </dd>

  <dt>Level 1: Resources</dt>
  <dd>
    This is the very beginning of RESTfulness: the subjects of the remote calls are encapsulated in logical resources which can be accessed through their own URI.
  </dd>

  <dt>Level 2: Methods</dt>
  <dd>
    As you know from the previous paragraph, HTTP methods are extremely important in a RESTful web service, as clients use them to make certain assumptions about the effect of their requests and the cacheability of the responses. At level 2, we start using the correct HTTP methods for resource URIs.
  </dd>

  <dt>Level 3: Hypermedia</dt>
  <dd data-markdown>
    This is something that many so-called RESTful APIs get wrong. The Uniform Interface constraint requires that a client only really needs to know about the entry-point of a resource. All subsequent interactions should be made through hypermedia provided by the server along with the resource. We'll expand on this in the paragraph below (_"What is HATEOAS?"_).
  </dd>
</dl>

Again, these four levels are all preconditions of a RESTful web service: they must all be implemented (and more) for a web service to be called RESTful.

<aside class="info" data-markdown>
### What Is HATEOAS?
Here's another acronym for you: HATEOAS. It stands for _Hypermedia As The Engine Of Application State_ and it's the concept behind the third level of the Richardson Maturity Model.

The principle is that a client should be able to enter the web service through a fixed entrypoint and perform all subsequent interactions through hypermedia provided by the responses. This distinguishes REST from other architectures where the client constructs its requests from the documentation (either human or machine-readable).

Let's see an example response from an API implementing HATEOAS (note that HTTP headers are preferred and used in this book, but links can also be included in the body):

```json
GET /posts/1

Link: </posts/1>; rel="self",
      </posts/1/comments>; rel="comments"

{
  "id": 1,
  "title": "My blog post"
}
```

As you can see, the response headers contain a `Link` header that exposes two links:

- the first one points the client to the requested resource (which should always be included);
- the second one points it to the comments resource related to this post.

Note that the headers do not tell the client anything about what to do with the related resources: they only tell them _where_ they can be found. HATEOAS does not have anything to do with machines magically understanding how to use our API, as it is commonly  misunderstood; it simply guarantees that clients can perform a flow of related operations without ever "leaving" the web service.

HATEOAS is what makes REST so resilient to change and so similar to how the web works: when you visit a website, you only enter the homepage's URL. From that point, all interactions happen by clicking on a link or button (i.e. they are driven by the hypertext). Unfortunately, HATEOAS is also the least implemented of the REST architectural constraints. As a result, a great part of the "RESTful" APIs are not really RESTful (they usually belong to level 2 of the Richardson Maturity Model): if websites worked the same way, you'd have to build all the URLs by hand, referring to the website's documentation. The situation is so bad that Roy Fielding [wrote a blog post about it](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven). Please, don't be the guy who makes Roy mad.
</aside>

## REST vs. the Rest
This section briefly compares the REST web service architecture with the most popular alternatives, presenting the advantages and drawbacks of each approach.

It is worth noting that this comparison is not formally correct, since REST is an architectural style, while both SOAP and (XML-)RPC are information exchange protocols. For the sake of simplicity, we will be using the protocol name to also reference an architectural style that uses the protocol.

### SOAP
SOAP (Simple Object Access Protocol) is an information exchange protocol designed in 1998 at Microsoft. It reached the W3C recommendation status in 2003, thus becoming a standard.

SOAP uses XML Information Set (which is not necessarily expressed in XML) to deliver structured information. A SOAP message is made of three (sometimes four) parts:

<dl>
  <dt>Envelope</dt>
  <dd>The envelope wraps the XML document, identifying it as a SOAP message.</dd>
  
  <dt>Header</dt>
  <dd>The header contains application-specific information about the SOAP message.</dd>
  
  <dt>Body</dt>
  <dd>The body contains the call and the response of the SOAP message.</dd>
  
  <dt>Fault</dt>
  <dd>This section contains any errors occurred while processing the call.</dd>
</dl>

Here's an example SOAP message (taken from Wikipedia):

```xml
<?xml version="1.0"?>
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope" xmlns:m="http://www.example.org/stock/Surya">
  <soap:Header>
  </soap:Header>
  <soap:Body>
    <m:GetStockPrice>
      <m:StockName>IBM</m:StockName>
    </m:GetStockPrice>
  </soap:Body>
</soap:Envelope>
```

The SOAP protocol presents many disadvantages when compared with REST:

- because of its verbosity, it requires more bandwidth to send;
- XML is slow to parse (even though XML Information Set can be used with JSON);
- because there is no mandatory vocabulary for expressing operation types, there is no guarantee of operation safety, idempotence or cacheability (these are all things that come for free with HTTP);
- there is tight coupling between the client and the server, which means most server-side changes require the clients to be updated accordingly.

The rigidness of SOAP is, at the same time, an advantage: since both the server and the client must adhere to a pre-defined contract, any violations of the contract are easy to spot automatically.

### XML-RPC

- History
- Example structure
- Function vs. information (resource vs. procedure)

Pros:

- Makes more sense in enterprise environments

Cons:

- No HTTP verbs (no guarantee of safety, idempotence or cacheability)
- Less standardized than REST (because there are no resources?)