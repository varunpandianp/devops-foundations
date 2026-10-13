# Linux Fundamentals, CLI, Package Management & Service Management

## 1. What Is Linux?

Linux is an open-source operating system widely used to run servers, cloud infrastructure, containers, and DevOps applications.

### Why Linux is important for DevOps

- Most cloud servers run Linux.
- Docker containers commonly use Linux-based images.
- Kubernetes worker nodes frequently run Linux.
- DevOps engineers use Linux CLI for deployment, automation, troubleshooting, and monitoring.
- Services such as Nginx, Jenkins, and application servers often run as Linux services.

### Linux architecture

    User
      |
      v
    Applications and Commands
      |
      v
    Shell (Bash, Zsh)
      |
      v
    System Libraries and System Calls
      |
      v
    Linux Kernel
      |
      v
    Hardware / Virtual Machine

**Kernel:** Manages CPU, memory, processes, devices, and other system resources.

**Shell:** Accepts commands and allows users to interact with the operating system.

**CLI:** Command-Line Interface used to operate a system through commands.

---

## 2. Linux Distributions

A Linux distribution packages the Linux kernel with utilities, package managers, and other software.

| Distribution | Common package manager |
|---|---|
| CentOS Stream | DNF |
| RHEL | DNF |
| Rocky Linux | DNF |
| AlmaLinux | DNF |
| Fedora | DNF |
| Ubuntu | APT |
| Debian | APT |
| Amazon Linux 2023 | DNF |
| Amazon Linux 2 | YUM |

CentOS Stream 10 uses DNF as its primary package manager. On many systems, `yum` is available as a compatibility command.

---

## 3. Linux CLI Basics

### Basic command structure

    command [options] [arguments]

Example:

    ls -l /etc

- `ls` — command
- `-l` — option
- `/etc` — argument

### Common CLI commands

    pwd                 # Print current working directory
    ls                  # List files and directories
    ls -l               # Detailed listing
    ls -la              # Include hidden files
    clear               # Clear terminal screen
    date                # Display date and time
    cal                 # Display calendar, if installed
    history             # Show command history
    man ls              # Open the manual for ls
    ls --help           # Show command help
    echo "Hello Linux"  # Print text
    hostname            # Display hostname
    uname -a            # Display kernel/system information

Linux commands are generally case-sensitive.

For example, `ls` and `LS` are different commands.

---

## 4. Linux Shells

A shell interprets commands and runs programs.

### Common types of shells

| Shell | Description |
|---|---|
| Bash | Bourne Again Shell; widely used in DevOps |
| sh | Traditional POSIX-style shell interface |
| Zsh | Interactive shell with customization features |
| Fish | User-friendly interactive shell |
| Ksh | Korn shell |

### Check the current shell

    echo $SHELL
    ps -p $$ -o comm=

### List available shells

    cat /etc/shells

### Switch to Bash for the current session

    bash

### Change a user's login shell

    chsh -s /bin/bash username

This may require administrative privileges and a valid shell path. It changes the user's configured login shell, not just the current terminal.

---

## 5. Linux File System Structure

Linux uses a hierarchical directory structure beginning at `/`, called the root directory.

| Directory | Purpose |
|---|---|
| `/` | Root of the filesystem |
| `/home` | Regular users' home directories |
| `/root` | Root user's home directory |
| `/etc` | System configuration files |
| `/var` | Variable data such as logs and application data |
| `/var/log` | Log files |
| `/tmp` | Temporary files |
| `/usr/bin` | Common user commands |
| `/usr/sbin` | Administrative commands |
| `/opt` | Optional or third-party applications |
| `/dev` | Device files |
| `/proc` | Process and kernel information |
| `/sys` | Kernel and device information |
| `/boot` | Boot-related files |
| `/mnt` | Common temporary mount point |
| `/media` | Common mount point for removable media |

**DevOps example:** Application configuration may live in `/etc`, logs in `/var/log`, and a custom application in `/opt` or `/usr/local`.

---

## 6. Linux File and Directory Commands

### Navigate directories

    pwd
    ls
    ls -l
    ls -la
    cd /etc
    cd ..
    cd ~
    cd -

### Create files and directories

    touch app.log
    mkdir project
    mkdir -p project/config/dev
    cp app.log app-backup.log
    cp -r project project-backup

### Move and rename

    mv app.log application.log
    mv application.log project/

### Remove files and directories

    rm application.log
    rmdir empty-directory
    rm -r project-backup

**Warning:** `rm -r` deletes files recursively. Check the target carefully before running it. Avoid using `rm -rf` on paths you have not verified.

