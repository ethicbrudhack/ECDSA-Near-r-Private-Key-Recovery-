# 🔐 ECDSA Near-r Private Key Recovery  
`recover_near_r.py`

> ⚙️ Minimal, precise, and extremely fast recovery tool for analyzing cases  
> where two ECDSA signatures were created with **nearly identical nonces** (`k₂ = k₁ + t`).  
>  
> The script allows you to recover:  
> - ephemeral keys `k₁` and `k₂`  
> - private key `d`  
> - difference `t`  
> assuming |t| ≤ Δ (for example, 2¹⁶ or 2²⁰).

---

## 🚀 Overview

The script searches over a small integer range Δ to find the correct offset `t`
between two ECDSA nonces:

> **k₂ = k₁ + t**

It solves the algebraic system from the ECDSA equations and returns  
the recovered `(k₁, k₂, d, t)` when both signatures are consistent.

---

## ✨ Features

| Feature | Description |
|----------|-------------|
| ⚙️ **Direct brute-force over t** | Scans `t` in ±Δ range |
| 🧮 **Algebraic solution of ECDSA equations** | Uses the linear relation between two signatures |
| 🔍 **Dual verification** | Validates recovered `d` by checking `s₂` consistency |
| 🚫 **Automatic skip** | Ignores degenerate cases (non-invertible values mod n) |
| 📈 **Flexible range** | Adjustable `max_delta` for deeper search |
| 🧩 **Readable console output** | Optional verbose printing for found results |
| 🧠 **Zero dependencies** | Pure Python — no external libraries required |

---

## 📂 File Structure

| File | Description |
|------|-------------|
| `recover_near_r.py` | Main recovery script |
| `README.md` | Documentation (this file) |

---

## ⚙️ Usage

1. Insert your parameters in the section:

```python
r1 = int("0x...", 16)
r2 = int("0x...", 16)
s1 = int("0x...", 16)
s2 = int("0x...", 16)
z1 = int("0x...", 16)
z2 = int("0x...", 16)
n  = 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141
Run the script:

python3 recover_near_r.py


Example output:

Found: t=-37, k1=0x13c56a..., k2=0x13c568..., d=0x1f7a3b...
Found 1 solutions.
t = -37
k1 = 0x13c56a1d...
k2 = 0x13c568fa...
d  = 0x1f7a3b2c...
----

🧩 Functions
Function	Description
mod_inv(a, n)	Modular inverse with safe exception handling
try_recover_near_r()	Main brute-force loop returning (k₁, k₂, d, t)
verbose=True	Prints solutions immediately when found
max_delta	Sets Δ range (e.g., 2¹⁶ = 65,536)
🧠 Mathematical Background

Given two ECDSA signatures (r₁, s₁, z₁) and (r₂, s₂, z₂):

s₁ = k₁⁻¹ (z₁ + r₁·d) mod n
s₂ = k₂⁻¹ (z₂ + r₂·d) mod n


and assuming k₂ = k₁ + t,
we can algebraically solve for k₁ as:

k₁ = (r₂·z₁ - r₁·z₂ + r₁·s₂·t) * (r₂·s₁ - r₁·s₂)⁻¹ mod n
k₂ = k₁ + t


then recover the private key:

d = (s₁·k₁ - z₁) * r₁⁻¹ mod n


Verification step:

s₂ ≡ k₂⁻¹ (z₂ + r₂·d) mod n


If satisfied — the result is valid ✅

⚙️ Parameters
Parameter	Description	Default
max_delta	Range of t values tested (±Δ)	2**20
verbose	Print solutions immediately	False
n	SECP256k1 curve order	0xFFFFFFFF...
⚡ Performance Notes

Pure Python implementation — no dependencies

Linear complexity O(2Δ)

Example: Δ = 2¹⁶ → ~130,000 iterations total

Easily scales to 2²⁰ if CPU allows

Prints results live in verbose mode

🧩 Example Workflow

Extract (r, s, z) from two ECDSA signatures (from your blockchain dataset).

Insert them into the script.

Run with max_delta = 2**16 for initial scan.

Increase range if no match found.

When a valid (k₁, k₂, d) appears — success.

🔒 Ethical Use Notice

This script is provided solely for educational and cryptographic research purposes.
It demonstrates the vulnerability of reused or near-reused ECDSA nonces
and should never be used for unauthorized key extraction or exploitation.

You may:

Audit your own cryptographic implementations

Analyze nonce reuse in testnet or research datasets

You must not:

Attempt to recover private keys from third-party data

Use this script on live or mainnet keys without authorization

⚖️ Always respect cryptographic privacy and ethical research boundaries.

🪪 License

MIT License
© 2025 — Author: [Ethicbrudhack]

💡 Summary

This script illustrates how tiny differences in ECDSA nonces (k₂ ≈ k₁)
can reveal the private key — entirely through modular arithmetic.

“A single nonce reused twice is a whisper of the private key.”
— [Ethicbrudhack]

BTC donation address: bc1q4nyq7kr4nwq6zw35pg0zl0k9jmdmtmadlfvqhr
