# What is REST API?

**REST** is an acronym for **Representational State Transfer**, it is an architectural style for distributed hypermedia systems.

In order to understand REST, one needs to know what an API is and who is a client and what is a Resource.

- **API** - this is an interface that helps two softwares to communicate.
- **Client** - this is the person who uses the API, client can also be a web browser.
- **Resource** - any object the API can provide information about.

RESTful  web application exposes information about itself in the form of information about its resources. It also enables the client to take actions on those resources.

REST enables transfer of a representation data from the server to the client. 

The representation of the state can be in a **JSON** format and probably for most API this is indeed the case, it can also be **XML** or **HTML** format.

Server operations when a client sends an API request :

- an identifier for the resource you are interested in, URL known as the endpoint.
- the operation server is supposed to operate, common methods are **CRUD**.

In order for an API to be RESTful, it has to adhere to 6 constraints:

**Uniform Interface** -> in order to obtain a uniform interface, multiple architectural constraints are needed to guide the behaviour of components.

- the request to the server has to include a resource identifier.
- the response the server returns includes enough information so the client can modify the resource.
- each request to the API contains all the information the server needs to perform the request and each response the server returns contains all the information the client needs in order to understand the response.
- hypermedia as the engine of application state.

**Client -server separation** -> client and server act independently, each on its own, and the interaction between them is only in the form of request, initiated by the client only and responses which the server sends to the client only as a reaction to a request.

The server awaits a request from the client.

**Stateless** -> means that the server does not remember anything about the user who uses the API.

**Layered System** -> allows the system to be composed of hierarchical layers by constraining component behaviour such that each component cannot “see” beyond the immediate layer with which they are interacting.

**Cacheable** -> data in the server sends contain information whether is cacheable, it ut is it might contain some sort of a version number, the version number is what makes caching possible. 

The client knows which version of the data it already has, the client can avoid sending a new request, client should also know if the version number has expired in which the client should send another request to the server to get the post updated data about the state of the resource.

**Code on demand** -> REST allows client functionality to be extended by downloading and executing code in the form of applets or scripts. The simplifies clients by reducing the number of features required to be pre-implemented.


See you in the next section 👉