### Read files

    cat /etc/os-release
    less /var/log/messages
    head -n 10 app.log
    tail -n 10 app.log
    tail -f app.log

`tail -f` is useful for observing a log while an application is running.

### Search file content

    grep "ERROR" app.log
    grep -i "error" app.log
    grep -n "ERROR" app.log
    grep -r "listen" /etc/nginx/

### Find files

    find /etc -name "*.conf"
    find /var/log -type f
    find /tmp -type f -mtime -1

### File permissions

    ls -l
    chmod 644 app.conf
    chmod 755 deploy.sh
    chown user:group app.conf

Permission example:

    -rwxr-xr--

- `r` = read
- `w` = write
- `x` = execute
- First permission group = owner
- Second permission group = group
- Third permission group = others

Numeric permissions:

- `4` = read
- `2` = write
- `1` = execute
- `644` = owner read/write; group and others read
- `755` = owner full permissions; group and others read/execute

Use `sudo` when required and grant only the permissions a file or service actually needs.

---

## 7. The `vi` / `vim` Editor

`vi` and `vim` are terminal-based text editors commonly used to edit configuration files and systemd unit files.

Open or create a file:

    vi app.conf

### Three important modes

| Mode | Purpose |
|---|---|
| Normal mode | Navigate, delete, copy, paste, and execute editor commands |
| Insert mode | Type or edit text |
| Command-line mode | Save, quit, search, and run editor commands |

### Essential `vi` commands

| Command | Action |
|---|---|
| `i` | Insert before cursor |
| `a` | Append after cursor |
| `o` | Open a new line below |
| `O` | Open a new line above |
| `Esc` | Return to Normal mode |
| `h` | Move left |
| `j` | Move down |
| `k` | Move up |
| `l` | Move right |
| `0` | Move to the beginning of the line |
| `$` | Move to the end of the line |
| `gg` | Go to the first line |
| `G` | Go to the last line |
| `dd` | Delete a line |
| `yy` | Copy a line |
| `p` | Paste after the cursor |
| `u` | Undo |
| `Ctrl + r` | Redo |
| `/text` | Search forward for text |
| `n` | Go to the next search result |
| `:w` | Save |
| `:q` | Quit |
| `:wq` | Save and quit |
| `:q!` | Quit without saving |
| `:set number` | Show line numbers |
| `:%s/old/new/g` | Replace all occurrences in the file |

### Practice editing a file

    vi test.txt

1. Press `i`.
2. Type `Learning Linux for DevOps`.
3. Press `Esc`.
4. Type `:wq`.
5. Press Enter.

Verify:

    cat test.txt

---

## 8. User and Identity Commands

### `whoami`

Displays the effective username of the current process.

    whoami

### `id`

Displays user ID (UID), primary group ID (GID), and supplementary groups.

    id
    id username

### `who`

Shows users currently logged in.

    who

### `w`

Shows logged-in users and their current activity.

    w

### `su`

Switch to another user.

    su - username

Switch to root, if permitted:

    su -

The `-` starts a login shell and loads the target user's login environment.

### `sudo`

Run a command with elevated privileges if authorized.

    sudo systemctl status sshd
    sudo cat /etc/shadow

Use elevated privileges only when necessary. Do not share passwords or expose sensitive files unnecessarily.

### Related commands

    groups
    passwd
    getent passwd username
    getent group groupname

- `groups` — show group memberships.
- `passwd` — change a password when permitted.
- `getent passwd` — query user account information.
- `getent group` — query group information.

---

## 9. SSH — Secure Shell

SSH is used to connect securely to remote Linux servers.

### Connect to a remote server

    ssh username@server-ip

Example:

    ssh ec2-user@192.0.2.10

The example IP is reserved for documentation and is not a real server.

### Connect using a private key

    ssh -i ~/.ssh/my-key.pem ec2-user@server-ip

Restrict access to a private key:

    chmod 600 ~/.ssh/my-key.pem

### Copy a file to a remote server

    scp app.conf username@server-ip:/tmp/

### Copy a file from a remote server

    scp username@server-ip:/var/log/app.log .

### Troubleshoot SSH

    ssh -v username@server-ip
    systemctl status sshd
    ss -tlnp

On CentOS and RHEL-style systems, the SSH server service is normally `sshd`. On Ubuntu, the service is also commonly named `ssh`.

SSH connections require a reachable server, a listening SSH daemon, suitable firewall/network rules, and valid authentication.

---

## 10. Download Files Using `curl` and `wget`

### `curl`

`curl` transfers data to or from URLs. It is useful for API testing, HTTP troubleshooting, and downloading files.

