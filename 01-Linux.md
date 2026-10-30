# 01 — Linux Commands: Complete DevOps Beginner Reference

## 1. What is Linux?

Linux is an operating system used to run servers, applications, websites, and cloud infrastructure.

DevOps engineers use Linux to:
- Manage files and directories.
- Install and configure software.
- Manage users and permissions.
- Start, stop, and troubleshoot services.
- Monitor CPU, memory, and disk usage.
- Connect to remote servers.
- Automate tasks using shell scripts.
- Run Docker containers and support CI/CD pipelines.

**Remember:** Linux commands are instructions you give to the operating system.

## 2. Understand the Linux command structure

General syntax:

```bash
command [options] [arguments]
```

Example:

```bash
ls -l /etc
```

Explanation:
- `ls` — command to list files.
- `-l` — option to show detailed information.
- `/etc` — argument specifying which directory to list.

## 3. Basic commands — start here

### 3.1 `pwd` — Print Working Directory

Shows the directory you are currently working in.

```bash
pwd
```

Example output:

```text
/root
```

DevOps use: Know your current location before creating, modifying, or deleting files.

Memory trick: **P**rint **W**here **D**irectory.

### 3.2 `ls` — List files and directories

Displays the contents of a directory.

```bash
ls
ls -l
ls -a
ls -lh
ls -lah
ls /etc
```

| Command | Explanation |
|---|---|
| `ls` | List visible entries |
| `ls -l` | Show detailed information |
| `ls -a` | Include hidden entries |
| `ls -lh` | Detailed listing with human-readable sizes |
| `ls -lah` | Detailed listing including hidden entries |
| `ls /etc` | List entries in `/etc` |

Linux hidden files usually start with a dot (`.`), such as `.bashrc`.

DevOps use: Inspect configuration directories, application files, and deployment folders.

### 3.3 `cd` — Change Directory

Moves you from one directory to another.

```bash
cd /etc
cd /var/log
cd ..
cd ~
cd -
```

| Command | Explanation |
|---|---|
| `cd /etc` | Go to `/etc` |
| `cd ..` | Go up one directory |
| `cd ~` | Go to your home directory |
| `cd -` | Return to the previous directory |

Memory trick: **C**hange **D**irectory.

### 3.4 `mkdir` — Make Directory

Creates a new directory.

```bash
mkdir practice
mkdir -p devops/linux/commands
```

- `mkdir practice` creates one directory.
- `mkdir -p devops/linux/commands` creates the full directory path if its parent directories do not exist.

### 3.5 `touch` — Create an empty file

```bash
touch notes.txt
touch practice.sh
```

Creates an empty file if it does not exist. If the file already exists, `touch` updates its timestamps without deleting its contents.

### 3.6 `cat` — Display file contents

```bash
cat notes.txt
```

Display a file's contents in the terminal.

You can also combine files:

```bash
cat file1.txt file2.txt
```

DevOps use: Quickly inspect configuration files and logs. For very long files, use `less` instead.

### 3.7 `less` — Read large files

```bash
less /var/log/syslog
```

Useful keys inside `less`:
- `Space` — next page.
- `b` — previous page.
- `/error` — search for the word `error`.
- `n` — next search match.
- `q` — quit.

DevOps use: Read large log files without printing the entire file to the terminal.

### 3.8 `head` and `tail`

Show the beginning or end of a file.

```bash
head notes.txt
head -n 20 notes.txt
tail notes.txt
tail -n 50 notes.txt
tail -f /var/log/syslog
```

| Command | Explanation |
|---|---|
| `head file.txt` | First 10 lines by default |
| `head -n 20 file.txt` | First 20 lines |
| `tail file.txt` | Last 10 lines by default |
| `tail -n 50 file.txt` | Last 50 lines |
| `tail -f file.txt` | Follow new lines as the file grows |

DevOps use: `tail -f` is particularly useful for watching logs while an application starts or a deployment runs.

