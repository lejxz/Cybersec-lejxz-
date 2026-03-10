# Web Exploitation Basics

## 📋 Summary
* **Core Concept:** Web exploitation involves identifying and leveraging security vulnerabilities in web applications to gain unauthorized access, execute malicious code, or extract sensitive data.

> **Takeaways:** Web exploitation is a critical skill in cybersecurity that focuses on understanding how web applications can be compromised through various attack vectors. This includes client-side attacks (affecting users' browsers), server-side attacks (targeting the web server or application logic), and attacks on the communication layer between them. Mastery of these techniques is essential for both offensive security (penetration testing) and defensive security (secure development and protection).

## 📖 Definition

* **Web Exploitation:** The practice of identifying and exploiting security flaws in web applications, services, or protocols to compromise confidentiality, integrity, or availability.
* **Vulnerability:** A weakness in a system's design, implementation, or configuration that can be exploited to violate security policies.
* **Attack Vector:** The method or pathway used by an attacker to access or harm a target system.
* **Payload:** The malicious code or commands delivered through an exploit to achieve the attacker's objective.
* **Requirements:**
    * Basic understanding of HTTP protocol and web architecture
    * Knowledge of HTML, CSS, JavaScript, and server-side languages
    * Familiarity with common web frameworks and databases
    * Understanding of network fundamentals and security principles

## 📊 Common Vulnerability Categories

| Vulnerability Type | OWASP Rank | Severity Level | Exploitation Difficulty |
| :--- | :--- | :--- | :--- |
| Broken Access Control | #1 | Critical | Medium |
| Cryptographic Failures | #2 | High | Medium |
| Injection (SQL, XSS, etc.) | #3 | Critical | Low to Medium |
| Insecure Design | #4 | High | Variable |
| Security Misconfiguration | #5 | Medium to High | Low |

* **Impact Assessment:** Evaluates the potential damage an exploit can cause to the system and its users.
* **Exploitability:** Measures how easy it is for an attacker to successfully execute the exploit.
* **Detection Difficulty:** Determines how challenging it is to identify the vulnerability or ongoing attack.

## ❓ Why we use it

* **Penetration Testing:** Security professionals use web exploitation techniques to identify vulnerabilities before malicious actors can exploit them.
* **Security Research:** Understanding exploitation methods helps researchers develop better defense mechanisms and security tools.
* **Vulnerability Assessment:** Organizations need to evaluate their web applications' security posture through controlled exploitation attempts.
* **Incident Response:** Knowledge of exploitation techniques aids in investigating security breaches and understanding attack patterns.
* **Secure Development:** Developers who understand exploitation methods can write more secure code and implement proper defenses.

## ⚙️ How it works

### Phase 1: Reconnaissance (Information Gathering)
1. **Passive Reconnaissance:** Collect publicly available information without directly interacting with the target.
   * Search engine queries (Google dorking)
   * WHOIS lookups for domain registration details
   * DNS enumeration to discover subdomains
   * Social media and company website analysis
   * Review of archived versions using Wayback Machine

2. **Active Reconnaissance:** Directly interact with the target to gather technical details.
   * Port scanning to identify open services
   * Technology fingerprinting (web server, framework, CMS)
   * Banner grabbing to determine software versions
   * Directory and file enumeration
   * Analysis of robots.txt and sitemap.xml files

### Phase 2: Scanning and Enumeration
3. **Vulnerability Scanning:** Use automated tools to identify potential security weaknesses.
   * Run web application scanners (Nikto, OWASP ZAP, Burp Suite Scanner)
   * Check for known CVEs in identified software versions
   * Test for common misconfigurations
   * Identify input fields and parameters for testing

4. **Manual Enumeration:** Conduct detailed analysis of application functionality.
   * Map application structure and user flows
   * Identify authentication and authorization mechanisms
   * Locate hidden parameters and endpoints
   * Analyze client-side code (JavaScript) for sensitive information
   * Test API endpoints and their parameters

### Phase 3: Vulnerability Analysis
5. **Input Validation Testing:** Test how the application handles malicious input.
   * Inject SQL payloads in database queries
   * Insert XSS payloads in reflected or stored contexts
   * Test for command injection in system calls
   * Attempt path traversal in file operations
   * Test for XML/XXE injection in parsers

6. **Authentication Testing:** Evaluate authentication mechanisms for weaknesses.
   * Test for weak or default credentials
   * Attempt brute force or credential stuffing attacks
   * Check for session fixation vulnerabilities
   * Test password reset functionality
   * Verify multi-factor authentication implementation

7. **Authorization Testing:** Verify proper access control enforcement.
   * Test for horizontal privilege escalation (accessing other users' data)
   * Test for vertical privilege escalation (gaining admin privileges)
   * Attempt forced browsing to restricted resources
   * Test for Insecure Direct Object References (IDOR)
   * Verify API endpoint authorization

### Phase 4: Exploitation
8. **Exploit Development:** Create or adapt exploit code for identified vulnerabilities.
   * Write custom scripts or modify existing exploits
   * Craft payloads specific to the target environment
   * Test exploits in a controlled environment first
   * Prepare multiple exploitation methods as backup

9. **Exploit Execution:** Launch the attack against the target system.
   * Deliver payload through the vulnerable component
   * Bypass security controls (WAF, input filters)
   * Execute code or commands on the target
   * Establish initial access to the system

### Phase 5: Post-Exploitation
10. **Maintain Access:** Establish persistent access to the compromised system.
    * Create backdoor accounts or web shells
    * Modify application code to maintain entry points
    * Set up reverse shells or command-and-control channels

11. **Privilege Escalation:** Increase access level within the system.
    * Exploit local vulnerabilities for elevated privileges
    * Leverage misconfigurations or weak permissions
    * Access sensitive configuration files or credentials

12. **Data Extraction:** Retrieve targeted information from the system.
    * Dump database contents
    * Access sensitive files and documents
    * Extract user credentials or session tokens
    * Capture network traffic or communications

13. **Lateral Movement:** Expand access to other systems or networks.
    * Pivot to internal network systems
    * Compromise additional user accounts
    * Access connected databases or services

### Phase 6: Documentation and Reporting
14. **Evidence Collection:** Document all findings and exploitation steps.
    * Capture screenshots of successful exploits
    * Record command history and tool outputs
    * Document the exploitation path and techniques used
    * Save proof-of-concept code and payloads

15. **Report Generation:** Create detailed documentation of security findings.
    * Describe each vulnerability with severity rating
    * Provide step-by-step reproduction instructions
    * Include remediation recommendations
    * Assess business impact and risk level

## 💻 Usage / Program Example

### Example 1: SQL Injection Detection (Python)

```python
import requests

def test_sql_injection(url, param):
    # Common SQL injection payloads
    payloads = [
        "' OR '1'='1",
        "' OR '1'='1' --",
        "' OR '1'='1' /*",
        "admin' --",
        "1' UNION SELECT NULL--"
    ]
    
    vulnerable = False
    
    for payload in payloads:
        # Construct the malicious request
        test_url = f"{url}?{param}={payload}"
        
        try:
            response = requests.get(test_url, timeout=5)
            
            # Check for common SQL error messages
            error_indicators = [
                "SQL syntax",
                "mysql_fetch",
                "ORA-",
                "PostgreSQL",
                "Microsoft SQL Server"
            ]
            
            for indicator in error_indicators:
                if indicator.lower() in response.text.lower():
                    print(f"[!] Potential SQL Injection found with payload: {payload}")
                    vulnerable = True
                    break
                    
        except requests.exceptions.RequestException as e:
            print(f"[X] Error testing payload: {e}")
    
    return vulnerable

# Example usage (for authorized testing only)
# test_sql_injection("http://testsite.local/search", "id")
```

### Example 2: XSS (Cross-Site Scripting) Testing

```python
import requests
from html.parser import HTMLParser

class XSSDetector(HTMLParser):
    def __init__(self):
        super().__init__()
        self.found_payload = False
    
    def handle_starttag(self, tag, attrs):
        # Check if our test payload appears in attributes
        for attr, value in attrs:
            if value and "<script>" in value.lower():
                self.found_payload = True

def test_xss(url, param, payload="<script>alert('XSS')</script>"):
    """
    Test for reflected XSS vulnerability
    Note: Only use on applications you have permission to test
    """
    test_url = f"{url}?{param}={payload}"
    
    try:
        response = requests.get(test_url, timeout=5)
        
        # Check if payload appears unescaped in response
        if payload in response.text:
            print(f"[!] Potential XSS vulnerability detected!")
            print(f"[!] Payload reflected without sanitization")
            return True
        
        # Check for partially escaped payload
        parser = XSSDetector()
        parser.feed(response.text)
        
        if parser.found_payload:
            print(f"[!] Payload detected in HTML structure")
            return True
            
    except requests.exceptions.RequestException as e:
        print(f"[X] Error during testing: {e}")
    
    return False

# Example usage (for authorized testing only)
# test_xss("http://testsite.local/search", "query")
```

### Example 3: Automated Reconnaissance Script

```python
import requests
import socket
from urllib.parse import urlparse

def perform_reconnaissance(target_url):
    """
    Automated reconnaissance script
    Gathers basic information about target web application
    """
    print(f"[*] Starting reconnaissance on {target_url}")
    
    parsed_url = urlparse(target_url)
    domain = parsed_url.netloc
    
    # 1. DNS Resolution
    try:
        ip_address = socket.gethostbyname(domain)
        print(f"[+] IP Address: {ip_address}")
    except socket.gaierror:
        print(f"[X] Could not resolve domain: {domain}")
        return
    
    # 2. HTTP Headers Analysis
    try:
        response = requests.get(target_url, timeout=5)
        print(f"[+] Status Code: {response.status_code}")
        
        # Identify server and technologies
        headers_of_interest = ['Server', 'X-Powered-By', 'X-AspNet-Version']
        for header in headers_of_interest:
            if header in response.headers:
                print(f"[+] {header}: {response.headers[header]}")
        
        # Check security headers
        security_headers = ['X-Frame-Options', 'X-XSS-Protection', 
                          'Content-Security-Policy', 'Strict-Transport-Security']
        print("\n[*] Security Headers Check:")
        for header in security_headers:
            if header in response.headers:
                print(f"[+] {header}: Present")
            else:
                print(f"[-] {header}: Missing")
                
    except requests.exceptions.RequestException as e:
        print(f"[X] Error connecting to target: {e}")
    
    # 3. Common Files Discovery
    common_files = ['/robots.txt', '/sitemap.xml', '/.git/config', 
                   '/admin', '/.env', '/backup.sql']
    
    print("\n[*] Checking for common files:")
    for file_path in common_files:
        test_url = f"{target_url.rstrip('/')}{file_path}"
        try:
            r = requests.get(test_url, timeout=3)
            if r.status_code == 200:
                print(f"[+] Found: {file_path}")
            elif r.status_code == 403:
                print(f"[!] Forbidden: {file_path} (exists but access denied)")
        except:
            pass

# Example usage (for authorized testing only)
# perform_reconnaissance("http://testsite.local")
```

### Example 4: Complete Exploitation Workflow

```python
import requests
import sys
from urllib.parse import urljoin

class WebExploitationWorkflow:
    def __init__(self, target_url):
        self.target_url = target_url
        self.session = requests.Session()
        self.vulnerabilities = []
    
    def phase1_reconnaissance(self):
        """Phase 1: Information Gathering"""
        print("[*] Phase 1: Reconnaissance")
        
        try:
            response = self.session.get(self.target_url, timeout=5)
            print(f"[+] Target is reachable (Status: {response.status_code})")
            
            # Identify technologies
            if 'Server' in response.headers:
                print(f"[+] Server: {response.headers['Server']}")
            
            return True
        except Exception as e:
            print(f"[X] Reconnaissance failed: {e}")
            return False
    
    def phase2_scanning(self):
        """Phase 2: Vulnerability Scanning"""
        print("\n[*] Phase 2: Scanning for Vulnerabilities")
        
        # Test common endpoints
        common_paths = ['/admin', '/login', '/api', '/dashboard']
        
        for path in common_paths:
            url = urljoin(self.target_url, path)
            try:
                r = self.session.get(url, timeout=3)
                if r.status_code in [200, 301, 302]:
                    print(f"[+] Found endpoint: {path}")
            except:
                pass
    
    def phase3_vulnerability_analysis(self):
        """Phase 3: Analyze Specific Vulnerabilities"""
        print("\n[*] Phase 3: Vulnerability Analysis")
        
        # Test for SQL injection
        sql_payload = "' OR '1'='1"
        test_url = f"{self.target_url}?id={sql_payload}"
        
        try:
            response = self.session.get(test_url, timeout=5)
            if "SQL" in response.text or "syntax" in response.text:
                print("[!] Potential SQL Injection vulnerability detected")
                self.vulnerabilities.append("SQL Injection")
        except:
            pass
    
    def phase4_exploitation(self):
        """Phase 4: Attempt Exploitation"""
        print("\n[*] Phase 4: Exploitation")
        
        if "SQL Injection" in self.vulnerabilities:
            print("[*] Attempting to exploit SQL Injection...")
            # In real scenarios, this would contain actual exploitation code
            print("[+] Exploitation successful (simulated)")
    
    def phase5_post_exploitation(self):
        """Phase 5: Post-Exploitation Activities"""
        print("\n[*] Phase 5: Post-Exploitation")
        print("[*] Maintaining access and gathering data...")
        # This phase would include data extraction, privilege escalation, etc.
    
    def phase6_reporting(self):
        """Phase 6: Generate Report"""
        print("\n[*] Phase 6: Reporting")
        print(f"[+] Total vulnerabilities found: {len(self.vulnerabilities)}")
        for vuln in self.vulnerabilities:
            print(f"    - {vuln}")
    
    def run_full_workflow(self):
        """Execute complete exploitation workflow"""
        print("="*60)
        print("Web Exploitation Workflow")
        print("="*60)
        
        if not self.phase1_reconnaissance():
            print("[X] Stopping workflow due to reconnaissance failure")
            return
        
        self.phase2_scanning()
        self.phase3_vulnerability_analysis()
        
        if self.vulnerabilities:
            self.phase4_exploitation()
            self.phase5_post_exploitation()
        else:
            print("\n[*] No vulnerabilities found to exploit")
        
        self.phase6_reporting()

# Example usage (for authorized testing only)
# workflow = WebExploitationWorkflow("http://testsite.local")
# workflow.run_full_workflow()
```

## 🛡️ Common Defense Mechanisms

* **Input Validation:** Verify and sanitize all user input before processing. Use allowlists rather than denylists when possible.
* **Output Encoding:** Encode data before displaying it to prevent injection attacks like XSS.
* **Prepared Statements:** Use parameterized queries to prevent SQL injection attacks.
* **Authentication & Authorization:** Implement strong authentication mechanisms and verify user permissions for every action.
* **Security Headers:** Configure HTTP security headers (CSP, X-Frame-Options, HSTS) to mitigate various attacks.
* **Rate Limiting:** Prevent brute force and automated attacks by limiting request rates.
* **Web Application Firewall (WAF):** Deploy WAF solutions to filter malicious traffic patterns.
* **Secure Session Management:** Implement secure session handling with proper timeout and token rotation.
* **Logging and Monitoring:** Maintain comprehensive logs and monitor for suspicious activities.

## 🔍 Tools and Frameworks

* **Burp Suite:** Comprehensive web application security testing platform for manual and automated testing.
* **OWASP ZAP:** Open-source web application security scanner for finding vulnerabilities.
* **SQLMap:** Automated SQL injection and database takeover tool.
* **Metasploit Framework:** Penetration testing framework with extensive web exploitation modules.
* **Nikto:** Web server scanner that detects dangerous files, outdated versions, and configuration issues.
* **Nmap:** Network scanner for port scanning and service enumeration.
* **Gobuster/Dirb:** Directory and file brute-forcing tools.
* **Wireshark:** Network protocol analyzer for traffic analysis.

## ⚖️ Legal and Ethical Considerations

* **Authorization Required:** Always obtain written permission before testing any system you do not own.
* **Scope Definition:** Clearly define what systems and methods are in-scope for testing.
* **Data Handling:** Handle any discovered data responsibly and maintain confidentiality.
* **Responsible Disclosure:** Report vulnerabilities to the appropriate parties through proper channels.
* **Compliance:** Ensure all testing activities comply with relevant laws and regulations.

## References

* [OWASP Top 10](https://owasp.org/www-project-top-ten/) — The most critical security risks to web applications.
* [PortSwigger Web Security Academy](https://portswigger.net/web-security) — Free online training for web application security.
* [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/) — Comprehensive framework for testing web application security.
* Web Application Hacker's Handbook — Dafydd Stuttard & Marcus Pinto, Wiley Publishing.
* [NIST National Vulnerability Database](https://nvd.nist.gov/) — Repository of standards-based vulnerability management data.
* [PTES Technical Guidelines](http://www.pentest-standard.org/) — Penetration Testing Execution Standard for standardized testing methodology.