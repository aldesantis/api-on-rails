# Introduction

It's not a secret anymore that Ruby on Rails can be used to easily create RESTful, rich HTTP APIs.
In fact, a plethora of articles have emerged in the last few years about:

- how to accept and serve JSON in Rails;
- how to use UUIDs instead of incremental IDs;
- how to version your API.

The list goes on and on, but the pattern is that every article will explain the benefits of and
teach you how to do exactly one (or two) things. Most of them are about creating proof-of-concept
examples and fail to address common scenarios and issues in API development.

The result is that the majority of developers have no idea how to properly approach the creation of
an HTTP API and have to resort to trial and error. Combine this with tight development timelines and
demanding stakeholders and you get the perfect recipe for slow, untested, unmanageable, undocumented
monstrosities that "just work".
