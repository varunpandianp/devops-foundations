# Node.js Application Deployment — DevOps Learning Notes

## 1. What Is Node.js Application Deployment?

Node.js application deployment means preparing a Node.js application and running it in an environment where users or other services can access it.

A deployment typically involves:

1. Preparing application source code.
2. Installing the correct Node.js runtime.
3. Installing dependencies.
4. Configuring environment variables.
5. Starting the application.
6. Keeping the application running.
7. Restarting it if it crashes.
8. Collecting logs and monitoring its health.
9. Configuring networking, security, and reverse proxying.
10. Updating the application safely when new code is released.

### Example Architecture

```text
Developer
   |
   v
GitHub Repository
   |
   v
CI/CD Pipeline
   |
   v
Production Server
   |
   +--> Node.js Runtime
   |
   +--> npm Dependencies
   |
   +--> Process Manager
   |
   +--> Node.js Application
              |
              v
         Database / APIs
```

A reverse proxy such as Nginx may sit in front of the application to handle public HTTP/HTTPS traffic.

---

## 2. What Is a Node.js Application?

A Node.js application is a program written in JavaScript that executes using the Node.js runtime.

Example: `app.js`

```javascript
const http = require('node:http');

const server = http.createServer((req, res) => {
    res.writeHead(200, {
        'Content-Type': 'text/plain'
    });

    res.end('Hello from Node.js!');
});

server.listen(3000, '127.0.0.1', () => {
    console.log('Application listening on port 3000');
});
```

Run the application:

```bash
node app.js
```

Test it from another terminal:

```bash
curl http://127.0.0.1:3000
```

Expected output:

```text
Hello from Node.js!
```

This example uses Node.js's built-in HTTP module, so no external npm package is needed.

---

## 3. Typical Node.js Project Structure

A Node.js project can be organized as follows:

```text
nodejs-app/
├── node_modules/
├── src/
│   ├── app.js
│   ├── routes/
│   │   └── users.js
│   ├── controllers/
│   │   └── userController.js
│   ├── services/
│   │   └── userService.js
│   └── config/
│       └── config.js
├── tests/
│   └── app.test.js
├── public/
│   └── index.html
├── .env
├── .gitignore
├── package.json
├── package-lock.json
└── README.md
```

This is an example structure, not a requirement for every application.

### Purpose of Each File and Directory

| File or directory | Purpose |
|---|---|
| `src/` | Application source code |
| `src/app.js` | Application entry point in this example |
| `routes/` | Defines URL routes |
| `controllers/` | Handles request and response logic |
| `services/` | Implements reusable business logic |
| `config/` | Application configuration |
| `tests/` | Automated tests |
| `public/` | Static assets, if needed |
| `node_modules/` | Installed npm dependencies |
| `.env` | Local environment variables; should not contain committed secrets |
| `.gitignore` | Lists untracked files to ignore |
| `package.json` | Project metadata, dependencies, and scripts |
| `package-lock.json` | Records resolved dependency versions |
| `README.md` | Project documentation |

For a small application, a single `app.js` file may be enough.

---

## 4. Install and Run a Node.js Application

### Step 1: Verify Node.js and npm

```bash
node -v
npm -v
```

### Step 2: Create the Project Directory

```bash
mkdir nodejs-app
cd nodejs-app
```

### Step 3: Initialize the Project

```bash
npm init -y
```

This creates a default `package.json`.

### Step 4: Create the Application

Create `app.js`:

```javascript
const http = require('node:http');

const server = http.createServer((req, res) => {
    res.writeHead(200, {
        'Content-Type': 'text/plain'
    });

    res.end('Node.js application is running');
});

const PORT = process.env.PORT || 3000;

server.listen(PORT, '127.0.0.1', () => {
    console.log(`Server listening on port ${PORT}`);
});
```

### Step 5: Configure the npm Script

In `package.json`, add:

```json
{
  "scripts": {
    "start": "node app.js"
  }
}
```

Preserve any other fields already present in your file.

### Step 6: Run the Application

