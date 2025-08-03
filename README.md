# C++ HTTP Server Project

This project implements a lightweight HTTP server in C++. It supports basic HTTP methods, CGI execution, file upload functionality, MIME type handling, and customizable server configurations through a structured configuration file.

---

## Key Features

- Support for multiple server instances and port bindings
- Configurable via structured input files
- Request handling for GET, POST, and DELETE methods
- File upload functionality through HTTP POST
- Execution of CGI scripts with timeout handling
- MIME type resolution for serving static content
- Response status and error handling with custom error pages
- Efficient client handling using `poll` for non-blocking I/O
- Location-based routing with method restrictions and redirection
- Per-location configuration for CGI scripts and body size limits

---

## Project Structure

```
├── Server.hpp / Server.cpp       # Core server setup and request handling
├── Config.hpp / Config.cpp       # Configuration file parsing and validation
├── Client.hpp / Client.cpp       # Client connection and request processing
├── Response.hpp / Response.cpp   # HTTP response generation and delivery
├── Location.hpp                  # Location-specific configuration logic
├── main.cpp                      # Entry point and server initialization
```

---

## Workflow Overview

1. **Configuration Parsing**: The `Config` class tokenizes and interprets the configuration file, initializing `t_serv` structures with relevant attributes.
2. **Server Setup**: The `Server` class creates sockets for each defined port and prepares them for `poll`-based monitoring.
3. **Request Lifecycle**:
   - **GET** requests serve static content based on MIME type.
   - **POST** requests handle file uploads or trigger CGI execution.
   - **DELETE** requests remove the specified file from the server.
4. **CGI Handling**: For POST routes mapped to CGI, a new process is forked and executed with defined environment variables and I/O redirection. Execution is monitored with a timeout.
5. **Response Generation**: Based on the request outcome, appropriate HTTP responses are constructed and transmitted.

---

## Configuration File Example

```
server {
    host: 127.0.0.1;
    listen: 8080;
    server_name: example;
    root: /var/www/html;
    error_page: 404 /404.html;

    location: /upload {
        allow_methods: POST;
        limits_client_body_size: 1000000;
        cgi_path: .py=/usr/bin/python3;
    }

    location: /files {
        allow_methods: GET DELETE;
    }
}
```

---

## Build & Execution

### Prerequisites

- POSIX-compliant operating system

### Compilation

```bash
g++ -std=c++11 -Wall -Wextra -Werror -o myserver *.cpp
```

### Execution

```bash
./myserver config.conf
```

---

## Additional Notes

- The server ensures proper cleanup of client sockets and temporary files.
- CGI execution paths are adjusted to include the root directory if needed.
- Body size limits and request methods are strictly enforced per location block.
- Configuration files are validated for structure, required fields, and uniqueness.

---
