# Reflection
### Gregorius Ega Aditama Sudjali
### 2406434153

### Commit 1 Reflection notes
In this checkpoint, I learned that opening a TCP connection is only the first step of building a web server.
`TcpListener::bind("127.0.0.1:7878")` makes the program listen on a local port, and `incoming()` gives a sequence of 
`TcpStreams` that represent connection attempts. Moving the logic into `handle_connection` also made the code easier
to understand, because the program is now split into two responsibilities: accepting connections and processing them.

I also learned how the browser’s request starts to look at the byte and line level. Using `BufReader` and reading lines
until the empty line helped me see the HTTP request structure more clearly: request line, headers, then a blank line.
One detail I found interesting is that the browser may open multiple connections or retry when the server does not
send back a proper response yet, so seeing repeated connection messages is normal and not necessarily a bug.