# advprog-modul6

## Commit 1 Reflection notes

The `handle_connection` method processes an incoming TCP connection and reads the HTTP request from the client.

### Breakdown:
- **Creating a Buffered Reader** → `BufReader::new(&mut stream)` wraps the TCP stream for efficient reading.
- **Reading the HTTP Request** → `buf_reader.lines()` reads the incoming data line by line.
- **Storing the Request** → `.take_while(|line| !line.is_empty())` collects the request headers until an empty line is encountered.
- **Printing the Request** → `println!("Request: {:#?}", http_request);` displays the received request.

This method extracts and prints only the HTTP request headers from the client connection.