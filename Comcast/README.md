# Comcast Xfinity Dual Stack (IPv4 + IPv6) EdgeRouter Example config.boot Files

These files are example `config.boot` configuration files that can be be loaded on a factory-default Ubiquiti EdgeRouter
Lite (ERLite-3) or EdgeRouter POE (ERPOE-5) to enable dual-stack IPv4 & IPv6 networking on residential Comcast Xfinity networks.

- `config.boot.erl` - Comcast Xfinity configuration file for EdgeRouter Lite
- `config.boot.erx` - Comcast Xfinity configuration file for EdgeRouter X
- `config.boot.poe` - Comcast Xfinity configuration file for EdgeRouter POE
- `config.gateway.json` - JSON-formatted file for UniFi Security Gateway (runs EdgeOS)

# Port Settings
The default port/interface settings for each version of the example Comcast Xfinity `config.boot` files are:

###Comcast config.boot.erl####
- `eth0` = WAN (Cable Modem)
- `eth1` = Local Config Port
- `eth2` = LAN

###Comcast Fiber config.boot.erx####
- `eth0` = WAN (Cable Modem)
- `eth1`, `eth2`, `eth3`, & `eth4` = LAN (combined as `switch0`)

###Comcast config.boot.poe####
- `eth0` = WAN (Cable Modem)
- `eth1` = Local Config Port
- `eth2`, `eth3`, `eth4` = LAN (combined as `switch0`)

###Comcast config.gateway.json####
- `eth0` = WAN (Cable Modem)
- `eth2` = LAN

For all the files, `eth0` is always the **WAN** interface and `eth2` is always a valid **LAN** port to use during testing.

# Usage
Copy the raw contents of the appropriate `config.boot` file into your local clipboard.
Then create a blank `config.boot` file in `/home/ubnt` with:

    $ sudo vi /home/ubnt/config.boot

Once inside the vi editor, turn off the auto-indenting feature before you paste by typing

    :set noai

and pressing `ENTER`. If you’re not familiar with vi, make sure you type the `:` whenever they’re shown in this guide.

Now enter “insert” mode by pressing lowercase `i` (you don’t need `ENTER` after the `i` command).

Paste the copied raw `config.boot` file from your local system’s clipboard using your terminal client’s
Paste menu item or keyboard shortcut (usually `CTRL-V` on PC, `Command-V` on Mac, etc.). Now write and quit
the file by typing:

    :wq

and then `ENTER`.

Now you’re ready to copy your new `config.boot` file over the EdgeRouter’s default `config.boot` file with:

    $ sudo cp /home/ubnt/config.boot /config/config.boot

You can apply the new `config.boot` file by rebooting the router with the `reboot` command, or with:

    $ configure
    # load
    # commit
    # save
    # exit

# UniFi Security Gateway (USG) Usage
Following the instructions in this guide to learn more about how to apply the settings in the included `config.gateway.json` file to a USG via the UniFi Controller:

https://help.ubnt.com/hc/en-us/articles/215458888-UniFi-How-to-further-customize-USG-configuration-with-config-gateway-json

Be extremely careful when creating or editing a `config.gateway.json` file, as including malformed configuration options in a `config.gateway.json` file can lead to a provisioning loop. This is considered an advanced configuration option.

# Timekeeping

The `system ntp` configuration includes

    server time.comcast.com {
    }

This is mixed with [NTP Pool Project](https://www.ntppool.org/) servers because you could enjoy better quality time from a server nearer you in the network topology. Passing through fewer network hops could introduce lower latency and jitter than using a server that's nearer geographically.

In a chat conversation June 1 2021 with Xfinity Support, the agent could not confirm whether or not `time.comcast.com` serves [leap-smeared time](https://docs.ntpsec.org/latest/leapsmear.html), which should not be mixed with non-leap-smearing servers. If you discover symptoms attributable to leap smearing, change this to

    server time.comcast.com {
        noselect
    }

Alternatively, leave `time.comcast.com` selected, and add `noselect` to each of the pool servers configured. If you want more servers topologically nearby, you might also specify `time.comcast.net`, `time.xfinity.com`, and `time.xfinity.net`.

# Comcast Xfinity IPv6 Considerations

Based on the most recent [IPv6 information from Comcast](http://www.comcast6.net/), residential customers should be requesting IPv6 addressing with a prefix length of `/60` (which is what is used in these examples). Commercial customers should use a prefix length of `/56`.

If you edit the IPv6 settings in your `config.boot` and want to apply them immediately, do:

    $ release dhcpv6-pd interface eth0
    $ delete dhcpv6-pd duid 
    $ renew dhcpv6-pd interface eth0

Change `eth0` as needed to match your configuration's WAN interface.

# Test IPv6 Connectivity
Test your connection for IPv6 support by visiting these websites:
* https://ipv6-test.com/
* https://test-ipv6.com/
* http://testmyipv6.com/
* https://ipv6test.google.com/
* https://ipv6leak.com/
* https://ip6.me/
* https://ipinfo.io/
* https://ifconfig.me/
* https://ifconfig.co/
* https://api64.ipify.org/
* https://ident.me/
* https://checkip.amazonaws.com/
