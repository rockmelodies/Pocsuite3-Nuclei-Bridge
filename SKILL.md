---
name: pocsuite3-nuclei-bridge
description: >
  Dual-format POC generator — creates both Pocsuite3 Python POC scripts and
  Nuclei YAML templates from a single vulnerability report. Full compliance
  with 360 BugCloud POC submission standards and Nuclei template best practices.
  When a user provides a vulnerability report, CVE advisory, or asks to write
  a POC — load this skill first. Supports all common vuln types: RCE, file
  upload, SQL injection, SSRF, unauth access, file read, XSS, and more.
  Also load when: converting between POC formats, preparing submissions for
  BugCloud review, or writing batch-verification scripts.
---

# Pocsuite3-Nuclei-Bridge — Dual-Format POC Generator

## Overview

This skill generates **both** formats from a single vulnerability specification:

| Format | File | Use Case |
|--------|------|---------|
| **Pocsuite3** | `.py` | 360 BugCloud submission, batch verification, deep exploit |
| **Nuclei** | `.yaml` | Fast mass scanning, CI/CD integration, template ecosystem |

The bridge is bidirectional — given either format, generate the other. Given a raw vulnerability report, generate both simultaneously.

## Guiding Principles

1. **Convention over configuration** — follow the exact field naming and
   structure defined below. Reviewers reject non-conforming submissions.
2. **One-shot verification** — the `_verify()` method should produce a
   definitive true/false result in a single request whenever possible.
3. **Safe by default** — `_attack()` must only execute when the user
   explicitly invokes attack mode. Never include destructive operations in
   `_verify()`.
4. **SSL verification always disabled** — use `verify=False` in all
   `requests` calls. Production targets often have self-signed certificates.
5. **No external dependencies beyond pocsuite3 built-ins** — rely on
   `pocsuite3.api` imports only.

---

## POC File Structure

Every POC file follows this skeleton:

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
ProductName ComponentName VulnerabilityType (CVE-XXXX-XXXXX)

Description:
  Detailed vulnerability description in 2-3 sentences.
  Include impact, affected versions, and exploitation preconditions.

