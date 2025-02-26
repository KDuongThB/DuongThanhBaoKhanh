# Problem 3: Diagnose Me Doctor

## Task

Provide steps to troubleshoot this production issue.

### Scenario

You are working as a DevOps Engineer on a cloud-based infrastructure where a virtual machine (VM), running Ubuntu 24.04, with 64GB of storage is under your management. Recently, your monitoring tools have reported that the VM is consistently running at 99% memory usage. This VM is responsible for only running one service - a NGINX load balancer as a traffic router for upstream services.

### Challenge

Describe how you would troubleshoot the issue, and what causes/scenario do you expect to encounter, as well as what are the impacts and recovery steps for each possible root cause.

---

## Answer

### Diagnostic steps

- check ```top``` or ```htop``` to see what's eating up all the RAM
- or run

  ```bash
  ps aux --sort=-%mem | head -10
  ```
  
  to see top 10 processes that are using the most RAM.

### Possible Causes of this Scenario and their impacts

- nginx worker memory usage problem
- some OpenSSL memory leak issues
- might be xdg-desktop-portal-gnome memory leak (if the VM is running Ubuntu Desktop)
- Disk Caching borrows a little too much of that tasty RAM

Impacts may include:

- Performance issues: slowness or inability to continue working
- System unresponsiveness
- Processes crashing
- which may lead to downtime

### Some Recovery Actions to take

- identify and kill or restart processes that use too much memory
- Maybe wait a while and monitor it to see if anything changes
- System restart
- update programs to get patches and fixes
- Memory and CPU usage limit per process
- if it actually needs more RAM (poor thing didn't have much from the start), maybe allocate more RAM to VM