```bash
npm start
```

Test it:

```bash
curl http://127.0.0.1:3000
```

Stop the foreground process using `Ctrl+C`.

**Important:** The application above binds to localhost for safe local testing. If a reverse proxy or another machine must connect directly, configure the appropriate interface and firewall deliberately.

---

## 5. What Is `package.json`?

`package.json` is the main metadata and configuration file for a Node.js project.

It describes the project, its dependencies, scripts, and other settings.

Example:

```json
{
  "name": "nodejs-app",
  "version": "1.0.0",
  "description": "Node.js deployment learning project",
  "main": "app.js",
  "scripts": {
    "start": "node app.js",
    "dev": "node --watch app.js",
    "test": "node --test"
  },
  "dependencies": {},
  "devDependencies": {}
}
```

The scripts shown are examples. The `test` script requires actual test files, and the `dev` script requires a Node.js version that supports `--watch`.

### Important Fields

**`name`**

Identifies the package or project.

**`version`**

Specifies the project's version.

**`description`**

Describes the project.

**`main`**

Historically identifies a package's main entry point. It is not, by itself, the command that starts an application.

**`scripts`**

Defines named commands that can be executed using npm.

**`dependencies`**

Lists packages needed by the application at runtime.

**`devDependencies`**

Lists packages used for development tasks, such as testing, linting, and building.

**`type`**

Controls how `.js` files are interpreted for module loading. For example, `"type": "module"` enables ECMAScript module syntax by default for `.js` files within that package scope.

---

## 6. npm Scripts Inside `package.json`

npm scripts let developers define repeatable commands for development, testing, building, and production startup.

Example:

```json
{
  "scripts": {
    "start": "node app.js",
    "dev": "node --watch app.js",
    "test": "node --test",
    "lint": "eslint .",
    "build": "echo Build step goes here"
  }
}
```

These scripts require the relevant tools and files. For example, `eslint` must be installed, and a real build script should perform the application's actual build.

### Run Scripts

```bash
npm start
npm run dev
npm test
npm run lint
npm run build
```

For most custom script names, use `npm run <script-name>`. npm provides convenient shortcuts for commands such as `start` and `test`.

### What Happens When You Run `npm start`?

If `package.json` contains:

```json
{
  "scripts": {
    "start": "node app.js"
  }
}
```

Running:

```bash
npm start
```

causes npm to execute:

```bash
node app.js
```

npm is the command runner in this example. It does not itself become the application's production server or process manager.

---

## 7. What Is `package-lock.json`?

`package-lock.json` records the resolved dependency tree for an npm project.

It helps teams and CI/CD pipelines install consistent dependency versions.

### Why Is It Important?

- Improves reproducibility.
- Records exact resolved package versions.
- Helps prevent unexpected dependency changes.
- Supports reliable automated deployments.

### Production Installation

For an application with a committed lockfile:

```bash
npm ci --omit=dev
```

This installs the runtime dependencies recorded in the lockfile and removes an existing `node_modules` directory before installing.

Use it when development dependencies are not needed to run the application. If the project requires a build step or runtime code depends on a package incorrectly classified as a development dependency, adjust the workflow accordingly.

**Best practice:** Commit `package.json` and `package-lock.json` to Git. Do not normally commit `node_modules/`.

---

# Production Process Management

## 8. What Is a Process?

A process is a running instance of a program managed by the operating system.

When you run:

```bash
node app.js
```

Linux starts a Node.js process to execute the application.

You can inspect processes using:

```bash
ps aux
```

Find Node.js processes:

```bash
ps aux | grep node
```

Inspect listening ports:

```bash
sudo ss -lntp
```

---

## 9. What Is a Process Manager?

A process manager supervises an application process and helps control its lifecycle.

Depending on the tool, capabilities may include:

- Starting and stopping applications.
- Restarting applications after crashes.
- Managing environment variables.
- Collecting or directing logs.
- Starting applications after a reboot.
- Managing multiple application instances.
- Providing process status and monitoring information.

### Why Do We Need a Process Manager?

