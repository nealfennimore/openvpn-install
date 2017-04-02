# openvpn-install
OpenVPN [road warrior](http://en.wikipedia.org/wiki/Road_warrior_%28computing%29) installer for Debian, Ubuntu and CentOS.

This script will let you setup your own VPN server in no more than a minute, even if you haven't used OpenVPN before. It has been designed to be as unobtrusive and universal as possible.

## Installation
Run the script and follow the assistant:

`wget https://git.io/vpn -O openvpn-install.sh && bash openvpn-install.sh`

Once it ends, you can run it again to add more users, remove some of them or even completely uninstall OpenVPN.

## I want to run my own VPN but don't have a server for that
You can get a little VPS for just $2.99/month at [Bandwagon Host](https://bandwagonhost.com/aff.php?aff=575&pid=12).

## Donations

If you want to show your appreciation, you can donate via [PayPal](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=VBAYDL34Z7J6L) or [Bitcoin](https://pastebin.com/raw/M2JJpQpC). Thanks!

# Enable with UFW

This tutorial will use OpenVPN over UDP, so ufw must also allow UDP traffic over port 1194.
```sh
ufw allow 1194/udp
```

The ufw forwarding policy needs to be set as well. We'll do this in ufw's primary configuration file.

```sh
vim /etc/default/ufw
```

Look for DEFAULT_FORWARD_POLICY="DROP". This must be changed from DROP to ACCEPT. It should look like this when done: `DEFAULT_FORWARD_POLICY="ACCEPT"`

Next we will add additional ufw rules for network address translation and IP masquerading of connected clients.
```sh
vim /etc/ufw/before.rules
```
Add the lines between # START OPENVPN RULES and # END OPENVPN RULES to make the top of your before.rules file look like below.

```sh
#
# rules.before
#
# Rules that should be run before the ufw command line added rules. Custom
# rules should be added to one of these chains:
#   ufw-before-input
#   ufw-before-output
#   ufw-before-forward
#

# START OPENVPN RULES
# NAT table rules
*nat
:POSTROUTING ACCEPT [0:0] 
# Allow traffic from OpenVPN client to eth0
-A POSTROUTING -s 10.8.0.0/8 -o eth0 -j MASQUERADE
COMMIT
# END OPENVPN RULES

# Don't delete these required lines, otherwise there will be errors
*filter
```

With the changes made to ufw, we can now enable it. Enter into the command prompt:
```sh
ufw reload
```
