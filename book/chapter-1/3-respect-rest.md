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

In order to implement REST, a web service has to implement the following architectural constraints:

- http://whatisrest.com/rest_constraints/index

<dl>
  <dt>Client-Server</dt>
  <dd></dd>

  <dt>Stateless</dt>
  <dd></dd>

  <dt>Cache</dt>
  <dd></dd>

  <dt>Interface / Uniform Contract</dt>
  <dd></dd>

  <dt>Layered System</dt>
  <dd></dd>

  <dt>Code-On-Demand</dt>
  <dd></dd>
</dl>

## The Richardson Maturity Model

- http://martinfowler.com/articles/richardsonMaturityModel.html

## REST against the rest

### REST vs. RPC

- http://stackoverflow.com/questions/7410040/what-differentiates-a-rest-web-service-from-a-rpc-like-one

### REST vs. SOAP

- http://stackoverflow.com/questions/19884295/soap-vs-rest-differences
