# QNSQY for Codex

QNSQY is a post-quantum cryptography (PQC) toolkit from Quantum Sequrity. This
plugin lets Codex use the QNSQY program installed on your own computer. With
it, the agent can:

*   find quantum-vulnerable cryptography and write a CBOM (cryptography bill of
    materials);
*   encrypt files for a recipient's public key with ML-KEM;
*   export your public key;
*   check ML-DSA signatures, QNSQY release signatures, hashes and encrypted
    files.

The tools have no field for a password, passphrase, private key, API key or
payment token, refuse any call that adds one, and never return one.

## Requirements

*   **Linux** (x86_64 or ARM64) with QNSQY's current release installed from our
    signed packages at https://quantumsequrity.com/download, so that `qnsqy` is
    on your `PATH`. The plugin starts whatever `qnsqy` your `PATH` finds first,
    so install QNSQY only from there.
    Windows and macOS are not supported yet.
*   **Codex** on the same computer. Start it from a normal install, not with
    `npx`: inside a project folder, npx puts that folder's `node_modules/.bin`
    first on your `PATH`, so a `qnsqy` placed there would start instead of
    yours.
*   **A QNSQY account, signed in with the QNSQY app or with `qnsqy login`** in
    a separate terminal window outside Codex. Codex does not pass
    `QNSQY_API_KEY` to the plugin, so a sign-in through that variable does not
    work here.

## Install

1.  Add our marketplace and install the plugin:

    ```
    codex plugin marketplace add quantumsequrity/qnsqy-codex
    codex plugin add qnsqy@quantumsequrity-codex
    ```

2.  Add Codex's setting for asking before each QNSQY call to
    `~/.codex/config.toml`:

    ```
    [plugins."qnsqy@quantumsequrity-codex".mcp_servers.qnsqy_strict]
    default_tools_approval_mode = "prompt"
    ```

    `codex mcp get qnsqy_strict` then shows
    `default_tools_approval_mode: prompt`. That is all we have checked: we have
    not watched Codex prompt with this setting in a session, we do not know
    Codex's default, and an approval mode that approves everything may
    override it.
3.  Start a new Codex session. `codex mcp list` shows the plugin's server,
    `qnsqy_strict`, running `qnsqy --mcp-profile=strict`.

This is the official repository. Check the spelling, `quantumsequrity`,
before you install. Our Claude Code plugin also installs in Codex, but it was
not made for it: in Codex, a server that you add yourself under the name
`qnsqy` takes the place of its server. Use this plugin for Codex.

**Update:** `codex plugin marketplace upgrade quantumsequrity-codex`, then
`codex plugin add qnsqy@quantumsequrity-codex` again.
**Uninstall:** `codex plugin remove qnsqy@quantumsequrity-codex`, then
`codex plugin marketplace remove quantumsequrity-codex`.

### Remove a QNSQY server that you added yourself

If you added QNSQY to Codex yourself before, for example with
`codex mcp add`, remove that entry: `codex mcp list` shows every server, and
`codex mcp remove <name>` removes one. Remove every QNSQY entry except
`qnsqy_strict`, which is the plugin's own; such an entry is often called
`qnsqy`. Until you remove it, the plugin's instructions tell the agent to use
no QNSQY tool at all. If that server runs `qnsqy --mcp`, its tools with the
same names as the strict ones stay visible to the agent, and some of them take
a password, such as its encrypt tool. The instructions are for the model; they
do not block anything.

## What the plugin runs, sends and fetches

*   **Runs**: Codex starts your installed QNSQY as a local tool server named
    `qnsqy_strict`, with `qnsqy --mcp-profile=strict`, in the plugin's own data
    folder (`codex mcp get qnsqy_strict` shows it), so the agent gives files to
    the tools as full paths. Each tool call runs the matching QNSQY command on
    your computer, inside QNSQY's sandbox.
*   **Tools**: the agent sees 13 tools (10 on the Free plan):
    *   scan;
    *   encrypt and batch encrypt;
    *   export a public key;
    *   verify, batch verify and verify a release;
    *   hash, check a hash, and check an encrypted file;
    *   status, usage and version.

    Batch encrypt, batch verify and key export need the Pro plan or above.
