# PS4 firmware 14.00 security research

Public, source-linked notes on vulnerabilities and security changes relevant to PlayStation 4 system software 14.00. The goal is to help the homebrew and security research community distinguish demonstrated behavior from tentative claims.

## Current assessment

Snapshot: 2026-10-26. The research repository [ps4-13xx-research](https://github.com/adri22235/ps4-13xx-research) reports a set of FreeBSD-derived kernel candidates and FW 14.00 WebKit/kernel data. These are leads, not validated 14.00 kernel exploits.

| Area | Current evidence | Confidence / caveat |
| --- | --- | --- |
| FreeBSD-derived KEX candidates | Source repo flags CVE-2026-7270 (`execve`) and CVE-2026-101308 (`sound(4)`) among others. | Upstream bugs are documented; presence, reachability and impact in Sony's FreeBSD 9-derived Orbis kernel are unconfirmed. |
| WebKit gadget offsets | Source repo reports a +0x4000 delta for a sample of gadgets between 13.50 and 14.00 and lists 29 named gadget entries (including compound gadgets) in its JS file. | Researcher-reported. The decrypted SPRX and a reproducible analysis artifact are not included in the referenced files, so this repo has not independently verified them. |
| Kernel data offsets | Source repo reports PRISON0 `0x0111FA18`, ROOTVNODE `0x02136E90`, ALLPROC `0x01B28538`. | Reported from Al-Azif / Scene-Collective material; not independently checked against a 14.00 kernel image here. |
| Complete jailbreak | ConsoleMods’ exploit chart reports no public userland or kernel exploit for 14.00 or newer. | Community status snapshot; may change. |

A CVE that affects current upstream FreeBSD does not automatically affect Orbis: Sony uses a substantially older, modified kernel base, and the vulnerable code, device node, syscall exposure and relevant fixes must each be checked in the firmware-specific build.

## Research notes

- [Vulnerability and firmware status notes](RESEARCH.md)
- [Sources and claim map](SOURCES.md)

## Scope

This repository is a source-based research index. Technical offsets and gadget addresses are included as researcher-reported data, with provenance and validation limits stated. No claim is made that they constitute a working exploit chain. Please submit corrections with primary evidence, affected firmware versions, test conditions and reproducible observations that do not expose private user data.

## Contributing

Open an issue or pull request with the source URL, date checked, affected component and firmware range, observed impact, and confidence level. Separate confirmed device tests from inference based on upstream code or changelogs.

## License

Unless otherwise noted, original notes in this repository are dedicated to the public domain under [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/). External sources retain their own licenses and attribution.
