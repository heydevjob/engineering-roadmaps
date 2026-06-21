# Linux Cheatsheet

The commands you reach for when a service won't start, a disk fills up, or a log holds the answer. Grouped by the task in front of you, beginner to advanced.

## Files & navigation

```bash
pwd                                               # print the current directory
ls -lah                                           # long listing, human sizes, including hidden files
cd -                                              # jump back to the previous directory
find . -name '*.log'                              # find files by name, recursively
find . -type f -size +100M                        # find files larger than 100MB
find /var/log -mtime -1                           # files modified in the last 24 hours
tree -L 2                                         # directory tree, two levels deep
du -sh *                                          # size of each item in the current dir
du -h --max-depth=1 / | sort -h                   # find which top-level dir is eating disk
stat file                                         # full metadata: size, perms, timestamps
```

## Permissions

```bash
ls -l file                                        # read the rwx bits and owner
chmod +x script.sh                                # make a file executable
chmod 644 file                                    # owner read/write, others read-only
chmod -R 750 dir                                  # recursive: owner rwx, group rx, others none
chown user:group file                             # change owner and group
chown -R user:group dir                           # recursive ownership change
sudo -u postgres command                          # run a command as another user
umask 022                                          # default perms for newly created files
```

## Processes & signals

```bash
ps aux                                            # every process with CPU/memory
ps aux | grep nginx                               # find a process by name
top                                               # live process view, sorted by CPU
htop                                              # nicer interactive process viewer
pgrep -a node                                     # PIDs and command lines matching a name
kill <pid>                                        # send SIGTERM (graceful stop)
kill -9 <pid>                                     # send SIGKILL (force, no cleanup)
pkill -f 'python app.py'                          # kill by matching the full command line
nice -n 10 command                                # start a process at lower priority
lsof -p <pid>                                     # files and sockets a process has open
lsof -i :8080                                     # which process is listening on a port
```

## systemd & services

```bash
systemctl status nginx                            # is it running, and the last log lines
systemctl start nginx                             # start a service now
systemctl stop nginx                              # stop it now
systemctl restart nginx                           # stop then start
systemctl reload nginx                            # reload config without dropping connections
systemctl enable nginx                            # start it automatically on boot
systemctl disable nginx                           # do not start on boot
systemctl is-active nginx                         # one-word state, scriptable
systemctl daemon-reload                           # reload unit files after editing them
systemctl list-units --failed                     # everything that failed to start
```

## Disk & memory

```bash
df -h                                             # free space per mounted filesystem
df -i                                             # inode usage (full inodes look like a full disk)
du -sh /var/log                                   # total size of a directory
free -h                                           # RAM and swap in human units
vmstat 1                                          # live memory/CPU/IO, one line per second
iostat -x 1                                       # per-device disk IO and utilization
ncdu /                                            # interactive disk usage explorer
lsblk                                             # block devices and mount points
dmesg | tail                                      # kernel ring buffer (OOM kills show here)
```

## Networking

```bash
ss -tlnp                                          # listening TCP sockets and owning process
ss -tnp                                           # active TCP connections
ip addr                                           # interfaces and their IP addresses
ip route                                          # the routing table
ping -c 4 host                                    # basic reachability test
curl -I https://example.com                       # headers only, check status and redirects
curl -v https://example.com                       # verbose: TLS handshake, request, response
curl -s -o /dev/null -w '%{http_code}\n' url      # print just the HTTP status code
dig example.com                                   # full DNS query and answer
dig +short example.com                            # just the resolved address
nc -zv host 5432                                  # test whether a TCP port is open
traceroute host                                   # path and where it breaks
```

## Logs & journalctl

```bash
journalctl -u nginx                               # all logs for one service
journalctl -u nginx -f                            # follow a service's logs live
journalctl -u nginx --since '10 min ago'          # logs in a time window
journalctl -p err -b                              # errors only, this boot
journalctl -xe                                    # recent logs with extra context (after a failure)
tail -f /var/log/syslog                           # follow a plain log file
tail -n 100 /var/log/nginx/error.log              # last 100 lines of a file
grep -i error /var/log/app.log                    # case-insensitive search in a log
```

## Text tools

```bash
grep -rn 'TODO' .                                 # recursive search with line numbers
grep -v '^#' config.conf                          # filter out comment lines
sed 's/old/new/g' file                            # replace text on stdout
sed -i 's/old/new/g' file                         # replace in place, edits the file
awk '{print $1}' access.log                       # print the first whitespace field
awk -F: '{print $1}' /etc/passwd                  # split on a custom delimiter
sort file | uniq -c | sort -rn                    # count and rank duplicate lines
cut -d, -f2 data.csv                              # pull one column from a CSV
wc -l file                                        # count lines
tr -d '\r' < file                                 # strip carriage returns (CRLF to LF)
```

## Users

```bash
whoami                                            # current user
id                                                # your UID, GID, and groups
sudo adduser deploy                               # create a user interactively
usermod -aG docker deploy                         # add a user to a group (e.g. docker)
passwd                                            # change your password
su - deploy                                       # switch to another user's login shell
groups deploy                                     # which groups a user belongs to
last                                              # recent login history
```

## Archives & transfer

```bash
tar -czf out.tar.gz dir/                          # create a gzipped archive of a directory
tar -xzf out.tar.gz                               # extract a gzipped archive
tar -tzf out.tar.gz                               # list contents without extracting
zip -r out.zip dir/                               # create a zip archive
unzip out.zip                                     # extract a zip
scp file user@host:/path/                         # copy a file to a remote host over SSH
scp -r dir user@host:/path/                       # copy a directory recursively
rsync -avz dir/ user@host:/path/                  # sync a directory, only transferring changes
rsync -avz --delete src/ dst/                     # mirror, deleting files removed from source
```

## Practice it live

Knowing `ss -tlnp` exists is not the same as using it to find the process squatting on a port, or running `du` down a tree to stop a disk from filling. On [HeyDevJob](https://heydevjob.com) these commands run in real cloud workspaces with genuinely broken systems - a crashed service that won't restart, a disk filling from a runaway worker, a log that names the failure - that you fix from your browser. Free to start, no card, no setup. [Practice on HeyDevJob →](https://heydevjob.com)
