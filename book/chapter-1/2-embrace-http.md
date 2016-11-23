# T2: Embrace HTTP

HTTP is cool. Not in a young, hipster-y way. HTTP is the dinosaur of the web: it has been here for
almost _forty_ years (HTTP 0.9 dates back to 1980). That's a lot for this industry: if a standard
has survived forty years of evolution, it means the guys behind it have done a pretty good job
thinking about how it could be (ab)used not only in their days, but also in the future.

I expect you already know what a request and a response are (if you don't, you might not be ready
for this book just yet) and you have at least a vague understanding of what the place of headers,
status codes and methods is in a web application of any kind.

## Methods

Before Rails, `GET` and `POST` were the only two HTTP methods we knew. `GET` was used when clicking
on links, `POST` was used for submitting forms and that was about it. Need to create an item? Use
`POST`. Need to update an item? Use `POST`. Need to delete an item? Use `POST`. Or `GET`. Whatever,
dude, as long as it works.

Rails showed us that there are four HTTP methods: `GET`, `POST`, `PUT` and `DELETE`. `GET` retrieves
a resource, `POST` creates a resource, `PUT` updates a resource, `DELETE` deletes a resource. We
finally felt like we were using HTTP at its full potential!

That's not the entire story, either. The HTTP/1.1 specification lists 9 methods. We're not going to
use all of them: some are meant to be handled by the web server rather than our application. For the
sake of completeness, though, I'm going to list them all. Don't worry if some of the stuff in the
table doesn't make much sense right away: we'll define it in the next paragraphs.

Method    | Used* | Safe | Idempotent | Description
--------- | ----- | ---- | ---------- | -----------
`GET`     | Y     | Y    | Y          | Retrieves a representation of the resource at the given URI.
`HEAD`    | Y     | Y    | Y          | Retrieves a representation of resource at the given URI without the body.
`POST`    | Y     | N    | N          | Creates the provided resource child of the one identified by the given URI.
`PUT`     | Y     | N    | Y          | Stores (i.e creates or updates) the provided resource at the given URI.
`PATCH`   | Y     | N    | N          | Submits a partial modification to the resource at the given URI.
`DELETE`  | Y     | N    | Y          | Deletes the resource at the given URI.
`OPTIONS` | Y     | Y    | Y          | Returns the methods the server supports for the given URI.
`TRACE`   | N     | Y    | Y          | Echoes the request, so that the client can trace any modifications made to it.
`CONNECT` | N     | N/D  | N/D        | Converts the connection to a TCP/IP tunnel, useful for upgrading it to SSL.

(*) Whether the method is generally used when designing and implementing a RESTful API. From now
on, these are the only methods we're going to talk about.

<aside class="info" data-markdown>
### POST vs. PUT vs. PATCH

The `POST`, `PUT` and `PATCH` methods do similar things, so much so that they're often confusing
for beginners. (Just think about this: by default, Rails 3 would incorrectly accept `PUT` in place
of `PATCH` for partially updating resources.) For this reason, it's worth spending a few extra words
on them and their differences.

#### POST

`POST` is used when the payload of the request should be processed by the resource at the given URI.
This might sound a bit abstract, so let's try to clarify it with an example:

```json
POST /posts

{
  "title": "My new blog post",
  "body": "Lorem ipsum dolor sit amet, consectetur adipiscing elit."
}
```

This request asks the server to have the `/posts` resource (which can be thought of as the
collection of all posts in the system) accept and process the request entity.

A `POST` request does not necessarily result in a new resource being created (although it does in
our example): it can also be used to annotate or append data to an existing resource. Generally
speaking, the behavior of a `POST` request is determined by the request URI.

#### PUT

The `PUT` method is a bit more predictable than `POST`. It _puts_ - pretty literally - the enclosed
entity at the provided URI. Let's see an example:

```json
PUT /posts/1

{
  "title": "My new blog post",
  "body": "Lorem ipsum dolor sit amet, consectetur adipiscing elit."
}
```

After this request, the client can assume that `GET /posts/1` will return a representation of the
entity that was previous `PUT` (assuming no parallel operations have occurred).

