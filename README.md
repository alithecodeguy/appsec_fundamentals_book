# Networking for Application Security Engineers

The API logs `200 OK`. The dispatcher’s browser reports “Failed to fetch.” Both observations can describe the same request. Which part of the system would you inspect next?

These 19 study notes are for web developers moving into application security and AppSec engineers reviewing how an application uses the network. They follow a fictional shipment dashboard from a dispatcher’s laptop through DNS, TLS, a CDN, and a private API. Along the way, you’ll read packet fields, HTTP exchanges, and application logs to work out who received a request, who authorized it, and what the browser could see.

**Start with [chapter 01](01-network-layers-and-devices.md)** for a first pass. If you already know the networking basics, go straight to the [browser security lab](10-browser-security.md#9-observe-the-decisions-on-loopback) to investigate the opening question. The [study checklist](appsec-networking-checklist.md) gives you a question to answer for each chapter.

## Chapter guide

| What do you want to understand? | Chapters |
| --- | --- |
| How does a laptop deliver a frame to the right neighbor? | [01 · Layers and devices](01-network-layers-and-devices.md) · [02 · IPv4, IPv6, and subnetting](02-ipv4-ipv6-and-subnetting.md) · [03 · Local networks](03-local-networks.md) |
| Which route, address, and listening socket does the client use? | [04 · Routing and NAT](04-routing-and-nat.md) · [05 · Ports, sockets, TCP, and UDP](05-ports-sockets-tcp-and-udp.md) · [06 · DNS and service discovery](06-dns.md) |
| What does HTTPS protect, and how does a proxy know where a request ends? | [07 · TLS, HTTPS, and certificates](07-tls-https-and-certificates.md) · [08 · URLs and HTTP semantics](08-urls-and-http-semantics.md) · [09 · HTTP versions and request boundaries](09-http-versions-and-request-boundaries.md) |
| When will the browser send a cookie, and when can JavaScript read the response? | [10 · Browser security](10-browser-security.md) · [11 · Cookies and credentials](11-cookies-and-credentials.md) |
| Which client address can the API trust, and which responses may a CDN reuse? | [12 · Proxies and gateways](12-proxies-and-gateways.md) · [13 · HTTP caching](13-http-caching.md) |
| Which destinations can a worker reach, and what enforces that limit? | [14 · Server-side requests and webhooks](14-server-side-requests-and-webhooks.md) · [15 · Firewalls and segmentation](15-firewalls-and-segmentation.md) · [16 · Cloud and container networking](16-cloud-and-container-networking.md) |
| What happens to subscriptions after access changes, or to a create request after a timeout? | [17 · Real-time connections](17-real-time-connections.md) · [18 · Availability and retries](18-availability-and-retries.md) |
| Where did a failed request stop, and which observation proves it? | [19 · Diagnosis and observability](19-diagnosis-and-observability.md) |

## Try the first exercise

Open the [single-frame exercise](01-network-layers-and-devices.md#6-practice-with-a-single-frame-offline). It includes a complete 75-byte Ethernet frame carrying a DNS query. Before decoding it, predict which device owns the destination MAC address and which service owns the destination IP address.

Copy the Python block into a file named `frame.py` and run it with Python 3.8 or later.

```sh
python3 frame.py
```

Look for three destinations in the output. The Ethernet frame goes to the local gateway, the IP packet goes to the DNS resolver at `192.0.2.53`, and the DNS question asks about `api.example.com`. The exercise runs offline using Python’s standard library.

## Using the labs

You can read every chapter on GitHub. To run an exercise, open its lab section for the code, tools, and expected observations. Each lab supplies its own starting data. The shell examples target macOS and Linux.

Depending on the chapter, you’ll use Python, the SQLite CLI, OpenSSL, Node.js, curl, or browser DevTools. Docker is optional for the container exercise in chapter 16. The [lab requirements](appsec-networking-checklist.md) list the versions.

When you’ve worked through the notes, try the [practical self-check](appsec-networking-checklist.md#a-practical-self-check) before opening the worked answers. Trace one signed-in shipment read end to end and identify the evidence that would distinguish a DNS failure, a TLS error, an HTTP denial, and a browser policy decision.