Display an HTTP response:

    curl https://example.com

Show response headers:

    curl -I https://example.com

Download a file while preserving the remote filename:

    curl -O https://example.com/file.tar.gz

Download a file using a chosen local filename:

    curl -o app.tar.gz https://example.com/file.tar.gz

Follow HTTP redirects:

    curl -L -o app.tar.gz https://example.com/file.tar.gz

### Difference between `-o` and `-O`

- `curl -o filename URL` — save using the filename you specify.
- `curl -O URL` — use the filename from the URL path.

### `wget`

`wget` is commonly used to download files from HTTP, HTTPS, and FTP URLs.

    wget https://example.com/file.tar.gz

Choose a local filename:

    wget -O app.tar.gz https://example.com/file.tar.gz

Continue a supported interrupted download:

    wget -c https://example.com/file.tar.gz

**Important:** Verify the download source, checksum, and signature when available before installing downloaded software. A successful download does not guarantee that a file is safe or valid.

---

## 11. Check the Linux OS Version

### Display distribution information

    cat /etc/os-release

Example output fields:

    NAME="CentOS Stream"
    VERSION="10"
    ID="centos"

The exact values depend on the installed distribution and version.

### Other useful commands

    hostnamectl
    uname -a
    uname -r
    cat /etc/redhat-release

- `/etc/os-release` — distribution name and release information.
- `hostnamectl` — hostname and operating system details on systemd-based systems.
- `uname -r` — running kernel version.
- `/etc/redhat-release` — release information on many RHEL-derived systems.

**Remember:** The Linux distribution version and kernel version are different.

---

## 12. Linux Package Management

A package manager installs, updates, removes, and tracks software packages.

Examples include Nginx, Git, curl, Vim, and Python.

### Package formats and tools

| Tool | Purpose |
|---|---|
| RPM | Package format and low-level package tool used by RHEL-family distributions |
| DNF | Higher-level package manager used by modern Fedora/RHEL-family systems |
| YUM | Older package manager; often compatible with DNF on newer systems |
| APT | Higher-level package manager used by Debian and Ubuntu |
| dpkg | Low-level Debian package tool |

### RPM commands

Query an installed package:

    rpm -q curl

List files installed by a package:

    rpm -ql curl

Query which package owns a file:

    rpm -qf /usr/bin/curl

Install a local RPM package:

    sudo rpm -ivh package.rpm

RPM itself does not automatically resolve all dependencies like a higher-level package manager. Prefer DNF or YUM for normal repository-based installations.

### DNF commands — CentOS Stream 10

Update repository metadata and installed packages:

    sudo dnf check-update
    sudo dnf upgrade

Search for a package:

    dnf search nginx

Show package details:

    dnf info nginx

Install a package:

    sudo dnf install nginx

Remove a package:

    sudo dnf remove nginx

List installed packages:

    dnf list installed

Find which package provides a command or file:

    dnf provides /usr/bin/curl

### YUM commands

On systems where YUM is available:

    sudo yum install nginx
    sudo yum update
    sudo yum remove nginx
    yum search nginx

On modern RHEL-family systems, these commands may use DNF compatibility functionality.

### APT commands — Ubuntu/Debian

Refresh package indexes:

    sudo apt update

Upgrade installed packages:

    sudo apt upgrade

Install a package:

    sudo apt install nginx

Remove a package:

    sudo apt remove nginx

Remove a package and its package-managed configuration files:

    sudo apt purge nginx

Search for a package:

    apt search nginx

Show package details:

    apt show nginx

List installed packages:

    dpkg -l

### Package manager versus package format

- `rpm` and `dpkg` work directly with their respective package formats.
- `dnf`, `yum`, and `apt` provide higher-level package management and repository integration.
- `rpm` packages are common in the RHEL family.
- `.deb` packages are common in Debian and Ubuntu.

---

## 13. Process Management

A process is a running instance of a program.

### Common process commands

    ps
    ps aux
    top
    pgrep nginx
    pidof nginx
    kill PID
    kill -TERM PID

For a process you own, `kill PID` normally sends `SIGTERM`, allowing it to shut down gracefully. Use `kill -9 PID` only as a last resort because it prevents graceful cleanup.

### Check resource usage

    free -h
    df -h
    du -sh /var/log
    uptime

- `free -h` — memory usage.
- `df -h` — filesystem capacity.
- `du -sh` — directory size.
- `uptime` — uptime and load averages.

### Inspect listening ports

    ss -tuln
    sudo ss -tulnp

These commands help identify listening TCP/UDP sockets. Process details may require elevated privileges.

---

## 14. Linux Service Management with `systemctl`

