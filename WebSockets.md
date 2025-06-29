# WebSockets

WebSockets are a communication protocol that provides full-duplex, bidirectional communication channels over a single, long-lived connection between a client (like a browser) and a server. Unlike traditional HTTP, where the client sends a request and waits for the server’s response, WebSockets allow real-time communication, meaning both the client and server can send data to each other at any time without needing to repeatedly open new connections.

## Key Features of WebSockets:
1. Full-duplex communication: WebSockets enable two-way communication between the client and the server. This allows both sides to send and receive messages simultaneously, creating a more responsive user experience.

2. Persistent connection: WebSockets maintain a single connection open for the entire duration of communication. This is efficient for real-time applications since it avoids the overhead of establishing and closing connections with each request, which is typical in HTTP.

3. Low latency: WebSockets significantly reduce latency compared to traditional polling methods (e.g., AJAX long polling). Instead of continuously requesting new data from the server, WebSockets allow data to be sent instantly as it becomes available.

4. Protocol: WebSockets use the ```ws://``` or secure ```wss://``` protocol, which is different from the standard ```http://``` and ```https://``` used for normal web traffic. They begin as a standard HTTP request, then upgrade to the WebSocket protocol via a handshake.

## WebSocket Handshake:
-   The client initiates a WebSocket connection by sending an HTTP request with an Upgrade header, signaling the intention to establish a WebSocket connection.
-   The server responds with a similar Upgrade response, confirming the switch to WebSocket communication.
-   Once the handshake is complete, both the client and server can start sending messages over the open connection.
## Use Cases:
WebSockets are commonly used in applications that require real-time communication, such as:

-   Chat applications: Real-time message exchange between users.
-   Online gaming: Low-latency data exchange for multiplayer games.
-   Financial applications: Real-time stock price updates.
-   Collaborative tools: Applications like Google Docs where users see real-time updates from others.
-   Live feeds: Streaming live data such as sports scores or social media updates.

## Example :
Using WebSockets in Spring Boot on the server side allows you to handle real-time communication between clients and the server. Spring Boot makes it easier to set up WebSocket endpoints and handle bidirectional communication. Here’s how you can configure and use WebSockets with Spring Boot:

1. Add Dependencies
First, ensure that you have the WebSocket dependency in your pom.xml file:

    
    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-websocket</artifactId>
    </dependency>
    ```
2. Configure WebSocket
You need to create a configuration class to register WebSocket endpoints and enable WebSocket support.

    
    ```java
    import org.springframework.context.annotation.Configuration;
    import org.springframework.web.socket.config.annotation.EnableWebSocket;
    import org.springframework.web.socket.config.annotation.WebSocketConfigurer;
    import org.springframework.web.socket.config.annotation.WebSocketHandlerRegistry;

    @Configuration
    @EnableWebSocket
    public class WebSocketConfig implements WebSocketConfigurer {

        private final MyWebSocketHandler myWebSocketHandler;

        public WebSocketConfig(MyWebSocketHandler myWebSocketHandler) {
            this.myWebSocketHandler = myWebSocketHandler;
        }

        @Override
        public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
            registry.addHandler(myWebSocketHandler, "/ws")
                    .setAllowedOrigins("*"); // Allow cross-origin requests, adjust as necessary
        }
    }
    ```
    In this example:

    -   The MyWebSocketHandler is the class that will handle the WebSocket connection and messages.
    -   The WebSocket endpoint will be available at ```/ws```.


3. Create WebSocket Handler
Create a handler that will process WebSocket messages and events.

    ```java
    import org.springframework.web.socket.WebSocketSession;
    import org.springframework.web.socket.handler.TextWebSocketHandler;
    import org.springframework.web.socket.TextMessage;
    import org.springframework.stereotype.Component;

    @Component
    public class MyWebSocketHandler extends TextWebSocketHandler {

        @Override
        public void afterConnectionEstablished(WebSocketSession session) throws Exception {
            System.out.println("New WebSocket connection established: " + session.getId());
        }

        @Override
        protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
            System.out.println("Message received: " + message.getPayload());

            // Echo the message back to the client
            session.sendMessage(new TextMessage("Echo: " + message.getPayload()));
        }

        @Override
        public void afterConnectionClosed(WebSocketSession session, org.springframework.web.socket.CloseStatus status) throws Exception {
            System.out.println("WebSocket connection closed: " + session.getId());
        }
    }
    ```
    In this handler:

    -   ```afterConnectionEstablished```: Handles new WebSocket connections.
    -   ```handleTextMessage```: Receives and processes incoming messages and responds (here we simply echo the message back).
    -   ```afterConnectionClosed```: Handles cleanup after a WebSocket connection is closed.

4. Frontend WebSocket Client Example
Here’s a basic JavaScript example of a WebSocket client:

    ```javascript
    let socket = new WebSocket("ws://localhost:8080/ws");

    socket.onopen = function(e) {
    console.log("[open] Connection established");
    };

    socket.onmessage = function(event) {
    console.log(`[message] Data received from server: ${event.data}`);
    };

    socket.onclose = function(event) {
    if (event.wasClean) {
        console.log(`[close] Connection closed cleanly, code=${event.code} reason=${event.reason}`);
    } else {
        console.log('[close] Connection died');
    }
    };

    socket.onerror = function(error) {
    console.log(`[error] ${error.message}`);
    };
    
    // Sending a message
    socket.send("Hello Server!");

    ```
5. Run the Application
Once everything is set up, start your Spring Boot application. The WebSocket connection will be available at ```ws://localhost:8080/ws```. You can test it with the frontend WebSocket client.

