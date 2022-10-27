# DevOps info
DevOps-related info

---

## OS
- The Kernel loads first and manages the HW of the pc, using device drivers.
- File system management: Mac and linux has one root dir. Windows has multiple roots.
- OS manages users and permissions for the computers.
- OS assigns ports and ip addresses.
- Different distros are on the application level of the pc.

---

## VM
- Creating Virtual Machine Images (snapshots) is important in order to save all of your data in case of failure.
- VM has it’s own network, so the host can’t see it in it’s network by default.

---

## Linux Filesystem Hierarchy
- Every user will have it’s own space in /home dir, and root will have /root dir.
- /bin - binaries available for OS and all users.
- /sbin - binaries which needs sudo privilege.
- /lib directories - libraries for the executables.
- /usr - has also /bin and /sbin directories and they are the ones actually executed. Also has /local directory - this path /usr/local/bin will be where the user installs apps.
- /opt - apps which do not need other libraries (like /bin uses /lib dirs), so the app will be in one folder.
- /boot - booting the system (don’t touch).
- /etc - configuration files.
- /dev - devices connected to the system.
- /var - OS and apps logs.
- /tmp - temporary files (will be deleted).
- /media - external media (usb and such).
- /mnt - manually mount file systems.
- Hidden files - starts with a dot (dotfiles).

---

## Linux Commands
- ls -R = list recursively
- uname -a = complete info about the computer
- cat /etc/os-release = info about the linux distro
- lscpu / lsmem = info about the hardware (cpu, memory…)
- sudo adduser <username> = add new user
- sudo useradd = same but available in all linux and can get params
- cat /etc/passwd = list all users: <username>:<pass>:<uid>:<gid>:<gecos>:<homedir>:<shell>

---

## Package Manager
- The manager installs packages for us in the correct dirs (bin, etc,...)
- /etc/apt/sources.list = list of all of the sources for repositories that the package manager uses
- If what we want to install is not available through PM - We can install with “snap” PM. But with it, the dependencies aren’t shared! With apt we share the dependencies using /lib. We could also add to the /etc/apt/sources.list (we add PPA’s to the list).
- Debian based (ubuntu,debian,mint) uses apt,apt-get . Red Hat based (RHEL, CentOS, Fedora) uses yum,dnf

---

## Linux User Management
- There are superuser account, user account, service account for encapsulating the user privilege to only what they need to do.
- We’ll manage that with groups of users.
- Change user group = sudo usermod -g <groupname> <username>
- Change ownership of files = sudo chown / sudo chgrp

---

## Environment Variables
- Print all env vars = printenv 
- export <var>=<val> for current session
- Edit ./bashrc and run ‘source .bashrc’ for all sessions of the user
- Edit /etc/environment for system-wide configs

---

## Networking
- Devices connected to the same LAN are connected via switch (which the router acts as also) - The switch can route traffic between these devices
- When trying to access a device outside of the LAN we need a router for WAN connections
- IP of the router is Gateway
- The router knows if the device is the LAN based on the subnet (start IP and subnet mask that could be written as a CIDR block)
- NAT (Network Address Translation) will allow us to use a chosen public IP for WAN connections without exposing our LAN's private IP addresses
- There is SNAT(Static NAT), DNAT (Dynamic NAT) and PAT (Port Addressing Translation) which is the most widely used. In it - the router keeps a NAT port translation table for access to the LAN devices
- Firewall will consist of firewall rules which describe which IPs and ports are allowed to a specific IP address in our network
- DNS heirarchy consists of: Root domains (.) TLDs (.edu,.com,.gov,.us,.uk,...), Domain Name (mywebname)
- ICANN manages all of the domain names
- We can use subdomains for the domain we have - www.courses.mywebname.com , www.business.mywebname.com
- When trying to access a domain - The DNS client installed on our system checks with the Resolver if the IP of the domain is cached, if it doesn't it will recursively check with the Root Domain servers and so on... When the resolver has the answer IP it will cache it and return it to our DNS client
- Info can be displayed with ifconfig, netstat (for active listening processes), psaux (check process resources and ports), nslookup (for getting IP address of a domain, or the other way around), ping (check if IP is accessible), telnet client (check if a port is accessible)
- Wireshark can be used to check and record network communication
- Connection with SSH key pair: the client creates an SSH key pair, the public key is installed on the remote server (authorized_keys) and then the client can authenticate and communicate with remote server. The remote server will be added to the client's known_hosts file

---

## Build Tools
- We need to build our code to a single artifact and then we can move it and deploy to our servers from an artifact repo
- When creating Maven/Gradle project in java, the build tool will download dependencies for the project based on requirements files in it