A service is a background program that performs a task, such as serving web pages, running SSH, or starting an application.

Modern CentOS Stream, RHEL, Rocky Linux, Ubuntu, and many other distributions use **systemd** as their init and service manager.

`systemctl` is the command-line tool used to control systemd units.

### Check systemd

    systemctl --version

### Common systemctl commands

Check service status:

    systemctl status nginx

Start a service now:

    sudo systemctl start nginx

Stop a service:

    sudo systemctl stop nginx

Restart a service:

    sudo systemctl restart nginx

Reload service configuration, if supported:

    sudo systemctl reload nginx

Enable automatic startup at boot:

    sudo systemctl enable nginx

Disable automatic startup at boot:

    sudo systemctl disable nginx

Enable and start immediately:

    sudo systemctl enable --now nginx

Stop now and disable startup:

    sudo systemctl disable --now nginx

Check whether a service is active:

    systemctl is-active nginx

Check whether a service is enabled:

    systemctl is-enabled nginx

List running services:

    systemctl list-units --type=service --state=running

List failed units:

    systemctl --failed

### Important difference

| Command | Meaning |
|---|---|
| `start` | Start the service now |
| `stop` | Stop the service now |
| `restart` | Stop and start the service |
| `reload` | Ask the service to reload configuration, if supported |
| `enable` | Configure the service to start automatically at boot |
| `disable` | Prevent automatic startup at boot |
| `enable --now` | Enable at boot and start immediately |

**Remember:** `enable` does not necessarily start a service immediately. Use `enable --now` when both actions are required.

---

## 15. Configure a Custom Linux Service Using a systemd Unit File

A systemd unit file tells systemd how to start, stop, and manage a service.

This is useful for DevOps engineers who need to run a custom Python, Node.js, Java, or shell application reliably in the background.

### systemd unit file location

System-wide custom service files commonly belong in:

    /etc/systemd/system/

Example service file:

    /etc/systemd/system/devops-demo.service

### Example application

Create a directory and a simple script:

    sudo mkdir -p /opt/devops-demo
    sudo vi /opt/devops-demo/app.sh

Add the following script:

    #!/bin/bash
    while true; do
        echo "$(date): DevOps demo service is running"
        sleep 30
    done

Save and exit with `Esc`, then `:wq`.

Make the script executable:

    sudo chmod 755 /opt/devops-demo/app.sh

### Create the systemd unit file

    sudo vi /etc/systemd/system/devops-demo.service

Add the following configuration:

    [Unit]
    Description=DevOps Demo Background Service
    After=network.target

    [Service]
    Type=simple
    ExecStart=/opt/devops-demo/app.sh
    Restart=on-failure
    RestartSec=5
    User=root

    [Install]
    WantedBy=multi-user.target

### Understand each section

#### `[Unit]`

General information and startup dependencies.

- `Description` — human-readable service description.
- `After=network.target` — orders startup after the network target; it does not itself guarantee that network connectivity is ready.

#### `[Service]`

Defines how the application runs.

- `Type=simple` — systemd considers the service started when the process launched by `ExecStart` is running.
- `ExecStart` — command or executable used to start the application.
- `Restart=on-failure` — restart when the process fails.
- `RestartSec=5` — wait five seconds before a restart attempt.
- `User=root` — run as root.

For a production application, prefer a dedicated unprivileged service account instead of root whenever possible. The demo uses root only to keep initial file setup straightforward.

#### `[Install]`

Defines how the service is enabled.

- `WantedBy=multi-user.target` — allows the service to be enabled for the normal multi-user boot target.

### Load and start the service

After saving the unit file:

    sudo systemctl daemon-reload
    sudo systemctl start devops-demo
    sudo systemctl status devops-demo

Enable the service at boot:

    sudo systemctl enable devops-demo

Or do both in one command:

    sudo systemctl enable --now devops-demo

### View the service logs

    sudo journalctl -u devops-demo
    sudo journalctl -u devops-demo -f
    sudo journalctl -u devops-demo -n 50

- `journalctl -u` — display logs for a specific unit.
- `-f` — follow new log entries.
- `-n 50` — show the latest 50 entries.

The demo script writes to standard output, which systemd normally captures in the journal.

### Update the unit file

Whenever you change the systemd unit file itself, reload the configuration:

    sudo systemctl daemon-reload
    sudo systemctl restart devops-demo
    sudo systemctl status devops-demo

`daemon-reload` makes systemd reread unit files. It does not itself restart the application.

If only the application's own configuration changes, whether a restart or reload is needed depends on the application.

### Stop and remove the demo service

