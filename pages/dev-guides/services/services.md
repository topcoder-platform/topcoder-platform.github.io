# Services

Benefits of [12 Factor App patters](https://12factor.net/) are numerous and will documented. If you are not familiar with these patterns, we highly recommend reading about these first.


### Logging

**Request IDs**
Request Ids are a way of grouping all the information associated with a given request, even as that request makes its way across a distributed architecture. The benefits are two-fold:
* Provides a tagging mechanism for events that are produced, so that a full report of what occurred and timing in every component touched can be generated.
* Exposes an identifier to users, both internal and external, which can be used to track down specific issues that they’re running into.

In practice, the request ID is a UUID that’s generated at the beginning of a request and stored for its duration. Both Java & Node.js has libraries that support request id tracing. Please refer to Language specific guides for more information.

**Logentries**
We use [Logentries](logentires.com) for log management and analytics. Again, please refer to language specific guides for information on setting these up.

### Configuration
App's config is everything that is likely to vary between deployment environments (local, dev, production etc.). This includes
- external services, dbs, caching services, etc,
- Credentials and secrets
- authentication domains, application names (-dev, -prod, etc)


### Authentication & Authorization
To be updated based once Auth0 & API Gateway architecture is finalized.


### Language & Application type specific guides:

* [Node.js Service](nodejs-services.dev-guide.md)
* [Java Service](java-dropwizard-services.dev-guide.md) (wip)
