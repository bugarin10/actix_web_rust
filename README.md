# How to containarize a web framework in Actix with Rust

This code is a simple example of a web server implemented using the Actix web framework in Rust. Here's a brief explanation of how it works:

1. **Route Definitions**:
   - The `hello` function defines a handler for GET requests to the root path ("/"). It returns a simple response with the body "Rafael Dávila".
   - The `echo` function defines a handler for POST requests to the "/echo" path. It echoes back the request body.
   - The `manual_hello` function defines a handler for GET requests to the "/hey" path. It returns a simple response with the body "Hey there!".

2. **Main Function**:
   - The `main` function sets up the Actix web server.
   - It creates a new Actix `HttpServer` and configures it with a closure that sets up the application routes.
   - The `bind` method binds the server to the address "127.0.0.1" and port 8080.
   - Finally, the `run` method starts the server and awaits its completion.

Now, to build a Docker image and deploy the application:

1. **Dockerfile**:
   You need to create a Dockerfile to define the environment and dependencies for your application. Here's a basic example:

   ```Dockerfile
   FROM rust:latest AS builder
   WORKDIR /usr/src/app
   COPY . .
   RUN cargo build --release

   FROM debian:buster-slim
   WORKDIR /usr/src/app
   COPY --from=builder /usr/src/app/target/release/actix_app .
   EXPOSE 8080
   CMD ["./actix_app"]
   ```

   This Dockerfile sets up a multi-stage build. It first compiles the Rust application using a Rust-based image and then creates a lightweight production image based on Debian Buster slim.

2. **Building and Running the Docker Image**:
   Assuming you have Docker installed, you can use the following commands to build and run the Docker image:

   ```bash
   # Build the Docker image
   docker build -t actix-app .

   # Run the Docker container
   docker run -d -p 8080:8080 actix-app
   ```

   - `-t actix-app`: Tags the Docker image with the name "actix-app".
   - `-d`: Runs the container in detached mode (background).
   - `-p 8080:8080`: Maps port 8080 on the host to port 8080 in the container.

With these commands, your Actix web application should be running inside a Docker container and accessible at `http://localhost:8080`. Adjust the ports and other configurations as needed based on your deployment environment.