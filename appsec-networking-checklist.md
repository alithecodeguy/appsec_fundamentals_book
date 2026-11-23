# Networking for Application Security Engineers

This checklist covers web applications, APIs, and the services behind them. For each topic, mark it complete when you can answer the question using a real application and explain what evidence supports your answer.

## 1. Network foundations

- [ ] **Network layers and devices.** Use the TCP/IP and OSI models to follow an application message through a TCP stream or UDP datagram, an IP packet, and a local network frame. Know the roles of interfaces, switches, routers, and gateways. *When a browser calls an API, which device forwards packets and which component reads the HTTP request?*
- [ ] **IPv4, IPv6, and subnetting.** Read CIDR prefixes such as `10.20.0.0/16` and `2001:db8:1::/64`. Recognize public, private or unique-local, loopback, and link-local addresses. Understand dual-stack deployments. *Does a network rule cover the addresses the service uses over both IP versions?*
- [ ] **Local networks.** Learn Ethernet and Wi-Fi at the link layer, MAC addresses, switches, VLANs, ARP for IPv4, Neighbor Discovery for IPv6, and automatic configuration through DHCP or IPv6 SLAAC. *How does a host find the next device to send a packet to?*
- [ ] **Routing and address translation.** Read a routing table, a default route, and a more-specific route. Understand NAT, port address translation, VPN tunnels, ICMP, packet loss, and the maximum transmission unit (MTU). *Which route does an outbound connection take, and what source address will the destination see?*
- [ ] **Ports, sockets, TCP, and UDP.** Know listening and ephemeral ports and common service defaults such as DNS (53), HTTP (80), HTTPS (443), and SSH (22). Learn socket binding, TCP setup and teardown, ordered byte streams, retransmission, flow control, congestion control, and UDP datagram delivery. *If an API listens on `127.0.0.1`, who can reach that listener directly? What else determines reachability if it listens on `0.0.0.0`?*
- [ ] **DNS and service discovery.** Learn recursive resolvers, authoritative nameservers, delegation, A/AAAA, CNAME, NS, MX, and TXT records. Understand TTLs, negative caching, DNS over UDP and TCP, split DNS, and the risk of leaving an alias pointed at a retired service. *Why might a developer’s laptop and the production API resolve the same name to different addresses?*

## 2. Web and API networking to know in depth

- [ ] **TLS, HTTPS, and certificates.** Understand how a client authenticates a server and how the TLS handshake establishes keys to protect data in transit. Learn TLS 1.2/1.3, certificate authorities, trust stores, certificate chains, hostname verification, expiration, and how mutual TLS (mTLS) also authenticates the client. Recognize SNI for the requested server name and ALPN for application protocol negotiation. *If HTTPS terminates at a CDN, what protects the next connection to the application?*
- [ ] **URLs and HTTP semantics.** Read the scheme, authority, path, query, and fragment of a URL. Understand percent encoding, normalization, methods, headers, bodies, status codes, redirects, content types, and safe and idempotent methods. *Which parts of a browser URL reach the server, and which component uses the host and path to route the request?*
- [ ] **HTTP versions and message boundaries.** Learn how HTTP/1.1 uses content length or chunked transfer coding to delimit request bodies, how HTTP/2 multiplexes streams over TCP, and how HTTP/3 uses QUIC over UDP. Understand protocol translation between an edge proxy and the backend. *Do both components agree on where each request ends?* This matters when preventing request smuggling.
- [ ] **Browser origins and cross-origin requests.** Know origin (scheme, host, and port), site (scheme and registrable domain for ordinary web URLs), the same-origin policy, CORS, preflight requests, and credentialed `fetch`. Recognize mixed-content rules, HSTS, and CSP’s `connect-src`. *Can the browser send this request? Can JavaScript read its response? Answer each separately.*
- [ ] **Cookies, sessions, and API credentials.** Understand host-only and domain cookies, `Path`, `Secure`, `HttpOnly`, `SameSite`, bearer tokens in the `Authorization` header, and CSRF protection for cookie-authenticated actions. *Which credential accompanies this request, and where does the API check that this user may access this particular record?*
- [ ] **Proxies and trust boundaries.** Know forward and reverse proxies, layer 4 and layer 7 load balancers, CDNs, web application firewalls (WAFs), and API gateways. Trace TLS termination, the connection to the origin, and the provenance of `Host`, `Forwarded`, and `X-Forwarded-For`. *Which proxy sets the client address used for rate limiting, and can traffic reach the backend by any other route?*
- [ ] **HTTP caching and private data.** Understand browser caches, shared CDN caches, cache keys, freshness, revalidation, `ETag`, `Vary`, and `Cache-Control` directives such as `private`, `no-cache`, and `no-store`. *May a shared cache store the signed-in user’s response? Under what conditions could it reuse a stored response?*
- [ ] **Server-side HTTP, webhooks, and SSRF prevention.** Follow outbound calls made by URL previews, importers, and webhook workers. Understand URL parsing, DNS resolution, redirects, IP address scope, cloud metadata services, and outbound policy. For incoming webhooks, learn sender verification and replay protection. *Which destinations does a worker need, and where do the application and network enforce that policy?*

## 3. Production skills

