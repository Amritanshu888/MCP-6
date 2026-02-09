- SSE is Server Sent Events

## Stdio Vs SSE
STDIO(Standard Input/Output) Transport is primarily used for inter-process communication within the same system. It allows a client application to send data to a server application through the standard input and recieve responses via standard output streams. STDIO Transport utilizes standard input and output streams for communication. It's particularly suitable for command-line tools and local integrations where the client and server operate within the same process.

- That means with the help of this stdio connections u can make the communications from ur client to servers in ur local systems and here only one client can access one server. U can't use multiple clients to access the same server. This is the issue with the stdio(standard input and output method).
- Here we will see how to create MCP client and Server with the help of SSE.
- Here we will be implementing something called as an API and it will be running on a URL.
- Initially we will be creating a local host url with a port and later on we can also deploy this particular API over the cloud platform and it will be running as a remote and u can access this particular server with any kinds of MCP client.
- We can also use Multiple MCP client to access the same server. --> This is the advantage to use the SSE(Server Sent Event).

- To run any kind of MCP servers we need MCP host and MCP client, in this case u can use Claude Desktop, u can use Cursor IDE, even u can also create ur Custom MCP client.
- Whenever we created any kind of Clients we are trying to connect with the MCP servers, and that MCP server was running in my Local System, so whatever MCP servers we have created till now it was running in my Local System, to access that MCP server we had to give the local file locations.
- For running such servers we were using STDIO.

## SSE(Server Sent Event)
Server-Sent Events(SSE) is a technology that enables servers to push real-time updates to a web clients over a single, long-lived HTTP connection(i.e. here u are making connection with HTTP request). This allows for efficient, one-way communication from the server to the client, making it suitable for applications that require real-time data updates. In the context of the Model Context Protocol, SSE is utilized as a transport mechanism to enable communication between clients and servers. This approach allows servers to handle multiple client connections efficiently and supports features like authentication and scalability.

The server provides two main endpoints: 
1. SSE Endpoint(Event Stream Endpoint)
Clients connect to this endpoint to receive messages from the server.
This connection is established using a standard HTTP GET request with the header:
Content-Type: text/event-stream
Once connected, the server keeps the connection open and continuously streams events in real time. Each event is sent in a simple text-based format that may include fields such as:
- event → event type
- data → actual message payload
- id → unique event identifier
- retry → reconnection delay
event: update
data: {"status":"completed"}
This allows the client to automatically process updates as soon as they arrive without repeatedly polling the server.

2. Message/Publish Endpoint (Client → Server Communication)
Since SSE supports only one-way communication (server → client), clients use a separate HTTP endpoint (usually POST) to send data or requests back to the server.
Through this endpoint, clients can:
- Send commands
- Submit user input
- Trigger server-side tasks
- Authenticate or register sessions
This separates incoming and outgoing communication while maintaining efficiency.

## How it works together
1. Client opens SSE connection to the event endpoint.
2. Server keeps the connection alive.
3. Server pushes updates whenever new data is available.
4. Client sends actions or messages using standard HTTP requests.

This design avoids:
- Constant polling
- Excessive network overhead
- Repeated connection setup

## Benefits of SSE in Model Context Protocol (MCP)
Using SSE as the transport layer provides:
- Persistent connection with low latency
- Automatic reconnection support
- Lightweight compared to WebSockets
- Built-in HTTP compatibility (works with proxies/firewalls)
- Easy horizontal scalability
- Efficient broadcasting to multiple clients
Because MCP often requires streaming responses (e.g., tokens from LLMs, progress updates, or tool outputs), SSE is particularly well suited for real-time, incremental delivery of results.

## Key differences
1. STDIO Transport:
- Communication Type: Local, synchronous(stdin/stdout)
- Client Support: Single Client
- Scalability: Limited(single-process)
- Authentication: No built-in authentication
- Setup Complexity: Simple, minimal setup
- Use Case: CLI tools, local integrations
- Latency: Very low(local)
- Reliability: High for local processes

2. SSE Transport:
- Communication Type: Network-based, real-time(HTTP/SSE)
- Client Support: Multiple Clients
- Scalability: High(supports many clients)
- Authentication: Supports authentication(e.g. JWT, API keys)
- Setup Complexity: Requires HTTP endpoints, network config
- Use Case: Web apps, real-time notifications, dashboards
- Latency: Low, but can be affected by network conditions
- Reliability: Automatic reconnection and error handling

- Here MCP servers and clients that we are making that will be doing/making connections over the web server.
- First of all we will be creating a local host with a port number that means it will be running as an API and access from our client.
- Later on we can deploy this MCP server over the clouds and u can use remotely. That means with the help of any kind of MCP client we can access that particular MCP server even u can also use multiple MCP clients.

## Dockerizing MCP Server
- docker build -t terminal_server_sse . (Here we have also tagged the name we are keeping for Docker Image)
- Above is the command to build the docker image
- Before this be inside the folder where u have the docker file. cd servers --> cd terminal_server ---> cd sse_server
- Now when u open up ur docker desktop terminal_server_sse is available
- After dockerizing we will execute our server with our client.

## Test your MCP SSE Server and Client Locally
- For this u need to execute ur docker image as a container.
- How to execute:
- docker run --rm -p 8081:8081 -v C:/Users/Amritanshu Bhardwaj/mcp/workspace terminal_server_sse
- --rm means if some container is already running it will remove the container, -p is port mapping the port by which u want to execute ur application(u can keep any port with ur choice)
- Then we are passing the workspace location where we want to create the files using mcp server tools.
- After this we are specifying the image name(this image that i want to execute)
- Copy the command, open up ur terminal and then execute, make ensure u open the terminal in that sse_server directory.

## Note
- The terminal that we are using here is powershell.

## Other way of running:
- docker run -p 8081:8081 terminal_server_sse
- Directly run(it worked better)
- Run in detached mode:
- docker run -d -p 8081:8081 terminal_server_sse(not preffered as it was failing a lot)
- View Logs(if running detached):
- docker logs terminal_server_sse
- Stop the container:
- docker stop terminal_server_sse

- Note: Execute this in the sse_server directory  ---> Very Important(As ur dockerfile is there)

## Then we will launch my client
- uv run client_sse.py http://127.0.0.1:8081/sse
- Make ensure u are in the correct directory i.e. where the python file is present: cd clients then cd mcp_client
- This "/sse" is the route
- And after let's say if u have deployed, and whenever u will be deploying this server on the cloud u will be getting a different url as compared to what u have mentioned above.
- Ur application right now is running on my localhost
- Now we will be able to give our query in the terminal

- Give the query: add two numbers 2 and 5 using the tools
- It is utilizing the tool in my server right now and how it is accessing it is accessing from the url itself, from the API itself instead of using the local file.
- This is the main advantage that u will get whenever u are using the server sent event.