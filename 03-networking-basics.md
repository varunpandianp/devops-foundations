# Linux Networking Fundamentals and DNS Configuration

## 1. What Is Computer Networking?

Computer networking allows computers, servers, containers, and other devices to communicate with one another.

### Why networking is important for DevOps

- Connecting application servers to databases.
- Connecting private servers to the internet.
- Configuring AWS VPCs, subnets, route tables, and gateways.
- Troubleshooting application connectivity.
- Configuring DNS for applications and websites.
- Understanding communication between Docker containers and Kubernetes pods.

### Basic networking components

| Component | Purpose |
|---|---|
| IP address | Identifies a network interface on an IP network |
| Subnet mask/CIDR | Defines the network prefix and address range |
| Switch | Connects devices within a local network |
| Router | Forwards packets between IP networks |
| Default gateway | Router used when no more specific route matches |
| DNS server | Resolves domain names to DNS records, including IP addresses |
| Firewall | Controls permitted network traffic |
| NIC | Network Interface Card; provides network connectivity |
| MAC address | Link-layer address used for communication on Ethernet networks |

---

## 2. IP Address and CIDR Basics

An IP address identifies a network interface at the IP layer.

### IPv4 example

    IP address: 192.168.1.10
    Subnet mask: 255.255.255.0
    CIDR: 192.168.1.10/24
    Network: 192.168.1.0/24
    Typical host range: 192.168.1.1 - 192.168.1.254
    Broadcast: 192.168.1.255

In this example, `/24` means that 24 of the 32 IPv4 bits represent the network prefix.

### Private IPv4 address ranges

| Range | CIDR |
|---|---|
| 10.0.0.0 – 10.255.255.255 | 10.0.0.0/8 |
| 172.16.0.0 – 172.31.255.255 | 172.16.0.0/12 |
| 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 |

Private addresses are used inside private networks and are not directly routable across the public internet.

---

## 3. Switching: How Devices Communicate Within a Network

A network switch connects devices in a local area network (LAN).

A typical Ethernet switch learns which MAC addresses are reachable through its ports and forwards Ethernet frames accordingly.

### Example

    PC-A: 192.168.1.10
    PC-B: 192.168.1.20
    Subnet: 192.168.1.0/24

Both PCs are in the same IP subnet.

Communication flow:

    PC-A
      |
      v
    Ethernet Switch
      |
      v
    PC-B

If PC-A does not know PC-B's MAC address, it normally uses ARP to discover the IPv4-to-MAC mapping on the local link. PC-A can then send Ethernet frames to PC-B through the switch.

### Important points

- A switch primarily forwards frames using MAC addresses.
- A router forwards IP packets between networks.
- Devices on the same subnet can normally communicate directly at Layer 2, assuming connectivity and firewall rules allow it.
- A Layer 3 switch can also perform routing.

---

## 4. Routing: How Networks Communicate

Routing allows IP packets to move between different IP networks.

A router has network interfaces connected to different networks and uses a routing table to determine where packets should go.

### Example: Two networks connected by a router

    Network A                         Network B
    192.168.1.0/24                   192.168.2.0/24

    PC-A                              PC-B
    192.168.1.10                      192.168.2.20
          |                                  |
          v                                  v
    [Router interface A] ------------ [Router interface B]
       192.168.1.1                        192.168.2.1

PC-A wants to communicate with PC-B.

1. PC-A determines that `192.168.2.20` is outside its local `/24` subnet.
2. PC-A sends the packet to its default gateway, `192.168.1.1`.
3. The router checks its routing table.
4. The router forwards the packet through its interface connected to Network B.
5. PC-B receives the packet if routing and security rules allow it.
6. The reply follows a valid route back to PC-A.

**Important:** A default gateway is normally an IP address on the same local subnet as the host. The host needs a route to reach the gateway.

---

## 5. Default Gateway: Accessing Other Networks

A default gateway is the next-hop router used when the routing table has no more specific matching route.

### Example

    Linux server IP: 192.168.1.10/24
    Default gateway: 192.168.1.1
    DNS server:      192.168.1.53

When the Linux server connects to a destination outside `192.168.1.0/24`, it typically sends the traffic to `192.168.1.1`.

