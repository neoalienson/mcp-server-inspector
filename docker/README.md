# MCP Inspector Docker Image

This directory contains the Docker configuration for running the MCP Inspector in a containerized environment.

## Building the Image

To build the Docker image, run the following command from the project root:

```bash
docker build -t mcp-inspector -f docker/Dockerfile .
```

## Running the Container

To run the MCP Inspector container, use:

```bash
docker run -p 6274:6274 mcp-inspector
```

The inspector UI will be accessible at `http://localhost:6274`. By default, the container binds to all network interfaces (0.0.0.0), making it accessible from other devices on the network. For security reasons, you may want to restrict this to localhost only (see below).

## Connecting to MCP Servers

The containerized MCP Inspector supports network-based transports:

- **SSE (Server-Sent Events)**
- **Streamable HTTP**

Example for connecting to an SSE server:

```bash
docker run -p 6274:6274 mcp-inspector
```

Then in the UI, configure the connection to your SSE endpoint.

## Limitations

### No STDIO Transport Support

The containerized version of the MCP Inspector **does not support STDIO transport mode**. This limitation exists because:

1. Docker containers run as isolated processes that cannot directly access the host's STDIO streams
2. STDIO transport requires direct process communication which is not available in a containerized environment

To inspect local MCP servers that use STDIO transport, you should use the inspector directly on your host system rather than in a container.

### Network Configuration

When running in a container, ensure that:

1. Your MCP servers are accessible from within the container
2. You expose the necessary ports for network-based transports
3. You configure the correct URLs for SSE or Streamable HTTP connections

## Security Considerations

The MCP Inspector includes authentication by default. When running in a container:

1. A random session token is generated at startup
2. The token is printed to the container logs
3. You must use this token to authenticate with the inspector

Example of retrieving the session token:

```bash
docker logs <container-id>
```

Look for the line containing "Session token" in the logs.

## Custom Configuration

You can customize the inspector behavior using environment variables:

```bash
docker run -p 6274:6274 -e CLIENT_PORT=8080 -e SERVER_PORT=9000 mcp-inspector
```

Common environment variables:
- `CLIENT_PORT`: Port for the inspector UI (default: 6274)
- `SERVER_PORT`: Port for the proxy server (default: 6277)
- `HOST`: Bind address (default: 0.0.0.0 - all interfaces)
- `MCP_PROXY_AUTH_TOKEN`: Predefined authentication token
- `DANGEROUSLY_OMIT_AUTH`: Disable authentication (NOT RECOMMENDED)

To restrict the container to localhost only for security reasons, use:
```bash
docker run -p 127.0.0.1:6274:6274 -e HOST=localhost mcp-inspector
```

For more configuration options, see the main README.md in the project root.
