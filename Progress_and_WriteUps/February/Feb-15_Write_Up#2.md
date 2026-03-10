# Networking Basics

## 📋 Summary
* **Core Concept:** Networking fundamentals encompass the protocols, addressing schemes, and tools required for devices to communicate across networks, forming the foundation of cybersecurity and network administration.

> **Takeaways:** Understanding IP addresses, subnets, ports, and the TCP/IP model enables secure network configuration and troubleshooting. Mastery of common protocols (HTTP, HTTPS, SSH, FTP) and DNS is essential for both defensive security measures and offensive security assessments. Command-line tools provide direct network interaction capabilities necessary for security analysis.

## 📖 Definition

* **IP Address:** A numerical label assigned to each device connected to a network that uses the Internet Protocol for communication. IPv4 uses 32-bit addresses (e.g., 192.168.1.1), while IPv6 uses 128-bit addresses.

* **Subnet:** A logical subdivision of an IP network, created by dividing a larger network into smaller, manageable segments using subnet masks.

* **Port:** A numerical identifier (0-65535) that specifies a particular process or service on a networked device, enabling multiple services to operate simultaneously on a single IP address.

* **TCP/IP Model:** A four-layer networking framework (Link, Internet, Transport, Application) that defines how data is transmitted across networks.

* **Protocol:** A set of standardized rules governing data exchange between devices on a network.

* **DNS (Domain Name System):** A hierarchical naming system that translates human-readable domain names (e.g., example.com) into IP addresses.

* **Requirements:**
    * Basic understanding of binary and hexadecimal number systems
    * Familiarity with command-line interface operations
    * Knowledge of network hardware (routers, switches, network interface cards)

## 📊 Network Layer Comparison

| Layer | TCP/IP Model | Primary Function | Example Protocols |
| :--- | :--- | :--- | :--- |
| 4 | Application | User-facing services | HTTP, HTTPS, SSH, FTP, DNS |
| 3 | Transport | End-to-end communication | TCP, UDP |
| 2 | Internet | Routing and addressing | IP, ICMP, ARP |
| 1 | Link | Physical transmission | Ethernet, Wi-Fi |

### Port Number Ranges

| Range | Category | Purpose |
| :--- | :--- | :--- |
| 0-1023 | Well-Known Ports | System services (HTTP: 80, HTTPS: 443, SSH: 22) |
| 1024-49151 | Registered Ports | User applications and services |
| 49152-65535 | Dynamic/Private Ports | Temporary client-side ports |

## ❓ Why we use it

* **Network Addressing:** IP addresses and subnets enable unique device identification and efficient packet routing across complex network topologies.

* **Service Differentiation:** Port numbers allow multiple network services to operate concurrently on a single device without conflict.

* **Standardized Communication:** Protocols ensure interoperability between devices from different manufacturers and operating systems.

* **Security Applications:** Understanding networking fundamentals is critical for implementing firewalls, detecting intrusions, and conducting penetration testing.

* **Troubleshooting:** Network tools and protocol knowledge enable rapid diagnosis and resolution of connectivity issues.

## ⚙️ How it works

### IP Addressing and Subnetting

1. **Address Assignment:** Each device receives a unique IP address within its network segment.

2. **Subnet Calculation:** A subnet mask (e.g., 255.255.255.0 or /24) determines which portion of the IP address represents the network and which represents the host.
   
   **Example:** 192.168.1.100/24
   - Network portion: 192.168.1
   - Host portion: 100
   - Usable hosts: 254 (2^8 - 2)

3. **Routing Decision:** When a device sends data, it compares the destination IP with its subnet mask to determine if the destination is local or requires a gateway.

### TCP/IP Communication Process

1. **Application Layer:** User application generates data (e.g., HTTP request).

2. **Transport Layer:** TCP or UDP adds port numbers and handles data segmentation.
   - TCP: Connection-oriented, reliable delivery
   - UDP: Connectionless, faster but unreliable

3. **Internet Layer:** IP header added with source and destination IP addresses.

4. **Link Layer:** Frames created with MAC addresses for local delivery.

### DNS Resolution Process

1. **Query Initiation:** User enters domain name (e.g., example.com).

2. **Local Cache Check:** System checks local DNS cache.

3. **Recursive Query:** If not cached, query sent to configured DNS server.

4. **Hierarchical Resolution:** DNS server queries root servers, TLD servers, and authoritative nameservers.

5. **Response Return:** IP address returned to user application.

### Web Request Workflow

1. **DNS Resolution:** Browser resolves domain to IP address.

2. **TCP Connection:** Three-way handshake establishes connection (SYN → SYN-ACK → ACK).

3. **HTTP Request:** Browser sends HTTP/HTTPS request to server.

4. **Server Processing:** Web server processes request and generates response.

5. **Response Delivery:** Server sends HTTP response with status code and content.

6. **Connection Termination:** TCP connection closed (FIN → ACK).

## 💻 Usage / Program Example

### Python: Port Scanner (Basic Security Tool)

