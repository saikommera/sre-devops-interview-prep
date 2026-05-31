# Linux Performance & Debugging Cheatsheet

## CPU Performance
```bash
top -H -p <pid>        # threads for specific process
htop                   # interactive (install first)
mpstat -P ALL 1        # per-CPU utilization
perf top               # live kernel profiling
strace -p <pid>        # system calls
lsof -p <pid>          # open files/sockets

# Load average interpretation (1m, 5m, 15m)
# Rule: load avg / CPU count — > 1.0 means overloaded
nproc                  # number of CPUs
cat /proc/loadavg
```

## Memory
```bash
free -h                           # overview
vmstat -s | grep -i mem           # detailed
cat /proc/meminfo | head -20
ps aux --sort=-%mem | head -10    # top memory consumers

# OOM investigation
dmesg | grep -i "out of memory"
dmesg | grep -i "oom_kill"
journalctl -k | grep OOM
```

## Disk & I/O
```bash
df -h                  # disk usage
du -sh /var/log/*      # directory sizes
iotop -ao              # real-time I/O by process
iostat -xz 1           # I/O stats per device
lsblk                  # block devices
fdisk -l               # partition info

# Find large files
find / -xdev -size +100M -ls 2>/dev/null | sort -k7 -rn | head -20
```

## Network
```bash
ss -tlnp               # listening TCP ports + processes
ss -antp               # all TCP connections
netstat -tulpn         # alternative (older)
tcpdump -i eth0 port 80 -n
nmap -sS -p 80,443,8080 <host>
curl -w "\nTotal: %{time_total}s\n" -o /dev/null -s <url>

# Check connection states
ss -s
ss -ant | awk '{print $1}' | sort | uniq -c | sort -rn
```

## Process Debugging
```bash
pstree -p <pid>        # process tree
lsof -p <pid>          # files, sockets, pipes
cat /proc/<pid>/status # process status
cat /proc/<pid>/limits # resource limits
ls -la /proc/<pid>/fd  # file descriptors

# Zombie process
ps aux | grep Z        # find zombies
# Zombies can't be killed directly — kill parent process
```

## Kubernetes Node Debugging
```bash
# From the node
journalctl -u kubelet --since "1 hour ago"
systemctl status containerd
crictl ps -a           # container runtime
crictl logs <id>

# Network plugin
ls /etc/cni/net.d/
ip route show
iptables -L -n | grep <pod-ip>
```