### Inspect the routing table

    ip route

Example output:

    default via 192.168.1.1 dev eth0
    192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.10

Explanation:

- `default via 192.168.1.1` — default route through the gateway.
- `dev eth0` — outgoing network interface.
- `192.168.1.0/24` — directly connected network.
- `src 192.168.1.10` — preferred source IP for that route.

### Add a default route temporarily

    sudo ip route add default via 192.168.1.1 dev eth0

Replace `eth0` and `192.168.1.1` with the actual interface and gateway.

If a default route already exists, adding another may fail or create multiple routes with different metrics.

Inspect routes first:

    ip route

Remove an incorrect route only after confirming it is safe:

    sudo ip route del default via 192.168.1.1 dev eth0

Routes added using `ip route` are generally runtime configuration and may not survive reboot.

### How a router connects a private network to the internet

A typical home or cloud network has this structure:

    Linux Server
    192.168.1.10
          |
          v
    Default Gateway / Router
    192.168.1.1
          |
          v
    ISP or Cloud Edge
          |
          v
    Internet

For private IPv4 networks, the edge router commonly performs NAT so private addresses can communicate through a public IP address. NAT is not the same as routing; both routing and any required NAT/firewall configuration must be correct.

A server also needs a valid return route and permitted firewall rules. In AWS, internet access for a public subnet typically requires an Internet Gateway, a suitable route table, and a public IPv4 address or other supported public connectivity. Private subnets commonly use a NAT Gateway for outbound IPv4 internet access.

---

## 6. Linux Network Interfaces: `ip link` and `ip addr`

The `ip` command from the `iproute2` package manages network interfaces, IP addresses, and routes.

### List network interfaces

    ip link show

Displays interfaces and their link-layer status.

### List IP addresses

    ip addr show

Or use the shorter commands:

    ip a
    ip -br addr

### Bring an interface up or down

    sudo ip link set dev eth0 up
    sudo ip link set dev eth0 down

Bringing down the wrong interface can disconnect your SSH session.

### Assign an IP address temporarily

    sudo ip addr add 192.168.1.10/24 dev eth0

Verify:

    ip addr show dev eth0

Remove the address:

    sudo ip addr del 192.168.1.10/24 dev eth0

These commands modify runtime network configuration. They do not automatically make the settings persistent across reboots.

### Check the interface MAC address

    ip link show eth0

Look for the `link/ether` field.

### Find which route Linux will use

    ip route get 8.8.8.8

This helps troubleshoot the selected gateway, interface, and source IP.

---

## 7. How to Configure IP Addresses Persistently

Runtime configuration using `ip addr` may disappear after a reboot or network service restart.

Persistent configuration depends on the Linux distribution and the network manager.

### Check the network management service

    systemctl is-active NetworkManager
    nmcli device status
    nmcli connection show

CentOS Stream 10 commonly uses NetworkManager.

### Inspect connection configuration

    nmcli connection show

Display details of a connection:

    nmcli connection show "Wired connection 1"

The connection name varies by system.

### Example: Configure a static IPv4 address with NetworkManager

**Warning:** Run this on a test VM or through a console. Incorrect IP, gateway, or connection settings may disconnect a remote session.

Replace the connection name and network details with values suitable for your environment:

    sudo nmcli connection modify "Wired connection 1" \
      ipv4.method manual \
      ipv4.addresses 192.168.1.10/24 \
      ipv4.gateway 192.168.1.1 \
      ipv4.dns "192.168.1.53 1.1.1.1"

Apply the connection:

    sudo nmcli connection up "Wired connection 1"

Verify:

    ip addr
    ip route
    cat /etc/resolv.conf

Only use DNS servers and gateway addresses that are reachable and appropriate for your network. The sample IP addresses are illustrative.

---

## 8. IP Forwarding in Linux

IP forwarding allows a Linux machine to route packets between its network interfaces.

By default, many Linux systems do not forward IPv4 packets between interfaces.

### Check the current IPv4 forwarding value

    cat /proc/sys/net/ipv4/ip_forward

Typical output:

    0

Meaning:

- `0` — IPv4 forwarding is disabled.
- `1` — IPv4 forwarding is enabled.