Stop it:

    sudo systemctl stop devops-demo

Disable automatic startup:

    sudo systemctl disable devops-demo

Remove the unit file after stopping and disabling it:

    sudo rm /etc/systemd/system/devops-demo.service
    sudo systemctl daemon-reload

Verify:

    systemctl status devops-demo

The service should no longer be available as a configured unit once systemd has reloaded and the file has been removed.

---

## 16. Troubleshooting a Failed Service

When a service fails, investigate rather than repeatedly restarting it.

### Step 1: Check status

    sudo systemctl status devops-demo

Look for the active state, exit code, and recent error messages.

### Step 2: Read logs

    sudo journalctl -u devops-demo -n 100 --no-pager

### Step 3: Validate the unit file

    sudo systemd-analyze verify /etc/systemd/system/devops-demo.service

### Step 4: Check the executable

    ls -l /opt/devops-demo/app.sh
    head -n 5 /opt/devops-demo/app.sh

Check the executable permission, interpreter, file path, and whether the configured user can execute it.

### Step 5: Check systemd configuration

    sudo systemctl daemon-reload
    sudo systemctl restart devops-demo
    sudo systemctl status devops-demo

### Common causes of failure

- Incorrect `ExecStart` path.
- Missing executable permissions.
- Invalid unit file syntax.
- Missing runtime or application dependencies.
- Incorrect ownership or permissions.
- Application exits immediately.
- Port already in use.
- Incorrect environment variables.
- Access restrictions from SELinux or other security controls.

---

## 17. Other Important Linux Commands for DevOps

### Networking

    ip addr
    ip route
    ping -c 4 example.com
    ss -tuln
    curl -I https://example.com
    getent hosts example.com

### Environment variables

    env
    printenv
    echo "$PATH"
    export APP_ENV=dev

An exported variable is available to child processes started by that shell. It is not automatically persistent across logins or reboots.

### Disk and archive commands

    df -h
    du -sh /opt
    tar -czf backup.tar.gz project/
    tar -xzf backup.tar.gz

### Pipes and redirection

    ls -l | less
    grep "ERROR" app.log | wc -l
    echo "hello" > output.txt
    echo "another line" >> output.txt
    command 2> error.log

- `|` sends one command's output to another command.
- `>` redirects output and overwrites a file.
- `>>` appends output.
- `2>` redirects standard error.

---

## 18. Practical DevOps Lab

Complete this exercise on a Linux virtual machine or a disposable test server where you have administrative access.

- [ ] Check the Linux distribution using `cat /etc/os-release`.
- [ ] Check the kernel version using `uname -r`.
- [ ] Create a directory named `linux-lab`.
- [ ] Create a file using `touch`.
- [ ] Edit the file using `vi`.
- [ ] Copy and rename the file using `cp` and `mv`.
- [ ] Search for a word using `grep`.
- [ ] Inspect your identity using `whoami` and `id`.
- [ ] Install `curl` using the distribution's package manager if needed.
- [ ] Check a service using `systemctl status`.
- [ ] Create the `devops-demo.service` unit file.
- [ ] Start and enable the service.
- [ ] Read its logs using `journalctl`.
- [ ] Stop and disable the service.
- [ ] Remove the demo unit file and reload systemd.

---

## 19. Quick Revision Table

| Topic | Commands to remember |
|---|---|
| Navigation | `pwd`, `ls`, `cd` |
| File management | `touch`, `mkdir`, `cp`, `mv`, `rm` |
| File reading | `cat`, `less`, `head`, `tail` |
| Search | `grep`, `find` |
| Editing | `vi`, `vim` |
| Users | `whoami`, `id`, `who`, `su`, `sudo` |
| Remote access | `ssh`, `scp` |
| Downloads | `curl`, `wget` |
| OS details | `cat /etc/os-release`, `uname -r` |
| Packages | `rpm`, `dnf`, `yum`, `apt`, `dpkg` |
| Processes | `ps`, `top`, `pgrep`, `kill` |
| Resources | `free -h`, `df -h`, `du -sh` |
| Services | `systemctl`, `journalctl` |
| Networking | `ip`, `ss`, `ping`, `curl` |

## 20. Key Takeaways for DevOps

1. Linux CLI is essential for server administration and troubleshooting.
2. Learn file permissions, users, processes, networking, and shell basics.
3. Understand the difference between RPM packages and package managers such as DNF.
4. Use `systemctl` to manage services and `journalctl` to investigate their logs.
5. Use systemd unit files to configure applications to run as managed background services.
6. Prefer least-privilege service accounts and explicit file permissions.
7. Practice these commands in a test VM before changing production systems.