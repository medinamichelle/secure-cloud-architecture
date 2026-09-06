# Secure Cloud Architecture Plan

## CDN
The CDN (Content Delivery Network) stores cached copies of static content closer to users to improve loading speed and reduce direct load on backend infrastructure.

## Load Balancer
The load balancer acts as the single point of entry for user traffic, distributing incoming requests evenly across multiple application servers to ensure high availability and prevent server overload.

## Application Servers
Application servers process business logic and user requests from the web interface. These servers reside in a private subnet and can only communicate with the load balancer and the database.

## Database
The database stores sensitive student records. The database resides in an isolated private subnet and is strictly prohibited from direct internet connectivity.

---

# Public and Private Resources

| Resource | Public or Private? | Explanation |
| :--- | :--- | :--- |
| **CDN** | Public | Must be publicly accessible to deliver static content to users worldwide. |
| **Load Balancer** | Public | Receives incoming HTTPS traffic directly from end users on the Internet. |
| **Application Server** | Private | Processes application logic and should only accept traffic forwarded by the Load Balancer. |
| **Database** | Private | Contains sensitive data and must never be exposed to the public Internet. |

---

# Security Controls

### IAM
Access to cloud management platforms and infrastructure must follow strict Identity and Access Management policies. Users are assigned role-based permissions, limiting system visibility only to what is required for their job duties.

### MFA
Multi-Factor Authentication (MFA) must be enforced for all user accounts, particularly administrative and developer accounts, to protect against password theft and unauthorized access.

### Firewall / Security Group
Network traffic is regulated using explicit inbound and outbound security rules:
- **Internet → Load Balancer:** Allowed (Ports 80/443)
- **Load Balancer → Application Server:** Allowed (Port 8080/HTTP)
- **Application Server → Database:** Allowed (Port 3306/Database Port)
- **Internet → Database:** Blocked
- **Internet → Application Server:** Blocked

### Encryption
Student data must be encrypted **in-transit** (using HTTPS/TLS) to prevent eavesdropping during transmission, and **at-rest** (using AES-256) to protect stored database files from unauthorized physical or disk-level inspection.

### Logging
System activities, including user administrative actions, authentication logs, network flow logs, and database access logs, must be recorded to maintain a detailed audit trail.

### Monitoring
Real-time monitoring tools analyze logs to detect suspicious behavior, such as multiple failed login attempts, unexpected spikes in traffic, or unauthorized access requests, triggering immediate security alerts.

### Backup
Automated daily backups of the database are executed and stored securely. This ensures rapid data recovery in the event of hardware corruption, accidental deletion, or ransomware attacks.

---

# Principle of Least Privilege

| User | Allowed Access |
| :--- | :--- |
| **Administrator** | Full administrative access to manage cloud infrastructure, configuration settings, and IAM policies (requires MFA). |
| **Instructor** | Read and write access to student records and grades only through the application interface. |
| **Student** | Read-only access to view their own personal profile and academic records through the application interface. |
| **Developer** | Read and write access to application code repositories, staging environments, and deployment tools, but no direct production database access. |

---

# Shared Responsibility Model

| Responsibility | Cloud Provider or Customer? |
| :--- | :--- |
| **Physical data center** | Cloud Provider |
| **Physical servers** | Cloud Provider |
| **User accounts** | Customer |
| **Student data** | Customer |
| **IAM permissions** | Customer |
| **Application security** | Customer |
| **Database access rules** | Customer |
| **Backups** | Customer |

### What does Security OF the Cloud mean?
"Security OF the Cloud" refers to the cloud provider's responsibility to protect the underlying physical infrastructure, facilities, hardware, and core networking that run all cloud services.

### What does Security IN the Cloud mean?
"Security IN the Cloud" refers to the customer's responsibility to manage and secure the data, user access, application code, operating systems, network configurations, and security controls built on top of the provider's infrastructure.

---

# Architecture Questions

1. **Which resource should be directly accessible from the Internet?**  
   The CDN and the Public Load Balancer should be directly accessible from the Internet to accept incoming user traffic.

2. **Why should the database remain private?**  
   The database contains sensitive student records. Keeping it in a private subnet shields it from direct internet scans, unauthorized access, and cyberattacks.

3. **Why should users not connect directly to the database?**  
   Direct user access exposes sensitive data structures, circumvents application security logic, and increases the risk of data theft, deletion, or SQL injection attacks.

4. **What is the purpose of a load balancer?**  
   A load balancer distributes incoming user requests across multiple application servers to balance traffic, optimize performance, and prevent server overload.

5. **What happens if one application server fails?**  
   The load balancer detects the server failure via health checks and automatically reroutes traffic to the remaining functional application servers, keeping the service online without downtime.

6. **What is the purpose of a CDN?**  
   A Content Delivery Network (CDN) caches static website files (like images, CSS, and HTML) on edge servers geographically closer to users to improve page load speed and reduce backend bandwidth consumption.

7. **Why should administrator accounts use MFA?**  
   Administrator accounts have high privileges. MFA adds an extra layer of security, ensuring that a compromised password alone is not enough to grant access to critical cloud infrastructure.

8. **Why should administrator access not be given to every employee?**  
   Granting broad administrative access increases the risk of accidental misconfigurations, insider threats, and system vulnerability. Following the Principle of Least Privilege limits potential damage.

9. **Why are logging and monitoring important?**  
   Logging creates an audit trail of events for security investigations, while monitoring provides real-time detection and alerting for anomalous or suspicious activities.

10. **Why are backups important?**  
    Backups ensure business continuity and data preservation, enabling system restoration if data is lost, corrupted, or encrypted during a cyber attack or hardware failure.