### Enable forwarding temporarily

    sudo sysctl -w net.ipv4.ip_forward=1

Verify:

    cat /proc/sys/net/ipv4/ip_forward

To disable temporarily:

    sudo sysctl -w net.ipv4.ip_forward=0

### Enable forwarding persistently

The relevant sysctl configuration is normally under `/etc/sysctl.conf` or a file in `/etc/sysctl.d/`.

**It is not `/etc/systemctl.conf`.** `sysctl` manages kernel parameters; `systemctl` manages systemd services and units.

Create a dedicated configuration file:

    sudo vi /etc/sysctl.d/99-ip-forwarding.conf

Add:

    net.ipv4.ip_forward = 1

Apply the setting:

    sudo sysctl --system

Verify:

    sysctl net.ipv4.ip_forward

### When is IP forwarding required?

- Linux acting as a router.
- A Linux firewall routing traffic between networks.
- Some VPN gateway setups.
- Certain NAT gateway configurations.

### Important security note

Enabling IP forwarding alone does not create a fully functional router. You may also need:

- An IP address on each interface.
- Valid routing tables on the Linux router and its clients.
- Firewall rules that allow forwarding.
- NAT if private source addresses need translation to an external address.
- A return route from the destination network.

Use forwarding only when required and configure firewall policies carefully.

---

## 9. What Is DNS?

DNS stands for Domain Name System.

DNS translates domain names into DNS records. A common use is resolving a hostname to an IP address.

Example:

    www.example.com -> IP address

Without DNS, users would often need to remember numeric IP addresses instead of human-readable names.

### Common DNS roles

| Component | Role |
|---|---|
| Stub resolver | Client-side resolver used by an application or operating system |
| Recursive resolver | Resolves DNS queries on behalf of clients, using cache and other DNS servers |
| Root DNS server | Directs resolvers to the relevant top-level domain servers |
| TLD DNS server | Directs resolvers toward authoritative servers for a domain |
| Authoritative DNS server | Supplies the official DNS records for a domain |

A recursive resolver may be operated by an ISP, enterprise, cloud provider, or public DNS service.

---

## 10. Domain Names, TLDs, and Subdomains

Consider:

    api.dev.example.com

Breakdown:

- `.` — DNS root.
- `com` — top-level domain (TLD).
- `example.com` — registered domain in this example.
- `dev.example.com` — subdomain.
- `api.dev.example.com` — hostname under that subdomain.

### Domain hierarchy

    Root (.)
       |
       v
      com
       |
       v
    example.com
       |
       v
    dev.example.com
       |
       v
    api.dev.example.com

The labels are read from right to left when identifying the DNS hierarchy.

Examples of TLDs:

- `.com`
- `.org`
- `.net`
- `.in`
- `.edu`

A subdomain can have its own DNS records and may even be delegated to different authoritative DNS servers.

---

## 11. DNS Record Types

DNS stores different types of information, not just IP addresses.

| Record | Purpose | Example |
|---|---|---|
| A | Maps a name to an IPv4 address | `www.example.com -> 192.0.2.10` |
| AAAA | Maps a name to an IPv6 address | `www.example.com -> 2001:db8::10` |
| CNAME | Alias pointing to another DNS name | `shop.example.com -> store.example.net` |
| MX | Specifies mail servers for a domain | `example.com -> mail.example.com` |
| NS | Identifies authoritative name servers for a DNS zone | `example.com -> ns1.example.net` |
| TXT | Stores text data, often used for SPF, verification, and other policies | Domain verification |
| PTR | Used for reverse DNS lookups | IP address to hostname |
| SOA | Contains zone metadata, including the primary server and serial number | Zone configuration |
| SRV | Identifies a service endpoint, including port and priority information | Some messaging and directory services |

The IP addresses above are documentation examples, not real service endpoints.

### TTL — Time to Live

TTL tells DNS caches how long a record may be cached.

For example, a TTL of 300 seconds is five minutes. Actual cache behavior also depends on resolver implementation and other DNS policies.

---

## 12. Linux DNS Configuration Files

Three important locations are:

- `/etc/hosts`
- `/etc/resolv.conf`
- `/etc/nsswitch.conf`

They serve different purposes.

