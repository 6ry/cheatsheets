https://en.wikibooks.org/wiki/OpenSSH
https://wiki.archlinux.org/index.php/Secure_Shell
http://matt.might.net/articles/ssh-hacks/
http://blog.tjll.net/ssh-kung-fu/

https://pay.reddit.com/r/linux/comments/245jt9/ssh_kung_fu/ch3wruh
https://news.ycombinator.com/item?id=7660326
http://blog.cryptographyengineering.com/2013/08/is-cryptopocalypse-nigh.html
http://www.theguardian.com/world/2013/sep/05/nsa-how-to-remain-secure-surveillance



Controlmaster/Controlpath???


## sshd_config
The options most frequently tweaked are:

* Port: set this to the port on which you want sshd to run. Unless you have a compelling reason to move it, keep it on 22.
* PermitRootLogin: set this to no and then configure sudo to add a little security; another good setting is without-password, which will force the use of public key authentication for root.
* PasswordAuthentication: set this to no to disallow password authentication entirely and to require public key authentication.

## Port Forwarding
https://help.ubuntu.com/community/SSH/OpenSSH/PortForwarding

### local port forwarding

### reverse port forwarding
https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Proxies_and_Jump_Hosts

GatewayPorts yes

### dynamic port forwarding



## port knocking




## ssh_config

Host $ALIAS
    User $USER
    HostName $INTERNAL
    ProxyCommand ssh $USER2@$PUBLIC -W %h:%p



## ssh flags
-f (go to background)
-N (do not execute a remote program)
-T (disable pseudo-tty allocation)

-l
-p

-D lightweight/socks Proxy
-R reverse tunneling
-L local port forwarding

-W option -- which replaces netcat

# ssh ciphers, macs, and key-exhange (kex) algos
http://nerdbynature.de/s9y/?341
https://support.ssh.com/manuals/server-admin/44/Ciphers_and_MACs.html
http://xmodulo.com/how-to-speed-up-x11-forwarding-in-ssh.html

-m mac_spec
cipher?


## iptables


## autossh

http://www.gentoo-wiki.info/Autossh
http://www.harding.motd.ca/autossh/README
https://raymii.org/s/tutorials/Autossh_persistent_tunnels.html

```
$ autossh -M 0 -q -f -N \
    -o "ServerAliveInterval 60" \
    -o "ServerAliveCountMax 3" \
    -R 23456:localhost:22 ark-remote
```
forward local machine (localhost) port 22 to remote machine (ark-remote) port 23456

> The above command will make ssh send a keepalive request if no other
> data has been sent for 60 seconds, if it doesn't receive a reply after
> 3 attempts it will close the connection. autossh will then detect its
> been closed and attempt re-establish it.
> -o "ServerAliveInterval 60" 
> -o "ServerAliveCountMax 3" 
> 
> The "-M 0" option disables autossh's own monitoring which uses
> separate ports and is less reliable.
> 
> -f flag causes autossh to drop into the background before execution
> -N flag says no command
> -q flag says be quite
>
>
> ClientAliveInterval specifies number of seconds between every keep-alive
> message. Depending on quality of connection between Office Computer and
> the Server we may want to set it to, let’s say 10.
> 
> ClientAliveCountMax controls number of lost keep-alive messages that
> cause SSH server to pull the plug. We want it relatively big, but not
> too big. With ClientAliveInterval equals 10, its a good idea to loose
> the connection after keep-alive messages fail for lets say 5 minutes –
> 300 seconds. This means we can make ClientAliveCountMax equals 30.

