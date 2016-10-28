# HTTP APIs: The Right Way

## Introduction

It's not a secret anymore that Rails can be used to easily create RESTful, rich HTTP APIs. In fact,
a plethora of articles have emerged in the last few years about:

- how to accept and serve JSON in Rails;
- how to use UUIDs instead of incremental IDs;
- how to version your API.

The list goes on and on, but the pattern is that every article will explain the benefits of and
teach you how to do exactly one (or two) things. Most of them are about creating proof-of-concept
examples and fail to address common scenarios and issues in API development.

The result is that the majority of developers have no idea how to properly approach the creation of
an HTTP API and have to resort to trial and error. Combine this with tight development timelines and
demanding clients and you get the perfect recipe for slow, untested, unmanageable, undocumented
monstrosities that "just work".

These are just some of the technical challenges developers face daily when working on an HTTP API:

- What format do I use to document this?
- Okay, but what do I write in the documentation?
- What status code do I respond to this with?
- And what do I respond with if it breaks?
- Is this better done synchronously or asynchronously?
- How do I authenticate and authorize users?
- Not OAuth. Oh, God, please, not OAuth.
- How do I let my clients upload files?
- Great. Now, how do I let my clients upload files _quickly_?

After about three years, ten projects, countless pages of documentation and a sane amount of
swearing, I have decided to put together all I've learned about doing HTTP APIs "the right way" and
write a book (sort of) about it. But this is not just a mere collection of recipes: the book will
take you through the design and implementation of a fully-fledged JSON API and provide core
principles to guide you in each step along the journey, it aims to be the go-to reference

By the time you've reached the end, you will be equipped with workflow you can apply to _any_
particular set of business requirements and _any_ new challenges that might arise.

Are you ready? Buckle up, because it's going to be fun.
