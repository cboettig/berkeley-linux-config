## VPN (Berkeley bSecure / GlobalProtect)

Berkeley's VPN is Palo Alto GlobalProtect (CalNet + Duo over SAML). The official
client's login GUI doesn't work under Wayland, so use **openconnect** instead,
with [`gp-saml-gui`](https://github.com/dlenski/gp-saml-gui) handling the SAML
login (a small login window, fine on Sway via Xwayland).

### Install (Ubuntu 24.04 / 26.04)

```bash
sudo apt-get install -y openconnect python3-gi gir1.2-webkit2-4.1 python3-lxml pipx
pipx install --system-site-packages 'gp-saml-gui @ git+https://github.com/dlenski/gp-saml-gui.git'
pipx ensurepath   # then open a fresh shell
```

Same packages on both releases — 26.04 still ships `gir1.2-webkit2-4.1`, so
gp-saml-gui's GTK3/WebKit2 UI needs no changes.

### Connect

Authenticate **directly against a gateway** with `-g` (going via the portal
`vpn.berkeley.edu` stalls on a second cookie prompt):

```bash
# split tunnel — campus access, e.g. bearbuy.is.berkeley.edu
gp-saml-gui --allow-insecure-crypto -S -g campus-split.vpn.berkeley.edu

# full tunnel — library / journal PDFs (all traffic via campus)
gp-saml-gui --allow-insecure-crypto -S -g campus.vpn.berkeley.edu
```

`--allow-insecure-crypto` is **required on 26.04** — see [Troubleshooting](#troubleshooting).
It is not needed on 24.04, but is harmless there.

Complete the CalNet/Duo login in the popup; `-S` then runs `sudo openconnect`
(prompts for sudo). It stays in the foreground with a `tun0` address — leave the
terminal open; **Ctrl-C** disconnects. The SAML session is cached in
`~/.gp-saml-gui-cookies`, so reconnects usually skip Duo.

Verify: `ip -br addr show tun0` and `getent hosts bearbuy.is.berkeley.edu`.

### Aliases

```bash
alias vpn='gp-saml-gui --allow-insecure-crypto -S -g campus-split.vpn.berkeley.edu'   # connect (split)
alias vpn-full='gp-saml-gui --allow-insecure-crypto -S -g campus.vpn.berkeley.edu'    # connect (full)
alias vpn-down='sudo pkill -INT openconnect'                                          # disconnect
```

### Troubleshooting

**`ModuleNotFoundError: No module named 'gp_saml_gui'` after a release upgrade.**
The `vpn` alias itself is fine; the pipx venv behind it is not. pipx venvs
symlink the system interpreter, so a release upgrade moves Python out from under
them — the 24.04 venv was built on Python 3.12 and 26.04 ships 3.14, leaving its
`site-packages` orphaned. Rebuild:

```bash
pipx reinstall-all          # or, for just this one:
pipx uninstall gp-saml-gui
pipx install --system-site-packages 'gp-saml-gui @ git+https://github.com/dlenski/gp-saml-gui.git'
```

Check with `gp-saml-gui --help` (should print usage) and `pipx list` (should
report the current `python3 --version`).

**`SSL error ... [SSL: UNSAFE_LEGACY_RENEGOTIATION_DISABLED]` on 26.04.**
Add `--allow-insecure-crypto`, as the aliases above do. Berkeley's GlobalProtect
gateways don't implement RFC 5746 secure renegotiation. OpenSSL used to tolerate
that: `SSL_OP_LEGACY_SERVER_CONNECT` was on by default through OpenSSL 3.1, and
24.04 shipped 3.0.13. OpenSSL 3.2 flipped the default off, so 26.04's 3.5.5
refuses the handshake and gp-saml-gui dies at `prelogin.esp`.

Despite the name, the flag does **not** disable certificate verification — that
only happens with `--no-verify`, which you should not need. It re-enables legacy
renegotiation, drops the cipher security level to 1, and passes
`--allow-insecure-crypto` through to openconnect. The server certificate is
still checked against the system CA store.
