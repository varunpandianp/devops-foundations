# DevOps Networking Notes: IP Addresses, Ports, Loopback, and Request–Response Flow

## 1. What Is an IP Address?

An IP address identifies a network interface so devices can communicate over a network.

Examples:

- Laptop 1: `192.168.1.10`
- Laptop 2: `192.168.1.11`
- Home router: `192.168.1.1`
- Example web server: `203.0.113.10`

These are illustrative addresses. The `203.0.113.0/24` range is reserved for documentation and examples.

### Types of IP addresses

**Private IP address**

Used inside local networks, such as your home Wi-Fi.

Common private ranges:

- `10.0.0.0` – `10.255.255.255`
- `172.16.0.0` – `172.31.255.255`
- `192.168.0.0` – `192.168.255.255`

**Public IP address**

Used to identify an internet-routable endpoint. Your router commonly uses a public IP address, although some internet providers use carrier-grade NAT, so the router might not have its own directly reachable public IP.

**Loopback IP address**

Used by a computer to communicate with itself.

- IPv4: `127.0.0.1`
- IPv6: `::1`
- Common hostname: `localhost`

Examples:

- `http://127.0.0.1:3000`
- `http://localhost:5000`

These addresses refer to the computer on which the client is running. If you open `http://localhost:3000` on your brother's laptop, it points to his laptop, not yours.

**Important:** `0.0.0.0` is a special IPv4 bind address, not an address you normally use as the destination in a browser. An application bound to `0.0.0.0` listens on all applicable IPv4 interfaces.

## 2. What Is a Port Number?

An IP address helps identify a network endpoint. A port number identifies a particular transport-layer service or endpoint on that device.

Think of it this way:

- IP address = building address
- Port = door number
- Application = service inside the building

Example:

`192.168.1.10:8080`

- IP address: `192.168.1.10`
- Port: `8080`

A server can run multiple applications on the same IP address if they use different listening ports.

Example:

| Application | IP address | Port |
|---|---|---:|
| Node.js app | `127.0.0.1` | 3000 |
| Flask app | `127.0.0.1` | 5000 |
| Tomcat app | `127.0.0.1` | 8080 |

You can access each application separately:

- `http://localhost:3000`
- `http://localhost:5000`
- `http://localhost:8080`

## 3. What Is a Listening Port?

When a server application starts, it can bind to a particular IP address and port and listen for incoming connections.

For example, a Node.js application may listen on port `3000`.

```javascript
const http = require("http");

const server = http.createServer((req, res) => {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello from Node.js!");
});

server.listen(3000, "127.0.0.1", () => {
    console.log("Server listening on port 3000");
});
```

Save this as `app.js`, then run:

```bash
node app.js
```

Open this URL on the same computer:

```text
http://127.0.0.1:3000
```

You should see:

```text
Hello from Node.js!
```

Because the server binds to `127.0.0.1`, other computers cannot normally access it through your laptop's LAN IP.

### Make the application accessible from other devices on your LAN

Change the listening address:

```javascript
server.listen(3000, "0.0.0.0", () => {
    console.log("Server listening on port 3000");
});
```

Now the application listens on the laptop's applicable IPv4 interfaces. Another device on the same LAN can try:

```text
http://192.168.1.10:3000
```

Replace `192.168.1.10` with the actual LAN IP of the laptop running the application.

This also requires the firewall and Wi-Fi/network configuration to permit the connection. Do not expose a development server directly to the public internet.

## 4. Loopback vs. Specific IP vs. 0.0.0.0

| Bind address | Meaning | Typical use |
|---|---|---|
| `127.0.0.1` | Listen only on IPv4 loopback | Local development or private backend |
| `::1` | IPv6 loopback | Local IPv6 communication |
| `192.168.1.10` | Listen on that specific local interface, if assigned | LAN-only service |
| `0.0.0.0` | Listen on all applicable IPv4 interfaces | Services that need LAN access |

A listening address is not the same thing as the address users type into a browser.

For example, a service bound to `0.0.0.0:3000` might be accessed locally through `127.0.0.1:3000` or through the laptop's LAN IP, depending on network configuration.

## 5. What Is a Port Conflict?

Suppose three applications attempt to listen on:

```text
127.0.0.1:8080
```

Normally, only one can successfully bind to that same address and port using the same transport protocol.

The others may fail with an error such as:

```text
EADDRINUSE: address already in use
```

A simple solution is to use different ports:

- App A: `127.0.0.1:3000`
- App B: `127.0.0.1:5000`
- App C: `127.0.0.1:8080`

Two applications can sometimes use the same port number when bound to different local IP addresses. UDP and TCP also have separate port spaces. Special socket options and operating-system behavior can create exceptions, but do not rely on those for ordinary application deployment.

## 6. Common Default Port Numbers

