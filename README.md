# How to containarize a web framework in Actix with Rust

This repository contains a simple example of an Actix web server implemented in Rust.

## Rust Code

```rust
use actix_web::{get, post, web, App, HttpResponse, HttpServer, Responder};

#[get("/")]
async fn hello() -> impl Responder {
    HttpResponse::Ok().body("Rafael Dávila")
}

#[post("/echo")]
async fn echo(req_body: String) -> impl Responder {
    HttpResponse::Ok().body(req_body)
}

async fn manual_hello() -> impl Responder {
    HttpResponse::Ok().body("Hey there!")
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    HttpServer::new(|| {
        App::new()
            .service(hello)
            .service(echo)
            .route("/hey", web::get().to(manual_hello))
    })
    .bind(("127.0.0.1", 8080))?
    .run()
    .await
}
```

## Running with Docker

### Dockerfile

```Dockerfile
# Use the official Rust image as the base image
FROM rust:latest as builder

# Set the working directory in the container
WORKDIR /usr/src/app

# Copy the source code into the container
COPY . .

# Build the Rust application
RUN cargo build --release

# Use a smaller base image for the final container
FROM debian:buster-slim

# Set the working directory in the container
WORKDIR /usr/src/app

# Copy the compiled binary from the builder stage to the final image
COPY --from=builder /usr/src/app/target/release/actix .

# Expose the port on which your Actix web service listens
EXPOSE 8080

# Command to run the binary when the container starts
CMD ["./actix"]
```

### Building and Running the Docker Image

```bash
# Build the Docker image
docker build -t <USERNAME>/image_name .

# Run the Docker container
docker run -d -p 8080:8080 image_name
```

- `-t image_name`: Tags the Docker image with the name "actix-app".
- `-p 8080:8080`: Maps port 8080 on the host to port 8080 in the container.

With these commands, your Actix web application should be running inside a Docker container and accessible at `http://localhost:8080`. Adjust the ports and other configurations as needed based on your deployment environment.
