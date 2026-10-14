# Web Servers, Application Servers, Apache HTTP Server and Tomcat — DevOps Notes

## 1. What Is a Client–Server Architecture?

Client–server architecture is a model in which a client sends a request to a server, and the server processes the request and returns a response.

### Components

**Client**
- Initiates requests.
- Examples: web browsers, mobile apps, command-line tools.
- Sends HTTP or HTTPS requests.

**Server**
- Receives and processes requests.
- Returns HTML, CSS, JavaScript, images, JSON, or other responses.
- May communicate with databases and other services.

### Example

When a user visits `https://example.com`:

1. The browser resolves the domain name using DNS.
2. The browser establishes a connection to the server.
3. The browser sends an HTTP request.
4. The web server or application infrastructure processes the request.
5. The server returns an HTTP response.
6. The browser renders the content.

### Simple Architecture

```text
Client / Browser
       |
       | HTTP / HTTPS
       v
Web Server
       |
       v
Application Server
       |
       v
Database
```

This is a common architecture, but not every application requires a separate web server and application server.

---

## 2. What Is a Web Server?

A web server is software that accepts HTTP/HTTPS requests and returns HTTP responses.

The term can also refer to the physical or virtual machine running that software.

### Main Responsibilities

- Accept client connections.
- Serve static content.
- Route requests to backend applications.
- Terminate TLS/SSL in supported configurations.
- Apply access controls and security rules.
- Generate access and error logs.
- Handle redirects and URL routing.
- Proxy requests to application servers.

### Example

A user requests:

```text
https://example.com/index.html
```

The web server may return the file:

```text
/var/www/html/index.html
```

The browser receives the HTML and renders the page.

---

## 3. What Is an Application Server?

An application server runs application code and implements business logic.

It can process user requests, perform calculations, validate data, communicate with databases, and generate dynamic responses.

### Responsibilities

- Execute application code.
- Process business logic.
- Authenticate and authorize users.
- Communicate with databases.
- Generate dynamic HTML or JSON responses.
- Manage application components and runtime resources.

### Examples

- Apache Tomcat — Java Servlet and Jakarta EE web application runtime.
- Gunicorn — Python WSGI application server.
- uWSGI — Python application server.
- Node.js — JavaScript runtime that can host HTTP applications.
- WildFly — Java/Jakarta EE application server.
- Payara Server — Jakarta EE application server.

**Important:** These products do not all have identical capabilities. Node.js is a runtime, Gunicorn is a WSGI server, and Tomcat is a Java web container with additional server functionality.

---

## 4. Web Server vs Application Server

| Feature | Web Server | Application Server |
|---|---|---|
| Main purpose | HTTP handling and serving web content | Execute application logic |
| Static files | Commonly serves directly | May also serve files |
| Dynamic requests | Can proxy requests to applications | Executes application code |
| Database access | Usually delegated to the application | Commonly performed by application code |
| Examples | Apache HTTP Server, Nginx, Caddy | Tomcat, Gunicorn, WildFly |
| Typical use | TLS termination, reverse proxy, static content | Backend logic and dynamic responses |

Modern servers can overlap in functionality. For example, Nginx can serve static files and reverse-proxy dynamic requests, while Tomcat can accept HTTP requests directly.

---

## 5. Web Server vs Web Framework

A web server and a web framework solve different problems.

### Web Server

Accepts HTTP connections and handles web traffic.

Examples:
- Apache HTTP Server
- Nginx
- Caddy

### Web Framework

Provides libraries and conventions for building web applications.

Examples:
- Django — Python
- Flask — Python
- FastAPI — Python
- Express — Node.js
- Spring MVC / Spring Boot — Java
- Laravel — PHP
- Ruby on Rails — Ruby

### Example: Python Flask

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from Flask"
```

Flask defines the application route and response. A WSGI server such as Gunicorn can run the application in production, while Nginx or Apache can sit in front as a reverse proxy.

---

## 6. Static Websites vs Dynamic Websites

### Static Website

A static website serves pre-existing files without generating a new response from application logic for each request.

Examples:
- HTML pages
- CSS files
- JavaScript files
- Images
- Fonts
- Downloadable documents

Example directory:

```text
/var/www/html/
├── index.html
├── style.css
├── app.js
└── images/
    └── logo.png