Affected: ProductName <= X.Y.Z
Reference: https://nvd.nist.gov/vuln/detail/CVE-XXXX-XXXXX
"""

from pocsuite3.api import (
    Output, POCBase, POC_CATEGORY, register_poc, requests,
    VUL_TYPE, OptString, OptBool, OptInteger,
)
from urllib.parse import urljoin


class VulnName(POCBase):
    vulID       = "CVE-XXXX-XXXXX"          # Use CVE / CNNVD / CNVD / LDYCC
    version     = "1.0"
    author      = ["your-name"]
    vulDate     = "YYYY-MM-DD"
    createDate  = "YYYY-MM-DD"
    updateDate  = "YYYY-MM-DD"
    references  = ["https://nvd.nist.gov/vuln/detail/CVE-XXXX-XXXXX"]
    name        = "ProductName ComponentName VulnerabilityType (CVE-XXXX-XXXXX)"
    appPowerLink = "https://vendor.com"
    appName     = "ProductName"
    appVersion  = "<= X.Y.Z"
    vulType     = VUL_TYPE.COMMAND_EXECUTION  # See §VulType
    category    = POC_CATEGORY.EXPLOITS.WEBAPP
    desc        = """
        Detailed description (2-3 paragraphs).
        Include: what the vuln is, how it works, what the impact is.
    """
    samples     = ["http://target1.com", "http://target2.com"]
    install_requires = []

    def _options(self):
        # See §Options
        pass

    def _verify(self):
        # See §Verify
        pass

    def _attack(self):
        # See §Attack
        pass

    def _shell(self):
        # See §Shell (optional)
        pass

    def parse_output(self, result):
        output = Output(self)
        if result:
            output.success(result)
        else:
            output.fail("target is not vulnerable")
        return output


register_poc(VulnName)
```

---

## Metadata Fields (Required)

| Field | Type | Required | Description | Example |
|-------|------|:---:|-------------|---------|
| `vulID` | str | Yes | Vulnerability ID. Priority: CVE > CNNVD > CNVD > LDYCC | `"CVE-2024-1234"` |
| `vulDate` | str | Yes | Public disclosure date | `"2024-06-15"` |
| `createDate` | str | Yes | POC creation date | `"2024-08-01"` |
| `updateDate` | str | Yes | Last modification date | `"2024-08-01"` |
| `name` | str | Yes | Format: `Vendor Product Component VulnType (CVE-ID)` | `"Apache Tomcat AJP File Read (CVE-2020-1938)"` |
| `appPowerLink` | str | Yes | Vendor/product homepage URL | `"https://tomcat.apache.org"` |
| `appName` | str | Yes | Product name (English preferred) | `"Apache Tomcat"` |
| `appVersion` | str | Yes | Affected version range. Use `\|\|` to separate ranges, `&&` for AND | `">=9.0.0&&<=9.0.36\|\|>=8.0.0&&<=8.5.56"` |
| `vulType` | enum | Yes | See **VulType Enum** below | `VUL_TYPE.COMMAND_EXECUTION` |
| `category` | enum | Yes | `POC_CATEGORY.EXPLOITS.WEBAPP` for web; `.EXPLOITS.REMOTE` for network services | `POC_CATEGORY.EXPLOITS.WEBAPP` |
| `desc` | str | Yes | Multi-line description including impact, prerequisites, exploitation method | — |
| `samples` | list[str] | Yes | At least 2-3 confirmed vulnerable targets (URLs, not IPs) | `["http://target1.com:8080"]` |

---

## VulType Enum (Must Match Exactly)

| `VUL_TYPE` constant | Chinese name | Use when |
|---------------------|-------------|---------|
| `COMMAND_EXECUTION` | 命令执行 | RCE, OS command injection |
| `FILE_UPLOAD` | 文件上传 | Arbitrary file upload to RCE |
| `UNAUTHORIZED_ACCESS` | 未授权访问 | Auth bypass, sensitive endpoint leak |
| `SQL_INJECTION` | SQL注入 | SQL injection with data extraction |
| `CODE_EXECUTION` | 代码执行 | Deserialization, expression injection |
| `PATH_DISCLOSURE` | 路径遍历 | Directory traversal, file read |
| `XSS` | 跨站脚本 | Reflected/Stored XSS |
| `SSRF` | 服务端请求伪造 | Server-side request forgery |
| `WEAK_PASSWORD` | 弱口令 | Default/weak credentials |
| `INFORMATION_DISCLOSURE` | 信息泄露 | Sensitive data exposure |

---

## Options (Common Patterns)

```python
def _options(self):
    o = OrderedDict()
    o["port"] = OptString("8080", description="Target port")
    o["cmd"] = OptString("id", description="Command to execute in attack mode")
    o["timeout"] = OptInteger(10, description="Request timeout in seconds")
    return o
```

Always include `port` and `timeout` options. Add attack-specific options
(`cmd`, `path`, `filename`) as needed for `_attack()`.

---

## Verify Logic

### Golden Rules

1. **Minimize requests**: aim for ONE request. Two at most (fingerprint + exploit).
2. **Safe payloads only**: never execute destructive commands in `_verify()`.
   Use `echo pwned` / `md5("verify")` / reading a static string.
3. **Always ignore SSL**: `requests.get(url, verify=False, timeout=...)`.
4. **Feature-based detection**: validate the vulnerability by checking
   response characteristics — status codes, response bodies, headers.
   Never rely solely on HTTP 200.

### Standard Verify Pattern

```python
def _verify(self):
    result = {}
    target = self.url.rstrip("/")
    port = self.get_option("port")

    url = f"{target}:{port}/vulnerable/path"

    try:
        resp = requests.get(
            url,
            verify=False,
            timeout=10,
            allow_redirects=False,
            headers={"User-Agent": "Mozilla/5.0 (compatible; Pocsuite3/2.0)"}
        )
    except Exception as e:
        result["Error"] = str(e)
        return self.parse_output(result)

    # Feature checks
    if resp.status_code != 200:
        return self.parse_output(result)

    if "expected_signature" not in resp.text:
        return self.parse_output(result)

    result["VerifyInfo"] = {
        "URL": url,
        "ResponseLength": len(resp.text),
        "Signature": "expected_signature",
    }
    return self.parse_output(result)
```

### Request with Random Markers

For command execution, use a unique random marker to avoid false positives
from cached responses:

```python
from pocsuite3.lib.utils import random_str

marker = random_str(12)
cmd = f"echo {marker}"
# inject cmd into payload ...

if marker in resp.text:
    result["VerifyInfo"] = {"Command": cmd, "Marker": marker}
```

---

## Attack Logic

```python
def _attack(self):
    result = {}
    target = self.url.rstrip("/")
    cmd = self.get_option("cmd") or "id"

    payload = self._build_payload(cmd)

    try:
        resp = requests.post(
            f"{target}/vuln",
            data=payload,
            verify=False,
            timeout=30
        )
        result["AttackInfo"] = {
            "Command": cmd,
            "Output": resp.text.strip(),
            "Status": resp.status_code,
        }
    except Exception as e:
        result["Error"] = str(e)

    return self.parse_output(result)
```

---

## Output Format

`parse_output()` must return exactly ONE `Output` object. Use the helper:

```python
def parse_output(self, result):
    output = Output(self)
    if result:
        output.success(result)
    else:
        output.fail("target is not vulnerable")
    return output
```

The `result` dict can contain arbitrary keys; pocsuite3 serialises them.
Convention keys:

| Key | When to use |
|-----|------------|
| `URL` | The exact URL sent |
| `ResponseLength` | `len(resp.text)` for fingerprinting |
| `Error` | Connection/parse error |
| `VerifyInfo` | Verification-specific data |
| `AttackInfo` | Attack-mode output |

---

## File Naming (For Submission)

Format: `ProductName_VulID.py`

- CVE priority: `Apache-Tomcat_CVE-2020-1938.py`
- No CVE: `ProductName_VulType_Component.py` (e.g., `Ruijie-EWEB_Auth_Bypass.py`)
- No Chinese characters, no spaces in filenames

Required companion files for submission:
1. POC `.py` file (pocsuite3 format)
2. `.pcap` traffic capture (tcpdump during verify)
3. `.rules` Suricata detection rules
4. `.md` vulnerability detail documentation
5. Docker-compose or VM image (optional)

---

## Common Mistakes (Review Rejection Reasons)

1. **Hardcoded IP/domain in payload** — use `self.url` + `self.get_option("port")`
2. **No `verify=False`** — every request must ignore SSL
3. **Missing `self.parse_output(result)` pattern** — not using the standard output helper
4. **Class name ≠ file name** — they must match exactly
5. **Destructive verify payloads** — no `rm -rf /`, `shutdown`, `wget` in `_verify()`
6. **Generic 200-based detection** — must check actual feature/keyword in response
7. **No `samples` field** — must provide at least 2 verified targets
8. **Missing `allow_redirects=False`** — can mask 302 bypasses
9. **Chinese characters in filenames** — strictly ASCII filenames only
10. **`vulType` not matching actual exploit effect** — e.g., marking INFO_DISCLOSURE as COMMAND_EXECUTION

---

## Quick Reference: Vuln Type to Template

| Vulnerability | Verify Method | Attack Method |
|-------------|--------------|---------------|
| RCE (blind) | Inject `echo <marker>` or `sleep <N>` with time check | Execute user-specified `cmd` |
| RCE (visible) | Inject command, check stdout in response | Same |
| File Upload | Upload benign file (txt), verify via GET | Upload webshell, return path |
| File Read | Read known file (`/etc/passwd`, `windows/win.ini`) | Read arbitrary file |
| SQL Injection | `AND 1=1` vs `AND 1=2` or `sleep(5)` with time delta | Dump data via `UNION SELECT` |
| Unauthorized Access | GET sensitive endpoint, check for data leakage | Full data dump |
| SSRF | Make target request callback URL (dnslog/httplog) | Internal port scan |
| Default Credentials | Try `admin:admin`, check for successful auth | Login + action |

---

## Part 2: Nuclei Template Generation

### Nuclei Template Skeleton

```yaml
id: vendor-product-vulntype

info:
  name: Vendor Product Component - Vulnerability Type
  author: your-name
  severity: critical|high|medium|low|info
  description: |
    Detailed description (2-3 sentences).
    Include impact and affected versions.
  reference:
    - https://nvd.nist.gov/vuln/detail/CVE-XXXX-XXXXX
  classification:
    cve-id: CVE-XXXX-XXXXX
    cwe-id: CWE-xxx
  tags: tag1,tag2,tag3

http:
  - method: POST
    path:
      - "{{BaseURL}}/vulnerable/path"
    headers:
      Content-Type: application/x-www-form-urlencoded
    body: "param=payload"

    matchers-condition: and
    matchers:
      - type: status
        status:
          - 200
      - type: word
        words:
          - "expected_response_signature"
        part: body

    extractors:
      - type: regex
        name: extracted_data
        part: body
        regex:
          - 'pattern(.*)'
```

### Nuclei Severity Mapping

| CVSS | Nuclei severity | When to use |
|------|:---:|------|
| 9.0–10.0 | `critical` | RCE, auth bypass, full system compromise |
| 7.0–8.9 | `high` | SQLi, file read, sensitive info disclosure |
| 4.0–6.9 | `medium` | SSRF, XSS, directory listing |
| 0.1–3.9 | `low` | Minor info leak, fingerprinting |
| — | `info` | Fingerprinting only, no direct impact |

### Common Matcher Types

| Type | Use Case | Example |
|------|---------|---------|
| `status` | Check HTTP response code | `status: [200]` |
| `word` | Check for string in response | `words: ["root:"]` |
| `regex` | Pattern match in response | `regex: ["admin:[^:]+:"]` |
| `dsl` | Dynamic expression evaluation | `dsl: ["len(body)>1000"]` |
| `time` | Response time threshold (blind RCE) | `duration: [">5000"]` |

### Vuln-Type-to-Nuclei Patterns

| Vulnerability | Method | Path | Matcher |
|-------------|--------|------|---------|
| RCE (blind, sleep) | POST | `/vuln` | `type: time, duration: [">5000"]` via `sleep 5` |
| RCE (blind, echo) | POST | `/vuln` | `type: word, words: ["{{randstr}}"]` |
| RCE (visible) | GET | `/vuln?cmd=id` | `type: word, words: ["uid="]` |
| File Upload | PUT/POST | `/upload` | `type: dsl, dsl: ["status_code==200 && contains(body,'uploaded')"]` |
| File Read | GET | `/download?file=../../etc/passwd` | `type: word, words: ["root:x:0:"]` |
| SQLi (error) | GET | `/page?id=1'` | `type: word, words: ["SQL syntax"]` |
| SQLi (blind, time) | GET | `/page?id=1 AND SLEEP(5)` | `type: time, duration: [">5000"]` |
| Unauthorized Access | GET | `/admin/config` | `type: word, words: ["password","secret"]` |
| SSRF | POST | `/proxy` | `type: word, words: ["Interactsh"]` via `{{interactsh-url}}` |
| XSS | GET | `/search?q=<script>` | `type: word, words: ["<script>alert"]` |

### Flow Template (Multi-Step Attack)

For vulnerabilities requiring multiple sequential requests:

```yaml
http:
  - method: POST
    path: ["{{BaseURL}}/login"]
    body: "username=admin&password=admin"
    # req-1: authenticate
    matchers:
      - type: status
        status: [302]

  - method: GET
    path: ["{{BaseURL}}/admin/secret"]
    # req-2: access protected resource with session from req-1
    matchers:
      - type: word
        words: ["secret_data"]
```

### Variable Generation (randstr, interactsh)

```yaml
# --- Random string marker (blind RCE) ---
variables:
  marker: "{{rand_base(12)}}"

# In matcher:
  matchers:
    - type: word
      words: ["{{marker}}"]

# --- Interactsh (OOB / SSRF / blind RCE) ---
  matchers:
    - type: word
      part: interactsh_protocol
      words: ["dns", "http"]
```

### Template for Each Common Vuln Type

**RCE (blind — time-based)**:
```yaml
http:
  - method: POST
    path: ["{{BaseURL}}/rce"]
    body: "cmd=sleep+5"
    matchers:
      - type: time
        duration: [">4000", "<10000"]
```

**RCE (blind — echo marker)**:
```yaml
http:
  - method: POST
    path: ["{{BaseURL}}/rce"]
    body: "cmd=echo+{{marker}}"
    matchers:
      - type: word
        words: ["{{marker}}"]
```

**File Upload to RCE**:
```yaml
http:
  # Step 1: Upload
  - method: PUT
    path: ["{{BaseURL}}/upload/test.txt"]
    body: "pwned_by_nuclei"
    matchers:
      - type: status
        status: [200, 201]

  # Step 2: Verify file accessible
  - method: GET
    path: ["{{BaseURL}}/uploads/test.txt"]
    matchers:
      - type: word
        words: ["pwned_by_nuclei"]
```

**SQL Injection (error-based)**:
```yaml
http:
  - method: GET
    path: ["{{BaseURL}}/product?id=1'"]
    matchers:
      - type: word
        words:
          - "SQL syntax"
          - "mysql_fetch"
          - "ORA-01756"
        condition: or
```

**Unauthorized Access (config leak)**:
```yaml
http:
  - method: GET
    path: ["{{BaseURL}}/sys/config/oss"]
    matchers-condition: and
    matchers:
      - type: status
        status: [200]
      - type: word
        words: ["PASSWORD_CONFIG", "SIGN_WAYS"]
    extractors:
      - type: json
        name: config
        json:
          - ".result[]"
```

---

## Bridge: Converting Between Formats

### Pocsuite3 -> Nuclei (Key Mappings)

| Pocsuite3 | Nuclei |
|-----------|--------|
| `self.url` / `target` | `{{BaseURL}}` |
| `requests.get/post()` | `http:` block |
| `resp.status_code` | `matchers: type: status` |
| `"signature" in resp.text` | `matchers: type: word, words:` |
| `resp.text` regex extraction | `extractors: type: regex` |
| `random_str(12)` | `{{rand_base(12)}}` |
| `sleep(5)` time delta | `matchers: type: time, duration:` |
| `VUL_TYPE.COMMAND_EXECUTION` | `severity: critical` |
| `POC_CATEGORY.EXPLOITS.WEBAPP` | `http:` block |

### Nuclei -> Pocsuite3 (Key Mappings)

| Nuclei | Pocsuite3 |
|--------|-----------|
| `{{BaseURL}}` | `self.url` |
| `id:` | `vulID` |
| `info.name` | `name` |
| `info.author` | `author` |
| `info.severity` | map to `VUL_TYPE` |
| `http:` block with `method: GET/POST` | `requests.get/post()` |
| `matchers: type: status` | `resp.status_code` check |
| `matchers: type: word` | `"word" in resp.text` |
| `{{randstr}}` | `random_str()` |
| `{{interactsh-url}}` | DNS/http log callback |

---

> **Reference**: 360 BugCloud POC Submission Specification v2.0  
> **Pocsuite3**: https://github.com/knownsec/pocsuite3  
> **Nuclei**: https://github.com/projectdiscovery/nuclei  
> **Nuclei Template Guide**: https://docs.projectdiscovery.io/templates/introduction
