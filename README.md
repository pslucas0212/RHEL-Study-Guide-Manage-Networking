# RHEL Study: Guide Manage Networking

[RHEL Study Guide - Table of Contents](https://github.com/pslucas0212/RHEL-Study-Guide) 

In this unit we will practice creating a network link setting in RHEL.

ssh to the server you will be working on and sudo -i.

First let's find out the interfacename to the the network card

```
# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 52:54:00:00:fa:0b brd ff:ff:ff:ff:ff:ff
    altname enp0s3
    altname ens3
```
Let's see what is the current active link
```
# nmcli con show --active
NAME                UUID                                  TYPE      DEVICE 
Wired connection 1  3968ff88-8373-3810-a63f-1cdbb896767e  ethernet  eth0
```

Let's create our new connection
```
# nmcli con add con-name lab ifname eth0 type ethernet ipv4.method manual ipv4.addresses 172.25.250.11/24 ipv4.gateway 172.25.250.254 ipv4.dns 172.25.250.254
Connection 'lab' (86f55127-fe96-4b05-945f-c9f7f6ef5a39) successfully added.
```
Let's set it up to autoconnect on lab and not on Wired connection 1

```
# nmcli con modify lab connection.autoconnect yes
# nmcli con modif 'Wired connection 1' connection.autoconnect no
```

Let's add another IP address to the lab connection
```
# nmcli con modify lab +ipv4.addresses 10.0.1.1/24
```

Add the new ip address to con lab.nmconnection config file
```
# vi /etc/NetworkManager/system-connections/lab.nmconnection
[ipv4]
address1=172.25.250.11/24,172.25.250.254
address2=10.0.1.1/24
dns=172.25.250.254;
method=manual

```

update the local /etc/hosts file
```
# echo "10.0.1.1 private" >> /etc/hosts
```

Reboot the system and wait for it to restart to test the new connection.  Logon to server to check the new connection
```
# nmcli con show --active
NAME  UUID                                  TYPE      DEVICE 
lab   86f55127-fe96-4b05-945f-c9f7f6ef5a39  ethernet  eth0
```
