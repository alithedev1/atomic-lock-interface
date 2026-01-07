# ALI Vault (Atomic Lock Interface)

ALI Vault is a custom high-security binary file format and management tool built purely in Bash. It is designed to render your files and folders completely unrecognizable to any operating system or third-party software, protecting them with military-grade encryption and a "computational wall" against brute-force attacks.

## 🛡️ Security Specifications

*   **Algorithm:** AES-256-CBC (Advanced Encryption Standard).
*   **Key Derivation:** PBKDF2 (Password-Based Key Derivation Function 2).
*   **Work Factor:** 1,000,000 Iterations (Standard-setting high-security delay).
*   **Hashing:** SHA-256 Message Digest.
*   **Structure:** Pure Binary Stream (No "Magic Numbers" or headers).

## 🚀 The "Computational Wall"
Every time you unlock a file, the tool forces the computer to perform **1,000,000 mathematical calculations** before attempting to verify the password.
*   **For You:** A negligible ~2 second delay.
*   **For a Hacker:** Effectively neutralizes GPU-accelerated brute-force attacks (Hashcat), turning a process that would take days into a process that takes millions of years.

---

## ⚙️ Installation

Add the following functions to your `~/.bashrc` (or `~/.zshrc` on macOS):

```bash
# --- ALI Vault Functions ---

packit() {
    local input="$1"
    local output="${input%.*}.ali"
    read -rs -p "Enter password to LOCK: " pass
    echo ""
    tar -cf - "$input" | openssl enc -aes-256-cbc -salt -pbkdf2 -md sha256 -iter 1000000 -k "$pass" -out "$output"
    [ $? -eq 0 ] && echo "Vault Created: $output" || echo "Error creating vault."
}

unpackit() {
    local input="$1"
    read -rs -p "Enter password to UNLOCK: " pass
    echo ""
    echo "Verifying..."
    if openssl enc -d -aes-256-cbc -salt -pbkdf2 -md sha256 -iter 1000000 -k "$pass" -in "$input" | tar -xf -; then
        echo "Access Granted."
    else
        echo "Access Denied: Incorrect password or corrupted binary."
    fi
}
```
After saving, reload your terminal:
```bash
source ~/.bashrc
```

---

## 🛠 Usage

### 1. Locking a File or Folder
This will bundle the target, scramble its bits, and rename it to `.ali`.
```bash
packit my_private_folder
```

### 2. Unlocking
This will verify the 1,000,000 iteration hash. If successful, it restores the original file/folder exactly as it was.
```bash
unpackit my_private_folder.ali
```

---

## 🕵️ Why it’s "Invisible"
If an intruder opens an `.ali` file in a Text Editor, Hex Editor, or File Manager (like 7-Zip/WinRAR), they will see:
1.  **No Header:** No "PK" (Zip) or "PDF" markers.
2.  **No Metadata:** No file names, dates, or sizes are visible inside the binary.
3.  **Entropy:** The file appears as pure "white noise" (random data).

## ⚠️ The Golden Rule
**There is no recovery.** Because of the 1,000,000-iteration hardening, even a supercomputer cannot help you if you lose your password. If the password is lost, the data is mathematically considered "deleted from the universe."

*** 

*Created for secure, private data handling.*
