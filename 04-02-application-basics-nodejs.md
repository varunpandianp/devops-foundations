# Node.js Fundamentals — Learning Notes

## 1. What Is Node.js?

Node.js is an open-source, cross-platform JavaScript runtime environment that allows us to execute JavaScript outside a web browser.

Normally, JavaScript runs inside browsers such as Chrome. Node.js allows JavaScript to run on servers, command-line tools, automation scripts, and backend applications.

### Why Do We Need Node.js?

- To build backend applications and REST APIs.
- To create command-line tools.
- To automate tasks using JavaScript.
- To build real-time applications such as chat systems.
- To run JavaScript on servers.
- To use npm to manage JavaScript packages and dependencies.

### Example

Create a file named `app.js`:

```javascript
console.log("Hello from Node.js!");
```

Execute it:

```bash
node app.js
```

Output:

```text
Hello from Node.js!
```

Check the installed versions:

```bash
node -v
npm -v
```

- `node` executes JavaScript.
- `npm` manages JavaScript packages.

---

## 2. What Is a Package Manager?

A package manager is a tool used to install, update, remove, and manage software packages and their dependencies.

In the Node.js ecosystem, **npm (Node Package Manager)** is the default package manager commonly installed alongside Node.js.

### Why Do We Need npm?

Suppose we are building a Node.js application that needs Express to create an HTTP server.

Instead of writing all the functionality ourselves, we install the Express package using npm.

```bash
npm install express
```

npm downloads Express and the dependencies it requires, then makes them available to the application.

### Important npm Commands

```bash
npm init -y
npm install express
npm install
npm uninstall express
npm update
npm list
npm list -g --depth=0
npm view express
```

| Command | Purpose |
|---|---|
| `npm init -y` | Creates a default `package.json` |
| `npm install express` | Installs Express locally |
| `npm install` | Installs dependencies listed in `package.json` |
| `npm uninstall express` | Removes a local package |
| `npm update` | Updates packages within the permitted version ranges |
| `npm list` | Lists installed local packages |
| `npm list -g --depth=0` | Lists top-level global packages |
| `npm view express` | Displays package information from the npm registry |

---

## 3. What Is a Node.js Package?

A package is a collection of code and metadata that can be reused by an application.

Packages may contain JavaScript files, configuration files, documentation, and other resources.

Examples of popular npm packages:

- `express` — web applications and APIs.
- `axios` — HTTP requests.
- `dotenv` — environment variable configuration.
- `nodemon` — restarts applications when files change.
- `mongoose` — MongoDB object modeling.

A package is not necessarily the same as a single file or a module. A package can contain multiple modules.

---

## 4. Local Package Installation

A local package is installed within a particular project, normally under its `node_modules` directory.

### Example

Create a project:

```bash
mkdir myapp
cd myapp
npm init -y
```

Install Express:

```bash
npm install express
```

Typical project structure:

```text
myapp/
├── node_modules/
│   └── express/
├── package.json
├── package-lock.json
└── app.js
```

### What Happens During Local Installation?

1. npm downloads the package from the configured registry, if needed.
2. npm installs it under the project's `node_modules` directory.
3. npm records the dependency in `package.json`.
4. npm updates `package-lock.json` to record the resolved dependency tree.

### Why Use Local Installation?

- Different projects can use different package versions.
- Dependencies are tracked in `package.json`.
- Team members can install dependencies using `npm install`.
- CI/CD pipelines can install the project's required packages.
- It helps keep project dependencies isolated.

### Install a Package as a Development Dependency

```bash
npm install --save-dev nodemon
```

Development dependencies are recorded under `devDependencies` in `package.json`.

They are typically used for testing, linting, development servers, and build tools.

---

## 5. Global Package Installation

A global package is installed in npm's global package location and is generally intended to provide a command-line tool that can be used across projects.

### Example

```bash
npm install -g typescript
```

Verify:

```bash
tsc --version
npm list -g --depth=0
npm root -g
npm prefix -g
```

### Why Use Global Installation?

- To make command-line tools available across projects.
- To install utilities that developers use repeatedly.
- To run commands such as `tsc` without installing TypeScript separately in every project.

### Important Considerations

- Global packages are not automatically available as project dependencies.
- Global installation paths depend on the operating system and npm configuration.
- Avoid using `sudo` unnecessarily; permission problems are often better solved through an appropriate Node.js installation or npm configuration.

### Local vs Global Installation

| Feature | Local | Global |
|---|---|---|
| Command | `npm install package-name` | `npm install -g package-name` |
| Typical location | Project's `node_modules` | npm's global package directory |
| Scope | Specific project | Shared command-line environment |
| Recorded in project `package.json` | Normally yes | No |
| Common use | Express, Axios, project dependencies | TypeScript CLI, other developer tools |
| Version management | Per project | Shared global installation |

**Best practice:** Install application libraries locally. Install packages globally only when you specifically need a shared command-line tool.

---

## 6. What Is a Module in Node.js?

A module is a reusable unit of code that can expose functions, objects, or values to other parts of an application.

For foundational learning, Node.js modules can be grouped into two broad categories:

1. Built-in modules.
2. External modules.

Your own application files can also be modules.

---

## 7. Built-in Modules

Built-in modules are provided by Node.js. You do not need to install them separately using npm.

### Common Built-in Modules

