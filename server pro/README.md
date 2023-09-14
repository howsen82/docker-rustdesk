# create a network for the RustDesk Server Backend and the DMZ.
# For each application you use with the NPM (Nginx Proxy Manager) you should have a dedicated backend network to isolate it
```
docker network create --driver=bridge --subnet=192.168.254.0/29 RSBackend
docker network create --driver=ipvlan --subnet=192.168.1.0/24 --gateway=192.168.1.1 -o ipvlan_mode=l2 -o parent=eth0 DMZ
```

**Setup Firewall**

Configure the following Port forwarding/NAT ports from your public IP to the NPM Server.

- 21114 => 8080 TCP
- 21115 => 21115 TCP
- 21116 => 21116 TCP/UDP
- 21117 => 21117 TCP
- 21118 => 21118 TCP
- 21119 => 21119 TCP
- 443 => 443 TCP # If you want to use SSL

**Setup NPM**

Configure Stream Hosts for the following Ports:

- 21115 => 192.168.254.2:21115 TCP
- 21116 => 192.168.254.2:21116 TCP / UDP
- 21117 => 192.168.254.3:21117 TCP
- 21118 => 192.168.254.2:21118 TCP
- 21119 => 192.168.254.3:21119 TCP
- 80 => 127.0.0.1:8080 TCP # catches local traffic

Configure Proxy Host:

- Domain Name: rustdesk.example.com
- Scheme: http
- Forward Hostname / IP: 192.168.254.2
- Forward Port: 21114
- Block Common Exploits: Checked
- Optional: Configure SSL **(DO NOT REQUIRE - Client needs to be able to communicate without SSL.)**

**Setup RustDesk Server**
Connect to Server interface http://rustdesk.example.com or https://rustdesk.example.com if you have configured SSL for web interface.

**Setup RustDesk Client**

Configure the client:

- ID Server: rustdesk.example.com
- Relay Server: rustdesk.example.com
- API Server: http://rustdesk.example.com (use HTTPS if you have configured SSL)
- Key: {Server Key Here}

**Log in**

The default port of the web console is 21114. Enter http://<hbbs host>:21114 in the browser to enter the console page, as shown in the following figure. The default administrator username/password is admin/test1234

![Alt text](https://rustdesk.com/docs/en/self-host/rustdesk-server-pro/console/images/console-login.png "a title")