```python
import socket
import sys

def scan_port(target_ip, port):
    """
    Attempts to connect to a specific port on target IP.
    Returns True if port is open, False otherwise.
    """
    try:
        # Create TCP socket
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.settimeout(1)  # 1 second timeout
        
        # Attempt connection
        result = sock.connect_ex((target_ip, port))
        sock.close()
        
        return result == 0  # 0 means connection successful
    except socket.error:
        return False

def scan_common_ports(target_ip):
    """
    Scans common ports on target system.
    """
    # Common ports and their services
    common_ports = {
        21: "FTP",
        22: "SSH",
        23: "Telnet",
        25: "SMTP",
        53: "DNS",
        80: "HTTP",
        443: "HTTPS",
        3306: "MySQL",
        3389: "RDP",
        8080: "HTTP-Proxy"
    }
    
    print(f"Scanning {target_ip}...\n")
    print(f"{'Port':<10}{'Service':<15}{'Status'}")
    print("-" * 40)
    
    for port, service in common_ports.items():
        if scan_port(target_ip, port):
            print(f"{port:<10}{service:<15}OPEN")
        else:
            print(f"{port:<10}{service:<15}CLOSED")

# Usage example
if __name__ == "__main__":
    target = "127.0.0.1"  # Localhost for testing
    scan_common_ports(target)
```

### Python: Subnet Calculator

```python
import ipaddress

def analyze_subnet(ip_with_cidr):
    """
    Analyzes subnet information given IP address with CIDR notation.
    Example: '192.168.1.0/24'
    """
    try:
        network = ipaddress.IPv4Network(ip_with_cidr, strict=False)
        
        print(f"Network Analysis for {ip_with_cidr}\n")
        print(f"Network Address:    {network.network_address}")
        print(f"Broadcast Address:  {network.broadcast_address}")
        print(f"Subnet Mask:        {network.netmask}")
        print(f"Wildcard Mask:      {network.hostmask}")
        print(f"Total Addresses:    {network.num_addresses}")
        print(f"Usable Hosts:       {network.num_addresses - 2}")
        print(f"First Usable:       {list(network.hosts())[0]}")
        print(f"Last Usable:        {list(network.hosts())[-1]}")
        
    except ValueError as e:
        print(f"Error: {e}")

# Usage example
analyze_subnet("192.168.1.0/24")
```

### Python: DNS Lookup Tool

```python
import socket

def dns_lookup(hostname):
    """
    Performs DNS resolution for given hostname.
    """
    try:
        print(f"Resolving {hostname}...")
        ip_address = socket.gethostbyname(hostname)
        print(f"IP Address: {ip_address}")
        
        # Reverse DNS lookup
        try:
            reverse_name = socket.gethostbyaddr(ip_address)
            print(f"Reverse DNS: {reverse_name[0]}")
        except socket.herror:
            print("Reverse DNS: Not available")
            
    except socket.gaierror:
        print(f"Error: Cannot resolve {hostname}")

# Usage example
dns_lookup("www.google.com")
```

## 🛠️ Command-Line Tools

### ping
**Purpose:** Tests network connectivity and measures round-trip time.

```bash
# Basic ping
ping 8.8.8.8

# Ping with count limit (5 packets)
ping -c 5 google.com

# Ping with specific packet size
ping -s 1000 192.168.1.1
```

### nmap
**Purpose:** Network discovery and security auditing tool.

```bash
# Basic port scan
nmap 192.168.1.1

# Scan specific ports
nmap -p 22,80,443 192.168.1.1

# Service version detection
nmap -sV 192.168.1.1

# Scan entire subnet
nmap 192.168.1.0/24

# OS detection (requires root)
sudo nmap -O 192.168.1.1
```

### curl
**Purpose:** Transfers data using various network protocols.

```bash
# Basic GET request
curl https://api.example.com

# Save output to file
curl -o output.html https://example.com

# Include HTTP headers in output
curl -i https://example.com

# POST request with data
curl -X POST -d "key=value" https://api.example.com

# Follow redirects
curl -L https://example.com
```

### wget
**Purpose:** Non-interactive network downloader.

```bash
# Download file
wget https://example.com/file.zip

# Download recursively (entire website)
wget -r https://example.com

# Continue interrupted download
wget -c https://example.com/largefile.iso

# Download in background
wget -b https://example.com/file.zip
```

### netstat
**Purpose:** Displays network connections, routing tables, and interface statistics.

```bash
# Show all active connections
netstat -a

# Show listening ports
netstat -l

# Display with process IDs
netstat -p

# Show numerical addresses (no DNS resolution)
netstat -n

# Continuous monitoring
netstat -c

# Combined: Show all TCP listening ports with process info
netstat -tlnp
```

## 🔒 Security Considerations

* **Port Scanning Ethics:** Only scan networks you own or have explicit permission to test. Unauthorized scanning is illegal in many jurisdictions.

* **Default Credentials:** Many network services use default usernames and passwords. Change these immediately during configuration.

* **Encryption:** Always use encrypted protocols (HTTPS, SSH, SFTP) instead of plaintext alternatives (HTTP, Telnet, FTP) when transmitting sensitive data.

* **Firewall Configuration:** Implement firewall rules to restrict access to necessary ports only, following the principle of least privilege.

* **Network Segmentation:** Use subnets to isolate sensitive systems from general network traffic.

## References

* [RFC 791: Internet Protocol](https://www.rfc-editor.org/rfc/rfc791) — DARPA Internet Program Protocol Specification
* [RFC 793: Transmission Control Protocol](https://www.rfc-editor.org/rfc/rfc793) — TCP Specification
* [Computer Networking: A Top-Down Approach] — Kurose & Ross, Chapters 1-4
* [Nmap Network Scanning Guide](https://nmap.org/book/) — Official Nmap documentation for security scanning
* [IANA Port Number Registry](https://www.iana.org/assignments/service-names-port-numbers/) — Official port assignments