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


### Commit 2 Reflection notes

This checkpoint helped me understand that a web server must not only read requests, but also send a correctly
formatted  HTTP response. The response needs a status line, headers, a blank line, and then the message body.
Returning only `200 OK` is enough to make a minimal response, but reading `hello.html` and sending 
it back made the server actually  useful because the browser could render visible HTML content.

I also understood why `Content-Length` matters. Without it, the browser may not know how much body data to expect,
while with it the response becomes a proper HTTP message. Writing the response using
`stream.write_all(response.as_bytes())` showed me that the browser only sees raw bytes, so even a simple web page is
still just carefully structured text sent over a TCP stream.

![Commit 2 screen capture](/assets/images/commit2.png)

### Commit 3 Reflection notes
In this part, I learned that returning the same page for every route is not enough, because the server should validate
what the client actually requests. By checking whether the request line is exactly `GET / HTTP/1.1`, the server can
return `hello.html` for the main page and a `404 NOT FOUND` response for other paths. This made the server behave more
like a real web server, because it now distinguishes between a valid route and a missing page.

The refactoring step was also important. At first, the `if` and `else` branches repeated the same file-reading and 
response-writing logic. Refactoring that into a tuple like `(status_line, filename)` made the code cleaner because the 
only thing that changes between cases is the status and the file name, while the rest of the response construction
stays the same. I think this improves readability and maintainability, because future changes 
only need to be made in one place.

![Commit 3 screen capture](/assets/images/commit3.png)

### Commit 4 Reflection notes
This checkpoint made the weakness of a single-threaded server very obvious. By adding the `/sleep` route and 
calling `thread::sleep`, I could simulate a slow request and directly observe that the server handles requests
one by one. When one client accesses `/sleep`, another client requesting `/` must wait until the sleep finishes,
even though `/` itself is a fast request.

From this experiment, I learned the idea of head-of-line blocking in a simple and practical way. The problem
is not that the server cannot answer, but that the whole request pipeline is blocked by one slow task.
This helped me understand why concurrency matters in server design: even if the application logic is simple,
bad request scheduling can still make the user experience feel slow.