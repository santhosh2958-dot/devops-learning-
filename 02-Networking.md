# 02 — Linux Networking for DevOps

## 1. What Is Networking?

Networking allows computers and servers to communicate with each other.

For example, when your Ubuntu server downloads a package, it communicates with a repository server over a network.

As a DevOps engineer, you need networking to troubleshoot:

- Websites that are not opening
- Servers that cannot communicate
- DNS resolution failures
- Port and connection problems
- SSH connection failures
- Firewall issues
- Docker container networking

### Important networking terms

| Term | Meaning | Example |
|---|---|---|
| IP address | Address of a device on a network | `192.168.1.10` |
| Public IP | Address used to reach a server over the internet | A cloud server's public IPv4 |
| Private IP | Address used inside a private network | `172.31.2.84` |
| Subnet | A range of IP addresses | `192.168.1.0/24` |
| Gateway | Router used to reach other networks | `192.168.1.1` |
| DNS | Converts domain names into IP addresses | `google.com` → IP address |
| Port | Identifies a network service | SSH uses port `22` by default |
| Protocol | Rules for network communication | TCP, UDP, HTTP, HTTPS |
| Firewall | Controls allowed and blocked traffic | Allows or blocks port `443` |
| Latency | Time taken for a response | Measured in milliseconds |
| Packet loss | Network packets that fail to reach their destination | 5% packet loss |

---

## 2. Understand Your Linux Terminal Prompt

Example:

```bash
root@ip-172-31-2-84:/etc/apt/apt.conf.d#
```

| Part | Meaning |
|---|---|
| `root` | Current user |
| `ip-172-31-2-84` | Server hostname |
| `/etc/apt/apt.conf.d` | Current directory |
| `#` | Usually indicates a root shell prompt |

A normal non-root user's prompt commonly ends with `$`.

**Important:** Do not type the prompt itself. Type only the command after `#` or `$`.

For example, type:

```bash
hostname -I
```

Possible output:

```text
172.31.2.84 172.17.0.1
```

These are IP addresses assigned to the server's network interfaces. `172.31.2.84` may be your server's private IP, while `172.17.0.1` is commonly the default Docker bridge address if Docker networking is configured.

---

## 3. Check Your IP Address

### 3.1 `ip a` — Show all network interfaces

```bash
ip a
```

`ip a` is a shortcut for:

```bash
ip address
```

It displays network interfaces, IP addresses, and interface states.

Example:

```text
2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP>
    inet 172.31.2.84/20
```

Understand the output:

- `ens5` — network interface name
- `UP` — interface is administratively enabled
- `inet` — IPv4 address information
- `172.31.2.84` — IPv4 address
- `/20` — prefix length that describes the subnet

### 3.2 `ip -br a` — Short, readable output

```bash
ip -br a
```

`-br` means brief.

This is useful when you want a quick summary instead of a long output.

### 3.3 `hostname -I` — Show assigned IP addresses

```bash
hostname -I
```

This prints IP addresses assigned to the host, separated by spaces.

### 3.4 `ip link` — Show interfaces and their status

```bash
ip link
```

Shortcut:

```bash
ip l
```

Use this when checking whether an interface is enabled or disabled.

### 3.5 `ifconfig` — Older networking command

```bash
ifconfig
```

`ifconfig` is a traditional command. It may not be installed by default on newer Ubuntu versions.

If you see:

```text
ifconfig: command not found
```

Install the package that provides it:

```bash
sudo apt update
sudo apt install net-tools
```

Modern alternative:

```bash
ip a
```

**Remember:** Learn `ip a` first because it is part of the modern `ip` networking tools.

---

## 4. Check the Default Gateway and Routing Table

A routing table tells Linux where to send network traffic.

### 4.1 `ip r` — Show routes

```bash
ip r
```

Full command:

```bash
ip route
```

Example:

```text
default via 172.31.0.1 dev ens5
172.31.0.0/20 dev ens5 proto kernel scope link
```

Meaning:

- `default` — route used when no more specific route matches
- `via 172.31.0.1` — next-hop gateway
- `dev ens5` — interface used to send traffic

### 4.2 Show only the default route

```bash
ip route show default
```

### 4.3 Find which route Linux would use for an IP

```bash
ip route get 8.8.8.8
```

Useful when troubleshooting which interface and gateway are being used.

**Troubleshooting tip:** If the default route is missing, the server might not be able to reach other networks. Do not add a route blindly on a production server; first confirm the intended network configuration.

---

## 5. Check DNS

DNS stands for Domain Name System. It translates names such as `google.com` into IP addresses.

### 5.1 `nslookup`

```bash
nslookup google.com
```

