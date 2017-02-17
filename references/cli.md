## OSC CLI

This section explains the OSC CLI commands, which you can run in normal mode.

### Contents
* [clear](#clear)
* [debug](#debug) TODO: enable? "debug" does nothing
* [exit](#exit)
* [help](#help)
* [history](#history)
* [list](#list) TODO: shows the same as "help"
* [ping](#ping)
* [ping6](#ping6) TODO: "connect: network is unreachable"
* [reset](#reset)
* [server restart](#server-restart) TODO: is a restart needed after setting
* [server start](#server-start)
* [server status](#server-status)
* [server stop](#server-stop)
* [set network dns](#set-network-dns)
* [set network domain](#set-network-domain) TODO: cannot set to empty
* [set network gateway](#set-network-gateway) TODO: cannot set to empty
* [set network hostname](#set-network-hostname)
* [set network ip](#set-network-ip)
* [set network ntp](#set-network-ntp) TODO: ntpdate: Synchronizing with time server:[FAILED]
* [set passwd](#set-passwd) TODO: does not update password on web app
* [set time](#set-time)
* [set timezone](#set-timezone)
* [show arp](#show-arp)
* [show clock](#show-clock)
* [show filesystems](#show-filesystems)
* [show log](#show-log)
* [show log follow](#show-log-follow)
* [show log last](#show-log-last)
* [show log reverse](#show-log-reverse)
* [show network dns](#show-network-dns)
* [show network domain](#show-network-domain)
* [show network hostname](#show-network-hostname)
* [show network ip](#show-network-ip)
* [show network ntp](*show-network-ntp)
* [show network route](#show-network-route)
* [show process](#show-process)
* [show process monitor](#show-process-monitor)
* [show system memory](#show-system-memory)
* [show system uptime](#show-system-uptime) TODO: does not always match web app
* [show version](#show-version)
* [shutdown](#shutdown)
* [traceroute](#traceroute)
* [traceroute6](#traceroute6) TODO: "connect: network is unreachable"

### clear
Clears only the CLI screen. This command has no impact on the database of OSC.

**Syntax**: `clear`

### debug
Helps debug the connection between the OSC appliance and the client computer.

**Syntax**: `debug`

### exit
Logs off the current SSH connection with the OSC appliance and closes the SSH client window. 

**Syntax**: `exit`
### help
Provides a description of all commands.

**Syntax**: `help`

### history
Lists all the commands attempted so far on your OSC appliance including unsuccessful and attempted commands in earlier sessions.

**Syntax**: `history`

### list
Lists all the commands available in the normal mode along with the description for each command.

**Syntax**: `list`

### ping
Pings an IPv4 network host.

**Syntax**: `ping <A.B.C.D>`

|**Parameter**| **Description**|
|-------------|----------------|
|`<A.B.C.D>` | a 32-bit IPv4 address written as four eight-bit numbers separated by periods. Each number (A,B,C, and D) is an eight-bit number between 0-255.|

### ping6
Pings an IPv6 network host.

**Syntax**: `ping6 <A:B:C:D:E:F:G:H>`

|**Parameter**| **Description**|
|-------------|----------------|
|`<A:B:C:D:E:F:G:H>` | a 128-bit address written as an octet (eight groups) of four hexadecimal numbers, separated by colons. Each letter represents a group of hexadecimal numbers between 0000-FFFF. You can also use the shorter notations of an IPv6 address.|

### reset
Resets the OSC appliance **immediately without confirmation**.

**Syntax** `reset`

### server restart
Stops and then starts the OSC server.

**Syntax**: `server restart`

### server start
Starts the OSC server.

**Syntax**: `server start`

###server status
Displays the current status of the OSC server.

**Syntax**: `server status`

###server stop
Stops the OSC server.

**Syntax**: `server stop`

###set network dns
Configures primary and secondary name servers (IPv4 addresses) for the OSC appliance.

**Syntax**: `set network dns <A.B.C.D> <E.F.G.H>`

|**Parameter**| **Description**|
|-------------|----------------|
|`<A.B.C.D>` | IPv4 address of the primary name server.|
|`<E.F.G.H>` | IPv4 address of an optional secondary name server.|

**Notes**:
* To remove the name server configuration, enter `set network dns` without any IP address parameters.
* To remove just the secondary name server IP address, enter `set network dns <A.B.C.D>` without the second IP address parameter.
* You can also configure the name server IP addresses in the Network Settings page of OSC web application. The name servers you configure using the `set network dns` command reflect in the **Network Settings** page on the OSC web application.

##set network domain
Adds OSC to a network domain.

**Syntax** `set network domain <domain name>`

##set network gateway
Configures the IPv4 address of the default gateway for the OSC appliance.

**Syntax**: `set network gateway <A.B.C.D>`

|**Parameter**| **Description**|
|-------------|----------------|
|`<A.B.C.D>` | a 32-bit IPv4 address written as four eight-bit numbers separated by periods. Each number (A,B,C, and D) is an eight-bit number between 0-255.|

**Notes**:
* To remove the network gateway configuration, enter `set network gateway` without any IP address.
	* If you do not configure a default gateway, OSC appliance can communicate only with hosts on the same subnet and your current SSH session will be closed unless your client machine is on the same subnet as that of OSC.
* You can also configure the gateway IP addresses in the **Network Settings** page of OSC web application. The default gateway you configure using the `set network gateway` command reflects in the **Network Settings** page.

##set network hostname
Configures a network name for the OSC appliance. If this name is resolvable to the IP address of OSC appliance, you can use this hostname to use the OSC web application instead of the IP address.

**Syntax**: `set network hostname <WORD>`

|**Parameter**| **Description**|
|-------------|----------------|
|`<WORD>` | indicates a case-sensitive character string up to 25 characters. The string must begin with a letter and can include hyphens, underscores, and periods.|

The host name of OSC is displayed in the **Summary** page of OSC web application.

##set network ip
Reconfigures a static or dynamic IPv4 address for the OSC appliance. If it is a static IP address, you must specify the CIDR notation as well.

**Syntax**: `set network ip <A.B.C.D/CIDR> | dhcp`

|**Parameter**| **Description**|
|-------------|----------------|
|`<A.B.C.D/CIDR>` | indicates an IPv4 address followed by the CIDR notation to specify the network part and the host part. For example, the CIDR notation for a Class C address is 24.|
|`dhcp`| indicates that the network settings must be configured through a DHCP server. |

**Notes**:
* The SSH client will need to be closed and another session with the new IP address will need to be opened.
* The OSC server may need to be started using [`server start`](#server-start).
* The configured IP address is displayed in the **Summary** page of OSC web application.
* You can also configure the IP address from the **Network Settings** page of OSC web application.

##set network ntp
Configures Network Time Protocol (NTP) servers as the time source for the OSC appliance. You can specify the IPv4 addresses or host names of the NTP servers separated by a space. OSC appliance considers the first available NTP server from your list.

**Syntax**: `set network ntp <A.B.C.D or hostname> <E.F.G.H or hostname> ... <W.X.Y.Z or hostname>`

|**Parameter**| **Description**|
|-------------|----------------|
|`<A.B.C.D or hostname>` | the IPv4 address or name of the first priority NTP server.|
|`<E.F.G.H or hostname>` | the IPv4 address or name of the second priority NTP server.|
|`<W.X.Y.Z or hostname>` | the IPv4 address or name of the nth priority NTP server.|

**Notes**:
* If the hostname of NTP servers is specified, a name server must be configured for OSC appliance to resolve the hostname.
* To view the currently configured NTP server details, use [`show network ntp`](#show-network-ntp) command.

##set passwd
Changes the logon password for the OSC appliance. It prompts for the old password and then prompts for a new password. A password must contain at least eight characters and can consist of any alphanumeric character or symbol.

**Syntax**: `set passwd`

##set time
Sets the date and time on OSC appliance.
All parameters are mandatory.

**Syntax**: `set time MMDDmmhhCCCC`

|**Parameter**| **Description**|
|-------------|----------------|
|`MM` | month|
|`DD` | date|
|`mm` | minutes|
|`hh` | hour|
|`CCCC` | year|

**Example**: `set time 013006302014`  
This sets the time to Thursday, January 30, 06:30:00 2014

To view the current time on the OSC appliance, use the [`show clock`](#show-clock) command or the **Summary** page on the OSC web application.

##set timezone
Sets the time zone on the OSC appliance.

**Steps**:

1. Enter `set timezone`. The continents and oceans are listed.  
2. Enter the number corresponding to the continent or ocean. For example, to set the time zone to Pacific time, enter 10. The countries corresponding to the continent or ocean are listed.  
3. Enter the number corresponding to the country you want to select. For example, to set the time zone to Pacific time, enter 25. The time zones applicable to the selected country are listed.  
4. Enter the number corresponding to the time zone you want to select. For example, select 21 to set to Pacific time.  
5. Press 1 to confirm or 2 to cancel the operation.  

You can use [`show clock`](#show-clock) command or the **Summary** page on the OSC web application to check the current time zone on OSC.

##show arp
Displays the current Address Resolution Protocol (ARP) entries on the OSC appliance.

**Syntax**: `show arp`

##show clock
Displays the current date, time, and time zone configured on OSC appliance.

**Syntax**: `show clock`

##show filesystems
Displays the filesystems in OSC appliance.

**Syntax**: `show filesystems`

This command displays the following information:
* File system name
* Available space
* Space allocated
* Space used in percentage
* Space used currently
* Where the file system is mounted

##show log
Displays the OSC log files.

**Syntax**: `show log`

##show log follow
Follows OSC logs. Cancel (Ctrl-c) to exit this command.

**Syntax**: `show log follow`

##show log last
View the last *n* log entries

**Syntax**: `show log last <number>`

**Example**: `show log last 5`
Displays the last 5 log entries.

##show log reverse
Displays the log entries in reverse chronological order. That is, the latest log entry is displayed first.  

**Syntax**: `show log reverse`

##show network dns
Displays the IP addresses of the currently configured name servers

**Syntax**: `show network dns`

**Notes**:
* The **Network Settings** page on the OSC web application displays the name servers.
* To configure the name servers, use the [`set network dns`](#set-network-dns) command or the **Network Settings** page on the OSC web application.

##show network domain
Displays the network domain to which you have added OSC.

**Syntax**: `show network domain`

If no result is displayed, it means OSC is not part of any domain.

##show network hostname
Displays the network name for the OSC appliance.

**Syntax**: `show network hostname`

The host name of OSC is displayed in the **Summary** page of OSC web application.

##show network ip
Displays the IP address of the OSC appliance.

**Syntax**: `show network ip`

The IP address of OSC is displayed in the **Summary** and **Network Settings** pages of OSC web application.

##show network ntp
Displays the IP addresses or the host names of the Network Time Protocol (NTP) servers configured for the OSC appliance.

**Syntax**: `show network ntp`

To configure NTP server details, use [`set network ntp`](#set-network-ntp) command.

##show network route
Displays the routes configured on the OSC appliance.

**Syntax**: `show network route`

##show process
Displays the current system processes running on the OSC appliance.

**Syntax**: `show process`

##show process monitor
Use this command to monitor the system running on OSC.

**Syntax**: `show process monitor`

##show system memory
Displays the current memory usage on OSC.

**Syntax**: `show system memory`

##show system uptime
Displays how long the OSC has been up since the last restart, the number of users logged on, and the average load.

**Syntax**: `show system uptime`

##show version
Displays the software version of OSC virtual appliance.

**Syntax**: `show version`

##shutdown
Halts the OSC appliance and powers it off without confirmation. The OSC server may need to be started after this command using [`server start`](#server-start).

**Syntax**: `shutdown`

##traceroute
Displays the traceroute to host.

**Syntax**: `traceroute <A.B.C.D>`

|**Parameter**| **Description**|
|-------------|----------------|
|`<A.B.C.D>` | a 32-bit IPv4 address written as four eight-bit numbers separated by periods. Each number (A,B,C, and D) is an eight-bit number between 0-255.|

##traceroute6
Displays the traceroute to host.

**Syntax**: `traceroute6 <A:B:C:D:E:F:G:H>`

|**Parameter**| **Description**|
|-------------|----------------|
|`<A:B:C:D:E:F:G:H>` | a 128-bit address written as an octet (eight groups) of four hexadecimal numbers, separated by colons. Each letter represents a group of hexadecimal numbers between 0000-FFFF. You can also use the shorter notations of an IPv6 address.|
