# HTTP Analysis

## What is HTTP?
- HTTP stands for Hypertext Transfer Protocol
- It is the foundation of data communication on the web
- Plain text protocol meaning all data is readable
- Uses TCP port 80 by default
- Works at Application Layer 7 of OSI model
- Every website visit generates HTTP traffic
- Replaced by HTTPS in modern secure websites
- Still found on internal networks and older systems

---

## HTTP vs HTTPS
- HTTP → plain text → all data visible in Wireshark
- HTTPS → encrypted with TLS → data not readable
- HTTP uses port 80
- HTTPS uses port 443
- HTTP is dangerous on public networks
- Credentials sent over HTTP are visible to anyone

---

## HTTP Request Structure

### Request Line
- Method → GET POST PUT DELETE etc
- URI → path of requested resource
- HTTP Version → HTTP/1.0 HTTP/1.1 HTTP/2

### Request Headers
- Host → server hostname
- User-Agent → browser and OS information
- Accept → content types client accepts
- Accept-Language → preferred language
- Accept-Encoding → compression methods accepted
- Connection → keep-alive or close
- Cookie → session cookies sent to server
- Authorization → credentials for authentication
- Content-Type → type of data in request body
- Content-Length → size of request body
- Referer → previous page URL

### Request Body
- Contains data sent to server
- Used in POST and PUT requests
- Contains form data, JSON, XML
- Can contain login credentials

---

## HTTP Response Structure

### Status Line
- HTTP Version → HTTP/1.1
- Status Code → 200 404 500 etc
- Reason Phrase → OK Not Found etc

### Response Headers
- Content-Type → type of response data
- Content-Length → size of response body
- Server → web server software name
- Set-Cookie → sets cookie on client
- Location → redirect URL
- Cache-Control → caching instructions
- Content-Encoding → compression used
- X-Powered-By → backend technology used

### Response Body
- Contains requested content
- HTML CSS JavaScript images
- JSON or XML data for APIs

---

## HTTP Methods

### GET
- Requests data from server
- Parameters in URL
- No request body
- Used for retrieving web pages and resources

### POST
- Sends data to server
- Data in request body
- Used for form submissions and logins
- Contains credentials in plain text over HTTP

### PUT
- Updates existing resource on server
- Data in request body

### DELETE
- Deletes resource on server

### HEAD
- Same as GET but no response body
- Used to check resource existence

### OPTIONS
- Returns allowed HTTP methods
- Used in CORS preflight requests

### PATCH
- Partially updates resource

---

## HTTP Status Codes

### 1xx Informational
- 100 → Continue
- 101 → Switching Protocols

### 2xx Success
- 200 → OK
- 201 → Created
- 204 → No Content

### 3xx Redirection
- 301 → Moved Permanently
- 302 → Found temporary redirect
- 304 → Not Modified use cache

### 4xx Client Errors
- 400 → Bad Request
- 401 → Unauthorized need login
- 403 → Forbidden access denied
- 404 → Not Found
- 405 → Method Not Allowed
- 429 → Too Many Requests

### 5xx Server Errors
- 500 → Internal Server Error
- 502 → Bad Gateway
- 503 → Service Unavailable
- 504 → Gateway Timeout

---

## HTTP Display Filters in Wireshark

### Basic HTTP Filters
```bash
# show all HTTP traffic
http

# show HTTP requests only
http.request

# show HTTP responses only
http.response

# show HTTP version 1.1
http.request.version == "HTTP/1.1"
```

### Filter by HTTP Method
```bash
# show GET requests
http.request.method == "GET"

# show POST requests
http.request.method == "POST"

# show PUT requests
http.request.method == "PUT"

# show DELETE requests
http.request.method == "DELETE"
```

### Filter by Status Code
```bash
# show 200 OK responses
http.response.code == 200

# show 404 not found
http.response.code == 404

# show 401 unauthorized
http.response.code == 401

# show 500 server errors
http.response.code == 500

# show all error responses
http.response.code >= 400

# show all redirect responses
http.response.code >= 300 and http.response.code < 400
```

### Filter by Host and URI
```bash
# show traffic to specific host
http.host == "example.com"
http.host contains "google"

# show specific URI
http.request.uri == "/login"
http.request.uri contains "login"
http.request.uri contains "admin"
http.request.uri contains "password"
http.request.uri contains ".php"

# show specific file type requests
http.request.uri contains ".exe"
http.request.uri contains ".pdf"
http.request.uri contains ".zip"
```

