# T1: Be Boring
Ideally, a consumer of the API should never be surprised by how you handle things - while this is rarely the case in a real-world scenario, you should still aim at creating something that's as reproducible as possible. A good rule of thumb is that people should never be inspecting your response to figure out how to get what they want: they should _just know_.

Be boring: people will thank you for it.

This boils down to just two techniques which we'll be exploring in the next chapters, but let's touch on them briefly already to underline their importance.

## Consistency
This requires some initial work, but should be easy to follow once you get things rolling.

You should ensure that the way you parse incoming requests and the responses you produce to those requests are consistent across the entire set of operations and situations your API handles.

For instance, suppose you decide to use  this response format for all errors:

```json
{
  "error": "error_type",
  "error_message": "A human-readable description of the error",
  "meta": {
    "additional": "info"
  }
}
```

Once you have established _this_ is the way to go for all errors, you can't write a controller action like this:

```ruby
class PostsController < ApplicationController
  def create
    post = Post.new

    if post.save
      render status: :created, json: post
    else
      render status: :unprocessable_entity, json: post.errors
    end
  end
end
```

Can you see what's wrong with it? It does not follow the response format you had settled on at the beginning. In other words, it's not a response that can be easily parsed by a machine just by using your documentation. Consumers will have to _find out_ that you're doing things differently from what you said and that's definitely not a good thing.

The example shows an error response because, in my experience, this is where things go wrong (pun definitely intended) most often, but the same principle applies to all responses and all other kinds of behavior in your API.

## Documentation
You know what's _really_ boring? That's right, documentation.

In order to ensure that your API is understood by everyone, you have to document, document, document and then document some more.

You document the general architecture: the format you use for requests and responses, how youpaginate collections, the authentication protocol you use and any other relevant information.

You document every resource: what it is and how it integrates with the rest of your system, what operations can be performed on it, what conditions are required to perform them, what arguments they accept, what arguments they require and how they respond.

Most importantly, you ensure that all of this is done for every single line of code in your app. No guessing should be required of your consumers.

Also keep in mind that no one will read thousands of lines of an unindexed, ugly Markdown file. If your documentation is not beautiful, no one will read it. If you want to see what good docs look like, have a look at [Stripe's](https://stripe.com/docs/api), then try to do even better.

Anyway, we're going to dedicate an entire chapter to documentation, so don't worry: you won't have to figure it out by yourself.