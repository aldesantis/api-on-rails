# T3: Respect REST

REST is an acronym standing for _REpresentational State Transfer_ and a way of architecturing web
services. It was defined in 2000 by Roy Fielding, one of the authors of the HTTP specification and
ex-chairman of the Apache Software Foundation, in his dissertation entitled
[_Architectural Styles and the Design of Network-based Software Architectures_](http://www.ics.uci.edu/~fielding/pubs/dissertation/fielding_dissertation.pdf).

Later on, Fielding described REST as the result of a long discussion of the HTTP specification:

> Throughout the HTTP standardization process, I was called on to defend the design choices of the
> Web. That is an extremely difficult thing to do within a process that accepts proposals from
> anyone on a topic that was rapidly becoming the center of an entire industry. I had comments from
> well over 500 developers, many of whom were distinguished engineers with decades of experience,
> and I had to explain everything from the most abstract notions of Web interaction to the finest
> details of HTTP syntax. That process honed my model down to a core set of principles, properties,
> and constraints that are now called REST.

During the last decade, REST has quickly become the de-facto standard for the implementation of
public-facing web APIs, even though a few alternatives exist. A comparison of REST with the two most
popular web service architectures, SOAP and RPC, can be found in the last paragraph and will help
clarify why REST has become so popular.

## Concepts

These are the key concepts of a RESTful web service. We'll be expanding on what constraints REST
imposes on each one of them in the next paragraph.

<dl data-markdown>
  <dt>Resource</dt>
  <dd>
    A resource is a concept in your application (user, blog post etc.) which can be uniquely
    identified and operated on. Nouns are preferred over verbs for resource names. When REST is
    implemented over HTTP, resources are identified through a URI (e.g. `/users/1`).
  </dd>

  <dt>Representation</dt>
  <dd>
    A representation is a document which contains the state of a resource. Representations might be
    created both by the server (when it sends a resource to the client) and the client (e.g. when
    describing what updates to apply to a resource). For instance, the same user could be served in
    JSON and XML.
  </dd>

  <dt>Server</dt>
  <dd data-markdown>
    A server stores information about the resources accessible through the web service and allows
    clients to operate on them. Note that a logical server might consist of several physical servers
    operating transparently under the same identity. In fact, this is usually the case in large web
    services, for redundancy and performance. Servers do not expose a UI (User Interface).
  </dd>

  <dt>Client</dt>
  <dd>
    A client calls a server to operate a resources. Clients do not store any authoritative
    information about the resources, but might store context about them and/or how the web service
    is being used (e.g. authentication information) to improve the UX. Clients also provide a UI
    which can be graphical, textual or both.
  </dd>

  <dt>Request/response</dt>
  <dd>
    RESTful web services operate in request/response cycles. The client sends a request to the
    server, which handles it and returns a response.
  </dd>
</dl>

## Architectural constraints

In order to implement REST, a web service has to implement the following architectural constraints.

- http://whatisrest.com/rest_constraints/index

<dl>
  <dt>Client-Server</dt>
  <dd>
    Client-server refers to an architecture where the server stores all authoritative information
    about the resources of the web service. The client does not operate on the resources directly:
    it merely asks the server to perform operations on the resources.
  </dd>

  <dt>Stateless</dt>
  <dd>
    The server does not store any information about the state of the clients interacting with it.
    This makes a RESTful service to scale more easily as the number of clients grows. Additionally,
    it makes implementation and testing much easier.
  </dd>

  <dt>Cache</dt>
  <dd>
    All the responses provided by a server should contain explicit or implicit caching indications
    (i.e. whether the resource can be cached or not and, if yes, for how long).
  </dd>

  <dt>Uniform Interface</dt>
  <dd>
    All resources on the server should be accessed through the same interface. This minimizes the
    effort required to learn and use a RESTful service (it arguably makes the implementation easier
    as well). Over HTTP, this constraint is implemented by using HTTP methods and media types.
  </dd>

  <dt>Layered System</dt>
  <dd>
    A RESTful service is made of one or more layers that can only "see" the next one. These layers
    can be anything from load balancers to authentication proxies. The client does not know anything
    about any layers past the most external ones.
  </dd>

  <dt>Code-On-Demand</dt>
  <dd>
    This is an optional constraint of REST: a server can provide code that extends the functionality
    of and offload certain computations to the client.
  </dd>
</dl>

If any of the above constraints is not respected, a web service cannot be considered strictly
RESTful. The only exception is the **Code-On-Demand** constraint, which is optional.

## The Richardson Maturity Model

RESTfulness is a binary feature: your web service is either RESTful, when it implements all the
requirements, or non-RESTful. However, not all non-RESTful services are created alike: some of them
are very non-RESTful, some are a little bit non-RESTful, some are five minutes of work away from
RESTfulness.

Leonard Richardson devised a scale comprised of four levels of (non-)RESTfulness, where each level
builds upon and expands the pervious one. It is called the
[Richardson Maturity Model](https://www.crummy.com/writing/speaking/2008-QCon/act3.html) and
[Martin Fowler](http://martinfowler.com/articles/richardsonMaturityModel.html) wrote a blog psot
that summarizes it pretty effectively, but we're also going to give our description of it:

<dl>
  <dt>Level 0: RPC over HTTP</dt>
  <dd>
    This level is the most basic one: at this point, the web service is simply using HTTP as a
    tunnel to accept requests for a remote procedure and deliver their responses, all through thee
    same endpoint. There is no notion of resource or HTTP method.
  </dd>

  <dt>Level 1: Resources</dt>
  <dd>
    This is the very beginning of RESTfulness: the subjects of the remote calls are encapsulated in
    logical resources which can be accessed through their own URI.
  </dd>

  <dt>Level 2: Methods</dt>
  <dd>
    As you know from the previous paragraph, HTTP methods are extremely important in a RESTful web
    service, as clients use them to make certain assumptions about the effect of their requests and
    the cacheability of the responses. At level 2, we start using the correct HTTP methods for
    resource URIs.
  </dd>

  <dt>Level 3: Hypermedia</dt>
  <dd data-markdown>
    This is something that many so-called RESTful APIs get wrong. The Uniform Interface constraint
    requires that a client only really needs to know about the entry-point of a resource. All
    subsequent interactions should be made through hypermedia provided by the server along with the
    resource. We'll expand on this in the paragragh below (_"What is HATEOAS?"_).
  </dd>
</dl>

Again, these four levels are all preconditions of a RESTful web service: they must all be
implemented (and more) for a web service to be called RESTful.

<aside class="info" data-markdown>
### What is HATEOAS?

...
</aside>

## REST against the rest

### REST vs. RPC

- http://stackoverflow.com/questions/7410040/what-differentiates-a-rest-web-service-from-a-rpc-like-one

### REST vs. SOAP

- http://stackoverflow.com/questions/19884295/soap-vs-rest-differences