### Filter by Headers
```bash
# show specific user agent
http.user_agent contains "Mozilla"
http.user_agent contains "curl"
http.user_agent contains "python"
http.user_agent contains "sqlmap"
http.user_agent contains "nikto"

# show specific content type
http.content_type contains "json"
http.content_type contains "html"
http.content_type contains "xml"

# show requests with cookies
http.cookie

# show responses setting cookies
http.set_cookie

# show authorization header
http.authorization

# show requests with referer
http.referer
```

### Filter by Content
```bash
# find credentials in traffic
http contains "password"
http contains "passwd"
http contains "username"
http contains "login"
http contains "credential"

# find sensitive data
http contains "credit_card"
http contains "ssn"
http contains "token"
http contains "api_key"
```

---

## HTTP Credential Harvesting

### Finding Login Credentials
```bash
# filter POST requests to login pages
http.request.method == "POST" and http.request.uri contains "login"

# filter POST requests with credentials
http.request.method == "POST" and http contains "password"

# filter basic authentication
http.authorization contains "Basic"
```

### How to Read Credentials
```
1. Apply filter: http.request.method == "POST"
2. Click on POST packet
3. Expand Hypertext Transfer Protocol in packet details
4. Expand HTML Form URL Encoded section
5. See username and password fields in plain text
```

### Follow HTTP Stream for Credentials
```
Right click POST packet
Follow → HTTP Stream
See complete request including credentials
```

---

## HTTP Cookie Analysis

### What are Cookies?
- Small pieces of data stored by browser
- Sent with every HTTP request to server
- Used for session management and authentication
- Session cookies authenticate user after login
- Stealing session cookie = account takeover

### Cookie Filters
```bash
# show all requests with cookies
http.cookie

# show responses setting cookies
http.set_cookie

# find session tokens
http.cookie contains "session"
http.cookie contains "token"
http.cookie contains "auth"
http.set_cookie contains "session"
```

### Cookie Security Flags
- Secure → only sent over HTTPS
- HttpOnly → not accessible by JavaScript
- SameSite → controls cross site sending
- Missing flags are security vulnerabilities

---

## HTTP User Agent Analysis

### What is User Agent?
- String identifying browser and operating system
- Sent in every HTTP request header
- Reveals client software and OS version
- Attackers sometimes use custom or fake user agents

### Suspicious User Agents
```bash
# show curl requests often used by attackers
http.user_agent contains "curl"

# show python requests often used in scripts
http.user_agent contains "python"
http.user_agent contains "urllib"

# show scanning tools
http.user_agent contains "sqlmap"
http.user_agent contains "nikto"
http.user_agent contains "nmap"
http.user_agent contains "masscan"

# show empty user agent suspicious
http.user_agent == ""
```

---

## HTTP Redirect Analysis
```bash
# show all redirects
http.response.code == 301 or http.response.code == 302

# follow redirect chain
# check Location header in redirect response
http.location
```

---

## HTTP File Transfer Analysis

### Downloading Files
```bash
# show executable downloads
http.request.uri contains ".exe"
http.request.uri contains ".dll"
http.request.uri contains ".bat"
http.request.uri contains ".ps1"

# show archive downloads
http.request.uri contains ".zip"
http.request.uri contains ".rar"
http.request.uri contains ".tar"
```

### Extracting Files from HTTP
```
File → Export Objects → HTTP
Shows all files transferred over HTTP
Select file to export and save
Can recover images, documents, executables
```

---

## HTTP Security Analysis

### SQL Injection Detection
```bash
# find SQL injection attempts in URI
http.request.uri contains "'"
http.request.uri contains "SELECT"
http.request.uri contains "UNION"
http.request.uri contains "DROP"
http.request.uri contains "--"
http.request.uri contains "1=1"
```

### XSS Detection
```bash
# find XSS attempts
http.request.uri contains "<script>"
http.request.uri contains "javascript:"
http.request.uri contains "onerror"
http.request.uri contains "onload"
http.request.uri contains "alert("
```

### Directory Traversal Detection
```bash
# find path traversal attempts
http.request.uri contains "../"
http.request.uri contains "..%2F"
http.request.uri contains "%2e%2e"
```

### Web Scanner Detection
```bash
# detect web scanning tools
http.user_agent contains "sqlmap"
http.user_agent contains "nikto"
http.user_agent contains "dirbuster"
http.user_agent contains "gobuster"
http.user_agent contains "burpsuite"
```

---

## HTTP Statistics in Wireshark

### Request Response Statistics
```
Statistics → HTTP → Request/Response
Shows all HTTP request and response pairs
Displays timing information
```

### HTTP Packet Counter
```
Statistics → HTTP → Packet Counter
Shows count of each HTTP method
Shows count of each response code
```

### HTTP Load Distribution
```
Statistics → HTTP → Load Distribution
Shows traffic distribution by server
```

---


