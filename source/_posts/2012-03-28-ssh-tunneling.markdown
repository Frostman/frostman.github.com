---
layout: post
title: "SSH Tunneling"
date: 2012-03-28 09:01
comments: true
categories: [nix, Ssh, Tips, Tunneling, Port forwarding]
excerpt: Some tips about ssh tunneling.
---
I create SSH tunnels every day and this article is a small introduction into the SSH tunneling
(also known as "port forwarding").
A secure shell (SSH) tunnel consists of an encrypted tunnel created through a SSH protocol connection.
Users may set up SSH tunnels to transfer unencrypted traffic over a network through an encrypted channel.
SSH is typically used to log into a remote machine and execute commands, but it also supports tunneling,
forwarding TCP ports and X11 connections; it can transfer files using the associated SSH file transfer (SFTP)
or secure copy (SCP) protocols. Also tunnels provide an ability to bypass firewalls :)

So, let's start. Ports can be forwarded in three ways:

1. Local port forwarding;
2. Remote port forwarding;
3. Dynamic port forwarding (proxy).

Wikipedia says that:
    Port forwarding or port mapping is a name given to the combined technique of
    * translating the address and/or port number of a packet to a new destination
    * possibly accepting such packet(s) in a packet filter (firewall)
    * forwarding the packet according to the routing table.

##Tunneling with local port forwarding

Let's imagine that there are some computers with specific abilities:

* `work` - can't access `target` (for example, `target` is blocked by firewall or `target` can be
unaccessible from the `work`'s network)
* `bridge` (`gateway`) - accessible from `work` and can access `target` (need to have IP accessible from `work`)

So, we want to access `target` throw the `global` machine.

`Access from work computer to some external banned resource`

To create the SSH tunnel you should execute the following command from `work` (`local`) machine:

{% codeblock %}
ssh -L [<local-address-to-listen>:]<local-port-to-listen>:<target-host>:<target-port> <bridge>
{% endcodeblock %}

Now the SSH client at `work` will connect to the SSHd running at `bridge` and bind `<target-host>:<target-port>`
to listen for local requests to `[<local-address-to-listen>:]<local-port-to-listen>`. At the `bridge`
end the connection to `<target-host>:<target-port>` will be created. So `work` doesn’t need to know how to connect to
`<target-host>:<target-port>`, only `bridge` needs to worry about that. The channel between `work` and `bridge` will be
**encrypted** (SSH tunnel), but the connection between `bridge` and `<target-host>:<target-port>` will be **unencrypted**.

After executing this command, it is possible to access `<target-host>:<target-port>` by accessing
`[<local-address-to-listen>:]<local-port-to-listen>` at `work` computer. The `bridge` computer will act as a gateway
which would accept requests from `work` machine and fetch data/tunnelling it back.

##Tunneling with remote port forwarding  (reversed tunneling)

Let's reverse the problem.

* `home` - can't access `target` (for example, `target` is protected by firewall or `target` can be unacessible from the
`home`'s network)
* `bridge` (`gateway` or `work`) - accessible from `home` and can access `target` (need to have IP accessible from `home`)

To create the SSH tunnel you should execute the following command from `bridge` (`gateway`/`work`) machine:

{% codeblock %}
ssh -R [<home-address-to-listen>:]<home-port-to-listen>:<target-host>:<target-port> <home>
{% endcodeblock %}

After executing of this command, the SSH client at `bridge` will connect to SSHd running at `home` and create the tunnel.
Then the server will bind `[<home-address-to-listen>:]<home-port-to-listen>` to listen for incoming requests which would
be routed through the created SSH tunnel between `home` and `gateway`. Now it’s possible to access the "internal"
resource `<target-host>:<target-port>` by accessing `[<home-address-to-listen>:]<home-port-to-listen>` from the `home`
machine. The `gateway` will then create a connection to `<target-host>:<target-port>` and relay back the response to
`home` via the created SSH tunnel.

With this approach we need to create the splitted SSH tunnel for each new resource. But SSH provides us an ability to
proxy traffic to any resource using SSH tunnel and it named `dynamic port forwarding`.

## Dynamic port forwarding (proxy)

Dynamic port forwarding provides us an ability to configure one local port for tunnelling data to all remote destinations
using the SOCKS protocol. At the client side of the SSH tunnel a SOCKS proxy would be created and any application that can
work throw SOCKS protocol can use it to access some resources that accessible only from the other host.

{% codeblock %}
ssh -D [<local-address-to-listen>:]<local-port-to-listen> <gateway>
{% endcodeblock %}

##Additional SSH flags

There are several intresting `ssh` command flags:

* `-v` - verbose output
* `-q` - quiet mode
* `-1` - forces ssh to try protocol version 1 only
* `-2` - forces ssh to try protocol version 2 only
* `-4` - forces ssh to use IPv4 addresses only
* `-6` - forces ssh to use IPv6 addresses only
