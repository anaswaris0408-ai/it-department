# Steganography Toolkit — Encoding Only (Image + Audio) — Flask + SQLite

## What this app does (Encoding Only)
- Secure login + RBAC (Admin / Analyst)
- Encode hidden text messages into:
  - PNG/BMP images (LSB embedding)
  - WAV audio (LSB embedding)
- Capacity planning:
  - Calculates maximum payload capacity for the carrier
  - Estimates distortion and flags unsafe settings
- Adds “enterprise evidence integrity”:
  - SHA256 checksums for input/output
  - Blockchain-inspired local hash chain ledger in database/ledger.json
- Stores every run in SQLite history (data.db in app root)
- Dashboard charts + summary cards
- PDF report export (ReportLab)
- Export Artifact ZIP bundle
- Checksum Verification page

## Encoding design (high-level)
We pack a structured payload:
- Header includes:
  - MAGIC, VERSION
  - flags (compression/encryption)
  - original filename (for traceability)
  - plaintext length, compressed length
  - timestamp
  - salt + nonce
  - ciphertext
  - HMAC tag (auth)
  - CRC32 of plaintext (quick integrity)

Encryption:
- PBKDF2-HMAC-SHA256 derives two keys:
  - stream key for XOR keystream cipher
  - HMAC key for authentication
- Wrong password fails safely.

Compression:
- Uses zlib compression only if it reduces size.

Image embedding:
- LSB in RGB channels, configurable bits-per-channel (1 or 2).
- Output always saved as PNG for stability.

Audio embedding:
- WAV PCM (8-bit or 16-bit), 1 bit per sample (safe default)
- Output WAV preserved with same params.

## Run (Windows 10/11)
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
python app.py
Open http://127.0.0.1:5000

## PyInstaller
pyinstaller --noconfirm --clean ^
  --name stego_toolkit ^
  --add-data "templates;templates" ^
  --add-data "static;static" ^
  --add-data "database;database" ^
  app.py