An important distinction is that between `POST` and `PUT` will always replace the entity at the
given URI if it already exists (unless of course there is an application-level constraint forbidding
it).

#### PATCH

`PATCH` is used to apply a partial modification to an existing resource and is perhaps the simplest
of the methods to comprehend.

For instance, if we wanted to edit the title of an existing post, we could issue the following
request:

```json
PATCH /posts/1

{
  "title": "My new post title"
}
```

In these cases, `PATCH` is preferred over `PUT` for two reasons:

1. the client might not want or be able to provide the full updated entity, and
2. by sending exclusively the attributes to update, the client can save bandwidth.
</aside>

### Safety and Idempotence

You might have noticed that some methods are both safe and idempotent, some are not safe but
idempotent and some are neither safe nor idempotent.

So, what's the difference between idempotence and safety? Here are the definitions from
[section 9 of RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) (the one that
defines HTTP/1.1):

> #### 9.1.1 Safe Methods
>
> [...] the convention has been established that the GET and HEAD methods SHOULD NOT have the
> significance of taking an action other than retrieval. These methods ought to be considered
> "safe". [...]
>
> Naturally, it is not possible to ensure that the server does not generate side-effects as a result
> of performing a GET request [...]. The important distinction here is that the user did not request
> the side-effects, so therefore cannot be held accountable for them.
>
> #### 9.1.2 Idempotent Methods
>
> Methods can also have the property of "idempotence" in that (aside from error or expiration
> issues) the side-effects of N > 0 identical requests is the same as for a single request. The
> methods GET, HEAD, PUT and DELETE share this property. Also, the methods OPTIONS and TRACE SHOULD
> NOT have side effects, and so are inherently idempotent.

In other words, a **safe method** is a method that does not hold the user accountable for any
modification to the state of the system. For instance, an API might increment the visit counter for
a blog post every time it is retrieved. Even though it _does_ apply a modification, this request
would still be considered safe, because the user did not request that the counter be updated.

An **idempotent method**, on the other hand, guarantees that only the first of a set of identical
requests will modify the state of the system. You should note that the RFC does not mention the
response at all when talking about idempotency: sending the same `DELETE` request twice for the same
blog post, for instance, will result in a `200 OK` (or `204 No Content`) on the first time and in a
`404 Not Found` on the second, because the post cannot be found. The request is still idempotent
because the state of the system was not further modified by the second request.

As you might have noticed, our first example cannot be considered strictly idempotent either, since
every `GET` request for the blog post increments the counter (i.e. modifies the state of the
system). You'll see that, in practice, some of your implementations will not be idempotent (or
safe), even if they should. That's okay, as long as you don't forgo idempotency (or safety) in a way
that breaks the client's expectations about the result of their requests.

<aside class="info" data-markdown>
#### Why is PATCH non-idempotent?

You might be confused by the fact that PATCH is not included in the list of idempotent methods.
After all, modifying the same resource in the same way twice should always yield the same result,
right?

Well, PATCH is kind of a special snowflake in the HTTP world, in that it is not idempotent by
definition, but _can_ be idempotent by implementation.

Suppose that our blog posts API allows us to schedule posts to be published in the future. We can
modify the date of a post's publication with a request like this one:

```json
 PATCH /posts/1

 {
   "published_on": 1479909858
 }
 ```

It requests that the server sets the `published_on` property of the post to `1479909858` (a UNIX
time). This kind of PATCH request is idempotent, because - no matter if it's sent one or a million
times - the `published_on` property of the post will still be `1479909858` at the end.

But that's not the only possible implementation of this feature. Suppose our API accepts this kind
of request, that postpones the publication of the post by a day:

```json
 PATCH /posts/1

 {
   "published_on": "+1d"
 }
 ```

Can you spot the difference? Unlike the previous one, if we run this request a trillion times, it's
very likely that [no one's going to read our post](http://phys.org/news/2015-02-sun-wont-die-billion-years.html).
This is why `PATCH` is not idempotent by definition, even though we can (and should) make it so.
</aside>

## Headers

- Accept
- Content-Type
- Authorization
- Cache
- https://en.wikipedia.org/wiki/List_of_HTTP_header_fields

## Status Codes

- Take from Panther operation errors
