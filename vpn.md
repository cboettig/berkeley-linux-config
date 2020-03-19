## Configuring the VPN

NOTE: Previous mechanisms using openconnect are now deprecated!

Current directions, see: <https://security.berkeley.edu/services/bsecure/bsecure-remote-access-vpn>

Requires Berkeley CalNet login to download from <https://drive.google.com/drive/folders/1g9AH1-ykuJmSpvZOb-Vb4Q9S-KsR2dzP>.

On Ubuntu 19.10, I:

- downloaded version 5.1.2-c3.
- `tar xvf PanGPLinux-5.1.2-c3.tgz`
- `sudo dpkg -i GlobalProtect_UI_deb-5.1.2.0-3.deb`

Then enter <vpn.berkeley.edu> as the connection in the box that pops up.  Log in with CalNet credentials + Duo 2FA.

From the dropdown in the GUI, choose Library tunnel to be able to access PDF articles directly from browser (split tunnel doesn't do this). 