### A. `/etc/hosts`

This file maps hostnames to IP addresses locally.

Example:

    127.0.0.1       localhost
    192.168.1.20    app01.example.local app01

Append a new entry:

    echo "192.168.1.20 app01.example.local app01" | sudo tee -a /etc/hosts

Verify:

    getent hosts app01.example.local

**Important:** `/etc/hosts` is a local static mapping, not a DNS server. It can override DNS depending on the system's name-resolution order.

Avoid blindly appending duplicate or incorrect entries.

### B. `/etc/resolv.conf`

This file commonly contains DNS resolver settings.

Example:

    nameserver 192.168.1.53
    nameserver 1.1.1.1
    search example.local

- `nameserver` specifies a resolver IP address.
- `search` provides domain suffixes that may be appended to unqualified names, depending on the resolver configuration.

Check the current configuration:

    cat /etc/resolv.conf

Test a DNS query against a specific resolver:

    dig @192.168.1.53 app.example.local

On modern systems, `/etc/resolv.conf` may be generated or managed by NetworkManager, systemd-resolved, DHCP, or another network service. Direct edits may be overwritten. Configure DNS using the active network manager where appropriate.

### C. `/etc/nsswitch.conf`

This file controls how certain system libraries look up information, including hostnames.

Inspect the hosts entry:

    grep '^hosts:' /etc/nsswitch.conf

Example:

    hosts: files dns

This usually means:

1. Check local files such as `/etc/hosts`.
2. If there is no applicable result, query DNS.

The actual configuration may include `resolve`, `myhostname`, or other modules. Order and status rules can change the resolution behavior.

### Summary of the three files

| File | Purpose |
|---|---|
| `/etc/hosts` | Local hostname-to-IP mappings |
| `/etc/resolv.conf` | DNS resolver addresses and search settings |
| `/etc/nsswitch.conf` | Order and methods used for system name lookups |

---

## 13. Configure DNS on Linux

### Temporary resolver test

Use `dig` to query a DNS server directly:

    dig @1.1.1.1 example.com A

This tests a resolver directly and does not prove that the system's normal resolver configuration is correct.

### Configure DNS through NetworkManager

List connection names:

    nmcli connection show

Configure DNS for a connection:

    sudo nmcli connection modify "Wired connection 1" \
      ipv4.dns "192.168.1.53 1.1.1.1"

Bring the connection up:

    sudo nmcli connection up "Wired connection 1"

Verify:

    nmcli device show
    cat /etc/resolv.conf
    getent hosts example.com

The connection name and DNS server IPs must match your environment. Reconnecting can briefly interrupt network connectivity.

### Configure a local hostname

Add a local mapping:

    echo "192.168.1.20 app01.example.local" | sudo tee -a /etc/hosts

Resolve it:

    getent hosts app01.example.local

This is useful in labs, but it does not publish the hostname to other machines. To make a name available to an entire network, configure the relevant DNS zone or internal DNS service.

---

## 14. DNS Resolution: What Happens From Your System to the DNS Server and Back?

Suppose a user runs:

    curl https://www.example.com

The application needs an IP address for `www.example.com` before it can establish the network connection.

### High-level DNS resolution flow

    User/Application
          |
          v
    System name-resolution library
          |
          v
    Check applicable local sources
    such as /etc/hosts and NSS rules
          |
          v
    System resolver configuration
    /etc/resolv.conf or resolver service
          |
          v
    Recursive DNS Resolver
          |
          v
    Check DNS cache
          |
          +-----------------------------+
          |                             |
      Cache hit                     Cache miss
          |                             |
          |                             v
          |                        Root DNS server
          |                             |
          |                             v
          |                         TLD server
          |                             |
          |                             v
          |                     Authoritative DNS
          |                             |
          |                             v
          |                     DNS record response
          |                             |
          +--------------+--------------+
                         |
                         v
                  Recursive resolver
                         |
                         v
                  Client receives answer
                         |
                         v
                  Application connects
                  to the destination IP

### Step-by-step explanation

**Step 1: The application requests a hostname lookup**

A browser or command-line tool such as `curl` needs to resolve `www.example.com`.

**Step 2: The system checks its configured name-resolution sources**

