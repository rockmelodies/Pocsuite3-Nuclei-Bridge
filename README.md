# Pocsuite3-Nuclei-Bridge

<p align="center">
  <a href="#chinese"><img src="https://img.shields.io/badge/中文-简体-red?style=for-the-badge" alt="中文"></a>
  <a href="#english"><img src="https://img.shields.io/badge/English-EN-blue?style=for-the-badge" alt="English"></a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Pocsuite3-2.0+-blue?style=flat-square&logo=python" alt="Pocsuite3">
  <img src="https://img.shields.io/badge/Nuclei-3.0+-green?style=flat-square" alt="Nuclei">
  <img src="https://img.shields.io/badge/License-MIT-yellow?style=flat-square" alt="MIT">
  <img src="https://img.shields.io/badge/Vuln_Types-10+-orange?style=flat-square" alt="Vuln Types">
  <img src="https://img.shields.io/badge/Bidirectional-Yes-success?style=flat-square" alt="Bidirectional">
</p>

> 双格式 POC 生成器 — 一份漏洞报告，同时输出 Pocsuite3 + Nuclei 两份 POC  
> Dual-format POC Generator — one vulnerability report, two POC outputs

---

## 无法访问 GitHub? / Cannot Access GitHub?

```bash
# Windows (cmd)
set http_proxy=http://127.0.0.1:7897
set https_proxy=http://127.0.0.1:7897

# Linux / macOS / Git Bash
export http_proxy=http://127.0.0.1:7897
export https_proxy=http://127.0.0.1:7897

# 然后正常 git clone / Then git clone normally
git clone https://github.com/rockmelodies/Pocsuite3-Nuclei-Bridge.git
```

---

<h2 id="chinese">🇨🇳 中文</h2>

### 这是什么

从同一份漏洞报告**同时生成**两种格式的 POC：

| 格式 | 文件 | 场景 |
|:---|:---|:---|
| **Pocsuite3** | `.py` | 批量验证、深度利用、POC 提交 |
| **Nuclei** | `.yaml` | 快速大规模扫描、CI/CD 集成、模板生态 |

双向转换：Pocsuite3 ↔ Nuclei。

### 快速开始

```
你: "帮我给这个漏洞写 Pocsuite3 + Nuclei 双格式 POC"
   → 输出: .py + .yaml 两份 POC
```

| 漏洞类型 | 命令示例 |
|---------|---------|
| RCE | `"给 CVE-2024-1234 写双格式 POC"` |
| SQL 注入 | `"这个 SQL 注入漏洞生成 Pocsuite3 + Nuclei"` |
| 未授权访问 | `"契约锁配置泄露漏洞帮我写 POC"` |
| 文件上传 | `"文件上传 getshell 漏洞生成 POC"` |
| SSRF | `"SSRF 漏洞写 Pocsuite3 和 Nuclei"` |
| 格式互转 | `"把这个 Nuclei 模板转成 Pocsuite3"` |

### 支持的漏洞类型

| 类型 | Pocsuite3 | Nuclei |
|:---|:---|:---:|
| 命令执行 / RCE | `COMMAND_EXECUTION` | `critical` |
| 文件上传 / File Upload | `FILE_UPLOAD` | `critical` |
| 代码执行 / Code Exec | `CODE_EXECUTION` | `critical` |
| 未授权访问 / Unauth | `UNAUTHORIZED_ACCESS` | `high` |
| SQL 注入 / SQLi | `SQL_INJECTION` | `high` |
| 路径遍历 / Path Traversal | `PATH_DISCLOSURE` | `high` |
| 弱口令 / Default Creds | `WEAK_PASSWORD` | `high` |
| 信息泄露 / Info Leak | `INFORMATION_DISCLOSURE` | `medium` |
| SSRF | `SSRF` | `medium` |
| XSS | `XSS` | `medium` |

### 规范要点

| 规则 | 说明 |
|:---|:---|
| 单请求验证 | `_verify()` 尽量一次请求完成 |
| 忽略 SSL | 所有请求 `verify=False` |
| 安全 Payload | `_verify()` 中用 `echo` / `sleep` |
| ASCII 文件名 | `ProductName_CVE-YYYY-NNNN.py` |
| 无外部依赖 | 仅用 `pocsuite3.api` 内置 API |

### 项目结构

```
Pocsuite3-Nuclei-Bridge/
├── SKILL.md          ← Part 1: Pocsuite3 + Part 2: Nuclei + 双向桥接
├── README.md         ← 本文件
└── LICENSE
```

---

<h2 id="english">🇺🇸 English</h2>

### What Is This

Generate **both** POC formats from a single vulnerability report:

| Format | File | Use Case |
|:---|:---|:---|
| **Pocsuite3** | `.py` | Batch verification, deep exploitation, POC submission |
| **Nuclei** | `.yaml` | High-speed mass scanning, CI/CD integration, template ecosystem |

Bidirectional conversion: Pocsuite3 ↔ Nuclei.

### Quick Start

```
You: "Generate both Pocsuite3 and Nuclei POC for this CVE"
   → Output: .py + .yaml dual-format POC
```

| Vuln Type | Example |
|-----------|---------|
| RCE | `"Write dual-format POC for CVE-2024-1234"` |
| SQL Injection | `"Generate Pocsuite3 + Nuclei for this SQLi"` |
| Unauthorized Access | `"Write POC for config leak vulnerability"` |
| File Upload | `"Generate POC for file upload to RCE"` |
| SSRF | `"Write Pocsuite3 and Nuclei for SSRF"` |
| Format Conversion | `"Convert this Nuclei template to Pocsuite3"` |

### Supported Vuln Types

| Type | Pocsuite3 | Nuclei |
|:---|:---|:---:|
| RCE | `COMMAND_EXECUTION` | `critical` |
| File Upload | `FILE_UPLOAD` | `critical` |
| Code Execution | `CODE_EXECUTION` | `critical` |
| Unauthorized Access | `UNAUTHORIZED_ACCESS` | `high` |
| SQL Injection | `SQL_INJECTION` | `high` |
| Path Traversal | `PATH_DISCLOSURE` | `high` |
| Default Credentials | `WEAK_PASSWORD` | `high` |
| Information Disclosure | `INFORMATION_DISCLOSURE` | `medium` |
| SSRF | `SSRF` | `medium` |
| XSS | `XSS` | `medium` |

### Key Rules

| Rule | Description |
|:---|:---|
| Single-request verify | One HTTP request whenever possible |
| SSL always disabled | `verify=False` on all requests |
| Safe payloads | Use `echo` / `sleep` in `_verify()` |
| ASCII filenames | `ProductName_CVE-YYYY-NNNN.py` |
| Zero external deps | Only `pocsuite3.api` built-ins |

### Project Structure

```
Pocsuite3-Nuclei-Bridge/
├── SKILL.md          ← Part 1: Pocsuite3 + Part 2: Nuclei + Bridge
├── README.md         ← This file
└── LICENSE
```

---

## References

| Resource | Link |
|:---|:---|
| Pocsuite3 | https://github.com/knownsec/pocsuite3 |
| Nuclei | https://github.com/projectdiscovery/nuclei |
| Nuclei Template Guide | https://docs.projectdiscovery.io/templates/introduction |

---

<p align="center">
  <sub>Made by <a href="https://github.com/rockmelodies">rockmelodies</a></sub>
</p>

## License

MIT
