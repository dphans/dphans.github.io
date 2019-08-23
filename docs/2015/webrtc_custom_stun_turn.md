# Implementing TURN server on CentOS

**TURN is an extension of STUN, so TURN server has also STUN features. When we use both STUN and TURN servers, STUN is always attempted first to speedup the connection out of getting immediate candidates when users are sitting behind same NAT. And TURN is used as fallback option depending on client locations and network topologies.**

## Install Prerequisite for CentOS

```bash
yum install -y make gcc cc gcc-c++ wget
yum install -y openssl-devel libevent libevent-devel mysql-devel mysql-server
```

## Install LibEvent modules

As root, build and install the library with the usual:

```bash
wget https://github.com/downloads/libevent/libevent/libevent-2.0.21-stable.tar.gz
tar xvfz libevent-2.0.21-stable.tar.gz
cd libevent-2.0.21-stable && ./configure
make && make install && cd ..
```

## Download & Install TURN modules

```bash
wget http://turnserver.open-sys.org/downloads/v3.2.4.1/turnserver-3.2.4.1.tar.gz
tar -xvzf turnserver-3.2.4.1.tar.gz
cd turnserver-3.2.4.1
make && make install
```

## Configure “turnserver.conf” file

```bash
cd /etc
mkdir turnserver && cd turnserver
vi turnserver.conf
```

Paste into your editor there lines below:

```bash
user=username:password
listening-port=2222
listening-ip=127.1.1
```

Press Esc and :wq to save config file.

## “Permanently” run TURN server instance

```bash
cd /etc/turnserver/
nohup turnserver -v -r ip:port -a -b turnuserdb.conf -c turnserver.conf -u turn-username -r ip:port -p turn-password &
# Replace “ip:port” with yours! E.g. 127.1.1:2222, “turn-username” with custom-username, and “turn-password” with custom-password.
```

**Example:**

```bash
nohup turnserver -v -r 127.1.1:2222 -a -b turnuserdb.conf -c turnserver.conf -u baophan -r 127.1.1:2222 -p blablabla &
```

_Tip: You can run TURN server “forever”, need placed turnserver command between “nohup” and “&”, like this:_

```bash
nohup TURN-execution-command &
```

## Javascript

Embedding a visible password in your Javascript code may allow someone else to use your TURN server

```bash
var pc_config = {
  "iceServers": [
    {
      "url": "stun:stun.l.google.com:19302"
    },{
      "url":"turn:my_username@<turn_server_ip_address>",
      "credential":"my_password"
    }
  ]
};
```

## Android

```java
iceServers.add(
  new PeerConnection.IceServer(
    "turn:turn_server_ip_address",
    "myusername",
    "my password"
  )
);
```