Depending on the application and operating system, the system resolver may consult `/etc/hosts`, NSS modules, a local caching service, or other configured sources.

**Step 3: The configured resolver is contacted**

If no local source provides the answer, the system sends a DNS query through its configured resolver path. The resolver may be local or remote.

**Step 4: The recursive resolver checks its cache**

If a valid cached answer exists, the recursive resolver can return it without querying the DNS hierarchy again.

**Step 5: The resolver finds the authoritative answer**

If necessary, a recursive resolver typically queries the root, the relevant TLD, and then an authoritative DNS server. It follows referrals to find the answer. Some steps can be skipped when useful information is already cached.

**Step 6: The authoritative server returns DNS data**

For an A-record query, the authoritative server may return an IPv4 address. It can also return a CNAME, other records, or a negative response, depending on the zone.

**Step 7: The answer travels back to the client**

The recursive resolver returns the answer and may cache it according to DNS TTL and resolver policy.

**Step 8: The application connects to the IP address**

DNS resolution is complete, but the application still needs to establish its network connection. For HTTPS, it typically performs a TCP connection or another supported transport setup, negotiates TLS, and then sends the HTTP request.

### Important distinction

DNS resolves names to DNS data, including addresses. It does not itself deliver the web page, route the packets, or establish the HTTPS session.

### Does every DNS lookup contact root servers?

No. Cached information frequently avoids queries to the root and TLD servers. Local mappings and local caches may also answer a lookup without an external DNS query.

### Does DNS always use UDP?

No. Traditional DNS commonly uses UDP port 53, but TCP port 53 is also used, including for some large responses and zone transfers. DNS can also be transported over encrypted protocols such as DNS over TLS or DNS over HTTPS when configured.

---

## 15. `nslookup` — DNS Lookup Tool

`nslookup` queries DNS servers for information about domain names.

### Basic lookup

    nslookup example.com

### Query a specific DNS server

    nslookup example.com 1.1.1.1

### Query a record type

    nslookup -type=A example.com
    nslookup -type=AAAA example.com
    nslookup -type=MX example.com
    nslookup -type=NS example.com
    nslookup -type=TXT example.com

Output and available options may vary across implementations.

---

## 16. `dig` — Domain Information Groper

`dig` is a detailed DNS troubleshooting tool commonly used by system administrators and DevOps engineers.

### Basic lookup

    dig example.com

### Request only the answer section

    dig +noall +answer example.com

### Query specific record types

    dig example.com A
    dig example.com AAAA
    dig example.com MX
    dig example.com NS
    dig example.com TXT
    dig example.com SOA

### Query a specific resolver

    dig @1.1.1.1 example.com A

### Reverse DNS lookup

    dig -x 8.8.8.8

A reverse lookup requests PTR information for an IP address.

### Trace DNS delegation

    dig +trace example.com

This shows the DNS delegation path from the root toward the authoritative answer. Network restrictions or DNS policy may affect whether the trace completes.

### Useful output fields

A typical `dig` result may include:

- `status` — DNS response status.
- `QUESTION SECTION` — the requested name and record type.
- `ANSWER SECTION` — returned DNS records.
- `AUTHORITY SECTION` — authority or delegation information.
- `ADDITIONAL SECTION` — supplementary records, when present.
- `SERVER` — the resolver queried.
- `Query time` — time spent on the query.
- `TTL` — remaining cache lifetime in the displayed record.

### `nslookup` vs `dig`

| Feature | `nslookup` | `dig` |
|---|---|---|
| Basic DNS lookup | Yes | Yes |
| Query a specific resolver | Yes | Yes |
| Detailed DNS response | Limited | Extensive |
| Query multiple record types | Yes | Yes |
| Trace delegation | Limited or implementation-dependent | `dig +trace` |
| Common troubleshooting choice | Quick checks | Detailed investigation |

---

## 17. DNS Troubleshooting on Linux

If a domain is not resolving, follow a structured process.

### Step 1: Check the local hostname mappings

    grep -n "example.com" /etc/hosts

### Step 2: Check NSS configuration

    grep '^hosts:' /etc/nsswitch.conf

### Step 3: Check resolver configuration

    cat /etc/resolv.conf

