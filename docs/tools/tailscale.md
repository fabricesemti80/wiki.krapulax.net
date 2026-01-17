# Tailscale quick install

```sh
tailscale up --authkey=<authkey> --advertise-routes=10.0.0.0/16,192.168.0.0/16 --ssh --advertise-exit-node
```

**IMPORTANT**

When using device as a router, forwarding is also a must!

```sh
        echo "Enabling IP forwarding..."
        echo 'net.ipv4.ip_forward = 1' | tee -a /etc/sysctl.d/99-tailscale.conf
        echo 'net.ipv6.conf.all.forwarding = 1' | tee -a /etc/sysctl.d/99-tailscale.conf
        sysctl -p /etc/sysctl.d/99-tailscale.conf
```

## Switch Options

*   `--accept-dns`: Accept DNS configuration from the admin console. Defaults to accepting DNS settings.
*   `--accept-risk=<risk>`: Accept risk and skip confirmation for risk type. This can be either `lose-ssh` or `all`, or an empty string to not accept risk.
*   `--accept-routes`: Accept subnet routes that other nodes advertise. Linux devices default to not accepting routes.
*   `--advertise-connector`: Advertise this node as an app connector.
*   `--advertise-exit-node`: Offer to be an exit node for outbound internet traffic from the Tailscale network. Defaults to not offering to be an exit node.
*   `--advertise-routes=<ip>`: Expose physical subnet routes to your entire Tailscale network.
*   `--advertise-tags=<tags>`: Give tagged permissions to this device. You must be listed in `"TagOwners"` to be able to apply tags.
*   `--auth-key=<key>`: Provide an auth key to automatically authenticate the node as your user account.
*   `--client-id`: Client ID used to generate auth keys via workload identity federation.
*   `--client-secret`: OAuth Client secret used to generate auth keys; if it begins with `file:`, then it's a path to a file containing the secret.
*   `--exit-node=<ip|name>`: Provide a Tailscale IP or machine name to use as an exit node. You can also use `--exit-node=auto:any` to track the suggested exit node and automatically switch to it when available exit nodes or network conditions change. To disable the use of an exit node, pass the flag with an empty argument using `--exit-node=`.
*   `--exit-node-allow-lan-access`: Allow the client node access to its own LAN while connected to an exit node. Defaults to not allowing access while connected to an exit node.
*   `--force-reauth`: Force re-authentication.
*   `--hostname=<name>`: Provide a hostname to use for the device instead of the one provided by the OS. Note that this will change the machine name used in MagicDNS.
*   `--id-token`: ID token from the identity provider to exchange with the control server for workload identity federation; if it begins with `file:`, then it's a path to a file containing the token.
*   `--json`: Output in JSON format. Format is subject to change.
*   `--login-server=<url>`: Provide the base URL of a control server instead of `https://controlplane.tailscale.com`. If you are using Headscale for your control server, use your Headscale instance's URL.
*   `--netfilter-mode`: (Linux only) Advanced feature for controlling the degree of automatic firewall configuration. Values are either "off", "nodivert", or "on". Defaults to "on", except for Synology which defaults to "off".
*   `--operator=<user>`: Provide a Unix username other than `root` to operate `tailscaled`.
*   `--qr`: Generate a QR code for the web login URL. Defaults to not showing a QR code.
*   `--qr-format=<format>`: QR code formatting: `small` or `large`. Defaults to `small`.
*   `--reset`: Reset unspecified settings to default values.
*   `--shields-up`: Block incoming connections from other devices on your Tailscale network. Useful for personal devices that only make outgoing connections.
*   `--snat-subnet-routes`: (Linux only) Source NAT traffic to local routes that are advertised with `--advertise-routes`. Defaults to sourcing the NAT traffic to the advertised routes. Set to false to disable subnet route masquerading.
*   `--stateful-filtering`: (Linux only) Enable stateful filtering for subnet routers and exit nodes.
*   `--ssh`: Run a Tailscale SSH server.
*   `--timeout=<duration>`: Maximum amount of time to wait for the Tailscale service to initialize. `duration` can be any value parseable by `time.ParseDuration()`. Defaults to `0s`, which blocks forever.
*   `--unattended`: (Windows only) Run in unattended mode where Tailscale keeps running even after the current user logs out.