```

### Advantages

- Simple deployment.
- Fast content delivery.
- Fewer runtime dependencies.
- Easy to cache.
- Can be hosted on a web server or object storage with a CDN.

### Dynamic Website

A dynamic website generates or modifies responses using application logic, data, or user input.

Examples:
- Banking applications.
- E-commerce websites.
- Login systems.
- Dashboards.
- Social media applications.

Example request flow:

```text
Browser
   |
   v
Web Server / Reverse Proxy
   |
   v
Application Server
   |
   v
Database
   |
   v
Application generates response
   |
   v
Browser
```

### Static vs Dynamic

| Feature | Static | Dynamic |
|---|---|---|
| Content | Usually pre-existing files | Generated or customized by application logic |
| Database | Not normally required | Often used |
| Backend runtime | Not required for serving files | Required when application logic must execute |
| Performance | Often simple and fast | Depends on application, database, caching, and workload |
| Examples | Portfolio, documentation site | Banking portal, e-commerce app |

A website can contain both static and dynamic content.

---

## 7. Web Servers for Static and Dynamic Websites

### Static Website Servers

Common options:

- Apache HTTP Server
- Nginx
- Caddy
- Microsoft IIS
- Object storage plus a CDN, such as Amazon S3 and CloudFront

### Dynamic Website Architecture

Dynamic websites commonly use a web server or reverse proxy in front of an application runtime.

Examples:

| Application stack | Typical architecture |
|---|---|
| Python Django | Nginx + Gunicorn + Django |
| Python Flask | Apache/Nginx + Gunicorn + Flask |
| Node.js Express | Nginx + Node.js application |
| Java web application | Apache/Nginx + Tomcat |
| PHP application | Apache/Nginx + PHP-FPM |
| ASP.NET Core | IIS or Nginx + Kestrel |

The exact architecture depends on the application, security requirements, and deployment environment.

---

## 8. Common Web Servers and Application Servers

### Web Servers

**Apache HTTP Server**
- Open-source HTTP server.
- Commonly called Apache or `httpd`.
- Supports virtual hosts, modules, access controls, and reverse proxying.

**Nginx**
- HTTP server and reverse proxy.
- Commonly used for static content, load balancing, and TLS termination.

**Caddy**
- Web server known for convenient automatic HTTPS configuration.

**Microsoft IIS**
- Web server integrated with Windows Server.

### Application Servers and Runtimes

**Apache Tomcat**
- Runs Java Servlet and Jakarta Servlet applications.
- Supports JSP in compatible versions.
- Common for Java web applications.

**Gunicorn**
- Python WSGI server commonly used with Django and Flask.

**uWSGI**
- Application server commonly used for Python applications.

**Node.js**
- JavaScript runtime that can run web frameworks such as Express.

**WildFly**
- Application server for Java/Jakarta EE applications.

---

# Apache HTTP Server

## 9. What Is Apache HTTP Server?

Apache HTTP Server is an open-source web server commonly used on Linux servers.

On CentOS Stream, RHEL, and Fedora, its service and package are commonly named `httpd`. On Debian and Ubuntu, the service is commonly named `apache2`.

### Important Components

- Configuration files.
- Virtual hosts.
- Modules.
- Document root.
- Access logs.
- Error logs.
- HTTP/HTTPS listeners.
- Reverse proxy configuration.

### Common Ports

| Port | Purpose |
|---|---|
| 80 | HTTP |
| 443 | HTTPS |

These are conventional ports; the actual listening ports depend on configuration.

---

## 10. Install Apache HTTP Server on CentOS Stream / RHEL

Install the package:

```bash
sudo dnf install httpd -y
```

Start the service:

```bash
sudo systemctl start httpd
```

Enable it at boot:

```bash
sudo systemctl enable httpd
```

Check service status:

```bash
sudo systemctl status httpd
```

Restart the service:

```bash
sudo systemctl restart httpd
```

Reload configuration when supported and appropriate:

```bash
sudo systemctl reload httpd
```

Check the Apache version:

```bash
httpd -v
```

Check configuration syntax:

```bash
sudo apachectl configtest
```

Expected successful syntax output:

```text
Syntax OK
```

### On Ubuntu / Debian

```bash
sudo apt update
sudo apt install apache2 -y