Press `Ctrl+C` to stop following a log.

## 4. File and directory management

### 4.1 `cp` — Copy files and directories

```bash
cp notes.txt notes-backup.txt
cp notes.txt /tmp/
cp -r practice practice-backup
```

- `cp` copies files.
- `cp -r` copies directories and their contents recursively.

DevOps use: Copy configuration files before editing them.

### 4.2 `mv` — Move or rename

```bash
mv old.txt new.txt
mv notes.txt /tmp/
```

The first command renames a file. The second moves it.

### 4.3 `rm` — Remove files and directories

```bash
rm notes.txt
rmdir empty-directory
rm -r practice-backup
```

- `rm` removes a file.
- `rmdir` removes an empty directory.
- `rm -r` removes a directory and its contents recursively.

**Warning:** Deleted files may not be recoverable. Never practise destructive commands on important files or production servers. Avoid `rm -rf` until you fully understand the paths and consequences.

### 4.4 `find` — Find files and directories

```bash
find /tmp -name "*.log"
find /etc -name "*.conf"
find . -type f
find . -type d
```

| Option | Meaning |
|---|---|
| `-name` | Match a filename pattern |
| `-type f` | Find regular files |
| `-type d` | Find directories |
| `.` | Search from the current directory |

DevOps use: Find configuration files, scripts, and logs across directory structures.

### 4.5 `file` — Identify file type

```bash
file notes.txt
file /bin/ls
```

Reports the type of a file based on its contents and format.

### 4.6 `stat` — Inspect file metadata

```bash
stat notes.txt
```

Shows details such as file size, permissions, ownership, and timestamps.

## 5. Searching and text processing

### 5.1 `grep` — Search text

```bash
grep "error" notes.txt
grep -i "error" notes.txt
grep -n "error" notes.txt
grep -r "TODO" .
```

| Command | Explanation |
|---|---|
| `grep "error" file.txt` | Search for matching text |
| `grep -i "error" file.txt` | Ignore uppercase/lowercase differences |
| `grep -n "error" file.txt` | Show matching line numbers |
| `grep -r "TODO" .` | Search recursively from the current directory |

DevOps use: Find errors in application logs and search configuration files.

### 5.2 `wc` — Count lines, words, and bytes

```bash
wc notes.txt
wc -l notes.txt
wc -w notes.txt
```

- `wc -l` counts lines.
- `wc -w` counts words.
- `wc -c` counts bytes.

### 5.3 `sort` and `uniq`

```bash
sort names.txt
sort names.txt | uniq
sort names.txt | uniq -c
```

`sort` sorts lines. `uniq` removes adjacent duplicate lines, so sorting first helps group duplicates.

DevOps use: Summarize repeated entries in logs or lists.

### 5.4 `cut` — Extract fields from text

```bash
cut -d: -f1 /etc/passwd
```

- `-d:` specifies a colon as the delimiter.
- `-f1` selects the first field.

This example displays usernames from `/etc/passwd`.

### 5.5 `awk` — Process structured text

```bash
awk '{print $1}' notes.txt
```

Prints the first whitespace-separated field from each line.

DevOps use: Extract columns from command output and log files.

## 6. Linux permissions and ownership

Linux controls who can read, write, or execute files.

### 6.1 `chmod` — Change permissions

```bash
chmod 644 notes.txt
chmod 755 practice.sh
```

Common permission values:

| Number | Permission |
|---|---|
| `7` | Read + write + execute |
| `6` | Read + write |
| `5` | Read + execute |
| `4` | Read only |
| `0` | No permissions |

For `chmod 644 notes.txt`:
- Owner: read and write.
- Group: read only.
- Others: read only.

For `chmod 755 practice.sh`:
- Owner: read, write, execute.
- Group: read and execute.
- Others: read and execute.

DevOps use: Make deployment scripts executable and set appropriate configuration-file permissions.