- [ ] **Segmentation, firewalls, and service identity.** Understand inbound and outbound rules, stateful and stateless filters, network ACLs, segmentation, and service-to-service authentication. Read a policy in terms of source, destination, protocol, port, and direction. *Which workloads can connect to the database, and how does the database authenticate the caller?*
- [ ] **Cloud and container networking.** Learn virtual networks (VPCs/VNets), public and private subnets, route tables, security groups, NAT gateways, private endpoints, container network namespaces, and published ports. *What does `localhost` refer to from the application process, and which configuration actually exposes the service?*
- [ ] **Real-time connections.** Know polling, Server-Sent Events (SSE), and WebSockets. For WebSockets, review `wss`, browser `Origin` validation, session expiry, authorization for subscriptions and actions, message size, connection limits, and backpressure. *What happens to a live subscription when the user loses access to its data?*
- [ ] **Availability, timeouts, and retries.** Distinguish bandwidth exhaustion from expensive application work. Understand latency, connection and read timeouts, deadlines, rate and concurrency limits, request-size limits, and bounded retries with backoff and jitter. Know how idempotency affects retries. *If a create request times out after the database commits it, what prevents a retry from creating a duplicate?*
- [ ] **Network evidence and diagnosis.** Correlate browser timing, DNS answers, TLS details, socket state, packet captures, flow logs, proxy logs, and application logs. Understand what each observation point can see across an encrypted connection. *Can you distinguish DNS failure, connection refusal, a TLS validation error, an HTTP 403, and a response blocked by browser policy?*

### Tools worth using yourself

| Question | Tool or evidence |
| --- | --- |
| What did the browser send, and what did it receive? | Browser DevTools Network panel for headers, cookies, redirects, timing, and WebSocket messages. |
| Which records did this resolver return? | `dig` for DNS answers, aliases, and TTLs. |
| What HTTP response does a client outside the browser receive? | `curl` for status, headers, redirects, and timing. Use browser DevTools for browser policy decisions. |
| Which certificate chain did the endpoint present? | `openssl s_client` or a browser certificate viewer. Check the expected hostname and trust chain. |
| Which process is listening, and on which address and port? | `ss` on Linux or `lsof` on macOS. Inspect the routing table when the destination is on another network. |
| How far did the connection or request get? | Wireshark or `tcpdump` for packet evidence. Use gateway and application logs with a request ID to locate the HTTP decision. |

## 4. Add the topics your environment uses

| Environment | Topics to add |
| --- | --- |
| Kubernetes and microservices | Pod networking, cluster DNS, Services, Ingress or Gateway API, NetworkPolicy enforcement, service meshes, workload identity, and gRPC over HTTP/2. |
| Enterprise and hybrid networks | SSH, bastion hosts, VPN routing and split tunneling, IPsec or WireGuard, Kerberos, LDAP, SMB, and RDP. |
| Email and transactional messaging | SMTP, STARTTLS, MX records, SPF, DKIM, and DMARC alignment and policy. |
| DNS and internet-facing infrastructure | DNSSEC validation, DNS over HTTPS or TLS, CAA, anycast, peering, and routing protocols such as BGP and OSPF. |
| Mobile, media, or connected devices | Platform trust stores and certificate rotation for mobile apps, ICE/STUN/TURN for WebRTC, and MQTT or CoAP for devices. |

## A practical self-check

A signed-in shipment dashboard at `https://app.example.com` reads `https://api.example.com/shipments` through a CDN, then opens a WebSocket for updates.

1. Explain why the two URLs are same-site and cross-origin. Name the settings that determine whether the API receives a cookie and whether the frontend can read the response.
2. Draw the connections from browser to CDN to API. Label where DNS is used, where TLS ends, and which component can read the HTTP headers on each connection.
3. Identify who authorizes access to each shipment, which cache may store the list, and what happens to a subscription when the user’s permissions change.
4. If the page fails to load its data, name the first piece of evidence you would collect to distinguish a DNS, TLS, HTTP, or browser-policy failure.

For the first question, both URLs use HTTPS and share the registrable domain `example.com`, so they are same-site. Their hostnames differ, so they are cross-origin. With this cross-origin `fetch`, `credentials: "include"` lets the browser send cookies that match the API and satisfy its cookie policies. The API’s CORS response headers determine whether the browser exposes the credentialed response to the frontend. The API authorizes access to each shipment.

## References for study

| Subject | Reference |
| --- | --- |
| IP address ranges and TCP | [IANA IPv4 special-purpose addresses](https://www.iana.org/assignments/iana-ipv4-special-registry/iana-ipv4-special-registry.xhtml) · [IANA IPv6 special-purpose addresses](https://www.iana.org/assignments/iana-ipv6-special-registry/iana-ipv6-special-registry.xhtml) · [IETF TCP specification](https://www.rfc-editor.org/rfc/rfc9293.html) |
| DNS | [Cloudflare’s illustrated introduction to DNS](https://www.cloudflare.com/learning/dns/what-is-dns/) · [IETF DNS terminology](https://www.rfc-editor.org/rfc/rfc9499.html) |
| HTTPS | [OWASP Transport Layer Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Security_Cheat_Sheet.html) |
| HTTP and caching | [IETF HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html) · [IETF HTTP/1.1 message framing](https://www.rfc-editor.org/rfc/rfc9112.html#section-6) · [MDN HTTP caching](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Caching) |
| Browsers and proxies | [MDN CORS guide](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS) · [MDN cookie guide](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Cookies) · [MDN forwarded client addresses](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/X-Forwarded-For) |
| AppSec design reviews | [OWASP SSRF prevention](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html) · [OWASP WebSocket security](https://cheatsheetseries.owasp.org/cheatsheets/WebSocket_Security_Cheat_Sheet.html) · [OWASP API resource consumption](https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/) |
| Infrastructure | [AWS VPC networking](https://docs.aws.amazon.com/vpc/latest/userguide/how-it-works.html) · [Kubernetes networking](https://kubernetes.io/docs/concepts/services-networking/) · [NIST Zero Trust Architecture](https://csrc.nist.gov/pubs/sp/800/207/final) |
| Troubleshooting | [Chrome DevTools Network reference](https://developer.chrome.com/docs/devtools/network/reference) |
