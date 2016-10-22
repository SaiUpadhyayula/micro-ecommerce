# Ecommerce application

Rest-full, Hipermedia-based distributed  ecommerce application.

## REST based micro-services with Spring Boot

- Catalog(JPA-SQL product calatlog).
- Reviews (MongoDB product reviews)
- Recommendations (Neo4J recommendations)
- Orders (JPA)
- API Gateway (Reactive API proxy)


![Alt text](https://github.com/idugalic/micro-ecommerce/blob/master/apigateway.png)



### Catalog

The Catalog consists of categorized products. Products can be in one ore more categories, and category can contain one ore more products.
Products and Categories are exposed as REST resources using Spring Data RESTs capability to automatically expose Spring Data JPA repositories contained in the application.

### Reviews 

Review is entity(document) related to product by productId and to customer(user) by userName. The repository under this service is MongoDb
Reviews are exposed as REST resources using Spring Data RESTs capability to automatically expose Spring Data Mongo repositories contained in the application.

### Recommendations 

This service consists of Person and Product entities and Like relation entity that links them.
Recommendations are exposed as REST resources using Spring Data RESTs capability to automatically expose Spring Data Neo4j(Graph) repositories contained in the application.

### Orders

The implementation consists of mainly two parts, the order and the payment part. The Orders are exposed as REST resources using Spring Data RESTs capability to automatically expose Spring Data JPA repositories contained in the application. The Payment process  are implemented manually using a Spring MVC controller (PaymentController).

We're using Spring Data REST to expose the OrderRepository as REST resource without additional effort.

Spring Hateoas provides a generic Resource abstraction that we leverage to create hypermedia-driven representations. Spring Data REST also leverages this abstraction so that we can deploy ResourceProcessor implementations (e.g. PaymentorderResourceProcessor) to enrich the representations for Order instance with links to the PaymentController.



###  API Gateway

Implementation of an API gateway that is the single entry point for all clients. The API gateway handles requests in one of two ways. Some requests are simply proxied/routed to the appropriate service. It handles other requests by fanning out to multiple services.

http://microservices.io/patterns/apigateway.html

## Backing services
The premise is that there are third-party service dependencies that should be treated as attached resources to your cloud native applications. The key trait of backing services are that they are provided as bindings to an application in its deployment environment by a cloud platform. Each of the backing services must be located using a statically defined route


### Config server

The configuration service is a vital component of any microservices architecture. Based on the twelve-factor app methodology, configurations for your microservice applications should be stored in the environment and not in the project.

The configuration service is essential because it handles the configurations for all of the services through a simple point-to-point service call to retrieve those configurations. The advantages of this are multi-purpose.

Let's assume that we have multiple deployment environments. If we have a staging environment and a production environment, configurations for those environments will be different. A configuration service might have a dedicated Git repository for the configurations of that environment. None of the other environments will be able to access this configuration, it is available only to the configuration service running in that environment.

When the configuration service starts up, it will reference the path to those configuration files and begin to serve them up to the microservices that request those configurations. Each microservice can have their configuration file configured to the specifics of the environment that it is running in. In doing this, the configuration is both externalized and centralized in one place that can be version-controlled and revised without having to restart a service to change a configuration.

With management endpoints available from Spring Cloud, you can make a configuration change in the environment and signal a refresh to the discovery service that will force all consumers to fetch the new configurations.

### Service registry (Eureka)

Netflix Eureka is a service registry. It provides a REST API for service instance registration management and for querying available instances. Netflix Ribbon is an IPC client that works with Eureka to load balance requests across the available service instances.

When using client-side discovery, the client is responsible for determining the network locations of available service instances and load balancing requests across them. The client queries a service registry, which is a database of available service instances. The client then uses a load balancing algorithm to select one of the available service instances and makes a request.

The client-side discovery pattern has a variety of benefits and drawbacks. This pattern is relatively straightforward and, except for the service registry, there are no other moving parts. Also, since the client knows about the available services instances it can make intelligent, application-specific load balancing decisions such as using hashing consistently. One significant drawback of this pattern is that it couples the client to the service registry. You must implement client-side service discovery logic for each programming language and framework used by your service clients


### Authorization (Oauth2) server

For issuing tokens and authorize requests.


## Security

Spring Cloud Security offers a set of primitives for building secure applications and services with minimum fuss. 
A declarative model which can be heavily configured externally (or centrally) lends itself to the implementation of large systems of co-operating, remote components, usually with a central indentity management service. It is also extremely easy to use in a service platform like Cloud Foundry. 
Building on Spring Boot and Spring Security OAuth2 we can quickly create systems that implement common patterns like single sign on, token relay and token exchange.


## Running Instructions
### ___Local___
- Before try to run the services, make sure you have Neo4J and MongoDB running on localhost (on default ports).
- To run the services localy, just execute "mvn spring-boot:run" in each project subfolder. Run Configuration server project first, then Authorizatins service ...
- After you run services, trigger shell scripts under script folder of each service to create sample data.

#### Usage

- Get a token: $ curl -X POST -vu acme:acmesecret http://localhost:9999/uaa/oauth/token -H "Accept: application/json" -d "password=idugalic&username=idugalic&grant_type=password&client_secret=acmesecret&client_id=acme"
- Catalog service: $ curl http://localhost:8080/ -H "Authorization: Bearer <YOUR TOKEN>"
- Reviews service: $ curl http://localhost:8081/ -H "Authorization: Bearer <YOUR TOKEN>"
- Recommendations service: $ curl http://localhost:8082/ -H "Authorization: Bearer <YOUR TOKEN>"
- Orders service: $ curl http://localhost:8083/ -H "Authorization: Bearer <YOUR TOKEN>"
- Catalog service(proxy) : $ curl http://localhost:9000/catalog -H "Authorization: Bearer <YOUR TOKEN>"
- Reviews service(proxy): $ curl http://localhost:9000/reviews -H "Authorization: Bearer <YOUR TOKEN>"
- Recommendations service(proxy): $ curl http://localhost:9000/recommendations -H "Authorization: Bearer <YOUR TOKEN>"
- Orders service(proxy): $ curl http://localhost:9000/orders -H "Authorization: Bearer <YOUR TOKEN>"
- Mobile service (agregate): $ curl http://localhost:9000/product/1 -H "Authorization: Bearer <YOUR TOKEN>"