### 6.2 `chown` — Change ownership

```bash
chown username notes.txt
chown username:groupname notes.txt
```

Changes the file owner or owner and group. Replace the example names with actual users and groups.

Changing ownership may require administrator privileges.

### 6.3 `umask` — Default permission mask

```bash
umask
```

Displays the current mask that influences default permissions for newly created files and directories.

### 6.4 `sudo` — Run a command with elevated privileges

```bash
sudo apt-get update
sudo systemctl status ssh
```

Runs a command with privileges granted by the system's sudo configuration.

**Important:** Elevated privileges can change the entire server. Use them only when needed.

## 7. User and group management

### 7.1 `whoami` — Current username

```bash
whoami
```

Displays the effective username.

### 7.2 `id` — User and group IDs

```bash
id
id root
```

Displays user ID (UID), group ID (GID), and supplementary group membership.

### 7.3 `who` and `w`

```bash
who
w
```

Show logged-in users and session information. `w` also shows activity and load information.

### 7.4 `groups` — Group membership

```bash
groups
groups username
```

Shows the groups a user belongs to.

### 7.5 `useradd` and `usermod`

```bash
sudo useradd -m devuser
sudo usermod -aG sudo devuser
```

- `useradd -m` creates a user and a home directory.
- `usermod -aG` adds the user to an additional group.

**Caution:** Adding someone to the `sudo` group grants significant privileges. Practise only on a server you control.

## 8. Processes and system monitoring

### 8.1 `ps` — View processes

```bash
ps
ps aux
ps aux | grep nginx
```

Shows running processes and details such as their PID (process ID), user, CPU, and memory usage.

### 8.2 `top` — Live process monitoring

```bash
top
```

Shows changing CPU usage, memory usage, system load, and process information.

Press `q` to quit.

### 8.3 `kill` — Send a signal to a process

```bash
kill PID
```

Replace `PID` with the actual process ID. The default signal asks the process to terminate gracefully.

Avoid killing arbitrary processes, especially on production servers.

### 8.4 `free` — Check memory

```bash
free -h
```

Displays memory and swap usage. The `-h` option uses readable units such as MiB and GiB.

### 8.5 `df` — Check filesystem disk space

```bash
df -h
df -h /
```

Shows available and used disk space for mounted filesystems.

DevOps use: Troubleshoot applications that fail because the disk is full.

### 8.6 `du` — Check directory size

```bash
du -sh /var/log
du -sh *
```

- `-s` gives a summary.
- `-h` uses human-readable units.

Unlike `df`, which reports filesystem capacity, `du` estimates space used by files and directories.

### 8.7 `uptime` — Check system uptime and load

```bash
uptime
```

Shows how long the system has been running, the number of users, and system load averages.

### 8.8 `uname` — Kernel information

```bash
uname -a
uname -r
```

`uname -r` displays the running kernel release.

### 8.9 `hostnamectl` — Hostname and OS information

```bash
hostnamectl
```

Displays the hostname and system-related information on systems using systemd.

## 9. Services and logs

A service is a background program that provides functionality, such as SSH or Docker.

### 9.1 `systemctl` — Manage services

```bash
systemctl status docker
sudo systemctl start docker
sudo systemctl stop docker
sudo systemctl restart docker
sudo systemctl enable docker
sudo systemctl enable --now docker
```

| Command | Explanation |
|---|---|
| `status` | Inspect service state |
| `start` | Start a service now |
| `stop` | Stop a service |
| `restart` | Restart a service |
| `enable` | Configure it to start automatically at boot |
| `enable --now` | Enable it at boot and start it now |

Replace `docker` with the actual service name when needed.

**Remember:** `start` and `enable` are different. One starts a service now; the other configures startup at boot.

### 9.2 `journalctl` — Read system logs

```bash
journalctl
journalctl -u docker
journalctl -u docker --since today
journalctl -u docker -n 50
journalctl -u docker -f
```