It shows the DNS server used for the lookup and the returned address records.

### 5.2 `dig`

```bash
dig google.com
```

Short output:

```bash
dig +short google.com
```

Install `dig` if it is missing:

```bash
sudo apt update
sudo apt install dnsutils
```

### 5.3 `getent hosts`

```bash
getent hosts google.com
```

This uses the system's configured name-resolution mechanisms.

### 5.4 Check the configured DNS servers

```bash
cat /etc/resolv.conf
```

This file may show DNS settings, but on Ubuntu it can be managed by system services. Do not edit it directly without understanding how DNS is configured on your system.

### 5.5 Test DNS resolution

```bash
nslookup google.com
```

If this fails, try:

```bash
getent hosts google.com
```

Then test connectivity using an IP address:

```bash
ping -c 4 8.8.8.8
```

Interpretation:

- IP connectivity works, but DNS lookups fail: investigate DNS configuration.
- Both fail: investigate routing, firewall rules, security groups, or general connectivity.
- Ping fails alone: this does not prove the network is down; ICMP may be blocked.

---

## 6. Test Network Connectivity

### 6.1 `ping` — Test reachability

```bash
ping google.com
```

Stop a continuous ping using:

```text
Ctrl + C
```

Send only four packets:

```bash
ping -c 4 google.com
```

Ping tests whether a destination responds to ICMP packets. Some servers block ping, so a failed ping does not always mean the service is unavailable.

### 6.2 Test connectivity to an IP address

```bash
ping -c 4 8.8.8.8
```

### 6.3 `curl` — Test HTTP or HTTPS

Check a website response:

```bash
curl -I https://example.com
```

Follow redirects and display the final response headers:

```bash
curl -IL https://example.com
```

Check only the HTTP status code:

```bash
curl -s -o /dev/null -w "%{http_code}\n" https://example.com
```

Common HTTP status codes:

| Code | Meaning |
|---|---|
| `200` | Request succeeded |
| `301` / `302` | Redirect |
| `400` | Bad request |
| `401` | Authentication required |
| `403` | Forbidden |
| `404` | Not found |
| `500` | Server-side error |
| `502` | Bad gateway |
| `503` | Service unavailable |
| `504` | Gateway timeout |

A successful `curl` response confirms that an HTTP request received a response; it does not necessarily mean every part of a website is working.

### 6.4 `wget` — Download or test a URL

```bash
wget https://example.com
```

To test without saving the downloaded file:

```bash
wget --spider https://example.com
```

### 6.5 `traceroute` — Investigate the network path

```bash
traceroute google.com
```

Install it if needed:

```bash
sudo apt update
sudo apt install traceroute
```

Alternative, if installed:

```bash
tracepath google.com
```

These commands help investigate the path to a destination. Some routers do not respond to traceroute probes, so `*` entries are not automatically proof of a failure.

---

## 7. Check Ports and Listening Services

A port identifies a service that applications can connect to.

Common ports:

| Port | Common use |
|---|---|
| `22` | SSH |
| `53` | DNS |
| `80` | HTTP |
| `443` | HTTPS |
| `3306` | MySQL |
| `5432` | PostgreSQL |
| `6379` | Redis |
| `8080` | Alternative HTTP / application port |
| `27017` | MongoDB |

Actual port usage depends on how the software is configured.

### 7.1 `ss -tuln` — Show listening TCP and UDP ports

```bash
ss -tuln
```

Options:

- `-t` — TCP sockets
- `-u` — UDP sockets
- `-l` — listening sockets
- `-n` — show numeric addresses and ports

### 7.2 Show listening ports with process information

```bash
sudo ss -tulpn
```

The `-p` option shows process information where permitted.

### 7.3 Search for a specific port

For port `80`:

```bash
sudo ss -tulpn | grep ':80'
```

For port `22`:

```bash
sudo ss -tulpn | grep ':22'
```

For port `8080`:

```bash
sudo ss -tulpn | grep ':8080'
```

If no result appears, nothing matching that filter was found in the displayed listening sockets. Confirm whether the application is running and which address and port it is configured to use.

### 7.4 Check whether a remote port is reachable

If Netcat (`nc`) is installed:

```bash
nc -vz example.com 443
```

Test SSH connectivity to a server:

```bash
nc -vz SERVER_IP 22
```

Replace `SERVER_IP` with the real destination address.

A successful connection means the TCP connection could be established. It does not prove that application authentication will succeed.

---

## 8. Understand Network Interfaces

A network interface connects a computer or virtual machine to a network.

List interfaces:

```bash
ip link
```

Show IP addresses and interface details:

```bash
ip a
```