| Module | Purpose |
|---|---|
| `fs` | File system operations |
| `path` | File and directory path handling |
| `os` | Operating system information |
| `http` | HTTP servers and clients |
| `https` | HTTPS functionality |
| `events` | Event-driven programming |
| `crypto` | Cryptographic operations |
| `child_process` | Running child processes |
| `url` | URL parsing and handling |

### Example: File System Module

Create `app.js`:

```javascript
const fs = require('node:fs');

fs.writeFileSync('hello.txt', 'Hello from Node.js');

console.log('File created successfully');
```

Run:

```bash
node app.js
```

Check the created file:

```bash
cat hello.txt
```

The `fs` module is built into Node.js, so no `npm install fs` command is required.

### Example: Operating System Module

```javascript
const os = require('node:os');

console.log('Platform:', os.platform());
console.log('Architecture:', os.arch());
console.log('Free memory:', os.freemem());
```

Run:

```bash
node app.js
```

This is useful for understanding the environment in which an application runs.

---

## 8. External Modules

External modules are packages obtained from outside Node.js's built-in module collection. Many are installed from the npm registry.

### Example: Express

Create a project:

```bash
mkdir express-demo
cd express-demo
npm init -y
npm install express
```

Create `app.js`:

```javascript
const express = require('express');

const app = express();

app.get('/', (req, res) => {
    res.send('Hello from Express!');
});

app.listen(3000, () => {
    console.log('Server listening on port 3000');
});
```

Run:

```bash
node app.js
```

Test from another terminal:

```bash
curl http://localhost:3000
```

Expected response:

```text
Hello from Express!
```

Express is an external package, so we install it with npm before using it.

### How Node.js Finds External Packages

For a statement such as:

```javascript
const express = require('express');
```

Node.js searches the applicable module resolution locations, including nearby `node_modules` directories, to locate the package.

For a built-in module, use a name such as:

```javascript
require('node:fs');
```

---

## 9. Application Modules: Your Own Files

You can divide your application into multiple files to keep code reusable and maintainable.

For example:

`math.js`

```javascript
function add(a, b) {
    return a + b;
}

module.exports = { add };
```

`app.js`

```javascript
const math = require('./math');

console.log(math.add(10, 20));
```

Run:

```bash
node app.js
```

Output:

```text
30
```

Here, `math.js` is a module created by us. It is neither a built-in module nor an external npm package.

Node.js supports both CommonJS (`require`, `module.exports`) and ECMAScript modules (`import`, `export`).

---

## 10. What Is `package.json`?

`package.json` is the main metadata and configuration file for a Node.js project.

It can contain:

- Project name and version.
- Application description.
- Entry-point metadata.
- Scripts.
- Dependencies.
- Development dependencies.
- Other project configuration.

Create it:

```bash
npm init -y
```

Example:

```json
{
  "name": "myapp",
  "version": "1.0.0",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "^5.0.0"
  }
}
```

The Express version above is illustrative; the actual version depends on what npm installs.

Start the application using the script:

```bash
npm start
```

## 11. What Is `package-lock.json`?

`package-lock.json` records the resolved versions of dependencies and related dependency metadata.

It helps developers and CI/CD pipelines reproduce the dependency tree more consistently.

Typical workflow:

```bash
git clone <repository-url>
cd <project-directory>
npm ci
npm start
```

`npm ci` is commonly used in CI/CD pipelines because it installs dependencies according to the lockfile and expects the lockfile to be consistent with `package.json`.

---

## 12. Node.js and npm in DevOps

Node.js and npm are useful throughout the software delivery lifecycle.

### Example CI/CD Workflow

1. A developer pushes application code to GitHub.
2. A CI pipeline checks out the repository.
3. Node.js is installed or selected in the build environment.
4. `npm ci` installs the project's dependencies.
5. Tests and linting run.
6. The application is built if required.
7. A Docker image is created.
8. The image is pushed to a container registry.
9. The application is deployed to a server, Kubernetes, or a cloud platform.

Example commands used in a pipeline:

```bash
node --version
npm --version
npm ci
npm test
npm run build
```

The `build` script must be defined by the project; not every Node.js application has one.

### Why DevOps Engineers Need to Understand npm

- Installing dependencies in CI/CD pipelines.
- Troubleshooting failed builds.
- Managing application versions.
- Creating reproducible builds with lockfiles.
- Building and packaging Node.js applications.
- Containerizing applications with Docker.
- Understanding environment variables and runtime configuration.

---

## 13. Troubleshooting Commands

```bash
node -v
npm -v
which node
which npm
npm root
npm root -g
npm list
npm list -g --depth=0
npm config get prefix
npm config get registry
```

| Problem | What to check |
|---|---|
| `node: command not found` | Node.js installation and `PATH` |
| `npm: command not found` | npm installation and `PATH` |
| `Cannot find module` | Package installation and module resolution |
| `EACCES` permission error | Directory ownership and npm configuration |
| Package works locally but not in CI | Dependency declarations and lockfile |
| Different dependency versions | `package.json` and `package-lock.json` |

## 14. Key Takeaways

- Node.js executes JavaScript outside a browser.
- npm installs and manages packages.
- Local packages belong to a project.
- Global packages are generally used for shared CLI tools.
- Built-in modules come with Node.js.
- External packages are commonly installed through npm.
- Your own JavaScript files can also be modules.
- `package.json` describes project metadata and dependencies.
- `package-lock.json` records the resolved dependency tree.
- In DevOps pipelines, `npm ci` is commonly used for repeatable dependency installation.