| Command | Explanation |
|---|---|
| `journalctl` | Read the system journal |
| `-u docker` | Show logs for the Docker service |
| `--since today` | Show today's logs |
| `-n 50` | Show the latest 50 entries |
| `-f` | Follow new log entries |

DevOps use: Find out why a service failed to start.

### 9.3 `dmesg` — Kernel messages

```bash
dmesg
dmesg -T
```

Displays kernel ring-buffer messages, including hardware and driver-related events. `-T` attempts to display readable timestamps.

Access to some messages may be restricted.

## 10. Package management on Ubuntu

Ubuntu uses APT to manage software packages.

### 10.1 `apt-get update`

```bash
sudo apt-get update
```

Refreshes the local package index from configured repositories. It does not upgrade all installed packages.

### 10.2 `apt-get upgrade`

```bash
sudo apt-get upgrade
```

Upgrades installed packages when updates are available.

Review proposed changes before accepting them, especially on important servers.

### 10.3 `apt-get install`

```bash
sudo apt-get install git
sudo apt-get install docker.io
```

Installs packages available from configured repositories.

### 10.4 `apt-get remove`

```bash
sudo apt-get remove package-name
```

Removes an installed package. Replace `package-name` with the actual package name.

### 10.5 `apt-cache policy`

```bash
apt-cache policy git
apt-cache policy docker.io
```

Shows package versions and their repository availability.

### 10.6 `dpkg -l`

```bash
dpkg -l
dpkg -l | grep docker
```

Lists installed Debian packages and can help verify whether a package is installed.

## 11. Networking commands

### 11.1 `ip addr` — View IP addresses

```bash
ip addr
ip -br addr
```

Shows network interfaces and assigned IP addresses.

### 11.2 `ip route` — View routing table

```bash
ip route
```

Shows how network traffic is routed, including the default route.

### 11.3 `ping` — Test network reachability

```bash
ping -c 4 8.8.8.8
ping -c 4 google.com
```

Sends test packets and reports replies when available.

If ping fails, it does not always mean the server is down; firewalls may block ICMP traffic.

### 11.4 `ss` — Inspect sockets and listening ports

```bash
ss -tuln
ss -tulnp
```

- `-t` — TCP sockets.
- `-u` — UDP sockets.
- `-l` — Listening sockets.
- `-n` — Numeric addresses and ports.
- `-p` — Show process information when permitted.

DevOps use: Check whether a service is listening on the expected port.

### 11.5 `curl` — Test URLs and APIs

```bash
curl -I https://example.com
curl -sS https://example.com
```

- `-I` requests response headers.
- `-sS` suppresses normal progress output while still showing errors.

Useful for checking website responses and troubleshooting HTTP APIs.

### 11.6 `wget` — Download files

```bash
wget https://example.com/
```

Downloads a URL to a file when the server provides downloadable content. Check the URL and downloaded file before using it.

### 11.7 `hostname`

```bash
hostname
hostname -I
```

Shows the system hostname. `hostname -I` displays assigned IP addresses, where supported.

## 12. SSH and remote servers

### 12.1 `ssh` — Connect to a remote server

```bash
ssh username@SERVER_IP
```

Replace `username` and `SERVER_IP` with your actual remote account and server address.

SSH lets you securely administer remote Linux servers.

### 12.2 SSH using a private key

```bash
ssh -i /path/to/key.pem ubuntu@SERVER_IP
```

Use the correct private-key path and account for your EC2 instance.

Keep private keys secret. Never commit them to GitHub.

### 12.3 `scp` — Copy files between computers

Upload a file to a remote server:

```bash
scp notes.txt username@SERVER_IP:/tmp/
```

Download a remote file:

```bash
scp username@SERVER_IP:/tmp/notes.txt .
```

The `.` means the current local directory.

### 12.4 `rsync` — Synchronize files and directories

