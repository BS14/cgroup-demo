# cgroup-demo
Demonstrating memory limits in cgroup. 

# Creating a new cgroup
Creating a cgroup is easy, just create a directory. In this case we’ll create a group called “demo”. Once created, the kernel fills the directory with files that can be used to configure the cgroup.
```
$ mkdir /sys/fs/cgroup/demo
$ ls /sys/fs/cgroup/demo
```

# Adjusting values to limit the memory usage to 50 MB
To adjust a value we just have to write to the corresponding file. Let’s limit the cgroup to 100MB of memory and turn off swap.
```
$ echo "50000000" > /sys/fs/cgroup/demo/memory.max
$ echo "0" > /sys/fs/cgroup/demo/memory.swap.max
```

# Assign current PID to the cgroup
The tasks file is special, it contains the list of processes which are assigned to the cgroup. To join the cgroup we can write our own PID.
```
$ echo $$ > /sys/fs/cgroup/demo/cgroup.procs
```

# Running a memory hungry application
```
import time
f = open("/dev/urandom", "rb")
data = b""

i = 0
while True:
    chunk = f.read(10000000)  # 10MB
    if not chunk:
        break
    data += chunk
    i += 1
    print(f"{i * 10}MB")
    time.sleep(5)

```
Run the above python script. After the memory limit has been reached, the process gets killed. You can verify this using the kernel.log present in /var/log/kern.log.

# Verifying OOM kill 

```
$ grep oom /var/log/kern.log
```
