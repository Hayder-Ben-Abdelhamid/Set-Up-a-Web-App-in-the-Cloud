# Set Up a Web App in the Cloud with AWS EC2 & VS Code

**Author:** Hayder Ben Abdelhamid  
**Email:** haider.abdelhamid@gmail.com  
**Role:** Cybersecurity Engineering Student  


This document extracts the technical operational steps, terminal commands, and configuration flows required to deploy an EC2-hosted Java web application and establish secure remote development environments via VS Code and SSH.

---

## 1. Environment Identity & Instance Provisioning

Before initializing application build dependencies, launch an Amazon EC2 virtual machine to serve as the cloud application development host and configure basic network parameters.

### Phase 1: Security Group & SSH Ingress
* **Protocol Assignment:** Authorize inbound SSH access (Port 22).
* **Source Restriction:** Enforce IP-based perimeter filtering (`My IP`) to prevent unauthorized remote shell attempts.

### Phase 2: Key Pair Credential Management
AWS authenticates remote connection requests by verifying an asymmetric key pair:

```bash
# Move downloaded private key to designated working repository
mv ~/Downloads/nextwork-keypair.pem ~/Desktop/DevOps/
cd ~/Desktop/DevOps/
```

---

## 2. Local Terminal Authorization & Credential Hardening

Before opening an SSH channel from a Windows workstation, reset and restrict local key permissions using `icacls` to fix overly permissive key errors:

```bash
# Navigate to the local workspace directory
cd \Users\MSI\DevOps

# Reset and restrict key permissions for secure SSH execution
icacls "Hayder-keypair.pem" /reset
icacls "Hayder-keypair.pem" /grant:r "%USERNAME%:R"
icacls "Hayder-keypair.pem" /inheritance:r
```

---

## 3. Remote Shell & Environment Bootstrap

With credential permissions locked down, establish a secure SSH session to the target EC2 host and initialize build dependencies.

### Establish Primary SSH Session
```bash
# Connect to the remote EC2 instance using the restricted key pair
ssh -i "Hayder-keypair.pem" ec2-user@<EC2_PUBLIC_IPV4_DNS>
```

### Install Application Build Dependencies (Maven & Java)
```bash
# Update local package manager and install Java Corretto 8 & Apache Maven
sudo yum update -y
sudo yum install java-1.8.0-amazon-corretto-devel -y
sudo yum install maven -y
```

---

## 4. Application Generation & Remote IDE Configuration

Initialize the Java web application framework using Maven archetypes and establish an IDE management channel using VS Code's Remote - SSH extensions.

### Generate Web Application Archetype
```bash
# Execute Maven project generation from standard archetype templates
mvn archetype:generate \
  -DgroupId=com.nextwork.app \
  -DartifactId=nextwork-webapp-project \
  -DarchetypeArtifactId=maven-archetype-webapp \
  -DinteractiveMode=false
```

### Configure VS Code Remote SSH (`~/.ssh/config`)
Append the following configuration entry to VS Code's local SSH configuration file:

```text
Host aws-ec2-dev
    HostName <EC2_PUBLIC_IPV4_DNS>
    User ec2-user
    IdentityFile C:\Users\MSI\DevOps\Hayder-keypair.pem
```

---

## 5. Operational Validation & Out-of-Band Verification

Validate real-time file system synchronization between local terminal utilities (`nano`) and the remote VS Code IDE environment.

### Modifying Application Source (`src/main/webapp/index.jsp`)
```bash
# Navigate to web application deployment source directory
cd nextwork-webapp-project/src/main/webapp/

# Open application entry point in terminal editor
nano index.jsp
```

### Target Source Code Modification
```xml
<html>
<body>
<h2>Hello Hayder Ben Abdelhamid!</h2>
<p>This is my web app working!</p>
</body>
</html>
```

### Operational Check
* **Local Terminal Action:** Save changes in `nano`.
* **Expected Outcome:** Changes immediately reflect within the connected VS Code editor session without requiring a session re-authentication or full file pull.
