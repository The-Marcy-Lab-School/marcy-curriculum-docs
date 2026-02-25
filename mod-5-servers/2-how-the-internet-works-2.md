# 2. How the Internet Works, Part 2

{% hint style="info" %}
Watch this video before lecture: [HTTP and the Web Explained](https://www.youtube.com/watch?v=0QI6I6APomE)
{% endhint %}

{% embed url="https://www.youtube.com/watch?v=0QI6I6APomE" %}

In Part 1, we learned that the internet is physical infrastructure and that HTTP is the protocol clients and servers use to communicate. Now we'll go deeper: how does a URL like `https://www.google.com` actually resolve to a specific server? How does data find its way across the world to the right machine?

**Table of Contents:**

- [Essential Questions](#essential-questions)
- [Terms](#terms)
- [IP Addresses](#ip-addresses)
  - [IPv4](#ipv4)
  - [IPv6](#ipv6)
- [Domain Names](#domain-names)
  - [Anatomy of a Domain Name](#anatomy-of-a-domain-name)
  - [Types of Top-Level Domains](#types-of-top-level-domains)
- [DNS: The Internet's Phone Book](#dns-the-internets-phone-book)
  - [The Full HTTP Cycle with DNS](#the-full-http-cycle-with-dns)
- [How Data Finds Its Way: Routing](#how-data-finds-its-way-routing)
  - [Hierarchical Addressing](#hierarchical-addressing)
  - [Putting It All Together: The Letter Analogy](#putting-it-all-together-the-letter-analogy)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is an IP address? What problem does it solve?
2. What is the difference between IPv4 and IPv6? Why did IPv6 become necessary?
3. What is a domain name? How does it relate to an IP address?
4. What is DNS? What happens when you type a URL into your browser?
5. How do routers know where to send data packets?
6. If you had to explain the full journey of an HTTP request to someone using only a physical mail analogy, what would the analogy be?

## Key Concepts

* **IP Address** — a unique numerical label assigned to each device on a network, used to identify and locate it. Like a mailing address for your computer.
* **IPv4** — the original version of IP addresses, using four numbers separated by dots (e.g., `192.168.1.1`). Limited to about 4.3 billion addresses.
* **IPv6** — the newer version of IP addresses, using eight groups of hexadecimal numbers (e.g., `2001:0db8:85a3::8a2e:0370:7334`). Supports a virtually unlimited number of addresses.
* **Domain Name** — a human-readable name (like `google.com`) that maps to an IP address.
* **Top-Level Domain (TLD)** — the last part of a domain name (`.com`, `.org`, `.edu`, `.io`).
* **DNS (Domain Name System)** — the distributed system of servers that translates domain names into IP addresses.
* **DNS Server** — a server that stores domain name → IP address mappings and resolves queries.
* **DNS Resolution** — the process of looking up a domain name and finding the corresponding IP address.
* **ISP (Internet Service Provider)** — the company that connects you to the internet (e.g., Comcast, Verizon, AT&T).
* **Packet** — a small unit of data. Long messages are broken into packets, sent independently across the network, and reassembled at the destination.

## IP Addresses

Every device connected to the internet has an **IP address** — a unique identifier that works like a mailing address. When your computer sends a request to a server, it needs to know the server's IP address to deliver the data to the right place.

### IPv4

The original IP address format is **IPv4**, which looks like four numbers separated by periods:

```
192.168.1.1
142.250.80.46    ← one of Google's IP addresses
104.21.10.49
```

Each number is between 0 and 255 (one byte), giving IPv4 about **4.3 billion** possible addresses. That sounds like a lot, but with billions of smartphones, computers, tablets, smart TVs, and IoT devices all needing addresses, we ran out.

### IPv6

**IPv6** was developed to solve this shortage. IPv6 addresses look like eight groups of hexadecimal characters separated by colons:

```
2001:0db8:85a3:0000:0000:8a2e:0370:7334
```

Leading zeros can be omitted and consecutive groups of zeros can be shortened to `::`:

```
2001:db8:85a3::8a2e:370:7334
```

IPv6 supports approximately **340 undecillion** addresses (3.4 × 10³⁸). That's enough to give every grain of sand on Earth its own IP address — many times over.

<details>

<summary><strong>Q: Why can't we just add more IPv4 addresses?</strong></summary>

IPv4 uses 32 bits to represent an address (4 numbers × 8 bits each). The format is fixed—you can't just "add more" without changing the protocol itself. IPv6 uses 128 bits, which is an incomprehensibly larger address space. The internet has been slowly migrating to IPv6 for years, though many systems still run IPv4 internally and use workarounds like NAT (Network Address Translation) to share a single public IP across many devices.

</details>

<details>

<summary><strong>Q: Try it: What is your computer's IP address?</strong></summary>

Run the following in your terminal:

```sh
# On Mac/Linux
curl ifconfig.me

# Or
ipconfig getifaddr en0
```

The first command shows your *public* IP (what the internet sees). The second shows your *local* IP on your home network (usually starts with `192.168.` for IPv4). These are different because your router gives all your home devices local IP addresses, but they all share one public IP when accessing the internet.

</details>

## Domain Names

IP addresses uniquely identify servers, but they're hard to remember. Nobody wants to type `142.250.80.46` every time they want to visit Google. That's where **domain names** come in.

A **domain name** is a human-readable label (like `google.com` or `github.com`) that maps to an IP address. Domain names were invented so that humans could navigate the web using memorable words instead of number strings.

### Anatomy of a Domain Name

Take the URL: `https://www.google.com/search?q=http`

| Part      | Example   | What it is                                   |
| --------- | --------- | -------------------------------------------- |
| Protocol  | `https`   | How to communicate (HTTP with encryption)    |
| Subdomain | `www`     | A subdivision of the domain (often optional) |
| Domain    | `google`  | The name registered by the owner             |
| TLD       | `.com`    | Top-Level Domain                             |
| Path      | `/search` | The specific page or endpoint                |
| Query     | `?q=http` | Query parameters passed to the server        |

### Types of Top-Level Domains

The **Top-Level Domain (TLD)** is the last segment of a domain name. There are three main categories:

**Country Code TLDs (ccTLDs)** — reserved for specific countries:
* `.uk` — United Kingdom
* `.jp` — Japan
* `.io` — British Indian Ocean Territory (popular with tech companies)
* `.ai` — Anguilla (popular with AI companies)

**Generic TLDs (gTLDs)** — not tied to a country:
* `.com` — originally "commercial," now the most common
* `.org` — originally "organizations"
* `.edu` — U.S. educational institutions
* `.gov` — U.S. government

**New TLDs** — introduced more recently as the original TLDs filled up:
* `.app`, `.dev`, `.tech`, `.school`

<details>

<summary><strong>Q: Why do many tech startups choose <code>.io</code> as their TLD even though it's technically assigned to a British territory?</strong></summary>

`.io` was originally the ccTLD for the British Indian Ocean Territory, but the tech community adopted it because "IO" can be interpreted as "input/output" — a fundamental computing concept. It became fashionable in the startup world, and many companies use it even though they have no connection to the British Indian Ocean Territory. Domain registration doesn't require you to be from the country a ccTLD is assigned to.

</details>

## DNS: The Internet's Phone Book

Here's the problem: browsers communicate using IP addresses, but users type domain names. Something needs to translate `google.com` → `142.250.80.46`.

That something is the **Domain Name System (DNS)** — a distributed global system of servers that act like a phone book for the internet. When you look up a name in a phone book, you get a number. DNS does the same: look up a domain name, get an IP address.

**DNS servers** store records that map domain names to IP addresses. There are millions of DNS servers distributed around the world, organized in a hierarchy.

### The Full HTTP Cycle with DNS

Here's what actually happens when you type `https://www.github.com` into your browser and press Enter:

```
1. Browser checks its own DNS cache
   → "Have I looked up github.com recently? Do I already have the IP?"

2. If not cached: Query sent to your ISP's DNS resolver
   → "Hey, what's the IP address for www.github.com?"

3. ISP's DNS resolver checks its own cache
   → If found, returns the IP to your browser

4. If not cached: ISP's resolver queries a Root Name Server
   → "Who handles .com domains?"
   ← Root server: "Ask the .com TLD server"

5. ISP's resolver queries the .com TLD Name Server
   → "Who handles github.com?"
   ← .com server: "Ask GitHub's authoritative name server"

6. ISP's resolver queries GitHub's Authoritative Name Server
   → "What's the IP for www.github.com?"
   ← GitHub's server: "140.82.114.3"

7. ISP's resolver returns the IP to your browser and caches it

8. Browser sends an HTTP request to 140.82.114.3
   → GET / HTTP/1.1
   → Host: www.github.com

9. GitHub's server receives the request and sends back a response
   ← 200 OK + HTML/CSS/JS

10. Browser renders the page
```

{% hint style="info" %}
Steps 3–6 (the DNS resolution) typically happen in milliseconds. Results are cached at multiple levels so that future requests for the same domain are nearly instant.
{% endhint %}

<details>

<summary><strong>Q: What would happen if all DNS servers went offline?</strong></summary>

You could still access websites by typing their IP address directly (e.g., `http://140.82.114.3` for GitHub). But since most people don't know IP addresses off the top of their head, the web would effectively become inaccessible for most users. This is why DNS infrastructure is considered critical internet infrastructure—major DNS outages (like the Dyn DDoS attack in 2016) have taken down large portions of the web.

</details>

<details>

<summary><strong>Q: What does it mean for a DNS result to be "cached"? Why does caching DNS matter?</strong></summary>

Caching means storing the result of a lookup so you don't have to repeat it. Once your browser has resolved `github.com → 140.82.114.3`, it saves that result for a period of time (defined by the TTL — "time to live" — in the DNS record). Future requests for `github.com` skip the DNS lookup entirely and go straight to the IP. This makes browsing much faster. When companies update their server's IP address, they must wait for DNS caches around the world to expire before all users reach the new server—this delay is called "DNS propagation."

</details>

## How Data Finds Its Way: Routing

Now we know how a domain name becomes an IP address. But once the browser knows the server's IP, how does it actually *get there*? The internet spans continents—how does data navigate from your home to a server in another country?

### Hierarchical Addressing

IP addresses are structured hierarchically, which makes routing efficient. Just as a mailing address goes from country → state → city → street → house number, IP addresses encode location information that routers can use to make forwarding decisions.

Each router along the path doesn't need to know the full map of the internet. It just needs to know which direction to forward the packet toward the destination — like a relay race where each runner only knows to hand the baton to the next person.

```
Your computer
    ↓
Your home router
    ↓
Your ISP's network
    ↓
Regional network
    ↓
Internet backbone
    ↓
Destination country's network
    ↓
Destination ISP
    ↓
Destination server
```

<details>

<summary><strong>Q: Your data might travel through 10–20 routers between you and a server. Why doesn't this take a very long time?</strong></summary>

Each router hop takes microseconds — millionths of a second. The actual data travels through fiber optic cables at nearly the speed of light. Even a request that crosses multiple continents typically takes under 200 milliseconds. The part that takes time isn't the routing — it's the time for the server to process the request and generate a response, and the time to transfer large amounts of data.

You can see the routers your data passes through using `traceroute` (Mac/Linux) or `tracert` (Windows) in your terminal.

</details>

### Putting It All Together: The Letter Analogy

The best way to internalize this is through analogy. Sending an HTTP request is remarkably similar to sending a physical letter.

| Physical Mail                   | HTTP Request                    |
| ------------------------------- | ------------------------------- |
| Your home address               | Your IP address (client)        |
| Recipient's address             | Server's IP address             |
| Recipient's name                | Domain name (like `google.com`) |
| Address book / phone book       | DNS server                      |
| Post office                     | Your ISP                        |
| Postal sorting facilities       | Routers                         |
| The letter                      | HTTP request                    |
| The reply                       | HTTP response                   |
| Envelope                        | IP packet                       |
| Postage stamps / routing labels | IP headers                      |

When you send a letter:
1. You write the recipient's name and look up their address (DNS resolution)
2. You put the letter in an envelope with both addresses on it (IP packet)
3. You take it to the post office (your ISP)
4. Sorting facilities read the destination and pass it to the right carrier (routers)
5. The letter arrives at the destination (server receives request)
6. The recipient writes back and mails a reply (server sends response)
7. The reply travels back through the postal system and arrives at your door (response reaches your browser)

<details>

<summary><strong>Q: In the letter analogy, what does the "return address" represent in an HTTP request, and why is it necessary?</strong></summary>

The return address represents your IP address. It's included in every packet so the server knows *where to send the response*. Without it, the server could receive your request but have no way to send data back. IP packets always include a source address and a destination address for exactly this reason.

</details>

<details>

<summary><strong>Q: Trace the full journey from typing <code>https://api.github.com/users/octocat</code> in your browser to receiving a response. Include DNS, routing, HTTP, and the response.</strong></summary>

1. Browser parses the URL: protocol = `https`, domain = `api.github.com`, path = `/users/octocat`
2. Browser checks DNS cache for `api.github.com` — if not cached, queries DNS resolver
3. DNS resolver performs lookup: `api.github.com → [IP address]`
4. Browser opens a TCP connection to that IP address (with TLS/SSL for HTTPS)
5. Browser sends HTTP request: `GET /users/octocat HTTP/1.1` with headers including `Host: api.github.com`
6. Request travels through your router → ISP → backbone → GitHub's network → GitHub's server
7. GitHub's server receives the request, looks up the user "octocat" in its database
8. Server constructs a response: status `200 OK`, headers including `Content-Type: application/json`, body with JSON data about the user
9. Response travels back through the network to your browser
10. Browser receives the response and (since you typed this in the address bar) displays the raw JSON

</details>
