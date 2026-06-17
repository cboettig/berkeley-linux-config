## VPN (Berkeley bSecure / GlobalProtect)

Berkeley's VPN is Palo Alto GlobalProtect (CalNet + Duo over SAML). The official
client's login GUI doesn't work under Wayland, so use **openconnect** instead,
with [`gp-saml-gui`](https://github.com/dlenski/gp-saml-gui) handling the SAML
login (a small login window, fine on Sway via Xwayland).

### Install (Ubuntu 24.04)

```bash
sudo apt-get install -y openconnect python3-gi gir1.2-webkit2-4.1 python3-lxml pipx
pipx install --system-site-packages 'gp-saml-gui @ git+https://github.com/dlenski/gp-saml-gui.git'
pipx ensurepath   # then open a fresh shell
```

### Connect

Authenticate **directly against a gateway** with `-g` (going via the portal
`vpn.berkeley.edu` stalls on a second cookie prompt):

```bash
# split tunnel — campus access, e.g. bearbuy.is.berkeley.edu
gp-saml-gui -S -g campus-split.vpn.berkeley.edu

# full tunnel — library / journal PDFs (all traffic via campus)
gp-saml-gui -S -g campus.vpn.berkeley.edu
```

Complete the CalNet/Duo login in the popup; `-S` then runs `sudo openconnect`
(prompts for sudo). It stays in the foreground with a `tun0` address — leave the
terminal open; **Ctrl-C** disconnects. The SAML session is cached in
`~/.gp-saml-gui-cookies`, so reconnects usually skip Duo.

Verify: `ip -br addr show tun0` and `getent hosts bearbuy.is.berkeley.edu`.

### Aliases

```bash
alias vpn='gp-saml-gui -S -g campus-split.vpn.berkeley.edu'   # connect (split)
alias vpn-full='gp-saml-gui -S -g campus.vpn.berkeley.edu'    # connect (full)
alias vpn-down='sudo pkill -INT openconnect'                  # disconnect
```