| Port | Protocol/service | Common purpose |
|---:|---|---|
| 20/21 | FTP | File transfer and control |
| 22 | SSH | Secure remote login |
| 25 | SMTP | Email transfer between mail servers |
| 53 | DNS | Domain-name resolution |
| 67/68 | DHCP | Automatic IPv4 address configuration |
| 80 | HTTP | Standard web traffic |
| 110 | POP3 | Email retrieval |
| 123 | NTP | Time synchronization |
| 143 | IMAP | Email access |
| 161/162 | SNMP | Network monitoring/management |
| 389 | LDAP | Directory services |
| 443 | HTTPS | Secure web traffic |
| 445 | SMB | Windows file sharing |
| 465/587 | SMTP over TLS/submission | Secure email submission |
| 514 | Syslog | Logging, commonly UDP |
| 3306 | MySQL | Database connections |
| 3389 | RDP | Remote Desktop |
| 5432 | PostgreSQL | Database connections |
| 5672 | AMQP | Common RabbitMQ messaging port |
| 6379 | Redis | Redis connections |
| 8080 | HTTP alternate | Development, proxies, and application servers |
| 8443 | HTTPS alternate | Secure application endpoints |
| 9090 | Varies | Commonly used by monitoring tools such as Prometheus |
| 9200 | HTTP API | Common Elasticsearch endpoint |
| 27017 | MongoDB | Database connections |

These are common defaults, not guaranteed configurations. Applications can be configured to use different ports.

### Port ranges

- `0–1023`: Well-known ports
- `1024–49151`: Registered ports
- `49152–65535`: Dynamic/private port range commonly used for ephemeral client ports

Operating systems can configure their ephemeral port ranges differently.

## 7. How Does Your Browser Connect to Amazon?

Suppose you open:

```text
https://www.amazon.com
```

Your browser understands that HTTPS normally uses destination port `443` when no port is explicitly specified.

A simplified connection could look like this:

```text
Your laptop:
Private IP = 192.168.1.10
Source port = 51001 (example)

Amazon endpoint:
Destination IP = resolved by DNS
Destination port = 443
```

The connection is conceptually:

```text
192.168.1.10:51001  --->  Amazon-IP:443
```

The client usually does not listen on port 443. It initiates an outgoing connection to the server's destination port 443 and uses a temporary local source port.

The operating system automatically chooses the source port in ordinary browser connections.

### Does the browser contain code to listen on port 443?

No. The browser acts as a network client. It connects to the server's HTTPS endpoint.

The web server, load balancer, or other server-side endpoint accepts the connection on port 443. The application might not directly listen on 443 because a reverse proxy or load balancer can handle HTTPS in front of it.

## 8. How Does the Response Return to the Correct Browser?

Suppose your browser creates this connection:

```text
Laptop IP:51001  --->  Amazon-IP:443
```

Amazon sends the response back through the established connection:

```text
Amazon-IP:443  --->  Laptop IP:51001
```

The operating system tracks the connection using the protocol and endpoint addresses/ports. It delivers the received data to the socket belonging to the browser that owns that connection.

The browser then interprets the HTTP response and displays the page.

The source port is only one part of connection identification. The combination of source IP, source port, destination IP, destination port, and transport protocol distinguishes a TCP connection.

## 9. You and Your Brother Use Amazon on Two Laptops Connected to the Same Wi-Fi

Assume:

```text
Home router LAN IP: 192.168.1.1

Your laptop:
192.168.1.10

Brother's laptop:
192.168.1.11
```

Both laptops open Amazon over HTTPS.

### Before leaving your home network

Your connection:

```text
192.168.1.10:51001  --->  Amazon-IP:443
```

Your brother's connection:

```text
192.168.1.11:52001  --->  Amazon-IP:443
```

Both can use destination port `443` because they are separate connections originating from different devices.

### What happens through the home router?

Most home routers use Network Address Translation (NAT) to share one public IPv4 address among multiple devices.

The router tracks outgoing connections and their translations. An illustrative example:

```text
YOUR LAPTOP
192.168.1.10:51001
          |
          v
HOME ROUTER / NAT
Public IP: 198.51.100.20
Translated port: 61001
          |
          v
AMAZON
Amazon-IP:443
```

Your brother's connection might be translated separately:

```text
BROTHER'S LAPTOP
192.168.1.11:52001
          |
          v
HOME ROUTER / NAT
Public IP: 198.51.100.20
Translated port: 61002
          |
          v
AMAZON
Amazon-IP:443
```

The public IP and translated ports shown above are examples. The router chooses translations according to its NAT implementation; the original source port does not always have to change.

Both requests can use the same public IP address and remote port 443. NAT maintains connection-tracking information so it can map each incoming response to the correct internal connection.

### How the response reaches the correct laptop

1. You open Amazon on your laptop.
2. Your laptop creates an outgoing connection with a temporary source port.
3. Your router records the connection and any NAT translation.
4. Amazon sends a response to the router's public endpoint.
5. The router checks its connection-tracking/NAT table.
6. It forwards the response to your laptop's corresponding private IP and port.
7. Your laptop's operating system delivers the response to your browser.
8. Your brother's separate connection is handled independently.

