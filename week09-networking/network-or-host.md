# Week 9: Network or host? My troubleshooting card

Verified in the CIS226-Lab Ubuntu WSL2 distribution. The local WSL session replaces the LabEx VM and Codespace for this exercise.

## The four failures I saw

```text
curl: (6) Could not resolve host: no-such-site.invalid
curl: (7) Failed to connect to localhost port 9999 after 0 ms: Could not connect to server
curl: (28) Connection timed out after 5002 milliseconds
HTTP/1.0 404 File not found
```

## What each one means

- Could not resolve: a DNS/name-resolution problem. Check the spelling and DNS settings before checking the web server.
- Connection failure on localhost: a host-side problem in this test because no service was listening on port 9999. Check the process and listening address with `ss -tlnp`.
- Timed out: start with the network path, routing, and filtering. A timeout alone does not identify exactly where traffic was dropped.
- 404: the web server answered, but the requested file was missing. Check the URL and files in the document root using the Week 2 file-management skills.

## How it works

`127.0.0.1` is loopback. A listener bound to it accepts local loopback connections, but not connections addressed to the WSL network interface. With the site bound to `127.0.0.1:8000`, localhost returned 200 and the WSL IP failed. After restarting on `0.0.0.0:8000`, the service listened on all IPv4 interfaces and the WSL IP returned 200. This proves the lab fault was the host's bind address; it does not prove that a Windows firewall or router allows outside Internet access.

The inventory server stayed on loopback port 8080. SSH forwarded local loopback port 9090 to the server's loopback port 8080. The page loaded through the tunnel, and port 9090 failed again after the tunnel closed.

Both three-packet ping tests had 0% loss. The example.com test also reported one duplicate reply. UDP traceroute reached example.com at hop 9 after eight nonresponding hops.

The intern belonged to `otterstaff`, but `credentials.txt` was owned by `cis226:otterstaff` with mode 600, so the group still had no read permission. The backup round trip passed `cmp`. Both endpoints were on the same WSL machine for practice; a real backup should also go to a separate off-site system.
