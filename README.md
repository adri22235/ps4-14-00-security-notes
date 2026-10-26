# PS4 firmware 14.00 security research

Public, source-linked notes on vulnerabilities and security changes relevant to PlayStation 4 system software 14.00. The goal is to help the homebrew and security research community distinguish demonstrated behavior from tentative claims.

## Current assessment

As checked on 2026-10-26, public community references do not list a complete public jailbreak chain for firmware 14.00. That does not mean the firmware has no vulnerabilities. The strongest published leads and caveats are summarized below:

| Area | What public sources say | Confidence |
| --- | --- | --- |
| WebKit / Slopkit | The Slopkit chain works through 13.52. PS4 Dev Wiki marks it at least partially patched in 14.00; a port to 14.00 is not established by that status page. | Moderate; community-maintained, underlying analysis incomplete |
| AIO kernel bug (“relapse”) | PS4 Dev Wiki lists the public PS4 implementation through 13.52 and says it may be patched in 14.00. | Tentative for the 14.00 patch status |
| mast1c0re / LuaC0re | PS4 Dev Wiki says the PS2 emulator path remains unpatched as of PS4 14.00. This is user-mode research and is not, by itself, a kernel jailbreak. | Moderate; community-maintained status |
| Non-AF_UNIX sockets | PS4 Dev Wiki reports that since 14.00, non-privileged PS4 games cannot create non-AF_UNIX domain sockets. It notes the Internet Browser privilege question needs testing. | Reported behavior; scope caveat remains |
| Complete jailbreak | ConsoleMods’ exploit chart reports no public userland or kernel exploit for 14.00 or newer. | Community status snapshot; may change |

These findings describe public knowledge, not a claim that firmware 14.00 is secure. The status can change as researchers publish validated results.

## Research notes

- [Vulnerability and firmware status notes](RESEARCH.md)
- [Sources and claim map](SOURCES.md)

## Scope

This repository is a literature review and tracking index. It does not include exploit payloads, console-targeting instructions, leaked firmware, or claims of original discovery. Please submit corrections with primary evidence, affected firmware versions, test conditions, and reproducible observations that do not expose private user data.

## Contributing

Open an issue or pull request with the source URL, date checked, affected component and firmware range, observed impact, and confidence level. Separate confirmed device tests from inference based on upstream code or changelogs.

## License

Unless otherwise noted, original notes in this repository are dedicated to the public domain under [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/). External sources retain their own licenses and attribution.