**Important:** A shared public IP address does not mean both laptops receive the same response. Each network connection has its own tracking information.

Modern browsers can reuse or multiplex connections, and websites may contact many additional servers for images, scripts, APIs, and other resources. The simplified examples above illustrate the core principle.

## 10. What If Both Laptops Use the Same Source Port Number?

Suppose both laptops happen to use source port `51001`.

Your connection:

```text
192.168.1.10:51001 ---> Amazon-IP:443
```

Your brother's connection:

```text
192.168.1.11:51001 ---> Amazon-IP:443
```

There is no conflict because the source IP addresses differ.

Even when a router translates both connections to the same public IP, it tracks the connection mappings and ensures that each response is forwarded to the appropriate internal endpoint.

A port number is not globally unique by itself.

## 11. How to Check Listening Ports on Linux

### List listening TCP and UDP ports

```bash
sudo ss -lntup
```

Useful options:

- `-l`: Listening sockets
- `-n`: Numeric addresses and ports
- `-t`: TCP
- `-u`: UDP
- `-p`: Process information

### Check a specific port

```bash
sudo ss -lntp | grep ':8080'
```

### Find the process using a port

```bash
sudo lsof -i :8080
```

### Check your laptop's IP addresses

```bash
ip addr
```

### Check the default route/router

```bash
ip route
```

### Test an HTTP endpoint

```bash
curl -I http://127.0.0.1:8080
```

The `curl` command tests the HTTP response. It does not, by itself, prove that the application is accessible from another laptop.

## 12. Configure a Python Flask Application on a Specific Port

Install Flask in a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install flask
```

Create `app.py`:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from Flask!"
```

Run it on port 5000, accessible only from the same computer:

```bash
flask --app app run --host 127.0.0.1 --port 5000
```

Access it:

```text
http://127.0.0.1:5000
```

For LAN testing, you can bind to all IPv4 interfaces:

```bash
flask --app app run --host 0.0.0.0 --port 5000
```

Then access it from another device using the host laptop's LAN IP:

```text
http://192.168.1.10:5000
```

The Flask development server is intended for development, not production deployment.

## 13. Configure a Node.js Application on a Specific Port

```javascript
const http = require("http");

const PORT = 3000;
const HOST = "127.0.0.1";

const server = http.createServer((req, res) => {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello from Node.js!");
});

server.listen(PORT, HOST, () => {
    console.log(`Server listening at http://${HOST}:${PORT}`);
});
```

Run:

```bash
node app.js
```

Test:

```bash
curl http://127.0.0.1:3000
```

To accept connections from other devices on your LAN, use `0.0.0.0` as the bind host and configure the firewall appropriately.

## 14. How to Access Three Applications Through One Public HTTPS Port

In production, multiple applications can be reached through one public HTTPS endpoint using a reverse proxy.

For example:

```text
Users
  |
  v
Nginx :443
  |
  |-- app1.example.com ---> Node.js :3000
  |
  |-- app2.example.com ---> Flask :5000
  |
  |-- app3.example.com ---> Tomcat :8080
```

Nginx listens on the public HTTPS port and forwards requests to the correct backend based on the hostname or URL path.

The backend applications use their own ports. Nginx handles the incoming HTTPS connection and request routing.

This is different from having all three backend applications independently bind to the same IP address and port.

## 15. Key Concepts to Remember

1. An IP address identifies a network endpoint; a port identifies a service endpoint on that device.
2. A listening server waits for incoming connections on a configured address and port.
3. A client normally connects to the server's port and uses a temporary source port.
4. `localhost` and `127.0.0.1` refer to the computer running the client.
5. `0.0.0.0` is a wildcard IPv4 bind address, not a normal browser destination.
6. Multiple applications usually cannot bind to the same overlapping address and port using the same transport protocol.
7. Multiple devices can connect to the same server port without conflict.
8. A home router's NAT tracking helps direct responses to the correct internal connection.
9. A reverse proxy such as Nginx can route traffic to multiple applications through a shared public port.
10. A port conflict and an application capacity problem are different issues.

## Learning Log

**Topic:** IP Addresses, Ports, Loopback, and Request–Response Flow

**What I learned:**
- Difference between private IP, public IP, loopback IP, and bind addresses.
- Difference between server listening ports and client ephemeral ports.
- How HTTP/HTTPS requests and responses travel between a laptop and a remote server.
- How two laptops using the same Wi-Fi can access Amazon simultaneously.
- How NAT connection tracking routes responses to the correct device.
- How to configure Node.js and Flask applications to use specific ports.
- How to inspect listening ports and processes using Linux commands.
- How a reverse proxy allows multiple applications to share a public HTTPS entry point.

**Practical commands practiced:**

```bash
ip addr
ip route
sudo ss -lntup
sudo lsof -i :8080
curl -I http://127.0.0.1:8080
```

**DevOps relevance:** These concepts are fundamental to Linux administration, web application deployment, Docker networking, Kubernetes Services, load balancing, reverse proxies, firewall configuration, and troubleshooting connectivity issues.
