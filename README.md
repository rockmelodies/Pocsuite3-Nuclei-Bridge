# Pocsuite3-Nuclei-Bridge

> 双格式 POC 生成器 — 一份漏洞报告，同时输出 Pocsuite3 + Nuclei 两份 POC  
> Dual-format POC Generator — one vulnerability report, two POC outputs

<p align="center">
  <img src="https://img.shields.io/badge/Pocsuite3-2.0+-blue?style=for-the-badge&logo=python" alt="Pocsuite3">
  <img src="https://img.shields.io/badge/Nuclei-3.0+-green?style=for-the-badge&logo=nuclei" alt="Nuclei">
  <img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" alt="MIT">
  <img src="https://img.shields.io/badge/Skill-Production_Ready-red?style=for-the-badge" alt="Production Ready">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Vuln_Types-10+-orange?style=flat-square" alt="Vuln Types">
  <img src="https://img.shields.io/badge/Bidirectional-Yes-success?style=flat-square" alt="Bidirectional">
  <img src="https://img.shields.io/badge/BugCloud_Compliant-Yes-blue?style=flat-square" alt="BugCloud">
  <img src="https://img.shields.io/badge/Updated-2026--08-informational?style=flat-square" alt="Updated">
</p>

---

## 这是什么 / What Is This

从同一份漏洞报告**同时生成**两种格式的 POC：
Generate **both** formats from a single vulnerability report:

| 格式 / Format | 文件 / File | 场景 / Use Case |
|:---|:---|:---|
| **Pocsuite3** | `.py` | 360 BugCloud 提交、批量验证、深度利用 |
| **Nuclei** | `.yaml` | 快速大规模扫描、CI/CD 集成、模板生态 |

双向转换：Pocsuite3 ↔ Nuclei / Bidirectional conversion.

---

## 文件结构 / Project Structure

```
Pocsuite3-Nuclei-Bridge/
├── SKILL.md          ← 完整编写规范 (Part 1: Pocsuite3 + Part 2: Nuclei + Bridge)
├── README.md         ← 本文件 / This File
└── LICENSE
```

---

## 快速开始 / Quick Start

### 从漏洞报告生成 POC / Generate from Vuln Report

```
你: "帮我给这个漏洞写 Pocsuite3 + Nuclei 双格式 POC"
   → 输出: 符合 360 BugCloud 规范的 .py 文件
   → 同时输出: 对应的 Nuclei .yaml 模板

You: "Generate both Pocsuite3 and Nuclei POC for this CVE"
   → Output: 360 BugCloud-compliant .py file + Nuclei .yaml template
```

### 快速生成常用类型 / Quick Templates

| 漏洞类型 | 命令示例 |
|---------|---------|
| RCE | `"给 CVE-2024-1234 写双格式 POC"` |
| SQL 注入 | `"这个 SQL 注入漏洞生成 Pocsuite3 + Nuclei"` |
| 未授权访问 | `"契约锁配置泄露漏洞帮我写 POC"` |
| 文件上传 | `"文件上传 getshell 漏洞生成 POC"` |
| SSRF | `"SSRF 漏洞写 Pocsuite3 和 Nuclei"` |

### 格式互转 / Format Conversion

```
"把这个 Nuclei 模板转成 Pocsuite3"
"Convert this Pocsuite3 POC to Nuclei"
```

---

## 支持的漏洞类型 / Supported Vuln Types

| 类型 / Type | Pocsuite3 | Nuclei Severity | POC 复杂度 |
|:---|:---|:---:|:---:|
| 命令执行 / RCE | `COMMAND_EXECUTION` | `critical` | 中 |
| 文件上传 / File Upload | `FILE_UPLOAD` | `critical` | 中 |
| 代码执行 / Code Exec | `CODE_EXECUTION` | `critical` | 高 |
| 未授权访问 / Unauth | `UNAUTHORIZED_ACCESS` | `high` | 低 |
| SQL 注入 / SQLi | `SQL_INJECTION` | `high` | 中 |
| 路径遍历 / Path Traversal | `PATH_DISCLOSURE` | `high` | 低 |
| 弱口令 / Default Creds | `WEAK_PASSWORD` | `high` | 低 |
| 信息泄露 / Info Leak | `INFORMATION_DISCLOSURE` | `medium` | 低 |
| SSRF | `SSRF` | `medium` | 中 |
| XSS | `XSS` | `medium` | 低 |

---

## 规范要点 / Key Rules

| 规则 / Rule | 说明 |
|:---|:---|
| 单请求验证 | `_verify()` 尽量一次请求完成 |
| 忽略 SSL | 所有请求 `verify=False` |
| 安全 Payload | `_verify()` 中用 `echo` / `sleep`，不做破坏性操作 |
| ASCII 文件名 | `ProductName_CVE-YYYY-NNNN.py` |
| 无外部依赖 | 仅用 `pocsuite3.api` 内置 API |
| Pocsuite3 → Nuclei | 9 项自动映射（BaseURL / matchers / extractors） |
| Nuclei → Pocsuite3 | 9 项自动映射（status / word / regex / randstr） |

---

## 参考 / References

| 资料 | 链接 |
|:---|:---|
| Pocsuite3 | https://github.com/knownsec/pocsuite3 |
| Nuclei | https://github.com/projectdiscovery/nuclei |
| Nuclei 模板指南 | https://docs.projectdiscovery.io/templates/introduction |
| 360 BugCloud 提交规范 | 内部文档 |

---

<p align="center">
  <sub>Made with <a href="https://github.com/rockmelodies">rockmelodies</a></sub>
</p>

## License

MIT