Show a brief summary:

```bash
ip -br a
```

Typical interface names:

- `lo` — loopback interface
- `ens5` — a common interface name on cloud Linux servers
- `eth0` — a common Ethernet interface name
- `docker0` — Docker's default bridge interface, when configured

### Loopback address

The loopback IPv4 address is:

```text
127.0.0.1
```

Test it:

```bash
ping -c 4 127.0.0.1
```

Loopback traffic stays on the same machine. It does not test external network connectivity.

### Interface states

- `UP` — administratively enabled
- `DOWN` — administratively disabled
- `LOWER_UP` — the lower-level link reports that it is available

Do not change interface states on a remote production server without confirming the impact; you could lose your connection.

---

## 9. Useful Networking Commands — Quick Reference

| Command | Purpose |
|---|---|
| `ip a` | Show IP addresses and interfaces |
| `ip -br a` | Show a brief interface summary |
| `ip l` | Show network links |
| `ip r` | Show routing table |
| `ip route get 8.8.8.8` | Show the route to a destination |
| `hostname` | Show the hostname |
| `hostname -I` | Show assigned IP addresses |
| `ifconfig` | Legacy interface information |
| `ping -c 4 HOST` | Send four ICMP probes |
| `nslookup DOMAIN` | Query DNS |
| `dig +short DOMAIN` | Show concise DNS results |
| `getent hosts DOMAIN` | Resolve a hostname using system settings |
| `ss -tuln` | Show listening TCP/UDP sockets |
| `ss -tulpn` | Show listening sockets and process details where permitted |
| `curl -I URL` | Request HTTP response headers |
| `curl -v URL` | Show detailed connection information |
| `wget --spider URL` | Check a URL without downloading its body |
| `nc -vz HOST PORT` | Test a TCP connection |
| `traceroute HOST` | Investigate network path |
| `ip neigh` | Show the neighbour/ARP cache |
| `cat /etc/hosts` | Show local hostname mappings |
| `cat /etc/resolv.conf` | Inspect resolver configuration |
| `sudo ufw status` | Check Ubuntu UFW firewall status |

Replace `HOST`, `DOMAIN`, `URL`, and `PORT` with actual values. Commands such as `curl -v` can display request details, so avoid sharing output containing secrets or authentication headers.

---

## 10. Command-Line Symbols Used in Networking

Symbols let you combine commands and filter output.

### Pipe `|`

Passes the output of one command into another command.

```bash
ip a | grep inet
```

Meaning: display IP-address lines from the output of `ip a`.

```bash
ss -tuln | grep ':443'
```

Meaning: search listening-socket output for port `443`.

### Redirect `>`

Writes output to a file, replacing its existing contents.

```bash
ip a > network-info.txt
```

### Append `>>`

Adds output to the end of a file.

```bash
ip r >> network-info.txt
```

### Run commands in sequence `;`

```bash
hostname; ip a; ip r
```

The next command runs even if the previous command fails.

### Run the next command only if successful `&&`

```bash
nslookup google.com && curl -I https://google.com
```

The second command runs only if the first exits successfully.

### Run the next command if the first fails `||`

```bash
nslookup google.com || echo "DNS lookup failed"
```

### Search case-insensitively `grep -i`

```bash
ip a | grep -i inet
```

### Count matching lines `wc -l`

```bash
ss -tuln | wc -l
```

This counts displayed lines, including the header if one is present.

### Remember

- `|` = send output to another command
- `>` = write or overwrite a file
- `>>` = append to a file
- `&&` = run next command if successful
- `||` = run next command if unsuccessful
- `;` = run commands one after another

---

## 11. DNS Troubleshooting — Step by Step

Problem: A website name is not resolving.

### Step 1: Check DNS lookup

```bash
nslookup example.com
```

### Step 2: Try another resolver tool

```bash
getent hosts example.com
```

### Step 3: Inspect DNS settings

```bash
cat /etc/resolv.conf
```

### Step 4: Check routing

```bash
ip r
```

### Step 5: Test basic connectivity

```bash
ping -c 4 8.8.8.8
```

Remember, ICMP may be blocked.

### Step 6: Test the website

```bash
curl -I https://example.com
```

### How to interpret the results

| Result | Possible cause | Next step |
|---|---|---|
| DNS lookup fails | DNS server or resolver configuration issue | Inspect DNS configuration and reachability |
| DNS works, but website connection fails | Routing, firewall, remote service, or port issue | Check route and test the required TCP port |
| Website returns `403` | Access is forbidden by the server or an intermediary | Check permissions, authentication, and access rules |
| Website returns `404` | Requested resource not found | Check the URL and application routes |
| Website returns `502` or `504` | Proxy or upstream service issue | Check reverse proxy and backend logs |
| Ping fails, but curl works | ICMP may be blocked | Use the application's actual protocol for testing |

