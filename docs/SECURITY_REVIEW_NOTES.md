# Security review notes (high-level, code-assisted)

This is a **high-level review** of selected security-critical areas in `simplex-chat` source.
It is not a full formal audit.

## What looked good

- Remote-host executed commands are filtered with an explicit deny-list for sensitive local operations (network config, storage encryption, SQL execution, remote-host control, etc.).
- Remote-controller pairing requires a verification code match before session activation.
- Documentation states TLS constraints and multiple encryption layers.

## Potential compromise paths / risks to monitor

1. **Social engineering around trust verification**
   - Contact/member security-code verification is manual (`/verify`), so users who skip verification have weaker protection against active interception during key establishment.

2. **Remote control is high impact if user confirms wrong session**
   - Pairing appears to require session-code verification, but if a user confirms an attacker-presented code out-of-band, remote control can be granted.

3. **Local file-at-rest encryption default in controller init**
   - `encryptLocalFiles` is initialized to `False` at controller startup. If not changed by user/app settings, local received/sent files may remain unencrypted on device storage.

4. **Call encryption downgrade possibility (with prompt)**
   - Call offers include logic to request confirmation if peer negotiates from encrypted to non-encrypted call type. This is good UX defense, but still a possible downgrade vector if users accept prompts.

5. **Supply-chain trust still partly centralized**
   - Project docs state reproducible client builds are planned, not fully delivered yet; this remains a classic place where a malicious actor could target distribution/build infrastructure.

## Bottom line

No obvious intentional backdoor indicator was found in the reviewed files.
Most realistic risks appear to be:
- endpoint compromise,
- user-verification/social-engineering failures,
- optional/default-off protections in some local-storage and call paths,
- and broader supply-chain/distribution risk.
