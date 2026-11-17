<div align="center">

# TS-ACADEMY-CYBERSECURITY-CAPSTONE-PROJECT

### From Vulnerability to Compliance: Securing Legacy Systems at Apex Healthcare Solutions

**Network Vulnerability Assessment and Penetration Testing (VAPT) Report**

![Project](https://img.shields.io/badge/Project-Cybersecurity-blue)
![Group](https://img.shields.io/badge/Group-69-informational)
![Risk](https://img.shields.io/badge/Security_Posture-Poor%20%2F%20High%20Risk-red)
![Compliance](https://img.shields.io/badge/Compliance_Target-HIPAA-orange)

**Author:** Adie, Patrick Betiang  
**Group:** 69

</div>

---

## Table of Contents

- [Project Overview](#project-overview)
- [Executive Summary](#executive-summary)
- [Assessment Scope and Environment](#assessment-scope-and-environment)
- [Tools Used](#tools-used)
- [Key Vulnerability Findings](#key-vulnerability-findings)
- [Risk Assessment](#risk-assessment)
- [Recommended Security Controls](#recommended-security-controls)
- [Recommended GRC Policies](#recommended-grc-policies)
- [Conclusion](#conclusion)
- [Supporting Evidence](#supporting-evidence)
- [Disclaimer](#disclaimer)

---

## Project Overview

This capstone project presents a Network Vulnerability Assessment and Penetration Testing (VAPT) engagement conducted against two representative legacy systems in a simulated healthcare environment for **Apex Healthcare Solutions**.

The assessment examines existing vulnerabilities, explains their potential security impact, evaluates business risks, and recommends technical and organisational measures to reduce exposure.

The project follows the theme:

> **From Vulnerability to Compliance: Securing Legacy Systems at Apex Healthcare Solutions.**

## Executive Summary

The assessment identified six major vulnerability findings across two tested hosts. Testing was conducted using tools including Nmap, Nikto, Searchsploit, and Metasploit, alongside manual verification.

The Kioptrix-profile host was found to be running an outdated Apache/mod_ssl/OpenSSL stack associated with a publicly documented remote buffer overflow. Additional findings included weak SSL/TLS configurations and information-disclosure issues.

The Metasploitable 2 host exposed insecure legacy remote-access services, an unauthenticated root bindshell, and services running versions associated with known backdoors, including vsftpd 2.3.4 and UnrealIRCd 3.2.8.1.

### Overall Security Posture

**Poor / High Risk**

The findings indicate that the tested systems could be exposed to unauthorised access, remote code execution, disclosure of sensitive information, and potential compromise of system integrity.

For a healthcare organisation, these weaknesses could place sensitive patient information and clinical operations at risk. Remediation should begin with isolating and rebuilding the Metasploitable 2 host, followed by patching and hardening the Kioptrix-profile host.

## Assessment Scope and Environment

The assessment covered the following systems:

| System | IP Address | Description |
|---|---|---|
| Kioptrix Profile Host | `192.168.100.163` | Legacy Apache/mod_ssl web server |
| Metasploitable 2 Host | `192.168.129.128` | Intentionally vulnerable Linux host |
| Attacking Host | Kali Linux | `192.168.100.164` as recorded in the report |

**Note:** The original report also lists `192.168.129.128` alongside the Kali Linux address. Verify the attacking-host address for the Metasploitable network before publishing the final network diagram or configuration details.

## Tools Used

| Tool | Purpose |
|---|---|
| Nmap | Host discovery, port scanning, service identification, and vulnerability-script checks |
| Nikto | Web-server scanning and identification of common web-server misconfigurations |
| Searchsploit | Searching for publicly documented exploits associated with identified software versions |
| Metasploit Framework | Reviewing and testing relevant exploit modules in the authorised lab environment |
| Web Browser | Manual verification of web-server behaviour and exposed PHP source code |

## Key Vulnerability Findings

The assessment identified the following six major findings.

### 1. Kioptrix Profile Host

| Vulnerability | Affected Service | Risk Rating |
|---|---|---|
| Apache mod_ssl/OpenSSL Remote Buffer Overflow (`OpenFuck`) — CVE-2002-0082 | Apache 1.3.20 HTTPS / mod_ssl 2.8.4 | **HIGH** |
| Information Disclosure: directory indexing, default content, HTTP TRACE, and PHP source exposure | Apache HTTP — TCP 80 | **MEDIUM** |
| SSL/TLS weaknesses: POODLE, CCS Injection, and Logjam | HTTPS — TCP 443 | **MEDIUM** |

**Key observations:**

- The Apache/mod_ssl/OpenSSL versions were identified as outdated and associated with publicly available exploit code.
- Directory indexing and default Apache content were exposed.
- The HTTP TRACE method was enabled.
- A request to `/test.php` returned raw PHP source code instead of executing the script.
- Weak SSL/TLS protocols and cryptographic configurations were identified.

### 2. Metasploitable 2 Host

| Vulnerability | Affected Service | Risk Rating |
|---|---|---|
| Insecure legacy remote-access services and exposed root bindshell | Telnet (23), rexecd (512), rlogind (513), bindshell (1524) | **HIGH** |
| UnrealIRCd 3.2.8.1 Trojanized Server Backdoor — CVE-2010-2075 | IRC — TCP 6667 | **HIGH** |
| vsftpd 2.3.4 Anonymous FTP Access and Backdoor — CVE-2011-2523 | FTP — TCP 21 | **HIGH** |

**Key observations:**

- Telnet, rexec, and rlogin were exposed as legacy remote-access services that transmit information without modern transport encryption.
- A root-level bindshell was identified listening on TCP port 1524.
- UnrealIRCd 3.2.8.1 was identified as a version associated with a known backdoor.
- Anonymous FTP access was confirmed on vsftpd 2.3.4.

## Risk Assessment

Risk ratings were assigned based on the likelihood of exploitation and the potential impact on the affected systems.

| Finding | Likelihood | Potential Impact | Risk |
|---|---|---|---|
| Apache mod_ssl/OpenSSL Buffer Overflow | High | Unauthenticated remote root compromise | **HIGH** |
| SSL/TLS Downgrade Weaknesses | Medium | Decryption or tampering with session data | **MEDIUM** |
| Information Disclosure | High | Reconnaissance and source-code exposure | **MEDIUM** |
| vsftpd 2.3.4 Anonymous FTP / Backdoor | High | Unauthorised access and potential root compromise | **HIGH** |
| UnrealIRCd 3.2.8.1 Backdoor | High | Unauthenticated remote command execution | **HIGH** |
| Legacy Remote Access and Exposed Bindshell | High | Plaintext credential exposure and unauthenticated root access | **HIGH** |

## Recommended Security Controls

### Kioptrix Profile Host

| Identified Vulnerability | Recommended Control |
|---|---|
| Apache mod_ssl/OpenSSL Buffer Overflow | Upgrade Apache, mod_ssl, and OpenSSL to supported versions. Isolate the host on a segmented network until remediation is completed. |
| SSL/TLS Downgrade Weaknesses | Disable SSLv2/SSLv3 and weak export-grade ciphers. Enforce TLS 1.2 or higher with modern cipher suites and strong key exchange configurations. |
| Information Disclosure | Disable directory indexing and HTTP TRACE, remove default/sample content, and correct the PHP handler configuration to prevent source-code disclosure. |

### Metasploitable 2 Host

| Identified Vulnerability | Recommended Control |
|---|---|
| vsftpd 2.3.4 Anonymous FTP / Backdoor | Disable anonymous FTP, replace the affected service with a verified supported build, and migrate to authenticated encrypted file transfer such as SFTP or FTPS. |
| UnrealIRCd 3.2.8.1 Backdoor | Remove the IRC service if it is not required. If required, reinstall a verified version from an official source and maintain security updates. |
| Legacy Remote Access and Exposed Bindshell | Disable Telnet, rexec, and rlogin. Use SSH with key-based authentication. Isolate the host, investigate possible compromise, and rebuild it from a trusted image. |

## Recommended GRC Policies

The following Governance, Risk and Compliance (GRC) policies are recommended to prevent the recurrence of the identified weaknesses.

### 1. Patch and Vulnerability Management Policy

**Purpose:** Establishes a recurring process for identifying, prioritising, testing, and deploying security patches.

**Why it is needed:** The assessment identified outdated software and services associated with known vulnerabilities and backdoors. A formal patch-management process helps ensure that systems are updated promptly.

**Vulnerabilities addressed:**
- Apache mod_ssl/OpenSSL Buffer Overflow
- SSL/TLS weaknesses
- vsftpd 2.3.4 Backdoor
- UnrealIRCd Backdoor

### 2. Secure Configuration and Hardening Policy

**Purpose:** Defines mandatory security configurations for servers, web applications, and encryption protocols.

**Why it is needed:** The assessment found directory indexing, default Apache content, HTTP TRACE, and weak SSL/TLS configurations.

**Vulnerabilities addressed:**
- Information Disclosure
- Directory Indexing
- HTTP TRACE
- SSL/TLS Downgrade Weaknesses

### 3. Remote Access and Authentication Policy

**Purpose:** Requires secure, encrypted, and authenticated channels for remote administration and file transfers.

**Why it is needed:** Telnet, rexec, rlogin, and anonymous FTP were identified on the tested host. These services increase the risk of credential exposure and unauthorised access.

**Vulnerabilities addressed:**
- Anonymous FTP Access
- Legacy Plaintext Remote Access
- Exposed Root Bindshell

### 4. Incident Response Policy

**Purpose:** Establishes procedures for identifying, reporting, containing, investigating, and recovering from security incidents.

**Why it is needed:** The exposed root bindshell and backdoor-associated software versions require investigation beyond routine patching. The organisation should preserve relevant evidence and follow a documented incident response process.

**Vulnerabilities addressed:**
- vsftpd Backdoor
- UnrealIRCd Backdoor
- Exposed Root Bindshell
- Potential unauthorised system access

## Conclusion

The VAPT assessment demonstrated that outdated, misconfigured, and backdoor-affected software can expose legacy systems to serious security threats.

The six major findings identified across the Kioptrix-profile and Metasploitable 2 hosts include weaknesses that could lead to unauthorised access, remote code execution, information disclosure, and potential root-level compromise.

To reduce these risks, Apex Healthcare Solutions should prioritise the isolation and rebuilding of the Metasploitable 2 host, upgrade and harden the Kioptrix-profile host, disable insecure legacy services, and implement the recommended GRC policies.

Combining technical remediation with effective governance and continuous vulnerability management will help strengthen the organisation's security posture and support its compliance objectives.

## Supporting Evidence

Screenshots captured during the practical assessment should be uploaded to this repository and organised in an `evidence` folder.

| Figure | Evidence Description | Suggested Image Path |
|---|---|---|
| Figure 1 | Nmap vulnerability-script scan results on the Kioptrix host | `evidence/figure-01-nmap-kioptrix.png` |
| Figure 2 | Searchsploit results showing OpenFuck exploit references | `evidence/figure-02-searchsploit-openfuck.png` |
| Figure 3 | Nikto scan output showing web-server findings | `evidence/figure-03-nikto-scan.png` |
| Figure 4 | Browser output showing raw PHP source-code disclosure | `evidence/figure-04-php-source-disclosure.png` |
| Figure 5 | Nmap scan showing vsftpd 2.3.4 and anonymous FTP access | `evidence/figure-05-metasploitable-ftp.png` |
| Figure 6 | Nmap SMB host-discovery output for Metasploitable 2 | `evidence/figure-06-smb-discovery.png` |

Once the screenshots have been uploaded using the suggested filenames, display them in this section with Markdown image syntax. For example:

```md
![Nmap vulnerability scan on Kioptrix](evidence/figure-01-nmap-kioptrix.png)
```

## Disclaimer

This project was conducted as an educational cybersecurity capstone exercise using intentionally vulnerable lab systems. All vulnerability scanning and testing activities must be performed only on systems for which explicit authorisation has been granted.

The findings describe the tested lab hosts and should not be interpreted as evidence that a real Apex Healthcare Solutions production network was assessed.

---

<div align="center">

**Cybersecurity Capstone Project | Group 69**  
**Prepared by Adie, Patrick Betiang**

</div>


</body>
</html>