sudo systemctl enable --now apache2
sudo systemctl status apache2
```

Use the service and file paths appropriate to your distribution.

---

## 11. Apache Document Root

The document root is the directory from which Apache serves static website files for a particular virtual host.

On many RHEL-family default installations, it is:

```text
/var/www/html
```

Create a test page:

```bash
echo "<h1>Hello from Apache</h1>" | sudo tee /var/www/html/index.html
```

Test locally:

```bash
curl http://localhost
```

Expected response includes:

```html
<h1>Hello from Apache</h1>
```

If access fails, check the service, listening port, firewall, SELinux policy, and Apache configuration.

---

## 12. Apache Configuration File Paths

On CentOS Stream / RHEL, common locations include:

| Path | Purpose |
|---|---|
| `/etc/httpd/conf/httpd.conf` | Main Apache configuration |
| `/etc/httpd/conf.d/` | Additional configuration files |
| `/etc/httpd/conf.modules.d/` | Module-loading configuration on common packaged installations |
| `/var/www/html/` | Default document root |
| `/var/log/httpd/access_log` | Common default access log |
| `/var/log/httpd/error_log` | Common default error log |

On Ubuntu / Debian, common locations include:

| Path | Purpose |
|---|---|
| `/etc/apache2/apache2.conf` | Main configuration |
| `/etc/apache2/sites-available/` | Available virtual-host configurations |
| `/etc/apache2/sites-enabled/` | Enabled virtual-host configurations |
| `/etc/apache2/mods-available/` | Available module configurations |
| `/etc/apache2/mods-enabled/` | Enabled module configurations |
| `/var/www/html/` | Common default document root |
| `/var/log/apache2/access.log` | Common default access log |
| `/var/log/apache2/error.log` | Common default error log |

Exact paths can vary by distribution and configuration.

---

## 13. Apache Configuration File Basics

The main configuration file on CentOS Stream / RHEL is commonly:

```text
/etc/httpd/conf/httpd.conf
```

View it:

```bash
sudo less /etc/httpd/conf/httpd.conf
```

Search for important directives:

```bash
sudo grep -nE "^(Listen|ServerRoot|DocumentRoot|ErrorLog|CustomLog|Include)" /etc/httpd/conf/httpd.conf
```

### Important Directives

**ServerRoot**

Specifies the base directory used for server configuration and related files.

**Listen**

Specifies the IP address and port on which Apache listens.

Example:

```apache
Listen 80
```

**DocumentRoot**

Specifies the directory from which a virtual host serves files.

Example:

```apache
DocumentRoot "/var/www/html"
```

**ErrorLog**

Specifies the error log destination.

Example:

```apache
ErrorLog "logs/error_log"
```

**CustomLog**

Specifies the access log destination and format.

Example:

```apache
CustomLog "logs/access_log" combined
```

**Include**

Loads another configuration file.

Example:

```apache
IncludeOptional conf.d/*.conf
```

---

## 14. Apache Access Logs

An access log records HTTP requests handled by the server.

Typical information includes:

- Client IP address.
- Request timestamp.
- HTTP request method and path.
- HTTP status code.
- Response size.
- Referrer and user-agent, depending on the configured log format.

### Common Location

CentOS Stream / RHEL:

```text
/var/log/httpd/access_log
```

Ubuntu / Debian:

```text
/var/log/apache2/access.log
```

### View Access Logs

```bash
sudo tail -f /var/log/httpd/access_log
```

Generate a request from another terminal:

```bash
curl http://localhost
```

You should see a new access-log entry if the request is handled by the relevant virtual host and logging is configured there.

### Useful Commands

```bash
sudo tail -n 50 /var/log/httpd/access_log
sudo grep " 404 " /var/log/httpd/access_log
sudo less /var/log/httpd/access_log
```

A `404` response generally indicates that the requested resource was not found.

---

## 15. Apache Error Logs

Error logs record server errors, startup issues, configuration problems, and other diagnostic messages.

Common examples include:

- Permission denied.
- Missing files.
- Configuration syntax errors.
- Failed backend connections.
- Module loading failures.
- Startup or binding errors.

### Common Location

CentOS Stream / RHEL:

```text
/var/log/httpd/error_log
```

Ubuntu / Debian:

```text
/var/log/apache2/error.log
```

### View Error Logs

```bash
sudo tail -f /var/log/httpd/error_log
```

Inspect recent messages:

```bash
sudo tail -n 100 /var/log/httpd/error_log
```

Check the service logs:

```bash
sudo journalctl -u httpd -n 100 --no-pager
```

### Access Log vs Error Log

| Access log | Error log |
|---|---|
| Records requests and responses | Records errors and diagnostic messages |
| Useful for traffic analysis | Useful for troubleshooting |
| Can help identify 404 or 500 responses | Can explain why a request failed |
| Common file: `access_log` | Common file: `error_log` |

An HTTP status code alone does not always explain the underlying problem. Correlate the access log with the error log and application logs.

---

## 16. Apache Virtual Hosts

A virtual host allows one Apache server to serve multiple websites or domains.

For example:

```text
example.com
app.example.com
shop.example.com
```

These websites can use different document roots, log files, and configuration settings on the same server.

### Types of Virtual Hosts

**Name-based virtual hosts**
- Multiple domain names share an IP address.
- Apache selects the virtual host based on the HTTP `Host` header and related request information.

**IP-based virtual hosts**
- Virtual hosts are associated with different IP addresses.

Name-based virtual hosting is common in modern web deployments.

### Example on CentOS Stream / RHEL

Create a document root:

```bash
sudo mkdir -p /var/www/example.com
echo "<h1>Example Website</h1>" | sudo tee /var/www/example.com/index.html
```

Create a virtual-host configuration:

```bash
sudo vi /etc/httpd/conf.d/example.com.conf
```

Add:

```apache
<VirtualHost *:80>
    ServerName example.com
    ServerAlias www.example.com

    DocumentRoot "/var/www/example.com"

    <Directory "/var/www/example.com">
        Require all granted
        AllowOverride None
    </Directory>

    ErrorLog "/var/log/httpd/example.com_error.log"
    CustomLog "/var/log/httpd/example.com_access.log" combined
</VirtualHost>
```

Validate the configuration:

```bash
sudo apachectl configtest
```

Reload Apache:

```bash
sudo systemctl reload httpd
```

Test locally:

```bash
curl -H "Host: example.com" http://127.0.0.1/
```

This test checks virtual-host selection locally; public DNS and firewall configuration are separate requirements for external access.

### Important Virtual-Host Directives

- `ServerName`: Primary hostname.
- `ServerAlias`: Additional hostnames.
- `DocumentRoot`: Website directory.
- `<Directory>`: Directory-specific access and behavior rules.
- `ErrorLog`: Virtual-host error log.
- `CustomLog`: Virtual-host access log.

---

## 17. Apache Include Files

Apache uses include directives to load configuration from other files.

This helps administrators organize configuration into smaller, manageable files.

### Example

```apache
IncludeOptional conf.d/*.conf
```

On common RHEL-family installations, this allows Apache to load matching `.conf` files from:

```text
/etc/httpd/conf.d/
```

A virtual-host configuration can therefore be stored in:

```text
/etc/httpd/conf.d/example.com.conf
```

### Difference Between Include and IncludeOptional

- `Include`: Loads the specified file or pattern; failure to find required configuration can cause an error.
- `IncludeOptional`: Allows optional files or patterns to be absent.

Check included configuration and virtual hosts:

```bash
sudo apachectl -S
sudo apachectl configtest
```

**Best practice:** Keep each website's configuration in a separate file when appropriate, validate changes before reloading, and keep backups of important configuration.

---

## 18. Apache Modules

Apache supports modules that add or extend functionality.

Examples:

| Module | Purpose |
|---|---|
| `mod_ssl` | TLS/SSL support |
| `mod_proxy` | Proxy functionality |
| `mod_proxy_http` | HTTP reverse proxying |
| `mod_rewrite` | URL rewriting |
| `mod_headers` | HTTP header manipulation |
| `mod_deflate` | Response compression support |

List loaded modules:

```bash
sudo httpd -M
```

On Ubuntu / Debian, you can inspect enabled modules with:

```bash
apache2ctl -M
```

The exact commands and available modules depend on the installed package.

---

## 19. Apache as a Reverse Proxy

Apache can receive client requests and forward them to an application server.

Example:

```text
Browser
   |
   | HTTP / HTTPS
   v
Apache HTTP Server
   |
   | Reverse proxy
   v
Application Server at 127.0.0.1:8080
```

A simplified HTTP proxy configuration may look like this:

```apache
<VirtualHost *:80>
    ServerName app.example.com

    ProxyPreserveHost On
    ProxyPass "/" "http://127.0.0.1:8080/"
    ProxyPassReverse "/" "http://127.0.0.1:8080/"
</VirtualHost>
```

This requires the appropriate proxy modules to be installed and enabled, including `mod_proxy` and `mod_proxy_http`.

Validate before reloading:

```bash
sudo apachectl configtest
sudo systemctl reload httpd
```

A reverse proxy can help centralize TLS termination, routing, and access controls.

---

# Apache Tomcat

## 20. What Is Apache Tomcat?

Apache Tomcat is an open-source Java web container that implements Java Servlet and Jakarta Servlet specifications, depending on the Tomcat version. It also supports JSP in compatible versions.

Tomcat runs Java web applications packaged commonly as WAR files.

### What Is a WAR File?

WAR stands for **Web Application Archive**.

A WAR file packages a Java web application, including compiled classes, resources, and web application metadata.

Example:

```text
myapp.war
```

Tomcat can deploy a WAR file and serve its application, depending on the deployment configuration.

### What Is a JAR File?

JAR stands for **Java Archive**.

JAR files package Java classes and resources. They can be libraries or executable applications, depending on their contents and manifest.

Not every JAR is a web application, and not every Java application needs Tomcat.

---

## 21. Apache HTTP Server vs Apache Tomcat

| Feature | Apache HTTP Server | Apache Tomcat |
|---|---|---|
| Primary role | HTTP server and reverse proxy | Java web application container |
| Static files | Excellent support | Can serve files too |
| Java Servlet applications | Usually proxies to a Java runtime | Runs Servlet applications |
| Common service name | `httpd` on RHEL-family systems | Often `tomcat`, package-dependent |
| Common port | 80 / 443 | 8080 by default in many installations |
| Typical configuration | `httpd.conf`, virtual-host files | `server.xml`, context and application configuration |
| Application format | Static files or proxied applications | WAR files and deployed web applications |

They can be used together:

```text
Client
   |
   | HTTPS :443
   v
Apache HTTP Server
   |
   | Reverse proxy
   v
Tomcat :8080
   |
   v
Java Web Application
   |
   v
Database
```

Apache HTTP Server is not mandatory in front of Tomcat. Tomcat can serve HTTP requests directly.

---

## 22. Install and Run Tomcat

Tomcat installation depends on the operating system, package repositories, Java version, and required Tomcat release.

First, check Java:

```bash
java -version
```

If Java is not installed, install a JDK version supported by your chosen Tomcat release and application.

On some Linux distributions, Tomcat packages are available through the system package manager. Package names, versions, and configuration paths vary, so inspect the package details before installing.

Example on a compatible RHEL-family repository:

```bash
sudo dnf info tomcat
```

If the required package is available:

```bash
sudo dnf install tomcat -y
```

Check the service:

```bash
sudo systemctl status tomcat
```

If the service exists, enable and start it:

```bash
sudo systemctl enable --now tomcat
```

Check the listening port:

```bash
sudo ss -lntp
```

If Tomcat is configured to listen on port 8080, test it locally:

```bash
curl http://localhost:8080/
```

The exact response depends on the installed Tomcat version, deployment configuration, and whether an application is deployed.

**Important:** Some distributions do not provide the desired Tomcat release, or may split its files and service configuration into several packages. Use the official Apache Tomcat documentation when installing a specific release manually.

---

## 23. Tomcat Configuration File Paths

Common configuration locations for a traditional Tomcat installation include:

```text
$CATALINA_HOME/conf/server.xml
$CATALINA_BASE/conf/server.xml
$CATALINA_BASE/conf/web.xml
$CATALINA_BASE/conf/context.xml
$CATALINA_BASE/logs/
$CATALINA_BASE/webapps/
```

- `CATALINA_HOME`: Location of the Tomcat installation.
- `CATALINA_BASE`: Location of the configuration and runtime instance.
- `server.xml`: Main server configuration.
- `web.xml`: Default web application configuration.
- `context.xml`: Context-level configuration.
- `logs/`: Application and server logs.
- `webapps/`: Common application deployment directory.

The paths above are conceptual defaults; Linux distribution packages may use different locations and service layouts.

### Find Configuration Files on Linux

```bash
sudo find /etc /opt /usr/share /var/lib -name server.xml 2>/dev/null
```

Find Tomcat-related directories:

```bash
sudo find /etc /opt /usr/share /var/lib -type d -iname "*tomcat*" 2>/dev/null
```

---

## 24. Tomcat `server.xml`

`server.xml` is Tomcat's main server configuration file.

It can configure:
- Server and service structure.
- HTTP connectors.
- Ports and protocols.
- Engine and host configuration.
- Access logging.
- TLS connectors, depending on configuration.

A simplified connector example is:

```xml
<Connector
    port="8080"
    protocol="HTTP/1.1"
    connectionTimeout="20000"
/>
```

This illustrates an HTTP connector listening on port 8080. A real installation may include additional settings.

### Why Is `server.xml` Important?

If Tomcat cannot start or the application cannot be reached, inspect the connector settings, ports, configuration syntax, and server logs.

Always back up the configuration and validate changes in an appropriate environment before restarting production services.

---

## 25. Deploy a WAR File to Tomcat

In a typical installation that supports automatic deployment, a WAR file can be placed in the configured application deployment directory.

For example:

```bash
sudo cp myapp.war /path/to/tomcat/webapps/
```

Tomcat may deploy it automatically, depending on its configuration.

If the application context path is derived from the WAR filename, the application may be available at:

```text
http://localhost:8080/myapp/
```

This is a common default behavior, not a guarantee; deployment configuration can change the context path.

Check the logs if deployment fails:

```bash
sudo journalctl -u tomcat -n 100 --no-pager
```

Or inspect the Tomcat logs directory identified in your installation.

---

## 26. Troubleshooting Web Servers

### Apache Is Not Starting

```bash
sudo systemctl status httpd
sudo apachectl configtest
sudo journalctl -u httpd -n 100 --no-pager
```

### Website Returns 403 Forbidden

Check:

- Directory and file permissions.
- Apache `<Directory>` access rules.
- SELinux labels on RHEL-family systems.
- Whether the web server can read the document root.

### Website Returns 404 Not Found

Check:

- The requested URL.
- `DocumentRoot`.
- Virtual-host selection.
- Whether the file or application exists.
- URL rewriting and proxy rules.

### Website Returns 502 Bad Gateway

If Apache is reverse-proxying to an application server, check:

- Whether the backend application is running.
- The backend port and address.
- Whether the proxy modules are enabled.
- Backend application logs.
- Network connectivity and access controls.

### Tomcat Is Not Starting

Check:

```bash
java -version
sudo systemctl status tomcat
sudo journalctl -u tomcat -n 100 --no-pager
sudo ss -lntp
```

Potential causes include incompatible Java versions, invalid configuration, unavailable ports, missing files, and permission problems.

---

## 27. DevOps Production Best Practices

- Use HTTPS for production traffic.
- Keep server software and dependencies patched.
- Run services using appropriately restricted accounts.
- Do not expose application management interfaces unnecessarily.
- Restrict access to log files and configuration files.
- Store secrets securely, not in Git repositories.
- Configure log rotation and monitoring.
- Validate configuration before reloading or restarting.
- Use health checks and centralized logging.
- Use reverse proxies and load balancers where appropriate.
- Test deployments in a non-production environment first.
- Monitor HTTP status codes, latency, error rates, and backend health.

---

## 28. Quick Revision — Important Commands

### Apache HTTP Server

```bash
sudo dnf install httpd -y
sudo systemctl enable --now httpd
sudo systemctl status httpd
sudo apachectl configtest
sudo apachectl -S
sudo httpd -M
sudo tail -f /var/log/httpd/access_log
sudo tail -f /var/log/httpd/error_log
sudo journalctl -u httpd -n 100 --no-pager
curl http://localhost
```

### Tomcat

```bash
java -version
sudo systemctl status tomcat
sudo journalctl -u tomcat -n 100 --no-pager
sudo ss -lntp
curl http://localhost:8080/
```

### Important Paths

```text
Apache main config:
  /etc/httpd/conf/httpd.conf

Apache additional config:
  /etc/httpd/conf.d/

Apache document root:
  /var/www/html/

Apache access log:
  /var/log/httpd/access_log

Apache error log:
  /var/log/httpd/error_log

Tomcat main config:
  $CATALINA_BASE/conf/server.xml

Tomcat applications:
  $CATALINA_BASE/webapps/

Tomcat logs:
  $CATALINA_BASE/logs/
```

## 29. Final Takeaways

- A client sends requests; a server processes them and returns responses.
- A web server handles HTTP traffic and commonly serves static files.
- An application server or runtime executes application code.
- A web framework helps developers build application routes and business logic.
- Static websites can be served directly by Apache or Nginx.
- Dynamic websites often use a web server or reverse proxy in front of an application runtime.
- Apache HTTP Server uses `httpd` on many RHEL-family systems.
- Apache configuration is commonly stored in `/etc/httpd/`.
- Access logs record requests; error logs help troubleshoot failures.
- Virtual hosts allow multiple websites to share one Apache instance.
- Include directives load configuration from other files.
- Tomcat runs Java Servlet-based web applications and can deploy WAR files.
- `server.xml` configures important Tomcat server and connector settings.
- DevOps engineers must understand configuration, logging, deployment, troubleshooting, and secure operation.