```bash
rsync -av ./website/ username@SERVER_IP:/tmp/website/
```

Synchronizes the contents of the local `website` directory to the destination. A trailing slash affects how source directories are copied.

Use `rsync --dry-run` to preview changes before synchronizing important data.

## 13. Archives and compression

### 13.1 `tar` — Create an archive

```bash
tar -czf backup.tar.gz practice/
```

Creates a compressed archive of the `practice` directory.

- `-c` — Create an archive.
- `-z` — Use gzip compression.
- `-f` — Specify the archive filename.

### 13.2 Extract an archive

```bash
tar -xzf backup.tar.gz
```

Extracts the archive into the current directory.

Inspect the archive contents before extracting untrusted files.

## 14. Environment variables

Environment variables provide configuration to processes and scripts.

### 14.1 `env`

```bash
env
```

Displays environment variables available to the current process.

### 14.2 `echo`

```bash
echo "Hello DevOps"
echo "$HOME"
```

Prints text or expands an environment variable.

### 14.3 `export`

```bash
export APP_ENV=development
echo "$APP_ENV"
```

Exports a variable to child processes launched from the current shell.

This example lasts only for the current shell session unless you configure it to persist.

Never print or expose secret environment variables in shared logs.

## 15. Redirection and pipes

These features help combine commands and automate tasks.

### 15.1 `>` — Write output to a file

```bash
echo "Hello DevOps" > notes.txt
```

Creates the file or replaces its existing contents.

### 15.2 `>>` — Append output

```bash
echo "Second line" >> notes.txt
```

Adds output to the end of the file.

### 15.3 `|` — Pipe output to another command

```bash
ps aux | grep nginx
```

Sends the output of `ps aux` to `grep` for filtering.

### 15.4 `2>` — Redirect error output

```bash
ls /directory-that-does-not-exist 2> errors.txt
```

Saves standard error in `errors.txt`.

### 15.5 `tee` — Display and save output

```bash
echo "Practice completed" | tee result.txt
```

Displays the text and writes it to `result.txt`.

Use `tee -a` to append instead of replacing the file.

## 16. Shell and command-line helpers

### 16.1 `history`

```bash
history
```

Displays commands entered in the current shell's history.

### 16.2 `clear`

```bash
clear
```

Clears the terminal display. It does not delete files or erase command history.

### 16.3 `man`

```bash
man ls
man grep
man systemctl
```

Opens the manual page for a command.

Press `q` to exit.

### 16.4 `which` and `command -v`

```bash
which git
command -v docker
```

Help identify where a command is found or whether it is available in the shell's command lookup.

### 16.5 `date`

```bash
date
date -u
```

Shows the local date and time or UTC time.

## 17. Useful Linux directories

| Directory | Purpose |
|---|---|
| `/` | Root of the filesystem |
| `/home` | Home directories for ordinary users |
| `/root` | Root user's home directory |
| `/etc` | System and application configuration |
| `/var` | Variable data, including logs |
| `/var/log` | Common location for log files |
| `/tmp` | Temporary files |
| `/usr/bin` | Many user commands |
| `/usr/sbin` | Many system administration commands |
| `/opt` | Optional or third-party application software |
| `/dev` | Device files |
| `/proc` | Virtual filesystem exposing process and kernel information |
| `/sys` | Virtual filesystem exposing kernel and device information |

## 18. Daily Linux troubleshooting workflow

When an application is not working, use this sequence as a starting point.

### Step 1: Identify the server

```bash
hostname
uptime
```

### Step 2: Check disk and memory

```bash
df -h
free -h
```

### Step 3: Check whether the process exists

```bash
ps aux
```

### Step 4: Check service status

```bash
systemctl status SERVICE_NAME
```

Replace `SERVICE_NAME` with the real service name, such as `docker` or `ssh`.

### Step 5: Check service logs

```bash
journalctl -u SERVICE_NAME -n 50
```