Suppose you run:

```bash
node app.js
```

If the application crashes, it generally stops running. If the terminal session ends, the process may also terminate depending on how it was started and managed.

A process manager can restart the application according to a configured policy and manage its startup at boot.

**Important:** A process manager does not automatically make the application highly available. A single server can still fail, and restarting a crashing application does not fix its underlying bug.

---

## 10. What Is a Production Server?

A production server can refer to the machine or environment hosting a live application, or to server software that handles application requests.

For this topic, it is useful to distinguish a process manager from a request-handling server.

### Request-Handling Server

Examples:

- Node.js HTTP server.
- Express application running on Node.js.
- Nginx.
- Apache HTTP Server.

These receive and handle HTTP requests, or proxy requests to an application.

### Process Manager

Examples:

- PM2.
- Forever.
- Supervisor.
- systemd.

These start, supervise, and manage processes.

### Difference Between a Process Manager and a Production Server

| Feature | Process manager | Request-handling server |
|---|---|---|
| Main purpose | Manage process lifecycle | Handle HTTP requests |
| Restarts crashed process | Often supported | Not its primary role |
| Routes HTTP requests | Not normally | Yes |
| Serves HTTP responses | Not its primary role | Yes |
| Starts services after reboot | Supported by many tools | Depends on the service configuration |
| Examples | PM2, Forever, Supervisor, systemd | Node.js HTTP server, Express, Nginx, Apache |

A production deployment often uses both.

---

## 11. Production Architecture for Node.js

A common Linux deployment looks like this:

```text
Client / Browser
       |
       | HTTPS :443
       v
Nginx / Apache
       |
       | Reverse proxy
       v
Node.js Application :3000
       |
       v
Database / External Services

systemd or PM2
       |
       +--> Supervises Node.js process
```

### Responsibilities

**Nginx or Apache**
- Handles public HTTP/HTTPS traffic.
- Can terminate TLS.
- Routes requests to the Node.js application.
- Can serve static assets.
- Can apply proxy settings and access controls.

**Node.js application**
- Implements API endpoints and business logic.
- Processes requests.
- Communicates with databases and other services.

**PM2 or systemd**
- Starts and supervises the application.
- Can restart the application following a failure.
- Can manage startup after reboot when configured.

**Important:** Nginx is not a replacement for PM2 or systemd, and PM2 is not a replacement for Nginx.

---

# PM2

## 12. What Is PM2?

PM2 is a process manager commonly used for Node.js applications.

It can manage application processes, restart crashed processes, maintain logs, and support startup configuration.

### Install PM2

For a user-level Node.js environment:

```bash
npm install -g pm2
```

Verify:

```bash
pm2 --version
```

Use an appropriate installation and permissions strategy for the server. Avoid unnecessary use of `sudo npm install -g`.

### Start an Application

From the project directory:

```bash
pm2 start app.js --name nodejs-app
```

Here:

- `pm2 start` starts the application under PM2.
- `app.js` is the entry-point file.
- `--name nodejs-app` assigns a convenient process name.

### Check Application Status

```bash
pm2 status
pm2 list
pm2 describe nodejs-app
```

### View Logs

```bash
pm2 logs nodejs-app
```

View only recent lines:

```bash
pm2 logs nodejs-app --lines 100
```

### Restart the Application

```bash
pm2 restart nodejs-app
```

### Stop the Application

```bash
pm2 stop nodejs-app
```

### Remove the Process from PM2

```bash
pm2 delete nodejs-app
```

`delete` removes the process from PM2's process list; it is different from stopping the application temporarily.

---

## 13. Run Multiple Instances Using PM2

PM2 supports cluster mode for compatible Node.js applications.

Example:

```bash
pm2 start app.js --name nodejs-app -i 2
```

This asks PM2 to run two cluster instances.

### Why Use Multiple Instances?

- Can use multiple CPU cores.
- May increase throughput for suitable workloads.
- Can improve process-level resilience.
- Can support graceful reloads when the application and deployment configuration are compatible.

