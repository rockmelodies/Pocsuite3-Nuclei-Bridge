# Pocsuite3-Nuclei-Bridge

> 双格式 POC 生成器 — 一份漏洞报告，同时输出 Pocsuite3 + Nuclei 两份 POC  
> Dual-format POC Generator — one vulnerability report, two POC outputs

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## 这是什么 / What Is This

从同一份漏洞报告**同时生成**两种格式的 POC：
Generate **both** formats from a single vulnerability report:

| 格式 / Format | 文件 / File | 场景 / Use Case |
|:---|:---|:---|
| **Pocsuite3** | `.py` | 360 BugCloud 提交、批量验证、深度利用 |
| **Nuclei** | `.yaml` | 快速大规模扫描、CI/CD 集成、模板生态 |

双向转换：给定 Pocsuite3 POC → 生成 Nuclei 模板；给定 Nuclei 模板 → 生成 Pocsuite3 POC。
Bidirectional: Pocsuite3 → Nuclei and Nuclei → Pocsuite3.

---

## 快速开始 / Quick Start

### 从漏洞报告生成 POC / Generate from Vuln Report

```
你: "帮我给这个漏洞写一个 Pocsuite3 POC"
   → 输出: 符合 360 BugCloud 规范的 .py 文件
   → 同时输出: 对应的 Nuclei .yaml 模板

You: "Write a Pocsuite3 POC for this vulnerability"
   → Output: 360 BugCloud-compliant .py file
   → Also outputs: matching Nuclei .yaml template
```

### 格式互转 / Format Conversion

```
你: "把这个 Nuclei 模板转成 Pocsuite3"
   → 读取 .yaml → 生成 .py

You: "Convert this Pocsuite3 POC to Nuclei"
   → Read .py → Generate .yaml
```

---

## 支持的漏洞类型 / Supported Vuln Types

| 类型 / Type | Pocsuite3 | Nuclei Severity |
|:---|:---|:---:|
| 命令执行 / RCE | `COMMAND_EXECUTION` | `critical` |
| 文件上传 / File Upload | `FILE_UPLOAD` | `critical` |
| 未授权访问 / Unauth Access | `UNAUTHORIZED_ACCESS` | `high` |
| SQL 注入 / SQL Injection | `SQL_INJECTION` | `high` |
| 代码执行 / Code Execution | `CODE_EXECUTION` | `critical` |
| 路径遍历 / Path Traversal | `PATH_DISCLOSURE` | `high` |
| SSRF | `SSRF` | `medium` |
| XSS | `XSS` | `medium` |
| 弱口令 / Default Creds | `WEAK_PASSWORD` | `high` |
| 信息泄露 / Info Disclosure | `INFORMATION_DISCLOSURE` | `medium` |

---

## 文件结构 / Project Structure

```
Pocsuite3-Nuclei-Bridge/
├── SKILL.md          ← 完整编写规范 / Full Authoring Specification
│   ├── Part 1: Pocsuite3 POC 编写规范 (20+ 元数据字段 / _verify / _attack)
│   ├── Part 2: Nuclei YAML 模板规范 (Matchers / Extractors / Flow)
│   └── Bridge: 双向格式映射表 (Pocsuite3 ↔ Nuclei)
├── README.md         ← 本文件 / This File
└── LICENSE
```

---

## 规范要点 / Key Rules

| 规则 / Rule | 说明 / Description |
|:---|:---|
| 单请求验证 | `_verify()` 尽量一次请求完成 / One-request verification |
| 忽略 SSL | 所有请求 `verify=False` / SSL always disabled |
| 安全 Payload | `_verify()` 中使用 `echo` / `sleep`，不做破坏性操作 |
| ASCII 文件名 | `ProductName_CVE-YYYY-NNNN.py` |
| 不依赖外部库 | 仅用 `pocsuite3.api` 内置 API |

---

## 参考 / References

| 资料 / Resource | 链接 / Link |
|:---|:---|
| Pocsuite3 | https://github.com/knownsec/pocsuite3 |
| Nuclei | https://github.com/projectdiscovery/nuclei |
| Nuclei 模板指南 | https://docs.projectdiscovery.io/templates/introduction |
| 360 BugCloud 提交规范 | 内部文档 |

---

## License

MIT
