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