### Step 6: Check listening ports

```bash
ss -tuln
```

### Step 7: Test the application endpoint

```bash
curl -I https://example.com
```

These checks help narrow down the cause. They do not guarantee that every problem will be found.

## 19. Safe beginner practice exercise

Run the following commands one at a time on your Ubuntu server.

### Exercise 1: Identify your environment

```bash
whoami
pwd
hostname
uname -r
```

### Exercise 2: Create a practice directory

```bash
mkdir -p ~/devops-practice
cd ~/devops-practice
pwd
```

### Exercise 3: Create and read a file

```bash
touch notes.txt
echo "I am learning Linux for DevOps" > notes.txt
cat notes.txt
```

Expected output:

```text
I am learning Linux for DevOps
```

### Exercise 4: Copy and rename a file

```bash
cp notes.txt notes-backup.txt
mv notes-backup.txt revision.txt
ls -l
```

You should see `notes.txt` and `revision.txt`.

### Exercise 5: Search file contents

```bash
grep "Linux" notes.txt
wc -l notes.txt
```

Expected search output:

```text
I am learning Linux for DevOps
```

The line count should be `1`.

### Exercise 6: Inspect permissions

```bash
ls -l notes.txt
chmod 600 notes.txt
ls -l notes.txt
```

The permissions should change to `-rw-------` for a regular file.

### Exercise 7: Check system health

```bash
df -h
free -h
uptime
```

Review the output to learn how disk space, memory, and system load are displayed.

### Exercise 8: Check Docker

```bash
docker --version
systemctl status docker
```

If Docker is not installed, the first command may report that it cannot find the command. If the service is missing, it has not been installed or configured as a service. Do not worry; we will troubleshoot installation separately.

## 20. Quick revision table

| What do you want to do? | Command |
|---|---|
| Current directory | `pwd` |
| List files | `ls -lah` |
| Change directory | `cd` |
| Create a directory | `mkdir` |
| Create an empty file | `touch` |
| Read a file | `cat` |
| Read a large file | `less` |
| Follow a log | `tail -f` |
| Copy | `cp` |
| Move or rename | `mv` |
| Delete a file | `rm` |
| Find a file | `find` |
| Search text | `grep` |
| Change permissions | `chmod` |
| Change ownership | `chown` |
| Current user | `whoami` |
| List processes | `ps aux` |
| Monitor processes | `top` |
| Check disk space | `df -h` |
| Check memory | `free -h` |
| Check a service | `systemctl status` |
| Read service logs | `journalctl -u` |
| Refresh APT index | `sudo apt-get update` |
| Show IP addresses | `ip addr` |
| Show listening ports | `ss -tuln` |
| Test a URL | `curl -I` |
| Connect remotely | `ssh` |
| Copy remotely | `scp` |
| Read documentation | `man` |

## 21. Important rules to remember

1. Linux commands are case-sensitive: `File.txt` and `file.txt` are different names.
2. Use `pwd` and `ls` to check your location and files before changing anything.
3. Learn command options with `man COMMAND` or `COMMAND --help`.
4. Do not run commands copied from the internet unless you understand what they do.
5. Be especially careful with `rm`, `chmod`, `chown`, `sudo`, and commands that modify services.
6. Keep passwords, SSH private keys, cloud credentials, and other secrets out of GitHub.
7. Practise first in a test environment, not on production servers.

## 22. What to learn next

After becoming comfortable with these commands, continue with:

1. Linux filesystem and directory structure.
2. File permissions, ownership, and users.
3. Processes, services, and log troubleshooting.
4. Networking and SSH.
5. Bash shell scripting.
6. Git and GitHub.
7. Docker and containers.
8. AWS and cloud infrastructure.
9. CI/CD, Terraform, and Kubernetes.

**My advice:** Do not try to memorise every command in one day. Practise five to ten commands daily, understand what each one does, and return to this file whenever you need a reminder.

