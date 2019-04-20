## Configuring the VPN

### Ubuntu / network-manager

I find the most convenient way to configure the VPN is using Ubuntu's built-in default tool for handling network and wireless connections: Network Manager.  First, we need to install the correct VPN tool for Network Manager to use: the `openconnect` plugin.


```bash
sudo apt-get install -y network-manager-openconnect-gnome
```

Now, from the Network Manager app select `VPN Connections` -> `Configure VPN ...`. Click `Add`, 
select type from the dropdown list as `Cisco AnyConnect Compatible VPN (anyconnect)` from the dropdown list.  

Give the connection a name (e.g. Berkeley) and enter `ucbvpn.berkeley.edu` as the Gateway, leave the remaining settings as they are and click save.  

From the Network Manager app, again select `VPN Connections` and then select the name of the connection you just created.  It should open a dialog asking for you to select a group from a dropdown menu, a name, and a password. Select the appropriate group (e.g. `3-Library_VPN`) and log in using your CalNet ID. 

You should now be connected to the VPN and can access subscription journal content, etc.


### Command-line version

Adapted from [Rachel Domagalski](http://w.astro.berkeley.edu/~domagalski/linux/vpn.html)

The campus VPN service can be used to gain access to the UC Berkeley network
outside of campus using an encrypted connection. In particular, it can be used
with the campus library to gain access to journal articles and databases that
are restricted otherwise. While the UC Berkeley library website has
<a href="http://www.lib.berkeley.edu/Help/vpn.html">instructions</a> for 
connecting to the VPN service using the
<a href="http://ist.berkeley.edu/software-central/cisco-vpn">Cisco VPN client</a>,
instructions for GNU/Linux machines do not exist, although the GUI on the Cisco
client for GNU/Linux is pretty much identical to the Mac and Windows versions.
</p>

<p> It is not recommended to use the Cisco client, and these instructions will
explain how to use
<a href="http://www.infradead.org/openconnect/">openconnect</a> to connect to
the campus VPN service. The reasons to avoid the Cisco client include, but are
not limited to the following: </p>
<ul>
    <li>The Cisco client is not packaged for any GNU/Linux distribution. While
    the client comes with an installation script, this is not a substitute for
    proper packaging, as the install script cannot be guaranteed to conform to
    the standards set by the various GNU/Linux distributions that one might use.
    Openconnect, however, is properly packaged and is supplied in the
    repositories for mainstream GNU/Linux distributions, which means that
    installing, updating, and possibly removing the software are both easy and
    standardized.</li>
    <li>The Cisco client is closed-source, which means that the security of the
    software cannot be independently verified. This reason alone is important
    enough to reject the Cisco software, as security is highly important for
    networking, and it is always a bad idea to rely on security software
    programs that cannot be verified though any independent source (i.e.
    closed-source software). Openconnect is open source under the LGPL and the
    source code is freely available for independent researchers to verify the
    security and for the general public to view/modify.</li>
    <li>The <a href="http://www.infradead.org/openconnect/">openconnect</a>
    website lists several other deficiencies of the official Cisco client that
    openconnect fixes.</li>
</ul>

Openconnect is a command-line program, but for those who need/want a GUI, there
is an openconnect plugin for NetworkManager (the network manager for most
desktop environments). I have not attempted to test this, as I do not use
NetworkManager. Alternatively, one is free to attempt to use the official Cisco
client at their own risk (not recommended).

#### Basic Usage Instructions:

<ol>
    <li>Install the openconnect software from your package manager. On
    Debian/Ubuntu, the command to install openconnect is:<br />
    <tt>$ sudo apt-get install openconnect</tt><br />
    and on Fedora, the command should be: <br />
    <tt>$ sudo yum install openconnect</tt></li>
    <li>Run the openconnect client as root to connect to the UC Berkeley VPN
    Service: <br />
    <tt>$ sudo openconnect ucbvpn.berkeley.edu</tt></li>
    <li>You should now see the following, or something similar:<br />
    <tt>GROUP: [1-Campus_VPN|2-Campus_VPN_Full_Tunnel|3-Library_VPN|4-Campus_VPN_Split_Tunnel_v4_v6|5-Campus_VPN_Full_Tunnel_v4_v6|900-test]<br /></tt>
    To select a network, type the group that you want (example: 3-Library_VPN)
    and hit Enter. Information on the various groups available can be found
    <a href="http://www.net.berkeley.edu/vpn/">here</a>.</li>
    <li>You will now be prompted for a username and password and you can now log
    in with your Calnet ID. Once you do that, you will be connected to the UC
    Berkeley VPN service until you close openconnect.</li>
    <li>To disconnect from the campus VPN, simply stop the openconnect process
    that you started to connect. This can be done by entering the key
    combination of Ctrl-C in the terminal window used to launch
    openconnect.</li>
</ol>
