# Linux Daily Commands for DevOps — Ubuntu / EC2

> **Goal:** A beginner-friendly, practical command reference for daily DevOps work. Based on the commands listed on my Ubuntu server in `/bin` and `/sbin`, plus useful Git and Docker subcommands.
>
> **How to learn:** Read the meaning, run the example, inspect the output, and repeat. Commands shown with example names, IPs, or paths may need adjustment.

## Table of contents

1. [Important Linux basics](#1-important-linux-basics)
2. [Files and directories](#2-files-and-directories)
3. [Reading and searching text](#3-reading-and-searching-text)
4. [Server health and processes](#4-server-health-and-processes)
5. [Networking and SSH](#5-networking-and-ssh)
6. [Services, logs and cron](#6-services-logs-and-cron)
7. [Users and permissions](#7-users-and-permissions)
8. [Packages, storage and backups](#8-packages-storage-and-backups)
9. [Git and GitHub](#9-git-and-github)
10. [Docker basics](#10-docker-basics)
11. [Troubleshooting scenarios](#11-troubleshooting-scenarios)
12. [Daily practice checklist](#12-daily-practice-checklist)
13. [Shortcuts and safety](#13-shortcuts-and-safety)

---

## 1. Important Linux basics

| Command | In simple words | Example |
|---|---|---|
| `pwd` | Where am I? | `pwd` |
| `whoami` | Which user am I? | `whoami` |
| `hostname` | What is this server called? | `hostname` |
| `hostnamectl` | Server OS and hostname details | `hostnamectl` |
| `uname -r` | Linux kernel version | `uname -r` |
| `cat /etc/os-release` | Ubuntu version | `cat /etc/os-release` |
| `date` | Current date and time | `date` |
| `history` | Commands typed earlier (shell built-in) | `history` |
| `man` | Open a command manual | `man ls` |
| `command -v` | Find a command's location (shell built-in) | `command -v git` |
| `clear` | Clear the terminal | `clear` |

**Example:** `hostnamectl` is like looking at the information label on your server.

## 2. Files and directories

| Command | Meaning | Example |
|---|---|---|
| `ls` | List files | `ls -lah` |
| `cd` | Change directory (shell built-in) | `cd /var/log` |
| `mkdir` | Create folder | `mkdir -p ~/devops/practice` |
| `touch` | Create empty file | `touch notes.txt` |
| `cat` | Read small file | `cat notes.txt` |
| `less` | Scroll through long file; press `q` to quit | `less /etc/services` |
| `cp` | Copy file | `cp notes.txt notes.bak` |
| `mv` | Move or rename | `mv notes.bak backup.txt` |
| `rm` | Delete file | `rm backup.txt` |
| `rmdir` | Delete empty directory | `rmdir empty-folder` |
| `find` | Search files | `find /var/log -name '*.log'` |
| `stat` | File metadata | `stat notes.txt` |
| `file` | Detect file type | `file notes.txt` |
| `ln -s` | Create symbolic link | `ln -s notes.txt shortcut.txt` |

**Safe practice:**

```bash
mkdir -p ~/devops/practice
cd ~/devops/practice
printf 'Hello DevOps\n' > notes.txt
cat notes.txt
cp notes.txt notes-backup.txt
ls -l
mv notes-backup.txt saved-notes.txt
find . -name '*.txt'
```

**Important:** `rm` does not send files to a recycle bin. Avoid `rm -rf` until you understand the path and consequences.

## 3. Reading and searching text

| Command | Meaning | Example |
|---|---|---|
| `head` | First lines | `head -n 5 /etc/passwd` |
| `tail` | Last lines | `tail -n 20 /var/log/syslog` |
| `tail -f` | Follow new log lines | `tail -f app.log` |
| `grep` | Find matching lines | `grep -i 'error' app.log` |
| `grep -n` | Show matching line numbers | `grep -n 'failed' app.log` |
| `wc -l` | Count lines | `wc -l app.log` |
| `sort` | Sort lines | `sort names.txt` |
| `uniq -c` | Count adjacent repeated lines | `sort names.txt \| uniq -c` |
| `cut` | Extract fields | `cut -d: -f1 /etc/passwd` |
| `awk` | Work with columns | `awk '{print $1}' app.log` |
| `sed` | Select/edit text streams | `sed -n '1,10p' app.log` |
| `tee` | Display and write output | `echo test \| tee output.txt` |
| `jq` | Filter JSON | `echo '{"status":"ok"}' \| jq -r '.status'` |

**Pipe (`|`) = send the output of one command into another.**

```bash
ps aux | grep '[s]shd'
```

**Redirection:** `>` overwrites a file; `>>` appends to it.

```bash
echo 'first' > demo.log
echo 'second' >> demo.log
cat demo.log
```

> Note: `/var/log/syslog` may not exist on every Ubuntu configuration; use `journalctl` if needed.

## 4. Server health and processes

| Command | What it checks | Example |
|---|---|---|
| `uptime` | Server uptime and load average | `uptime` |
| `free -h` | RAM and swap | `free -h` |
| `df -h` | Disk usage by filesystem | `df -h` |
| `du -sh` | Size of one directory | `du -sh /var/log` |
| `top` | Live process/resource view (`q` quits) | `top` |
| `htop` | Interactive process viewer | `htop` |
| `ps aux` | Process list | `ps aux` |
| `pgrep` | Find process IDs by name | `pgrep -a sshd` |
| `lscpu` | CPU details | `lscpu` |
| `nproc` | Number of processing units available | `nproc` |
| `vmstat` | CPU/memory/system activity | `vmstat 1 5` |
| `iostat` | Disk I/O | `iostat -xz 1 3` |
| `dmesg` | Kernel messages | `sudo dmesg -T | tail -n 20` |
| `kill` | Signal a process | `kill -TERM PID` |

**Server health mini-check:**

```bash
uptime
free -h
df -h
ps aux --sort=-%mem | head
```

**Remember:** Linux load average is **not** the same as CPU utilization percentage. Investigate before killing processes.

## 5. Networking and SSH

| Command | Meaning | Example |
|---|---|---|
| `ip a` | IP addresses and interfaces | `ip a` |
| `hostname -I` | Host IP addresses | `hostname -I` |
| `ip route` | Routes/default gateway | `ip route` |
| `ping` | ICMP connectivity test | `ping -c 4 8.8.8.8` |
| `nslookup` | DNS lookup | `nslookup example.com` |
| `dig` | Detailed DNS query | `dig example.com` |
| `curl -I` | Request HTTP headers | `curl -I https://example.com` |
| `wget` | Download a URL | `wget https://example.com/` |
| `ss -tuln` | Listening TCP/UDP sockets | `ss -tuln` |
| `ss -tulnp` | Include process information (permissions permitting) | `sudo ss -tulnp` |
| `nc -vz` | Check TCP connection to a port | `nc -vz example.com 443` |
| `ssh` | Remote shell | `ssh ubuntu@SERVER_IP` |
| `scp` | Copy file via SSH | `scp notes.txt ubuntu@SERVER_IP:/home/ubuntu/` |
| `mtr` | Inspect network path | `mtr example.com` |

**When a website fails:**

```bash
nslookup example.com
curl -I --max-time 10 https://example.com
ss -tuln
```

A failed `ping` does not always mean a server is down: ICMP can be blocked. On EC2, also check security groups, network ACLs and routing when appropriate.

## 6. Services, logs and cron

| Command | Meaning | Example |
|---|---|---|
| `systemctl status` | Service health | `systemctl status ssh` |
| `systemctl start` | Start a service | `sudo systemctl start ssh` |
| `systemctl stop` | Stop a service | `sudo systemctl stop ssh` |
| `systemctl restart` | Restart service | `sudo systemctl restart ssh` |
| `systemctl enable` | Start at boot | `sudo systemctl enable ssh` |
| `systemctl is-active` | Quick service state | `systemctl is-active ssh` |
| `journalctl -u` | Logs for a service | `journalctl -u ssh -n 50 --no-pager` |
| `journalctl -f` | Follow live journal | `journalctl -f` |
| `crontab -l` | List current user's cron jobs | `crontab -l` |
| `crontab -e` | Edit current user's cron jobs | `crontab -e` |
| `timedatectl` | Time/time zone status | `timedatectl` |

**Example: SSH problem**

```bash
systemctl status ssh --no-pager
journalctl -u ssh -n 30 --no-pager
```

**Cron example:** `*/5 * * * * /usr/bin/date >> /tmp/cron-date.log 2>&1` runs every five minutes. Use `crontab -e` to add it; remember the job runs as the user whose crontab you edited.

## 7. Users and permissions

| Command | Meaning | Example |
|---|---|---|
| `whoami` | Current user | `whoami` |
| `id` | User/group IDs | `id` |
| `who` | Logged-in users | `who` |
| `groups` | Group memberships | `groups` |
| `sudo` | Run with elevated privileges | `sudo systemctl status ssh` |
| `adduser` | Add a user (administrative) | `sudo adduser trainee` |
| `passwd` | Set/change password | `sudo passwd trainee` |
| `usermod -aG` | Add user to a group | `sudo usermod -aG sudo trainee` |
| `chmod` | File permissions | `chmod 600 secret.txt` |
| `chown` | File owner | `sudo chown ubuntu:ubuntu notes.txt` |
| `visudo` | Safely edit sudoers | `sudo visudo` |

**Permission memory trick:** read = `4`, write = `2`, execute = `1`.

- `644`: owner read/write; group and others read.
- `600`: only owner read/write.
- `755`: owner read/write/execute; others read/execute.

**Practice safely:**

```bash
cd ~/devops/practice
touch private.txt
chmod 600 private.txt
ls -l private.txt
```

## 8. Packages, storage and backups

| Command | Meaning | Example |
|---|---|---|
| `apt update` | Refresh package index | `sudo apt update` |
| `apt install` | Install package | `sudo apt install nginx` |
| `apt list --installed` | Installed packages | `apt list --installed` |
| `dpkg -l` | Package inventory | `dpkg -l` |
| `lsblk` | Block devices | `lsblk` |
| `blkid` | Filesystem UUID/type | `sudo blkid` |
| `findmnt` | Mounted filesystems | `findmnt` |
| `tar -czf` | Make compressed archive | `tar -czf project-backup.tar.gz project/` |
| `tar -tzf` | Inspect archive contents | `tar -tzf project-backup.tar.gz` |
| `rsync -av` | Copy/sync files | `rsync -av project/ backup/` |
| `sha256sum` | File checksum | `sha256sum project-backup.tar.gz` |

**Archive example:**

```bash
cd ~/devops
mkdir -p project
echo 'hello' > project/readme.txt
tar -czf project-backup.tar.gz project/
tar -tzf project-backup.tar.gz
```

**Danger zone:** `mkfs`, `wipefs`, `fdisk`, `lvremove`, `pvremove` and some other storage commands can destroy data. Learn their purpose but do not experiment on your EC2 root volume.

## 9. Git and GitHub

These are **Git subcommands**, not all separate programs in `/bin`.

| Command | Meaning | Example |
|---|---|---|
| `git init` | Create repository | `git init` |
| `git status` | See changed files | `git status` |
| `git add` | Stage changes | `git add README.md` |
| `git commit` | Save a version | `git commit -m 'Update notes'` |
| `git log --oneline` | Short history | `git log --oneline` |
| `git diff` | See unstaged changes | `git diff` |
| `git branch` | List branches | `git branch` |
| `git switch` | Change branch | `git switch main` |
| `git clone` | Copy remote repo | `git clone REPO_URL` |
| `git pull` | Fetch and integrate remote changes | `git pull` |
| `git push` | Upload commits to remote | `git push` |
| `git restore` | Restore file from Git | `git restore important.txt` |

**Your file recovery practice:**

```bash
# If a tracked file was deleted but deletion is NOT committed:
git restore important.txt

# If deletion was committed and the preceding commit had the file:
git restore --source=HEAD~1 -- important.txt
git add important.txt
git commit -m 'Recover important file'
```

`HEAD~1` means the commit immediately before the current commit. It does not exist when the repository has only its first commit. Git can generally recover previously committed content, but it is not a replacement for external backups.

## 10. Docker basics

Your command lists include `docker`, `dockerd`, `containerd` and `runc`. A Docker executable being present **does not guarantee** the Docker daemon is running.

| Command | Meaning | Example |
|---|---|---|
| `docker --version` | CLI version | `docker --version` |
| `docker info` | Daemon information | `docker info` |
| `docker ps` | Running containers | `docker ps` |
| `docker ps -a` | All containers | `docker ps -a` |
| `docker images` | Downloaded images | `docker images` |
| `docker pull` | Fetch image | `docker pull nginx` |
| `docker run` | Start container | `docker run -d --name web -p 8080:80 nginx` |
| `docker logs` | Container logs | `docker logs web` |
| `docker exec` | Command in container | `docker exec web ls /` |
| `docker stop` | Stop container | `docker stop web` |

**First check:**

```bash
docker --version
sudo systemctl status docker --no-pager
```

If the service does not exist or cannot connect, troubleshoot installation/daemon setup before attempting containers. Running the example web container exposes port 8080 on the host; consider EC2 security groups and whether you want it reachable.

## 11. Troubleshooting scenarios

### Scenario A: Server is slow

```bash
uptime
free -h
top
ps aux --sort=-%cpu | head
```

**Think:** Is CPU busy, memory low, or load high? Load averages are not percentages.

### Scenario B: Disk is filling up

```bash
df -h
du -sh /var/log
sudo du -h -d 1 /var/log | sort -h
```

**Think:** Which filesystem is full? Which directory is large? Do not delete unfamiliar logs or system files without checking retention and impact.

### Scenario C: Service is down

```bash
systemctl status ssh --no-pager
journalctl -u ssh -n 50 --no-pager
```

**Think:** What is the error? Is the service installed, running and configured correctly?

### Scenario D: Website does not load

```bash
nslookup example.com
curl -I --max-time 10 https://example.com
ss -tuln
```

**Think:** DNS, network, port, application, firewall, or EC2 security group?

### Scenario E: File was accidentally deleted

```bash
cd /path/to/your/git/repository
git status
git log --oneline
git restore important.txt
```

**Think:** Was the deletion committed? `git restore important.txt` is for a tracked file's uncommitted deletion. For committed deletion, locate a commit that still contains the file.

## 12. Daily practice checklist

Run the commands and mark each item complete in GitHub Markdown:

- [ ] `pwd && whoami && hostname`
- [ ] `ls -lah`
- [ ] `uptime && free -h`
- [ ] `df -h && lsblk`
- [ ] `ip a && ip route`
- [ ] `curl -I https://example.com`
- [ ] `ss -tuln`
- [ ] `systemctl status ssh --no-pager`
- [ ] `journalctl -u ssh -n 10 --no-pager`
- [ ] `ps aux | head`
- [ ] `git status` (inside a Git repository)
- [ ] Explain one command in your own words.

**20 commands to learn first:**

`pwd`, `ls`, `cd`, `mkdir`, `cp`, `mv`, `rm`, `cat`, `grep`, `find`, `tail`, `df`, `free`, `top`, `ps`, `ip`, `ss`, `systemctl`, `journalctl`, `git`.

## 13. Shortcuts and safety

| Shortcut | Meaning |
|---|---|
| `Tab` | Autocomplete command/path |
| `Ctrl+C` | Interrupt foreground command |
| `Ctrl+L` | Clear screen |
| `Ctrl+R` | Search shell history |
| `Ctrl+A` | Start of command line |
| `Ctrl+E` | End of command line |
| `↑` / `↓` | Browse command history |
| `!!` | Repeat previous shell command (Bash history expansion) |
| `~` | Home directory |
| `.` | Current directory |
| `..` | Parent directory |

**Important safety reminders:**

1. Prefer a normal user and `sudo` only when needed; do not routinely work as `root`.
2. Double-check paths before `rm`, `chown`, `chmod`, and service changes.
3. Do not paste secrets, tokens, private keys or passwords into GitHub.
4. `kill`, `systemctl stop`, storage tools and firewall commands can interrupt services.
5. Use a disposable practice directory for experiments.
6. Check whether optional tools are installed using `command -v TOOL_NAME`.

---

## Revision method

For every new command, answer these four questions:

1. **What does it do?** (one simple sentence)
2. **When would a DevOps engineer use it?** (real scenario)
3. **What example can I run safely?**
4. **How do I understand the output?**

**Keep practicing a few commands daily rather than trying to memorize hundreds at once.**