However, application state should not depend solely on process memory if requests can be handled by different instances. Shared sessions, jobs, and state may need external storage or coordination.

Multiple processes on one machine do not protect against a complete machine failure.

---

## 14. Configure PM2 to Start After Reboot

PM2 can generate a startup integration for the system's init system.

Run:

```bash
pm2 startup
```

PM2 will display a command appropriate for the system. Follow the displayed instructions, then save the current process list:

```bash
pm2 save
```

### Why Are Both Needed?

- `pm2 startup` configures a mechanism to launch PM2 when the system starts.
- `pm2 save` saves the current PM2 process list so it can be restored.

The startup command can differ depending on the operating system and the user running PM2. Follow the exact command printed by PM2.

After a reboot, verify:

```bash
pm2 status
```

**Security note:** Run PM2 under the intended service user. Do not configure startup for one user and then expect another user's process list to be restored automatically.

---

## 15. PM2 Ecosystem Configuration

For larger applications, use an ecosystem configuration file rather than repeating lengthy commands.

Create `ecosystem.config.js`:

```javascript
module.exports = {
  apps: [
    {
      name: "nodejs-app",
      script: "./app.js",
      instances: 2,
      exec_mode: "cluster",
      autorestart: true,
      watch: false,
      env: {
        NODE_ENV: "development"
      },
      env_production: {
        NODE_ENV: "production"
      }
    }
  ]
};
```

Start in production mode:

```bash
pm2 start ecosystem.config.js --env production
```

Check the processes:

```bash
pm2 status
pm2 logs nodejs-app
```

### Important Configuration Options

- `name`: Application name.
- `script`: Entry-point file.
- `instances`: Number of instances.
- `exec_mode`: Execution mode, such as cluster.
- `autorestart`: Restart behavior after process exit.
- `watch`: Restart on file changes; generally keep disabled in production.
- `env`: Default environment variables.
- `env_production`: Production-specific environment variables.

Do not store passwords or access tokens directly in a committed ecosystem file. Use an appropriate secrets-management mechanism.

---

# Forever

## 16. What Is Forever?

Forever is a Node.js process-management tool designed to keep scripts running continuously and restart them under supported failure conditions.

It is another option for supervising Node.js processes, though PM2 and systemd are often more common choices for new deployments.

### Install Forever

```bash
npm install -g forever
```

Verify:

```bash
forever --version
```

### Start an Application

```bash
forever start app.js
```

### List Managed Processes

```bash
forever list
```

### Stop an Application

Use the script path or the process identifier shown in `forever list`, according to the installed version.

For example:

```bash
forever stop app.js
```

### View Help

```bash
forever --help
```

### Limitations and Considerations

- Fewer operational features than some newer process-management approaches.
- Operational behavior depends on the installed version and configuration.
- Does not replace a reverse proxy, monitoring system, or secure deployment process.

For a new learning project, focus on PM2 and systemd first, then learn Forever for comparison.

---

# Supervisor

## 17. What Is Supervisor?

Supervisor is a process-control system that can manage long-running programs on Unix-like systems.

It is not limited to Node.js. It can supervise Python applications, workers, and other programs.

### Install on a Compatible Linux Distribution

On some RHEL-family distributions:

```bash
sudo dnf install supervisor -y
```

The package and service names can differ by distribution. Verify the package is available for your system.

### Typical Configuration File

A common location is:

```text
/etc/supervisord.conf
```

Application-specific configuration files may be stored in:

```text
/etc/supervisord.d/
```

These paths vary by distribution.

### Example Program Configuration

Create an application configuration file in the appropriate Supervisor configuration directory:

```ini
[program:nodejs-app]
directory=/opt/nodejs-app
command=/usr/bin/node /opt/nodejs-app/app.js
autostart=true
autorestart=true
startsecs=5
user=nodeapp
stdout_logfile=/var/log/nodejs-app.out.log
stderr_logfile=/var/log/nodejs-app.err.log
```

This example assumes:

- Node.js is installed at `/usr/bin/node`.
- The application is in `/opt/nodejs-app`.
- A suitable user named `nodeapp` exists.
- The configured log paths are writable by the appropriate service.

Adjust these paths and permissions to match the actual server.

### Reload Configuration and Manage the Process

Depending on the installation and service setup, commands may include:

```bash
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl status
```

Start or restart the application:

```bash
sudo supervisorctl start nodejs-app
sudo supervisorctl restart nodejs-app
```

Check logs using the configured files:

```bash
sudo tail -f /var/log/nodejs-app.out.log
sudo tail -f /var/log/nodejs-app.err.log
```

### Why Use Supervisor?

- Manages multiple unrelated programs.
- Supports automatic restarts.
- Supports per-program users and working directories.
- Provides centralized process control.

Supervisor is a good general-purpose option when a server already uses it to manage application processes.

---

# systemd

## 18. What Is systemd?

systemd is a Linux service and system manager. It can start, stop, and supervise applications as services.

It is not specific to Node.js.

### Why Use systemd?

- Native integration with many Linux distributions.
- Starts services at boot.
- Supports restart policies.
- Provides centralized logging through the system journal.
- Controls service users and environment settings.
- Manages service dependencies.

### Example Service

Assume the application is installed in `/opt/nodejs-app`.

Create:

```bash
sudo vi /etc/systemd/system/nodejs-app.service
```

Example:

```ini
[Unit]
Description=Node.js Application
After=network.target

[Service]
Type=simple
User=nodeapp
Group=nodeapp
WorkingDirectory=/opt/nodejs-app
Environment=NODE_ENV=production
Environment=PORT=3000
ExecStart=/usr/bin/node /opt/nodejs-app/app.js
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Adjust the user, group, executable path, and application directory to match the actual installation.

Reload systemd:

```bash
sudo systemctl daemon-reload
```

Enable and start the service:

```bash
sudo systemctl enable --now nodejs-app
```

Check status:

```bash
sudo systemctl status nodejs-app
```

View logs:

```bash
sudo journalctl -u nodejs-app -f
```

Restart:

```bash
sudo systemctl restart nodejs-app
```

Stop:

```bash
sudo systemctl stop nodejs-app
```

### systemd vs PM2

| Feature | systemd | PM2 |
|---|---|---|
| Type | Operating-system service manager | Application process manager |
| Manages non-Node.js programs | Yes | Primarily application processes, especially Node.js |
| Restarts failed processes | Yes, when configured | Yes, when configured |
| Starts at boot | Native service integration | Uses startup integration |
| Node.js cluster mode | Not built in | Supported |
| Application log commands | `journalctl` or configured logging | `pm2 logs` |
| Per-process application controls | General service controls | Node.js-focused management features |

**Best practice:** You do not always need both PM2 and systemd supervising the same application. Choose a clear process-management design. PM2 can itself be started by systemd, but avoid unnecessary layers of competing restart policies.

---

## 19. Process Manager vs Reverse Proxy vs Runtime

These components perform different jobs.

| Component | Role | Examples |
|---|---|---|
| Runtime | Executes JavaScript | Node.js |
| Web framework | Defines application routes and middleware | Express |
| Process manager | Supervises process lifecycle | PM2, Forever, Supervisor, systemd |
| Web server / reverse proxy | Handles public web traffic and forwards requests | Nginx, Apache |
| Database | Stores application data | PostgreSQL, MySQL, MongoDB |
| Monitoring system | Collects metrics and alerts | Prometheus, Grafana |

### Example

```text
Internet
   |
   v
Nginx :443
   |
   v
Node.js / Express :3000
   |
   v
Database

