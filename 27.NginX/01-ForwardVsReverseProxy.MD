# Proxies, Reverse Proxies & Load Balancers

This guide explains the key differences and use cases of **Forward Proxies**, **Reverse Proxies**, and **Load Balancers** in web infrastructure.

---

## 🔹 Forward Proxy

**Definition**: A server that sits between a client (e.g., a laptop) and the public internet.

### 📌 Analogy
Like a personal assistant making a restaurant reservation on your behalf — the server (restaurant) only sees the assistant, not you.

### ✅ Key Functions
- Filters outbound traffic from internal network to internet.
- Blocks malicious sites/scripts.
- Acts as a **security layer** between private network and internet.
- **Caches** content to reduce bandwidth usage.
- Logs user activity.

### 🏢 Use Case in Companies
- Routes all employee traffic through a proxy.
- Prevents virus/malware entry via shady websites.
- Centralizes content filtering and security policies.

---

## 🔹 Reverse Proxy

**Definition**: A server that sits between external clients and internal web servers.

### 📌 Analogy
Like a restaurant receptionist directing guests to the appropriate table based on availability and preferences.

### ✅ Key Functions
- Handles **incoming traffic** to internal servers.
- **Distributes traffic** (load balancing).
- **Shields internal servers** from direct exposure.
- Implements SSL termination and traffic encryption.
- Caches responses to speed up delivery.
- Provides request logging and debugging tools.

### 🔧 Popular Tool: 
- **Nginx** — used as reverse proxy & high-performance web server.

---

## 🔹 Load Balancers vs Reverse Proxies

### ❓ Are Cloud Load Balancers a Replacement?
**No.** You typically use both **cloud load balancers** and **reverse proxies** together.

### ⚙️ Combined Setup
- **Cloud Load Balancer**:
  - Sits **outside** private network.
  - First point of contact.
  - Basic traffic distribution.

- **Reverse Proxy**:
  - Sits **inside** private network.
  - Handles **intelligent routing** based on headers, cookies, or session.
  - Manages **SSL/TLS termination**, caching, security.

### 🛠️ Benefits of Layered Setup
- Higher **security** (encapsulation).
- Improved **scalability**.
- Smarter **traffic routing** for microservices (e.g., based on request paths or user sessions).

---

## 🔹 Example: Node.js & Reverse Proxies

### ❓ Is ExpressJS a Reverse Proxy?
No. But it can **act like one** using middleware logic.

### ⚖️ Comparison: Nginx vs ExpressJS
| Feature             | Nginx                         | ExpressJS                       |
|---------------------|-------------------------------|----------------------------------|
| Type                | Web server & reverse proxy    | Minimalist web framework         |
| Performance         | High concurrent connection handling | Lower performance           |
| Use Case            | Static files, load balancing, SSL | Dynamic content & APIs       |

**Best Practice**: Use Nginx **in front of** ExpressJS:
- Nginx handles static content, SSL, load balancing.
- Express handles business logic and dynamic routing.

---

## 🔹 Kubernetes Context

- **Ingress Controller** = Reverse Proxy in Kubernetes.
- Works with **Cloud Load Balancer**:
  - Load balancer manages **external traffic**.
  - Ingress controller manages **internal routing & security**.

---

