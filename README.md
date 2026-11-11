# Omni OTP (`otp`)

# About

**`otp`** is a multi-factor authentication (TOTP/OTP) manager and code generator for the [Omni Shell Framework](../omni/README.md). It stores Base32 secrets in a simple flat file, supports adding accounts directly from `otpauth://` URIs or manual service/user/secret flags, generates time-based one-time passwords via `oathtool`, and automatically copies codes to the system clipboard when `xclip` is available.

## Key Features

- **Standard `otpauth://` URI Parsing**: Register new accounts directly from `otpauth://totp/...` URLs (including URL-decoded labels and query parameters) or via explicit `--service`, `--user`, and `--secret` options.
- **Clipboard Integration**: Automatically copies generated TOTP codes to the clipboard via `xclip -selection clipboard` (with stdout fallback when `xclip` is not installed).
- **Batch & Single Lookup**: Generate a single service's OTP code by name or display all registered accounts' current codes at once.
- **Tab Completion**: Automatically completes registered account names and CLI flags.

## Prerequisites

- `oathtool` (e.g., `sudo apt-get install oathtool`)
- `xclip` (optional, for automatic clipboard copying)

---

# Install

## Via the Official `omni` Provider

```bash
omni providers install omni
omni tools use otp --provider omni
```

## From a Local Directory

```bash
omni tools use -p /path/to/omni/otp
```

## Directly from GitHub

```bash
omni tools use -u https://github.com/omnishell/otp.git
```

---

# Usage

## Configuring the Secrets File

By default, `otp` reads and writes secrets to the path specified in `$OTP_SECRETS_FILE`, or you can pass `--file <path>` to any invocation:

```bash
export OTP_SECRETS_FILE="$HOME/.omni/otp_secrets"
```

Each entry in the secrets file is stored in the colon-delimited format:

```text
<name>:<type>:<base32_secret>
```

---

## Commands

### Register a New OTP Entry (`otp new`)

Add an entry using an `otpauth://` URL:

```bash
# Positional syntax
otp [--file <path>] new <name> "<otpauth_url>"

# Flag syntax
otp [--file <path>] new --name <name> --url "<otpauth_url>"
```

Or add an entry manually using `--service`, optional `--user`, and `--secret`:

```bash
otp [--file <path>] new <name> --service <service> [--user <user>] --secret <base32_secret>
```

**Examples:**

```bash
# Register from an otpauth:// URI
otp new github "otpauth://totp/GitHub:alice?secret=JBSWY3DPEHPK3PXP&issuer=GitHub"

# Register manually with service, user, and Base32 secret
otp new aws --service AWS --user alice@example.com --secret JBSWY3DPEHPK3PXP
```

### Generate a TOTP Code (`otp <name>`)

Generate the current 6-digit TOTP token for `<name>`. If `xclip` is installed, the token is copied directly to your clipboard:

```bash
otp [--file <path>] <name>
```

**Example:**

```bash
otp github
# Output: OTP for 'github' copied to clipboard! (or prints the 6-digit code)
```

### Generate Codes for All Entries (`otp`)

Calling `otp` with no subcommand prints the current TOTP codes for every entry in the secrets file:

```bash
otp [--file <path>]
```

### List Registered Entries (`otp list`)

List all configured entry names:

```bash
otp [--file <path>] list
```

### Remove an Entry (`otp rm`)

Delete an entry from the secrets file:

```bash
otp [--file <path>] rm <name>
otp [--file <path>] rm --name <name>
```