### Step 4: Test system-level resolution

    getent hosts example.com

`getent` follows the system's configured name-service lookup behavior more closely than querying a DNS server directly.

### Step 5: Query DNS directly

    dig example.com A
    dig @1.1.1.1 example.com A

If a direct query works but `getent` fails, investigate local resolver configuration, NSS, or a local DNS service.

### Step 6: Check network reachability and routes

    ip addr
    ip route
    ip route get 1.1.1.1

### Step 7: Check DNS service reachability

    ping -c 4 1.1.1.1

A successful ping is not required for DNS to work because ICMP may be blocked. A failed ping does not by itself prove that DNS is unavailable.

### Common DNS errors

| Problem | Possible cause |
|---|---|
| `NXDOMAIN` | The queried name does not exist in the relevant DNS view |
| `SERVFAIL` | Resolver or authoritative processing failure |
| `REFUSED` | Server refuses the query |
| `connection timed out` | Network, firewall, resolver, or reachability problem |
| Wrong IP address | Stale cache, incorrect record, split DNS, or local override |
| Some domains work, others fail | Search configuration, filtering, delegation, or resolver issue |
| `getent` fails but direct `dig` works | NSS or system resolver configuration problem |

---

## 18. Practical Networking and DNS Lab

Perform this exercise on a Linux test VM.

### Part A: Inspect networking

    ip link
    ip addr
    ip route
    ip route get 8.8.8.8
    cat /etc/os-release

### Part B: Inspect DNS configuration

    cat /etc/hosts
    cat /etc/resolv.conf
    grep '^hosts:' /etc/nsswitch.conf

### Part C: Test DNS resolution

    getent hosts example.com
    nslookup example.com
    dig example.com A
    dig @1.1.1.1 example.com A
    dig +trace example.com

Some tools may need to be installed, and firewall or network policy may restrict direct DNS queries.

### Part D: Test local hostname resolution

Add a temporary lab entry:

    echo "192.168.1.20 app01.example.local" | sudo tee -a /etc/hosts

Test it:

    getent hosts app01.example.local

Remove the exact line after the exercise if it is no longer needed.

### Part E: Understand routing and forwarding

    ip route
    cat /proc/sys/net/ipv4/ip_forward
    sysctl net.ipv4.ip_forward

Do not enable forwarding or change gateway routes on a production system without understanding the network and firewall impact.

---

## 19. Quick Revision

| Concept | Main command or file |
|---|---|
| List network interfaces | `ip link` |
| Display IP addresses | `ip addr` |
| Display routes | `ip route` |
| Test selected route | `ip route get DESTINATION` |
| Add a temporary IP | `ip addr add ADDRESS/PREFIX dev INTERFACE` |
| Add a temporary route | `ip route add ...` |
| Check IPv4 forwarding | `cat /proc/sys/net/ipv4/ip_forward` |
| Configure persistent forwarding | `/etc/sysctl.conf` or `/etc/sysctl.d/*.conf` |
| Local hostname mapping | `/etc/hosts` |
| Resolver configuration | `/etc/resolv.conf` |
| System lookup order | `/etc/nsswitch.conf` |
| Quick DNS query | `nslookup` |
| Detailed DNS query | `dig` |
| Trace DNS delegation | `dig +trace` |
| System-level hostname lookup | `getent hosts` |
| Persistent NetworkManager settings | `nmcli` |

## 20. Key Takeaways for DevOps

1. A switch connects devices on a local network; a router forwards packets between networks.
2. A default gateway is the next-hop router used for destinations without a more specific route.
3. `ip link`, `ip addr`, and `ip route` inspect and modify runtime networking state.
4. `/proc/sys/net/ipv4/ip_forward` reports whether IPv4 forwarding is enabled.
5. Use `sysctl` configuration to persist kernel parameter settings.
6. `/etc/hosts`, `/etc/resolv.conf`, and `/etc/nsswitch.conf` have distinct responsibilities.
7. DNS maps names to DNS records; it does not route packets or deliver application responses.
8. `nslookup` is useful for quick checks, while `dig` provides detailed DNS information.
9. Use `getent hosts` to test system-level name resolution.
10. Always distinguish temporary configuration from persistent configuration.