systemd or PM2 supervises the application
```

The process manager is responsible for keeping the application process running; Nginx handles incoming web traffic.

---

# Production Deployment Workflow

## 20. Deploy a Node.js Application on Linux

### Step 1: Obtain the Source Code

```bash
git clone https://github.com/USERNAME/REPOSITORY.git
cd REPOSITORY
```

Replace the example repository URL with the actual repository.

### Step 2: Verify the Runtime

```bash
node -v
npm -v
```

Use the Node.js version supported by the application.

### Step 3: Install Dependencies

If the repository contains a committed `package-lock.json`:

```bash
npm ci
```

For production-only dependencies, when the application does not need development dependencies at runtime:

```bash
npm ci --omit=dev
```

If the project requires compilation or bundling, run the appropriate build command before excluding dependencies required by that build.

### Step 4: Configure the Environment

Provide required environment variables through a secure configuration mechanism.

Example:

```bash
export NODE_ENV=production
export PORT=3000
```

These commands affect the current shell and its child processes only. For a service-managed deployment, configure environment variables in the service or secret-management system.

### Step 5: Test the Application

```bash
npm start
```

Test the configured port:

```bash
curl http://127.0.0.1:3000/
```

Stop the foreground application after testing with `Ctrl+C`.

### Step 6: Start with a Process Manager

For example, using PM2:

```bash
pm2 start app.js --name nodejs-app
pm2 status
pm2 logs nodejs-app
```

Or use a `systemd` service for native Linux service management.

### Step 7: Configure the Reverse Proxy

If the application must be publicly accessible, configure Nginx or Apache as appropriate. Ensure the application listener, proxy target, firewall, and TLS settings agree.

### Step 8: Monitor and Maintain

- Check application status.
- Review logs.
- Monitor CPU and memory.
- Monitor HTTP error rates and response latency.
- Apply security updates.
- Test application restarts and deployments.
- Keep secrets out of Git.
- Plan for rollback if a release fails.

---

## 21. Troubleshooting Commands

### Check Node.js

```bash
node -v
npm -v
which node
which npm
```

### Check Application Processes

```bash
ps aux | grep node
```

### Check Listening Ports

```bash
sudo ss -lntp
```

### Check PM2

```bash
pm2 status
pm2 logs nodejs-app
pm2 describe nodejs-app
```

### Check systemd

```bash
sudo systemctl status nodejs-app
sudo journalctl -u nodejs-app -n 100 --no-pager
```

### Check the HTTP Response

```bash
curl -i http://127.0.0.1:3000/
```

### Common Problems

| Problem | What to check |
|---|---|
| `node: command not found` | Runtime installation and `PATH` |
| `Cannot find module` | Dependencies and working directory |
| `EADDRINUSE` | Another process is using the port |
| Application exits immediately | Startup errors and logs |
| HTTP 502 from Nginx | Application process and proxy target |
| Process does not start at boot | Startup integration or service enablement |
| Permission denied | Service user and filesystem permissions |
| Wrong environment configuration | Service environment variables |

---

## 22. Production Best Practices

- Use a supported Node.js LTS release.
- Commit `package.json` and `package-lock.json`.
- Use `npm ci` for reproducible CI/CD dependency installation.
- Keep runtime dependencies under `dependencies`.
- Do not commit `node_modules`, secrets, or unnecessary generated files.
- Run the application as a dedicated, unprivileged user.
- Use a process manager or service manager.
- Use Nginx or Apache as a reverse proxy when appropriate.
- Enable HTTPS for public applications.
- Keep application and dependency versions patched.
- Configure health checks and monitoring.
- Collect and rotate logs.
- Handle shutdown signals correctly.
- Test restarts and rollback procedures.
- Avoid running the application as root unless there is a specific, justified need.

---

## 23. Key Takeaways

- Node.js executes the application.
- `package.json` defines project metadata, dependencies, and scripts.
- `package-lock.json` records the resolved dependency tree.
- npm installs dependencies and runs configured scripts.
- PM2, Forever, Supervisor, and systemd can manage process lifecycles.
- Nginx and Apache handle web traffic and can act as reverse proxies.
- A process manager is not the same as a web server.
- PM2 supports Node.js-focused process management and cluster mode.
- Forever is another Node.js process-management option.
- Supervisor manages a range of long-running programs.
- systemd provides native Linux service management.
- Production deployment also requires security, monitoring, logging, and reliable dependency management.