Key Concepts:
-   **WebSocketConfig**: Registers the WebSocket endpoints and enables WebSocket support.
-   **WebSocketHandler**: Handles WebSocket events like connection establishment, message reception, and connection closure.
-   **WebSocketSession**: Represents an active WebSocket session where you can send and receive messages.
With this setup, you can implement real-time communication using WebSockets in your Spring Boot application. You can extend the logic further to handle various use cases like chat applications, live data feeds, notifications, etc.


## Here are some common WebSocket interview questions along with detailed answers to help you prepare:

### 1. **What is a WebSocket and how does it work?**
   **Answer:**
   WebSocket is a communication protocol that provides full-duplex communication channels over a single, long-lived connection between the client and server. Unlike HTTP, which is a request-response protocol, WebSockets allow the client and server to send messages to each other independently without needing to continuously reopen new connections. The connection begins with a standard HTTP request (the WebSocket handshake), which is then upgraded to a WebSocket connection. Once established, messages can be exchanged freely in real-time.

---

### 2. **What is the difference between WebSockets and HTTP?**
   **Answer:**
   - **Full-duplex vs. Half-duplex:** WebSockets provide full-duplex communication, meaning both the client and the server can send data to each other simultaneously. HTTP follows a request-response pattern, which is half-duplex, meaning the client must wait for the server's response before making another request.
   - **Connection Lifetime:** In WebSockets, the connection is established once and kept open as long as needed. In HTTP, a new connection is created for each request/response.
   - **Real-time Communication:** WebSockets are ideal for real-time communication where low latency is required, while HTTP is not optimized for this.

---

### 3. **How does the WebSocket handshake work?**
   **Answer:**
   The WebSocket handshake is the initial step to establish a WebSocket connection:
   1. The client sends an HTTP `Upgrade` request to the server, asking to switch from HTTP to WebSocket.
   2. The server responds with an HTTP 101 status code if it supports WebSockets and agrees to upgrade the protocol.
   3. After the handshake, the connection switches from HTTP to WebSocket, allowing real-time, bidirectional communication.

   Example HTTP request:
   ```http
   GET /chat HTTP/1.1
   Host: server.example.com
   Upgrade: websocket
   Connection: Upgrade
   Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
   Sec-WebSocket-Version: 13
   ```

   Example HTTP response:
   ```http
   HTTP/1.1 101 Switching Protocols
   Upgrade: websocket
   Connection: Upgrade
   Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
   ```

---

### 4. **What are the advantages of using WebSockets?**
   **Answer:**
   - **Real-time Communication:** WebSockets enable low-latency communication, making them ideal for applications that require live updates such as online games, stock trading platforms, and chat applications.
   - **Efficient Resource Usage:** WebSockets maintain a single, open connection, reducing the overhead of multiple HTTP requests.
   - **Full-duplex:** Both client and server can send data independently of each other.
   - **Scalability:** Since WebSockets reduce the overhead of opening and closing connections, they can handle more simultaneous connections efficiently.

