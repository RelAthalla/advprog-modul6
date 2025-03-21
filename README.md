# advprog-modul6

## Commit 1 Reflection notes

The `handle_connection` method processes an incoming TCP connection and reads the HTTP request from the client.

### Breakdown:
- **Creating a Buffered Reader** → `BufReader::new(&mut stream)` wraps the TCP stream for efficient reading.
- **Reading the HTTP Request** → `buf_reader.lines()` reads the incoming data line by line.
- **Storing the Request** → `.take_while(|line| !line.is_empty())` collects the request headers until an empty line is encountered.
- **Printing the Request** → `println!("Request: {:#?}", http_request);` displays the received request.

This method extracts and prints only the HTTP request headers from the client connection.

## Commit 2 Reflection notes

The updated `handle_connection` method not only reads the HTTP request but also sends an HTTP response.  

### Key Differences from the Previous Version:
1. **Handling HTTP Response** → The new version constructs and sends an HTTP response, whereas the previous version only printed the request.
2. **Reading HTML File** → Uses `fs::read_to_string("hello.html").unwrap();` to load an HTML file as the response body.
3. **Building the Response** → Formats a proper HTTP response with status, content length, and body.
4. **Sending Data to Client** → `stream.write_all(response.as_bytes()).unwrap();` writes the response back to the client.

### What I Learned:
- This version makes the server functional by responding with an actual HTML file.
- It introduces file reading and response formatting, which are essential for web servers.
- Unlike before, it now completes the request-response cycle.

![Commit 2 screen capture](/img/commit2.png)

## Commit 3 Reflection notes

### Overview
Initially, our server responded with `hello.html` for every request, regardless of the requested path. This approach was not practical, as a real web server should differentiate between valid and invalid requests. To improve this, we modified the `handle_connection` function to check the requested URL and serve appropriate responses.

### Changes and Improvements
1. **Request Validation**  
   - The function now reads only the first line of the HTTP request to determine the requested path.
   - If the request is for `/`, it serves `hello.html` with a `200 OK` response.
   - If the request is for any other path, it serves `404.html` with a `404 NOT FOUND` response.

2. **Selective Response Handling**  
   - Introduced a conditional check to determine which file to serve.
   - Returns different status codes based on whether the requested page exists.

3. **Refactoring for Efficiency**  
   - Previously, the server stored all request headers in a `Vec`, but now it only processes the first line.
   - The response format is simplified and dynamically adjusts based on the file being served.

### Why the Refactoring Was Needed
- To make the server behave more like a real web server by handling valid and invalid requests differently.
- To improve efficiency by reducing unnecessary request processing.
- To provide a user-friendly error page instead of serving incorrect content.

### Screenshot of the 404 Page
![Commit 3 screen capture](/img/commit3.png)

Now, when users visit `http://127.0.0.1:7878/`, they see the main page.  
If they visit `http://127.0.0.1:7878/[something]`, they see a custom 404 error page.

## Commit 4 Reflection notes

### Overview
Previously, our server handled requests sequentially without any delays. However, real-world servers often experience slow responses due to heavy load or network latency. In this milestone, we introduced an artificial delay for a specific request (`/sleep`) to simulate a slow response.

### Changes and Observations
1. **Introducing a Delay for `/sleep`**
   - We modified `handle_connection` to check if the request is for `/sleep`.
   - If the request matches `GET /sleep HTTP/1.1`, the server waits **10 seconds** before responding.
   - Other requests (`/`) still respond immediately.

2. **Impact on Server Performance**
   - Since the server runs on a **single thread**, handling a slow request blocks all other incoming requests.
   - When opening `http://127.0.0.1:7878/sleep` in one tab and `http://127.0.0.1:7878/` in another, the second request **gets delayed until the first one finishes**.
   - This behavior demonstrates the **limitations of single-threaded servers** in handling multiple concurrent users.

3. **Why This Refactoring is Necessary**
   - It highlights the need for **multi-threading** or **asynchronous processing** in web servers.
   - A real-world server should handle multiple requests efficiently without one request blocking others.
   - The next step is implementing **threaded request handling** to improve performance.

## Commit 5 Reflection notes

### Overview
In this milestone, we improved our web server by implementing a **ThreadPool** to handle multiple incoming requests concurrently. Previously, our server operated in a **single-threaded** manner, which caused performance issues when handling multiple clients.

### Problem with Single-threaded Server
Initially, our server processed requests **sequentially** using a single thread. This had a significant drawback:
- If one request was slow (e.g., `GET /sleep` which waits for 10 seconds), it blocked all other incoming requests.
- This led to high **latency** and **poor responsiveness**, making the server inefficient under heavy loads.

### Solution: Implementing ThreadPool
To address this issue, we introduced a **ThreadPool**, which allows multiple worker threads to handle requests concurrently. Key improvements:
- **Parallel Processing**: Requests are distributed among available worker threads, reducing wait times.
- **Non-blocking Execution**: Slow requests no longer delay the entire server.
- **Scalability**: We can adjust the number of worker threads (`ThreadPool::new(4)`) based on server load.
- **Better Performance**: The server can now handle multiple connections efficiently, improving response times.

### How ThreadPool Works
1. We create a `ThreadPool` with a **fixed number of worker threads**.
2. When a request arrives, it is added to a **job queue**.
3. Available worker threads pick up and process the jobs asynchronously.
4. The server remains responsive even under high traffic.

### Key Takeaways
 **Concurrency Boost**: Multiple requests are handled in parallel, reducing response time.
 **No More Blocking**: Slow requests do not stall other connections.
 **Real-world Optimization**: Most production servers use similar multithreading techniques to improve efficiency.

### Conclusion
By implementing a **ThreadPool**, our web server is now more robust and scalable. It can handle multiple users simultaneously, making it significantly more efficient than the initial single-threaded version. 

## Commit 6 Reflection notes

The build function enhances thread pool creation by introducing explicit error handling through a Result type. Instead of panicking when an invalid size (e.g., zero) is provided, it returns a PoolCreationError, allowing the caller to handle the issue gracefully. This approach improves code reliability and aligns with Rust’s best practices for error management. By using Result<ThreadPool, PoolCreationError> rather than Option<ThreadPool> or an immediate panic!, errors are handled more effectively, ensuring safer and more predictable execution. Additionally, this structured error handling makes future extensions and modifications easier without disrupting existing functionality.