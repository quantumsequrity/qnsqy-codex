---
name: qnsqy
description: Read before calling any qnsqy_ tool. Use when the user asks to use QNSQY or a .qs file, or asks to scan their files for quantum-vulnerable cryptography or a CBOM, encrypt files to a public key, export a public key, check signatures, hashes or encrypted files, or decrypt, sign, create keys or sign in with QNSQY on their own computer.
---

# QNSQY

QNSQY is a post-quantum cryptography toolkit from Quantum Sequrity. It
implements the FIPS 203 (ML-KEM) and FIPS 204 (ML-DSA) algorithms, but it is
not FIPS 140-validated, SOC 2 certified, FedRAMP authorized or HIPAA-audited,
so never say or suggest that it is.

These rules apply whenever QNSQY, its tools or a `.qs` file is part of the
task.

## Where the tools work

The QNSQY tools come from the QNSQY program on the user's own computer.

*   **Where they work today**: Codex on a Linux computer that has QNSQY's
    current release installed.
*   **Where they do not work**: Windows and macOS, which are not supported yet.
    Tell the user so, and point them to https://quantumsequrity.com/download.

## Rules

1.  **Use only this plugin's strict QNSQY tools.** They come from the server
    named `qnsqy_strict`, and there are 13 of them: `qnsqy_scan`,
    `qnsqy_encrypt`, `qnsqy_batch_encrypt`, `qnsqy_key_export`,
    `qnsqy_verify`, `qnsqy_batch_verify`, `qnsqy_verify_release`,
    `qnsqy_hash`, `qnsqy_hash_verify`, `qnsqy_verify_integrity`,
    `qnsqy_status`, `qnsqy_usage` and `qnsqy_version`. Codex names them with
    the server name in front, as in `mcp__qnsqy_strict__qnsqy_scan`. None of them
    has a field for a password, passphrase, private key, API key or payment
    token, or a `pubkey` or `force` field.

    Before your first QNSQY call, check your tool list. Another QNSQY server
    is running next to this plugin, for example one that the user added by
    hand, if you see any of these:
    *   a QNSQY tool from a server other than `qnsqy_strict`;
    *   a QNSQY tool that is not on the list, such as `qnsqy_decrypt`,
        `qnsqy_sign` or `qnsqy_login`;
    *   one of the 13 names twice;
    *   a QNSQY tool with one of the fields above.

    Then use no QNSQY tool at all. Tell the user that another QNSQY server is
    running, and that the plugin's README says how to remove it.
2.  **Never ask for, accept or pass on a secret.** A secret is a password,
    passphrase, private key, API key or payment token. Never ask for one,
    accept one, repeat one or pass one on, whether in a tool call, a command or
    a message. If the user offers one:
    *   do not use it;
    *   ask them to keep it out of the chat;
    *   give the matching hand-off from rule 6.
3.  **Never put a file name inside a command.** You may name a file in a
    sentence, but never inside a command.
4.  **Give every file and folder to the tools as a full path** that starts
    with `/`, such as the project folder's path followed by the file name.
    Codex starts the QNSQY server in the plugin's data folder, so a relative
    path such as `report.pdf` would point there instead of at the user's file.
5.  **Never run `qnsqy` in your own shell.** Use the strict tools. For
    anything they do not cover, give a hand-off.
6.  **Hand-offs.** Use these fixed words:
    *   **Decrypt**: "Open the QNSQY app from your desktop menu and choose the
        file in the app, or run `qnsqy decrypt` in a separate terminal window,
        outside Codex and not with `!`. QNSQY asks for the password in its own
        window or prompt."
    *   **Sign**: "Sign it in the QNSQY app, or run `qnsqy sign` in a separate
        terminal window, outside Codex and not with `!`."
    *   **Create a key pair**: "Create it in the QNSQY app, or run
        `qnsqy keygen-enc` (for encryption) or `qnsqy keygen-sign` (for
        signing) in a separate terminal window, outside Codex and not with
        `!`."
    *   **Sign in**: "Sign in with the QNSQY app, or run `qnsqy login` in a
        separate terminal window, outside Codex and not with `!`."
    *   **Create an account or an API key**: "Create the account at
        https://quantumsequrity.com or in the QNSQY app, and manage API keys in
        your dashboard at https://quantumsequrity.com/dashboard."
    *   **Anything else that needs a password**: "This needs a password, so
        use the QNSQY app, or a separate terminal window outside Codex, for
        it." This covers password encryption, the vault, rekey, deniable
        containers, time-lock, stego, polyglot files, migrate, threshold
        decryption, and ABE authority and user keys.

## Which tool for which task

The tools you see depend on the user's plan and on their QNSQY build, so rely
on the tool list that you actually have.

*   **Find quantum-vulnerable cryptography or make a CBOM**: `qnsqy_scan`. Use
    `format: cyclonedx` for a CycloneDX 1.6 CBOM. It does not overwrite an
    existing report file.
*   **Encrypt for someone**: `qnsqy_encrypt` for one file and
    `qnsqy_batch_encrypt` for several files (Pro and above). Set `recipient` to
    the recipient's public key file.
*   **Share my public key**: `qnsqy_key_export` (Pro and above). It writes the
    public key to a new file.
*   **Check what I received**:
    *   **A signed `.qs` file and the sender's public key**: `qnsqy_verify`.
    *   **Several signed files**: `qnsqy_batch_verify` (Pro and above).
    *   **A QNSQY release file and its `.sig` file**: `qnsqy_verify_release`,
        which checks against the release key built into QNSQY.
    *   **Hashes**: `qnsqy_hash` and `qnsqy_hash_verify`.
    *   **An encrypted `.qs` file, checked without a password**:
        `qnsqy_verify_integrity`.
*   **Check my setup**:
    *   **Account and plan**: `qnsqy_status`.
    *   **Credits**: `qnsqy_usage`.
    *   **The program**: `qnsqy_version`.

## Troubleshooting

*   **No QNSQY tools appear.** Common causes:
    *   QNSQY is not installed, or it is not on `PATH`;
    *   QNSQY is older than the current release, which stops on the strict
        option;
    *   the computer runs Windows or macOS, which are not supported yet.

    On Linux, the user installs the current release from
    https://quantumsequrity.com/download and then restarts Codex.
*   **Another QNSQY server is running** (rule 1): the user removes it as the
    README says, then restarts Codex.
*   **"Not logged in"** or **"Login required"**: the user signs in with the
    QNSQY app, or with `qnsqy login` in a separate terminal window outside
    Codex. Scanning needs a signed-in account on every plan. Codex does not pass
    `QNSQY_API_KEY`, `QNSQY_CONFIG_DIR` or `XDG_CONFIG_HOME` to the server, so a
    sign-in that depends on one of them does not work here.
*   **A plan limit message**: some algorithms and tools need Pro or Business.
*   **"Not accepted in the strict profile"** or **"Required in the strict
    profile"**: fix the call. Never add a password; give a hand-off instead.
*   **The QNSQY app does not open on a desktop that has only X11**: the app
    needs Wayland. It opens on X11 only with `--allow-x11`, and QNSQY does not
    recommend that, because X11 lets other programs read keystrokes. Suggest the
    matching `qnsqy` command in a separate terminal window outside Codex
    instead.
