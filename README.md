[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/NENsd3bP)
[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=20399987&assignment_repo_type=AssignmentRepo)


### **Explanation of my Shell script**
---
Here is my updated script and what it does

```
#!/bin/env bash

logfile="/var/log/sysmon.log"

NET_IF=$(ip -o link show | awk -F': ' '!/lo/ {print $2; exit}')

while true; do
  echo "--- $(date) ---" >> $logfile

  echo "CPU:" >> $logfile
  mpstat 1 1 | awk '/Average:/ {print "User:", $3"%  System:", $5"%  Idle:", $12"%"}' >> $logfile

  echo "MEMORY:" >> $logfile
  free -h >> $logfile

  echo "DISK:" >> $logfile
  df -h / >> $logfile

  echo "NET ($NET_IF):" >> $logfile
  grep $NET_IF /proc/net/dev >> $logfile

  echo "" >> $logfile
  sleep 300   # 300 seconds = 5 minutes
done
```

- `logfile="/var/log/sysmon.log"`

  This variable **"logfile"** Sets the path of the log file where system monitoring data will be stored.

- `NET_IF=$(ip -o link show | awk -F': ' '!/lo/ {print $2; exit}')`

  Finds the first active network interface that isn’t lo (loopback)

   - ip -o link show (lists all network interfaces)
   - awk -F (extracts the interface name) e.g, enp0s3
Then ouput it to **"NET_IF"**

### CPU usage:
---

**mpstat** tool was installed on my VM

- `mpstat 1 1 | awk '/Average:/ {print "User:", $3"%  System:", $5"%  Idle:", $12"%"}' >> $logfile` 

  mpstat 1 1 collects CPU stats over 1 second.

  The awk command extracts user CPU%, system CPU%, and idle CPU%.


### Screenshot of Output result of my sysmon shell script
---
![](./week_1-DevOps_&_Platform%20Engineering/images/sys%20log.png)

**Memory Section :** (free -h) Showing how RAM is being used and how much is left

- total: Total physical RAM (1.9Gi).

- used: Currently used RAM (824Mi).

- free: Completely unused RAM (145Mi).

- shared: Memory used by tmpfs (shared between processes, here 9.0Mi).

- buff/cache: Memory used by kernel buffers and cache (997Mi).

- available: Actual memory available for applications (972Mi).

**Disk Section** (df -h /) Showing disk usage of the root filesystem

- Filesystem: /dev/sda3 (your root partition).

- Size: Total space (24G).

- Used: Currently used (14G).

- Avail: Free space available (9.3G).

- Use%: Percentage of disk used (60%).

- Mounted on: Mount point /.

**Network Section** (/proc/net/dev) This shows network throughput and errors for my network interface. enp0s3

- 141470684 → Bytes received

- 95634 → Packets received

- 0 → Receive errors

- 19 → Multicast packets received

- 505517 → Bytes transmitted

- 6768 → Packets transmitted