*   **Sends**: the plugin itself sends nothing. QNSQY talks to our billing
    server, `billing.quantumsequrity.com`, to check your plan and record usage,
    as section 2 of our [privacy policy](https://quantumsequrity.com/privacy)
    describes:
    *   **At sign-in and at each check-in**: a device ID derived on your
        device, your computer's name and the operating system type.
    *   **Before a metered operation**: the operation type, the data size, the
        program version and the chosen algorithms.
    *   **After a metered operation**: whether it succeeded, the bytes processed
        and how long it took.
    *   **For every operation**: a usage receipt with the operation type, data
        size, time, result, credits used and a hash of the file's path. The path
        itself is not sent, but a path that is easy to guess can be checked
        against its hash.
    *   **Audit forwarding**: if you have set up audit log forwarding in
        QNSQY's settings, audit events also go to the syslog server you
        configured.

    QNSQY never sends your files, their contents, your passwords or your keys.
*   **Environment**: Codex starts the server with only a few basic variables,
    such as `HOME`, `USER`, `PATH` and `LANG`, plus its own `PLUGIN_ROOT` and
    `PLUGIN_DATA` (checked with Codex CLI 0.165.0). QNSQY variables that you
    set in your shell, such as `QNSQY_API_KEY`, `QNSQY_CONFIG_DIR` or
    `QNSQY_REQUIRE_INTEGRITY`, and `XDG_CONFIG_HOME` do not reach it. So a
    `QNSQY_REQUIRE_INTEGRITY=1` in your shell does not apply to the plugin's
    server. The server never receives a payment token or remote-storage keys.
*   **The model sees** what you ask and what the tools return: file names, paths
    and results. All of this is part of your conversation with the model's
    provider, which is why the tools never take secrets. Your plan's terms
    decide how the provider keeps and uses it. Do not point the agent at files
    whose names, paths or results your organization may not share with that
    provider.
*   **Fetches**: nothing. The plugin downloads no code.

## Passwords, passphrases and keys

*   **No secret fields.** The tools have no field for a password, passphrase,
    private key, API key or payment token. QNSQY refuses any call that adds one
    before doing anything.
*   **Encryption.** Encryption through the plugin is to a public key file.
*   **Steps that need a password.** To decrypt, sign, create keys or sign in,
    use the QNSQY app from your desktop menu, or run `qnsqy` in a separate
    terminal window, outside Codex and not with Codex's `!`. QNSQY asks for the
    password in its own window or prompt. Create your account at
    https://quantumsequrity.com or in the app, and manage API keys in your
    dashboard.

## Limits

*   **Tool calls can be steered.** Text that the agent reads, for example inside
    a scanned file or a web page, can try to steer it. The strict tools refuse
    unlisted fields, including every secret field, and cannot overwrite or
    delete files. A valid call is still possible, such as encrypting a file to
    the wrong public key. Use the setting from Install step 2, and check each
    QNSQY call that Codex shows you before you approve it.
*   **The instructions load when the model reads them.** Codex shows the model
    the plugin's skill by its name and description, and the model reads the
    rules when it opens it. The strict tools' refusals do not depend on the
    model.
*   **The agent's own shell.** The agent could also run `qnsqy` in its shell,
    outside the strict tools. The plugin's instructions forbid it, but the
    plugin does not block shell commands, and depending on your approval
    settings Codex may run commands without asking. Never give the agent a
    password, and never approve a `qnsqy` command that carries one.
*   **Shell mode.** Codex runs a command that you type after `!` from inside
    the session. Run every `qnsqy` command that asks for a password in a
    separate terminal window instead.
*   **Settings that replace the plugin's server.** A server in your Codex
    settings with the plugin's server name, `qnsqy_strict`, takes the place of
    the plugin's. A trusted project's `.codex/config.toml` can also add
    servers. Do not use that name for a server of your own, and trust a project
    only after you have checked its `.codex` folder.
*   **Typed secrets.** If you type a password into the chat itself, it is part of
    the conversation, so don't.

## Example prompts

1.  "Scan ~/projects/billing for quantum-vulnerable cryptography and write a
    CycloneDX CBOM to cbom.json in that folder."
2.  "Encrypt report.pdf in this project for the public key in alice-enc.pub."
3.  "Export my public key named work to work.pub in this project so that I can
    send it to a colleague."
4.  "Check the signature on contract.qs with bob-sign.pub, then hash
    invoice.pdf with BLAKE3."
5.  "Which QNSQY plan am I on, and how much have I used?"

## Troubleshooting

*   **No QNSQY tools appear, or the server failed to start.** Common causes:
    *   QNSQY is not installed;
    *   `qnsqy` is not on your `PATH`;
    *   your QNSQY is older than the current release. Older releases do not know
        the strict option, so they stop.

    Install the current release from https://quantumsequrity.com/download, then
    restart Codex. On Windows and macOS QNSQY's strict mode is not available
    yet.
*   **A file is not found, or a report lands in the wrong folder**: ask the
    agent to use the file's full path. Codex starts the server in the plugin's
    data folder.
*   **The agent says that another QNSQY server is running**: remove it as
    described under Install, then restart Codex.
*   **"Not logged in"** or **"Login required"**: sign in with the QNSQY app,
    or with `qnsqy login` in a separate terminal window outside Codex. Scanning
    needs a signed-in account, on every plan. A sign-in or settings folder that
    depends on `QNSQY_API_KEY`, `QNSQY_CONFIG_DIR` or `XDG_CONFIG_HOME` does
    not reach the plugin's server.
*   **A plan limit message**: some algorithms, and the batch and key-export
    tools, need the Pro or Business plan (https://quantumsequrity.com/pricing).
*   **"Not accepted in the strict profile"**: the tools never accept passwords,
    passphrases, private keys, API keys or payment tokens. Finish that step in
    the QNSQY app or in a separate terminal window outside Codex.
*   **The QNSQY app does not open on a desktop that has only X11**: the app needs
    Wayland. It opens on X11 only with `--allow-x11`, which we do not
    recommend, because X11 lets other programs read keystrokes. Use the matching
    `qnsqy` command in a separate terminal window outside Codex instead, for
    example `qnsqy decrypt`.

## Security and support

*   **Certifications**: QNSQY implements the FIPS 203 (ML-KEM) and FIPS 204
    (ML-DSA) algorithms. It is not FIPS 140-validated, SOC 2 certified, FedRAMP
    authorized or HIPAA-audited.
*   **Support**: support@quantumsequrity.com
*   **Security reports**: security@quantumsequrity.com
    (https://quantumsequrity.com/security)
*   **Privacy policy**: https://quantumsequrity.com/privacy
*   **Terms**: https://quantumsequrity.com/terms

## Licence

See [LICENSE](LICENSE).
