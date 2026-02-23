# 1. How the Internet Works, Part 1

{% hint style="info" %}
Watch this video before lecture: [How does the Internet Work?](https://www.youtube.com/watch?v=wW2A5SZ3GkI)
{% endhint %}

Before we write a single line of server code, we need to understand the environment our servers live in. The internet is the infrastructure that makes everything we'll build possible. This lesson traces how data physically travels around the world and how the rules governing that travel—HTTP—shape the applications we build.

**Table of Contents:**

* [Essential Questions](1-how-the-internet-works-1.md#essential-questions)
* [Terms](1-how-the-internet-works-1.md#terms)
* [The Physical Internet](1-how-the-internet-works-1.md#the-physical-internet)
  * [Routers and Networks](1-how-the-internet-works-1.md#routers-and-networks)
  * [Local Area Networks (LANs)](1-how-the-internet-works-1.md#local-area-networks-lans)
  * [The World Wide Web](1-how-the-internet-works-1.md#the-world-wide-web)
  * [Submarine Cables and the Internet Backbone](1-how-the-internet-works-1.md#submarine-cables-and-the-internet-backbone)
* [HTTP: The Language of the Web](1-how-the-internet-works-1.md#http-the-language-of-the-web)
  * [The Client-Server Model](1-how-the-internet-works-1.md#the-client-server-model)
  * [The Request-Response Cycle](1-how-the-internet-works-1.md#the-request-response-cycle)
  * [HTTP Status Codes](1-how-the-internet-works-1.md#http-status-codes)
  * [HTTP Headers](1-how-the-internet-works-1.md#http-headers)

## Essential Questions

By the end of this lesson, you should be able to answer these questions:

1. What is the internet? How is data physically transmitted around the world?
2. What is the difference between the internet and the World Wide Web?
3. What is a LAN? How does your home network connect to the broader internet?
4. What is HTTP? Why do clients and servers need an agreed-upon protocol?
5. What happens during the request-response cycle?
6. What are HTTP status codes and what do they communicate?
7. What are HTTP headers? What kinds of information do they carry?

## Terms

* **Internet** — a global network of interconnected computers that communicate using standardized protocols.
* **Router** — a device that directs (routes) data packets from one network to another on their way to a destination.
* **Local Area Network (LAN)** — a network of devices connected together in a limited area, like your home or office Wi-Fi network.
* **World Wide Web (WWW)** — a system of documents and resources linked by URLs and accessed via HTTP. The Web is one service that runs *on top of* the internet.
* **Internet Backbone** — the high-speed data routes that carry the majority of internet traffic between major networks and continents.
* **Submarine Cable** — underwater fiber optic cables that carry internet data between continents.
* **Protocol** — an agreed-upon set of rules for how data should be formatted, transmitted, and received.
* **HTTP (HyperText Transfer Protocol)** — the protocol that governs how clients and servers communicate on the web.
* **Client** — any device or program that sends requests to a server (browser, mobile app, your JavaScript `fetch()` calls).
* **Server** — a computer or program that receives requests and sends back responses.
* **Request** — a message sent from a client to a server asking for a resource or action.
* **Response** — the message a server sends back to a client after processing a request.
* **HTTP Status Code** — a three-digit number in a response that tells the client whether the request succeeded, and if not, why.
* **HTTP Headers** — key-value pairs sent alongside a request or response that provide metadata about the message.

## The Physical Internet

The internet is easy to think of as something invisible and abstract—"the cloud." But the internet is, in fact, a massive physical infrastructure of cables, routers, and computers spanning every continent and ocean.

At its most basic level, the internet is just a global network of computers that can communicate with each other by sending data back and forth.

### Routers and Networks

Data doesn't travel directly from one computer to another in a single hop. Instead, it's broken up into small chunks called **packets** and each packet is passed from one device to the next until it reaches its destination.

**Routers** are the devices that make this happen. A router receives packets and decides which direction to send them next—like a postal sorting facility that reads a destination address and sends the package down the right conveyor belt.

<details>

<summary><strong>Q: Why break data into packets instead of sending it all at once?</strong></summary>

Packets let the network be more efficient and resilient. If one path is congested or broken, individual packets can be rerouted. Multiple packets can travel different paths simultaneously and be reassembled in order when they arrive. Sending one giant message would be fragile—if anything went wrong, you'd have to resend everything.

</details>

### Local Area Networks (LANs)

Before data reaches the internet, it starts on a **Local Area Network (LAN)**. Your home Wi-Fi network is a LAN. Your school or office network is a LAN.

A LAN connects devices in a limited physical area—your laptop, your phone, your printer. These devices can communicate with each other directly through your local router. When one of those devices wants to reach something *outside* the LAN (like a website), the router forwards the request outward to the internet.

```
Your laptop
     |
     ↓
 Home Router (LAN) ──→ Internet
     ↑
Your phone
```

<details>

<summary><strong>Q: What is the difference between your home router and the internet?</strong></summary>

Your home router creates your local network (LAN). It connects your devices to each other and to your Internet Service Provider (ISP). The ISP then connects you to the broader internet. Your router is the gateway between your private local network and the public internet.

</details>

### The World Wide Web

People often use "the internet" and "the web" interchangeably, but they're different things.

* **The Internet** is the physical infrastructure—the cables, routers, and protocols that allow computers to communicate.
* **The World Wide Web** is a *service* that runs on top of the internet. It's the system of web pages, websites, and web applications accessed via HTTP that we interact with in a browser.

Think of it this way: the internet is like the road network. The Web is like one type of vehicle (cars) that uses those roads. Other services—email, video calling, file transfer—also use the internet but are not "the web."

<details>

<summary><strong>Q: Name two services that use the internet but are not the Web.</strong></summary>

* **Email** — uses protocols like SMTP and IMAP, not HTTP
* **Video calls** (Zoom, FaceTime) — use protocols like WebRTC or SIP
* **SSH** — a protocol for securely logging into remote computers
* **FTP** — File Transfer Protocol for uploading/downloading files

</details>

### Submarine Cables and the Internet Backbone

When you load a website hosted on servers in another country, your data crosses continents. How?

**Submarine cables** — fiber optic cables laid along the ocean floor — carry the majority of international internet traffic. There are hundreds of these cables criss-crossing every ocean. They can carry hundreds of terabits of data per second.

On land, the internet is connected by a system of extremely high-speed data routes called the **Internet Backbone**. These routes are maintained by major telecommunications companies and Internet Exchange Points (IXPs) where different networks interconnect.

{% hint style="info" %}
You can see a live map of submarine cables at [submarinecablemap.com](https://www.submarinecablemap.com). The next time you load a page hosted in another country, your data likely traveled along one of those cables.
{% endhint %}

<details>

<summary><strong>Q: What would happen to global internet traffic if a major submarine cable were cut?</strong></summary>

Traffic would be rerouted through other cables, but speeds to and from the affected regions would slow significantly. This has happened in real life—in 2022, the Tonga volcanic eruption severed the island nation's only submarine cable, cutting off internet access for weeks.

</details>

## HTTP: The Language of the Web

Now that we understand the physical infrastructure, let's look at the rules that govern how web communication works.

**HTTP (HyperText Transfer Protocol)** is the protocol—the agreed-upon set of rules—that web clients and servers use to communicate. Every time you load a web page or your code calls `fetch()`, HTTP is the language being spoken.

A protocol is necessary because the client and server are separate programs, often running on different hardware, potentially built by different teams. Without an agreed-upon set of rules, they couldn't reliably communicate.

### The Client-Server Model

The web is built on a **client-server model**:

* The **client** initiates communication by sending a **request**
* The **server** receives the request, processes it, and sends back a **response**

This is the fundamental interaction pattern of the web, and it's the pattern we'll implement over and over again throughout this module.

Think of it like a restaurant:

| Restaurant       | Web                         |
| ---------------- | --------------------------- |
| Customer         | Client (browser, `fetch()`) |
| Kitchen / Cook   | Server                      |
| Placing an order | Sending an HTTP request     |
| The meal         | The HTTP response           |
| Menu             | API endpoints               |

<details>

<summary><strong>Q: Can a server also be a client?</strong></summary>

Yes! When your server makes a fetch request to a third-party API (like a weather API or the OpenAI API), your server is acting as a *client* to that other server. This is exactly what we'll do when we learn about securing API keys—your server receives a request from the browser and then makes its own request to a third-party API on behalf of the user.

</details>

### The Request-Response Cycle

Every HTTP interaction follows the same pattern:

1. **Client sends a request** — the client specifies what it wants using a URL, an HTTP method, optional headers, and optionally a body
2. **Server receives and processes the request** — the server reads the request, performs any logic needed (looking up data, running calculations, reading from a database), and prepares a response
3. **Server sends a response** — the response includes a status code, headers, and optionally a body with data
4. **Client receives and uses the response** — the client reads the status code, parses the body, and does something with it (renders content, stores data, shows an error)

```
CLIENT                          SERVER
  |                               |
  |  ── HTTP Request ──────────→  |
  |    Method: GET                |
  |    URL: /api/posts            |
  |    Headers: {...}             |
  |                               |  (processes request,
  |                               |   looks up data)
  |  ←──── HTTP Response ──────── |
  |    Status: 200 OK             |
  |    Headers: {...}             |
  |    Body: [{...}, {...}]       |
  |                               |
```

An HTTP **request** contains:
* **Method** — the type of action being requested (GET, POST, PATCH, DELETE)
* **URL** — the address of the resource being requested
* **Headers** — metadata about the request (more on these below)
* **Body** *(optional)* — data sent along with the request, typically used with POST and PATCH

An HTTP **response** contains:
* **Status Code** — a number indicating the result of the request
* **Headers** — metadata about the response
* **Body** *(optional)* — the data being sent back, such as JSON, HTML, or an image

<details>

<summary><strong>Q: Which HTTP method would you use to retrieve a list of all posts from an API? Which would you use to create a new post?</strong></summary>

* **GET** — to retrieve data without modifying anything
* **POST** — to submit new data to be created on the server

This mapping of HTTP methods to data operations is part of **REST** design, which we'll learn more about in a few lessons.

</details>

### HTTP Status Codes

When a server sends a response, it always includes an **HTTP status code** — a three-digit number that tells the client how the request went.

Status codes are grouped by their first digit:

| Range | Category      | Meaning                                                        |
| ----- | ------------- | -------------------------------------------------------------- |
| `1xx` | Informational | Request received, still processing                             |
| `2xx` | Success       | Request was received, understood, and fulfilled                |
| `3xx` | Redirection   | Client needs to take additional action to complete the request |
| `4xx` | Client Error  | The request contained an error the client made                 |
| `5xx` | Server Error  | The server failed to fulfill a valid request                   |

The most common ones you'll encounter:

| Code  | Name                  | When you'll see it                             |
| ----- | --------------------- | ---------------------------------------------- |
| `200` | OK                    | Successful GET, PATCH, or DELETE               |
| `201` | Created               | Successful POST (resource was created)         |
| `204` | No Content            | Success but no data to return                  |
| `301` | Moved Permanently     | Resource has a new permanent URL               |
| `400` | Bad Request           | Client sent invalid or malformed data          |
| `401` | Unauthorized          | Authentication required (not logged in)        |
| `403` | Forbidden             | Authenticated but not permitted to access this |
| `404` | Not Found             | The requested resource doesn't exist           |
| `500` | Internal Server Error | Something went wrong on the server             |

<details>

<summary><strong>Q: What's the difference between a 401 and a 403 response?</strong></summary>

* **401 Unauthorized** — the server doesn't know *who you are*. You need to log in (authenticate) before accessing this resource.
* **403 Forbidden** — the server knows who you are, but you don't have *permission* to access this resource. For example, a logged-in user trying to edit someone else's private data.

</details>

<details>

<summary><strong>Q: A user reports that clicking a button in your app gives them a spinning loader that never resolves. What status code range is most likely responsible, and what does that tell you about where the bug is?</strong></summary>

A `5xx` server error is most likely. The request reached the server but the server crashed or threw an error while trying to process it. This tells you the bug is in the *server code*, not in the client's request. You'd look at server logs to find the error.

</details>

### HTTP Headers

**HTTP headers** are key-value pairs that carry metadata about a request or response. They're separate from the body—they describe *how to interpret* the message, not the message itself.

**Common request headers:**

| Header          | Purpose                                             | Example            |
| --------------- | --------------------------------------------------- | ------------------ |
| `Content-Type`  | Tells the server what format the request body is in | `application/json` |
| `Authorization` | Carries credentials (API keys, tokens)              | `Bearer abc123`    |
| `Accept`        | Tells the server what format the client can handle  | `application/json` |

**Common response headers:**

| Header         | Purpose                                              | Example                 |
| -------------- | ---------------------------------------------------- | ----------------------- |
| `Content-Type` | Tells the client what format the response body is in | `application/json`      |
| `Set-Cookie`   | Tells the browser to store a cookie                  | `session=abc; HttpOnly` |
| `Location`     | Used with 3xx redirects to specify the new URL       | `/login`                |

{% hint style="info" %}
You can inspect HTTP headers for any request using your browser's DevTools. Open DevTools → Network tab → click on any request → look at the "Headers" section. Try it on any website!
{% endhint %}

<details>

<summary><strong>Q: When your JavaScript code calls <code>fetch('/api/posts', { method: 'POST', body: JSON.stringify(data), headers: { 'Content-Type': 'application/json' } })</code>, why is the Content-Type header necessary?</strong></summary>

The `Content-Type: application/json` header tells the server that the request body is formatted as JSON. Without it, the server doesn't know how to parse the incoming data—it might receive the raw string and not know it should be deserialized as JSON. Express uses this header to decide whether to apply its JSON-parsing middleware to the request body.

</details>
