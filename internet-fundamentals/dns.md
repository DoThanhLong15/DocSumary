# DNS (Domain Name System) – Overview and Key Concepts

## 1. What is DNS?

**DNS (Domain Name System)** is a distributed and hierarchical naming system used on the Internet to translate **human-readable domain names** into **IP addresses** that computers use to identify each other on the network.

Humans prefer domain names such as:

```
google.com
github.com
openai.com
```

But computers communicate using IP addresses:

```
142.250.190.14
140.82.114.3
```

DNS acts like the **phonebook of the Internet**, mapping domain names to IP addresses.

---

# 2. Why DNS is Important

Without DNS, users would have to remember IP addresses for every website.

DNS provides:

* Human-friendly domain names
* Scalable distributed architecture
* High availability
* Fast lookup via caching
* Load distribution capabilities

Example:

```
User enters: www.example.com
DNS resolves → 93.184.216.34
Browser connects to server using that IP
```

---

# 3. How DNS Works (Resolution Process)

When a user enters a domain name in the browser, a **DNS resolution process** begins.

## Step-by-Step DNS Lookup

```
User Browser
     │
     ▼
Recursive Resolver (ISP / Public DNS)
     │
     ▼
Root DNS Server
     │
     ▼
TLD DNS Server (.com, .org, .net)
     │
     ▼
Authoritative DNS Server
     │
     ▼
Returns IP Address
```

### Detailed Flow

1. User enters `www.example.com`
2. Browser checks **local DNS cache**
3. If not found → request sent to **Recursive Resolver**
4. Resolver queries **Root DNS Server**
5. Root server responds with **TLD server (.com)**
6. Resolver queries **TLD server**
7. TLD returns **Authoritative Name Server**
8. Resolver queries **Authoritative DNS**
9. Authoritative DNS returns **IP address**
10. Browser connects to the web server

---

# 4. DNS Architecture

DNS is designed as a **distributed hierarchical system**.

```
                Root DNS Servers
                       │
                       ▼
                TLD Name Servers
            (.com .org .net .vn)
                       │
                       ▼
            Authoritative Name Servers
                       │
                       ▼
                    Domains
                example.com
```

Hierarchy levels:

| Level               | Description                                  |
| ------------------- | -------------------------------------------- |
| Root Level          | Top of DNS hierarchy                         |
| TLD Level           | Domain extensions like `.com`, `.org`, `.vn` |
| Second-Level Domain | Domain registered by users                   |
| Subdomain           | Additional prefix like `www`, `api`          |

Example:

```
api.blog.example.com
```

Breakdown:

```
.com → TLD
example → second-level domain
blog → subdomain
api → sub-subdomain
```

---

# 5. DNS Servers Types

## 1. Recursive Resolver

A **recursive resolver** receives the DNS request from the client and performs the full lookup process.

Examples:

* ISP DNS
* Public DNS services

Popular public resolvers:

```
Google DNS → 8.8.8.8
Cloudflare DNS → 1.1.1.1
```

---

## 2. Root Name Servers

Root servers are the **top-level DNS servers**.

They do not know the exact IP of a domain but know where the **TLD servers** are located.

There are **13 logical root servers** worldwide.

Example:

```
a.root-servers.net
b.root-servers.net
```

---

## 3. TLD Name Servers

TLD servers manage **Top-Level Domains**.

Examples:

```
.com
.org
.net
.vn
.edu
```

They direct queries to the **authoritative DNS servers** of the domain.

---

## 4. Authoritative Name Servers

Authoritative servers store the **actual DNS records** for a domain.

Example:

```
example.com → 93.184.216.34
```

They provide the **final answer** in DNS resolution.

---

# 6. DNS Records

DNS records are entries stored in the authoritative DNS server.

## 1. A Record (Address Record)

Maps a domain to an **IPv4 address**.

Example:

```
example.com → 93.184.216.34
```

---

## 2. AAAA Record

Maps a domain to an **IPv6 address**.

Example:

```
example.com → 2606:2800:220:1:248:1893:25c8:1946
```

---

## 3. CNAME Record (Canonical Name)

Maps one domain name to another domain name.

Example:

```
www.example.com → example.com
```

Useful for aliasing.

---

## 4. MX Record (Mail Exchange)

Defines the mail server responsible for receiving emails.

Example:

```
example.com → mail.example.com
```

---

## 5. NS Record (Name Server)

Specifies which DNS servers are authoritative for a domain.

Example:

```
example.com → ns1.cloudflare.com
```

---

## 6. TXT Record

Stores arbitrary text data.

Common uses:

* Email verification
* Domain ownership verification
* SPF / DKIM records

Example:

```
v=spf1 include:_spf.google.com ~all
```

---

## 7. PTR Record

Used for **reverse DNS lookup**.

Maps an IP address back to a domain name.

Example:

```
8.8.8.8 → dns.google
```

---

# 7. DNS Caching

DNS caching improves performance by storing previous DNS queries.

Caches exist in multiple layers:

| Cache Location | Description                     |
| -------------- | ------------------------------- |
| Browser Cache  | Stored by the web browser       |
| OS Cache       | Stored in the operating system  |
| Resolver Cache | Stored by recursive DNS servers |

Example TTL:

```
TTL = 3600 seconds (1 hour)
```

After TTL expires, the resolver must query DNS again.

---

# 8. DNS TTL (Time To Live)

TTL specifies **how long a DNS record should be cached**.

Example:

```
A Record: example.com
TTL: 300 seconds
```

Meaning:

The record will be cached for **5 minutes**.

Lower TTL → faster updates but more DNS queries.

Higher TTL → better performance but slower updates.

---

# 9. Reverse DNS

Reverse DNS maps **IP addresses to domain names**.

Example:

```
8.8.8.8 → dns.google
```

Uses **PTR records**.

Common use cases:

* Email spam filtering
* Server identity verification
* Network troubleshooting

---

# 10. DNS Security

DNS was originally designed without security.

Several solutions were later introduced.

## DNSSEC (DNS Security Extensions)

DNSSEC ensures:

* Data authenticity
* Integrity of DNS responses
* Protection against DNS spoofing

It uses **cryptographic signatures** to validate DNS responses.

---

# 11. Common DNS Attacks

## 1. DNS Spoofing

Attackers return a **fake IP address**.

Example:

```
bank.com → attacker-server.com
```

---

## 2. DNS Cache Poisoning

Attackers inject malicious records into DNS cache.

---

## 3. DDoS on DNS

Attackers overload DNS servers to make domains unreachable.

---

# 12. Public DNS Providers

Some popular public DNS providers include:

| Provider       | DNS Address    |
| -------------- | -------------- |
| Google DNS     | 8.8.8.8        |
| Cloudflare DNS | 1.1.1.1        |
| OpenDNS        | 208.67.222.222 |

Benefits:

* Faster resolution
* Better privacy
* Higher availability

---

# 13. Example DNS Resolution

Example query:

```
User visits: www.example.com
```

DNS flow:

```
Browser
  ↓
Recursive Resolver
  ↓
Root Server
  ↓
TLD Server (.com)
  ↓
Authoritative Server
  ↓
Return IP Address
```

Result:

```
www.example.com → 93.184.216.34
```

Browser connects to that IP to retrieve the website.

---

# 14. Summary

DNS is a **core infrastructure of the Internet** responsible for translating domain names into IP addresses.

Key components:

* Recursive Resolvers
* Root Servers
* TLD Servers
* Authoritative Servers
* DNS Records
* DNS Caching
* DNS Security (DNSSEC)

Without DNS, the Internet would be far harder for humans to use because users would need to remember numeric IP addresses instead of simple domain names.
