## Configuring the VPN

Berkeley's bSecure VPN runs Palo Alto GlobalProtect (portal: `vpn.berkeley.edu`),
authenticating with CalNet + Duo via SAML. Berkeley distributes the official
GlobalProtect Linux client, but its login GUI relies on an embedded Qt WebKit
browser that does not play well with Wayland (Berkeley's own docs note the client
"requires X11"). Both the GUI **and** the CLI client route the SAML login through
that same embedded webview, so on a Wayland compositor (e.g. Sway) both tend to
fail at the login step.

You don't need the GlobalProtect client at all. **openconnect** speaks the
GlobalProtect protocol natively, and it has no display-server dependency — the
only graphical step is the CalNet/Duo SAML login, which we hand off to
[`gp-saml-gui`](https://github.com/dlenski/gp-saml-gui) (a small WebKit2-GTK
window that runs fine under Xwayland). This is cleaner and more robust than the
official client.

> The old note here said "openconnect is deprecated." That referred to Berkeley
> *dropping support* for it, not to openconnect's *ability* to connect — which
> works fine and is the approach documented below.

### Install (Ubuntu 24.04)

```bash
# openconnect + the SAML login window's dependencies
sudo apt-get install -y openconnect python3-gi gir1.2-webkit2-4.1 python3-lxml pipx

# gp-saml-gui, installed into a pipx venv that can see the system GTK/WebKit bindings
pipx install --system-site-packages 'gp-saml-gui @ git+https://github.com/dlenski/gp-saml-gui.git'
pipx ensurepath   # then open a fresh shell so ~/.local/bin is on PATH
```

### Connect

The portal (`vpn.berkeley.edu`) offers three gateways:

- **Library Access and Full Tunnel** (`campus.vpn.berkeley.edu`) — routes *all*
  traffic through campus; use this to read journal PDFs / licensed resources
  directly from the browser.
- **Split Tunnel (Default)** (`campus-split.vpn.berkeley.edu`) — only
  campus-bound traffic goes through the VPN. Enough to reach campus-only sites
  like `bearbuy.is.berkeley.edu`.
- **Restricted Tunnel** (`restricted.vpn.berkeley.edu`).

The key trick: authenticate **directly against the gateway** (with `-g`), not the
portal. The portal hands back a *portal* cookie, but the gateway then demands its
*own* SAML cookie — going through the portal leaves openconnect prompting for a
second cookie on a stdin that's already busy with the piped one. Hitting the
gateway directly mints the right (`gateway:prelogin-cookie`) cookie in one shot:

```bash
# Split tunnel (campus access, e.g. bearbuy):
gp-saml-gui -S -g campus-split.vpn.berkeley.edu

# Full tunnel (library / journal PDFs):
gp-saml-gui -S -g campus.vpn.berkeley.edu
```

`gp-saml-gui` opens the CalNet login window, you complete Duo, and then `-S`
launches `sudo openconnect …` for you (it prompts for your sudo password). On
success openconnect stays in the **foreground** and you'll see a `tun0` address —
**leave that terminal open**; closing it or Ctrl-C drops the VPN. It caches the
SAML session in `~/.gp-saml-gui-cookies`, so reconnects usually skip the Duo prompt.

Verify:

```bash
ip -br addr show tun0                 # should show a 10.x address
getent hosts bearbuy.is.berkeley.edu  # campus site resolves
```

### Notes

- Don't go through the portal (`-p` / the default). It selects a gateway but then
  stalls at a second `prelogin-cookie:` prompt (`Inappropriate ioctl for device`)
  because stdin is the piped cookie.
- `--authgroup="Split Tunnel (Default)"` *does* let openconnect pick a gateway
  from the portal without prompting, but you still hit the two-cookie problem
  above — so prefer connecting to the gateway directly with `-g`.
