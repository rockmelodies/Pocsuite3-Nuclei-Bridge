# Pocsuite3-Nuclei-Bridge

A professional skill repository for creating production-grade Pocsuite3 POC scripts aligned with the 360 BugCloud submission specification.

## What This Skill Covers

- Complete POC file structure and skeleton
- Metadata field specification (20+ required/optional fields)
- `VUL_TYPE` enumeration with selection guidance
- `_verify()` / `_attack()` / `_shell()` implementation patterns
- Options, output formatting, and file naming conventions
- 10 common review rejection reasons and how to avoid them
- Quick reference: vulnerability type to verify/attack method mapping
- Templates for RCE, file upload, SQL injection, SSRF, unauth access, file read

## Usage

Load this skill when:
- Writing Pocsuite3 POC scripts from vulnerability reports
- Converting Nuclei templates to Pocsuite3 format
- Preparing vulnerability submissions for BugCloud review
- Creating batch verification scripts

## Conventions

- Strict field naming per 360 BugCloud specification
- One-request verification whenever possible
- SSL verification always disabled
- Safe payloads in `_verify()`, destructive only in `_attack()`
- ASCII-only filenames: `ProductName_CVE-XXXX-XXXX.py`

## Reference

- [Pocsuite3](https://github.com/knownsec/pocsuite3)
- [Nuclei](https://github.com/projectdiscovery/nuclei)
- 360 BugCloud POC Submission Specification

## License

MIT
