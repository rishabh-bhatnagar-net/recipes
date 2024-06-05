---
title: "Golang Middlewares"
categories: [golang]
tags: [golang,backend,middleware,rest,api]
---

# Middlewares In Golang

<hr />

## About & Context & Examples & 
In the simplest terms: Middlewares are the intermediators or a layer of 
abstraction between the sender and the receiver of any request. According to IBM's definition [^1]
[^1]: https://www.ibm.com/in-en/topics/middleware
> Middleware is software that enables one or more kinds of communication or 
> connectivity between two or more applications or application components in a distributed network.

Middlewares are functions that take a first order function and returns another
first order function of the same type. For the math geeks, middlewares can be
called an identity morphism. That is, for each object of a category, the domain
and co-domain (or range) of a morphism must be the same.
 
For example: In real life, an interpreter who translates source text/speech from
one language to another is a middle-man. In software world, we'd call it as a 
middleware.

<hr />

### HTTP Middlewares:
HTTP middlewares are the middlewares where the type of function that we deal with is a httpHandler or an abstraction for a request.
 
| ```User --Req--> Endpoint --Req--> Middleware --Req--> Business Logic``` |
| :----------------------------------------------------------------------: |
|         <small>Sample Request Lifecycle With Middleware</small>          |


With golang, we get a minimal yet very powerful builtin `http` library.
We had talked about middleware accepting a certain type of input and returning 
the same type of object. For Http middlewares, `http.Handler` is the type that 
we deal with. http.Handler is a simple interface that has only one method 
`ServeHttp` that must be present with the object to be called a `http.Handler` 
type.  


```go
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
} 
```
{:refdef: style="text-align: center;"}
Interface definition of http.Handler
{:refdef:} 


### Pre-Requisite For Examples
Let's define a sample golang application with an endpoint to be used in the further examples.
```go
```

### A. Relay Middleware
Let's get started defining a simple middleware that does nothing. It relays 
the request got to it without modifying any attributes of the request or doing 
any processing on the request params.

```go
func RelayMiddleware(originalHandler http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		originalHandler.ServeHTTP(w, r)
	})
}
```
{:refdef: style="text-align: center;"}
Relay Middleware Example
{:refdef:}

The Usage of the relay-middleware is very simple. You can wrap the endpoint 
handler function with the Call to the RelayMiddleware.
```go
package main

import "net/http"

func RelayMiddleware(originalHandler http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		originalHandler.ServeHTTP(w, r)
	})
}

func greet(w http.ResponseWriter, r *http.Request) {
	_, _ = w.Write([]byte("hello world")) // ignoring the error
}

func main() {
	greetHandler := http.HandlerFunc(greet)

	http.Handle("/greet", greetHandler)
	http.Handle("/greet-with-middleware", RelayMiddleware(greetHandler))

	panic(http.ListenAndServe(":8069", nil))
}
```

### B. Logging Middleware
These come under the category of middlewares that are stateless with respect to
the request-response model. That is, they do not modify either incoming request 
or the outgoing response. These might just sniff in some information or store 
the meta-data around it
```go
func LoggingMiddleware(originalHandler http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		inTime := time.Now()
		log.Printf("got a new request @%v\n", inTime)

		originalHandler.ServeHTTP(w, r)

		outTime := time.Now()
		log.Printf("request got @%v was served by %v\n", inTime, outTime)
	})
}
```
The usage is similar as that of the Relay Middleware

<hr />

## Endpoints vs Middleware  

| Endpoint                                  | Middleware                                                        |
| ----------------------------------------- | ----------------------------------------------------------------- |
| Can be called by users                    | Cannot be called directly by users                                |
| Used by end-users                         | Used by developers to intercept and modify requests               |
| Represents a specific URL or URI          | Operates on requests and responses                                |
| Handles incoming requests                 | Sits between the client and the server, modifying requests        |
| Processes client requests                 | Performs pre- and post-processing tasks on requests and responses |
| Returns a response to the client          | Can be chained together for request processing                    |
| Part of the server application            | Part of the request processing pipeline                           |
| Executes the main logic of an application | Can be reusable across multiple endpoints                         |
| Typically represented by a URL route      | Can be applied globally or selectively to specific routes         |

<hr />

## Benefits of using middleware
1. core business logic is separated from other things.
2. grouping more than one endpoint