Do not immediately change DNS settings. First identify which step is failing.

---

## 12. Website Not Opening — Troubleshooting Workflow

Use this workflow when a website hosted on a Linux server does not open.

### Step 1: Check DNS resolution

```bash
nslookup your-domain.com
```

### Step 2: Check the server's IP and route

```bash
ip -br a
ip r
```

### Step 3: Test the HTTP/HTTPS response

```bash
curl -I http://your-domain.com
curl -I https://your-domain.com
```

Use the protocol configured for the site. HTTP and HTTPS are not interchangeable.

### Step 4: Check listening ports on the server

```bash
sudo ss -tulpn
```

Look for the relevant web-server or application port, usually `80`, `443`, or a configured application port.

### Step 5: Check the service

For Nginx:

```bash
sudo systemctl status nginx
```

For Apache on Ubuntu:

```bash
sudo systemctl status apache2
```

A service may not be installed, so a “unit not found” message can simply mean that the relevant service is absent.

### Step 6: Check the firewall

```bash
sudo ufw status
```

On a cloud server, also check the cloud firewall or security-group rules, network ACLs, and routing. Do not open all ports; allow only the required ports and source addresses.

---

## 13. SSH Troubleshooting

SSH is commonly used to connect to Linux servers remotely.

Connect to a server:

```bash
ssh USER@SERVER_IP
```

Example:

```bash
ssh ubuntu@192.168.1.10
```

Replace the username and address with your actual values.

### Common SSH problems

**Error: Connection timed out**

Possible causes:
- Server is unreachable.
- Port `22` is blocked.
- Security group or firewall rules do not permit access.
- Routing is incorrect.

Test the TCP port:

```bash
nc -vz SERVER_IP 22
```

**Error: Connection refused**

Possible causes:
- The server is reachable, but no service is accepting the connection on that port.
- SSH is stopped or listening on another port.
- A firewall is actively rejecting the connection.

On the server, if you have another way to access it:

```bash
sudo systemctl status ssh
sudo ss -tulpn | grep ':22'
```

**Error: Permission denied**

Possible causes:
- Wrong username.
- Incorrect SSH key or password.
- Incorrect permissions or server-side SSH configuration.

For a private key:

```bash
ssh -i ~/.ssh/my-key.pem USER@SERVER_IP
```

Replace the key path, username, and IP address with the correct values. Keep private keys secret.

---

## 14. Firewall Basics

A firewall controls which network connections are allowed or blocked.

Ubuntu commonly uses UFW as a simple firewall management tool.

### Check UFW status

```bash
sudo ufw status
```

Detailed status:

```bash
sudo ufw status verbose
```

### List rules with numbers

```bash
sudo ufw status numbered
```

### Important safety note

Do not enable, disable, or modify firewall rules on a remote server without confirming the impact. You could lock yourself out of SSH.

Before making a firewall change, confirm:
- Which ports the application needs
- Which source IPs should be allowed
- Whether a cloud security group or another firewall also controls access

Do not expose database ports publicly unless there is a specific, secured requirement.

---

## 15. Docker Networking Basics

Docker containers can have their own network interfaces and IP addresses.

### List Docker networks

```bash
docker network ls
```

### Inspect a network

```bash
docker network inspect bridge
```

### Show running containers

```bash
docker ps
```

### Show all containers, including stopped containers

```bash
docker ps -a
```

### Show published container ports

```bash
docker ps --format "table {{.Names}}\t{{.Ports}}"
```

### Check a container's logs

```bash
docker logs CONTAINER_NAME
```

Replace `CONTAINER_NAME` with the actual container name or ID.

### Test an application's published port

```bash
curl -I http://localhost:8080
```

This example assumes the application is published on host port `8080`.

Remember:
- `localhost` inside a container refers to that container itself.
- `localhost` on the host refers to the host machine.
- A container's private IP is not automatically reachable from every other machine.
- Published ports, routing, firewall rules, and application bind addresses all affect reachability.

If the `docker` command is missing, first confirm that Docker Engine is installed and running before troubleshooting its networks.

---

## 16. Common Networking Errors