---

### 5. **What are some common use cases for WebSockets?**
   **Answer:**
   - **Real-time chat applications**: WebSockets are commonly used to create chat applications that allow users to send and receive messages in real time.
   - **Online multiplayer games**: Games require constant real-time updates between players and the server, which WebSockets provide.
   - **Live data streaming**: Stock market applications, sports scores, and live dashboards use WebSockets to push real-time updates to users.
   - **Collaborative applications**: Real-time collaboration tools like Google Docs use WebSockets to update multiple users simultaneously.
   - **IoT (Internet of Things)**: Devices often use WebSockets to send real-time data to servers for monitoring and control.

---

### 6. **What is the difference between WebSockets and Server-Sent Events (SSE)?**
   **Answer:**
   - **Communication Direction:** WebSockets are bidirectional, meaning both client and server can send messages to each other. SSE is unidirectional; the server can push updates to the client, but the client cannot send data back over the same connection.
   - **Connection Type:** WebSockets maintain a persistent connection for full-duplex communication, while SSE is built on top of HTTP and uses long-lived connections to stream data.
   - **Use Case:** Use WebSockets for bidirectional communication (e.g., chat applications) and SSE for simple, real-time updates from the server to the client (e.g., live news feeds).

---

### 7. **What are some security concerns with WebSockets?**
   **Answer:**
   - **Lack of CORS (Cross-Origin Resource Sharing):** WebSockets do not have the same-origin policy as HTTP requests, making them potentially vulnerable to cross-origin attacks.
   - **Man-in-the-middle attacks:** If WebSocket connections are not secured with `wss://` (WebSocket Secure), attackers can intercept and tamper with data.
   - **DoS attacks:** Since WebSockets keep connections open for a long time, they can be targeted with Denial of Service (DoS) attacks to exhaust server resources.
   - **Cross-site WebSocket hijacking:** If an attacker can trick a victim into opening a malicious WebSocket connection, they might exploit the session. Proper token authentication and origin checking are essential.

---

### 8. **How do you secure WebSocket connections?**
   **Answer:**
   - **Use `wss://`**: Always use the WebSocket Secure (WSS) protocol (`wss://`) to encrypt the communication channel, similar to HTTPS.
   - **Token-based authentication:** Authenticate clients using tokens such as JWT (JSON Web Tokens) during the WebSocket handshake to ensure secure access.
   - **Origin Checking:** Verify the `Origin` header to prevent cross-site WebSocket hijacking attacks.
   - **Limit resource consumption:** Implement measures to close idle WebSocket connections and prevent abuse of server resources.
   - **Rate Limiting:** Use rate limiting on the server to prevent abuse or DoS attacks.

---

### 9. **How does WebSocket scaling work in a distributed system?**
   **Answer:**
   Scaling WebSockets in a distributed system involves:
   - **Load balancing**: WebSocket connections are typically sticky, meaning the same connection needs to be routed to the same server. This can be done using sticky sessions (session affinity) in load balancers.
   - **Message broadcasting**: In systems like chat apps, messages often need to be broadcast to multiple clients. A message broker (e.g., Redis Pub/Sub, Kafka) can be used to publish and broadcast messages across different servers handling WebSocket connections.
   - **Horizontal scaling**: WebSocket servers can be horizontally scaled by distributing WebSocket connections across multiple instances, with a shared message bus to ensure communication between clients connected to different servers.

---

### 10. **What are WebSocket subprotocols, and why are they used?**
   **Answer:**
   WebSocket subprotocols are used to define a specific protocol for communication over WebSockets. When the client initiates a WebSocket connection, it can request one or more subprotocols, and the server chooses one that it supports.

   Example:
   ```http
   Sec-WebSocket-Protocol: chat, superchat
   ```

   The server may respond:
   ```http
   Sec-WebSocket-Protocol: chat
   ```

   Subprotocols are useful when you want to define structured communication, like a chat protocol, custom binary protocols, or use existing protocols like STOMP (used in messaging applications).

---

These questions and answers cover the fundamentals of WebSockets, common use cases, and more advanced concepts like security and scalability, providing a solid foundation for an interview.
