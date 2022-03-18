# Switchboard

Switchboard is a no-code integration platform allowing users to build workflows for integrating multiple systems/APIs 
together.

An API which can be integrated with is known as an "App". The workflow, which is known as a "Board" in Switchboard, is a
Directed Acyclic Graph (DAG) where data passes from one App to another, potentially through other data processors called
"Actions".

Users are provided with a UX which dynamically changes depending on the App they're utilising. In order to facilitate 
this functionality, Switchboard uses industry standards for understanding an APIs capability and their data structures.
Currently, only HTTP APIs documented with OpenAPI 3.0.x specifications are supported, however future versions of
OpenAPI are planned, as is support for other protocols such as SOAP, gRPC and GraphQL.

## Creating Apps

In order to create an App, the user must provide a fully functional OpenAPI specification for the API. This may include 
some extra information which is considered optional by the formal OpenAPI specification, as such this document 
highlights the minimum requirements in order for Switchboard to accept it.

The formal specification for OpenAPI 3.0.3 can be found [here][openapi-3-0-3].

There is also a sample specification using Switchboard's `CreateApp` operation, demonstrating what a valid 
specification looks like at [./switchboard-sample-openapi.yaml](./switchboard-sample-openapi.yaml). In reality, proper 
specifications will be significantly larger since most APIs contain multiple operations.

### Servers

There must be at least one `/servers` element, containing valid details on where the API is hosted.

### Operations

The [Operation Object][operation-object] defines a single operation available on the API.

In order for the UI to render the correct forms for an operation, every operation in the specification must have 
`parameters`, `requestBody` and `responses` elements correctly defined as per that operation's definition. These 
elements should only be omitted if they do not apply to a given operation (i.e. some requests do not have any parameters
and/or request bodies)

If these elements are defined, the `schema` field should set and contain a valid JSON schema.

### Security Schemes

The [Security Scheme Object][security-scheme-object], defined at `/components/securitySchemes`, should be populated with
the available methods of authentication. This is important since most APIs require authentication in order to interact 
with it, and without it Switchboard won't know how to authenticate.

Currently, Switchboard only supports `oauth2` and `openIdConnect`, however this should be configured to be the correct 
type for the API, so that we can decide a priority on which other types we should implement next.

### Security Requirement Object

The [Security Requirement Object][security-requirement-object], defined on the root `security` field, or in the 
`security` field of an operation, declares which security schemes (referenced above) apply to the API. If defined at the
root of the document then all operations require that security scheme be satisfied. The field may also be defined on 
individual operations to override the default defined on the root.

[openapi-3-0-3]: https://spec.openapis.org/oas/v3.0.3
[operation-object]: https://spec.openapis.org/oas/v3.0.3#operation-object
[security-scheme-object]: https://spec.openapis.org/oas/v3.0.3#security-scheme-object
[security-requirement-object]: https://spec.openapis.org/oas/v3.0.3#security-requirement-object