| Error or symptom | What it usually means | What to check |
|---|---|---|
| `Temporary failure in name resolution` | Name resolution failed | DNS configuration, resolver reachability |
| `Could not resolve host` | Hostname could not be resolved | DNS lookup and hostname spelling |
| `Connection timed out` | No connection was established before timeout | Routing, firewall, security group, remote service |
| `Connection refused` | Connection was actively rejected | Service status, port, bind address, firewall |
| `No route to host` | Network path or reachability problem | Routes, interface state, network/firewall rules |
| `Network is unreachable` | No usable route for the destination | `ip a`, `ip r` |
| `Address already in use` | Another process may be using the port | `sudo ss -tulpn` |
| `403 Forbidden` | Server refuses the request | Permissions, authentication, access policy |
| `502 Bad Gateway` | Gateway/proxy received an invalid upstream response | Proxy and backend service |
| `504 Gateway Timeout` | Gateway/proxy did not receive an upstream response in time | Backend health, network path, timeout settings |

These errors indicate likely causes, not guaranteed diagnoses. Use the commands to narrow down the problem.

---

## 17. Daily Networking Health Check

Run these commands on your Ubuntu server:

```bash
# 1. Check hostname
hostname

# 2. Check IP addresses
ip -br a

# 3. Check routing table
ip r

# 4. Check DNS
getent hosts google.com

# 5. Test network reachability
ping -c 4 8.8.8.8

# 6. Test HTTPS
curl -I --max-time 10 https://example.com

# 7. Check listening ports
sudo ss -tulpn

# 8. Check firewall status
sudo ufw status
```

Some commands can fail because of network policy, firewall restrictions, or missing software. Read the output rather than assuming the entire server is broken.

---

## 18. Beginner Practice Exercises

Practice these commands on your own Ubuntu server.

- [ ] Run `ip a` and identify the active network interface.
- [ ] Run `ip -br a` and compare the output with `ip a`.
- [ ] Run `hostname -I` and identify the assigned IP addresses.
- [ ] Run `ip r` and identify the default gateway.
- [ ] Run `nslookup google.com`.
- [ ] Run `dig +short google.com`, if `dig` is installed.
- [ ] Run `getent hosts google.com`.
- [ ] Run `ping -c 4 8.8.8.8`.
- [ ] Run `curl -I https://example.com`.
- [ ] Run `ss -tuln` and identify the listening ports.
- [ ] Run `sudo ss -tulpn` and identify which processes own listening sockets, where shown.
- [ ] Run `ip a | grep inet` and explain the pipe symbol.
- [ ] Run `hostname; ip a; ip r` and explain the semicolons.
- [ ] Run `sudo ufw status` and understand the result.

### Mini challenge

Run the following commands and record your results:

```bash
hostname
hostname -I
ip -br a
ip r
getent hosts google.com
curl -I --max-time 10 https://example.com
ss -tuln
```

Create a short note containing:
1. Hostname
2. Private IP address
3. Default gateway
4. Whether DNS resolution works
5. Whether HTTPS works
6. Which ports are listening

Do not share passwords, private keys, tokens, or other secrets in your notes or screenshots.

---

## 19. Quick Revision — Memorize These First

| Need to… | Command |
|---|---|
| Find IP addresses | `ip a` |
| View IP addresses briefly | `ip -br a` |
| Find assigned IPs quickly | `hostname -I` |
| Find the default gateway | `ip r` |
| Check DNS | `nslookup google.com` |
| Get concise DNS results | `dig +short google.com` |
| Test basic reachability | `ping -c 4 8.8.8.8` |
| Test a website | `curl -I https://example.com` |
| Find listening ports | `ss -tuln` |
| Find the process using a listening port | `sudo ss -tulpn` |
| Test a remote TCP port | `nc -vz HOST PORT` |
| Check the firewall | `sudo ufw status` |
| Check local hostname mappings | `cat /etc/hosts` |
| Check resolver configuration | `cat /etc/resolv.conf` |

**Memory trick:**

- **IP** → What is my address?
- **Route** → Where does my traffic go?
- **DNS** → What IP belongs to this name?
- **Ping** → Does the destination respond to ICMP?
- **Curl** → Does the application respond?
- **SS** → Which sockets are listening?
- **NC** → Can I establish a TCP connection to this port?

---

## 20. Official Documentation

- Ubuntu networking: https://documentation.ubuntu.com/server/how-to/networking/
- Ubuntu firewall (UFW): https://documentation.ubuntu.com/server/how-to/security/firewalls/
- `ip` command manual: https://man7.org/linux/man-pages/man8/ip.8.html
- `ss` command manual: https://man7.org/linux/man-pages/man8/ss.8.html
- `curl` documentation: https://curl.se/docs/

---

**My learning order:** First understand `ip a`, `ip r`, DNS, `ping`, `curl`, and `ss`. Then practice troubleshooting DNS, SSH, web ports, firewalls, and Docker networking. These skills are useful for everyday Linux administration and DevOps work.
