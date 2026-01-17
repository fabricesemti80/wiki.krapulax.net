# DNS Troubleshooting on macOS

## 1. Find Active Network Interface

Use `ifconfig` to list all interfaces and identify the active one (look for `status: active` and an `inet` IP address):

```
ifconfig
```

Common interfaces:
- `en0`: Wi-Fi
- `en1`, `en10`, etc.: Ethernet
- `bridge100`: VM bridge

## 2. Check Current DNS Servers

Check the DNS configuration:

```
scutil --dns
```

Look for resolvers with your network's DNS servers (e.g., `10.0.40.1`).

## 3. Renew DHCP Lease (if DNS not updating)

Renew DHCP to pick up new DNS settings from the router:

```
sudo ipconfig set <interface> DHCP
```

Replace `<interface>` with the active interface (e.g., `en10`).

## 4. Flush DNS Cache

Flush the DNS cache (Big Sur and later):

```
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder
```

This clears the local DNS resolver cache and restarts the mDNSResponder service.

## 5. Test DNS Resolution

Test a specific DNS server and record:

```
dig @<dns_server> <domain>
```

Example:

```
dig @10.0.40.1 media.krapulax.home
```
