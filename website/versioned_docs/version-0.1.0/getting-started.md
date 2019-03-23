---
id: version-0.1.0-introduction
title: Getting Started
sidebar_label: Getting Started
original_id: introduction
---

A programmable proxy server.
Please, don't use it in production **yet**! It's nowhere near stable and changing too much.

## Why
Software development is getting harder and harder, on a typical medium/large solution there are lot's of servers at the data path: API Gateways, Load balancers, Cache servers, Proxies, and Firewalls, just to name a few. These servers generate latency and require much more engineering and monitoring to do it right.

The core idea of this project is to do more with less. PipeHub being a programmable proxy allow users to extend and customize it as needed. Features found in other servers can be added with Go packages.

If your requirement is covered by built-in features present on other servers like Nginx and Caddy, you're better of with then. PipeHub shines when you need to add logic that traverses the responsibility of multiple servers like by an HTTP query string, you can choose the server that gonna receive the request and the response can be cached or not. Also is very useful to do deep customizations that are not allowed by other servers, mainly because they are config based. And the best of all, it's done at the same server.

## How
The code is extended with a thing called `pipe`. It's a plain old Go code that is injected at compile time at the application.

Bellow a configuration sample:
```hcl
server {
  http {
    port = 80
  }
}

host {
  endpoint = "google"
  origin   = "https://www.google.com"
  handler  = "base.Default"
}

handler {
  path    = "github.com/pipehub/sample"
  version = "v0.8.1"
  alias   = "base"
}
```

The pipe points to the place where the Go code is, it should be a `go gettable` project. A pipe is a generic processor that can be used on multiple hosts. A host track the endpoint the proxy gonna listen, where the origin is, and which handler gonna be used to process the requests.

A real example of a pipe can be found [here](https://github.com/pipehub/sample).

## How to run it
First, create a config file:
```bash
cp cmd/pipehub/pipehub/pipehub.sample.hcl cmd/pipehub/pipehub/pipehub.hcl
# edit cmd/pipehub/pipehub/pipehub.hcl
```

Generate the binary:
```bash
make generate
```

Execute it:
```bash
./cmd/pipehub/pipehub start -c ./cmd/pipehub/pipehub.hcl
```

It's also possible to build from a docker image, just need to pass the config and a directory where the binary gonna be written:
```bash
docker run --rm -v $(pwd)/pipehub.hcl:/pipehub.hcl -v $(pwd):/pipehub/output pipehub/build:0.1.0
```

By default, it generates a linux amd64 based binary, but this can be changed with this arguments:
```bash
docker run --rm -e GOOS=darwin -e GOARCH=amd64 -v $(pwd)/pipehub.hcl:/pipehub.hcl -v $(pwd):/pipehub/output pipehub/build:0.1.0
```

All the possible `GOOS` and `GOARCH` can be found [here](https://golang.org/doc/install/source#environment).