One of the integration tests in an nginx module that I'm working on started
failing today.

It's because I was using the `dd-trace` npm module without specifying a
particular version, and there was recently a release that changes the
behavior of my FastCGI server.

This repository contains a docker-compose setup having three services:

- `bugged` is a FastCGI server using the new version 2.4.1 of dd-trace-js.
  It throws an exception when processing a request.
- `unbugged` is the same FastCGI server, but using the older version 2.4.0
  of dd-trace-js.  It does not encounter any errors when processing a request.
- `nginx` is a reverse proxy that binds to port 8080 on the host and accepts
  HTTP requests to the endpoints `/bugged` and `/unbugged`.

`./run` builds the service images, brings the services up, starts monitoring
their logs, and then uses `curl` to send a request to each of the FastCGI
servers.

`/bugged` encounters an error, while `/unbugged` does not, as can be seen in
their logs.
