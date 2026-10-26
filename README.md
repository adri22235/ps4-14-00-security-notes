# PS4 Firmware 14.00 Security Research & Architecture Dossier

[![ISO/IEC 29147](https://img.shields.io/badge/Standard-ISO%2FIEC%2029147%20CVD-blue.svg)](https://www.iso.org/standard/45170.html)
[![Target](https://img.shields.io/badge/Target-PS4%20Orbis%20OS%2014.00-red.svg)](https://github.com/adri22235/ps4-13xx-research)
[![Author](https://img.shields.io/badge/Researcher-%40adri22235-brightgreen.svg)](https://github.com/adri22235)

Public, source-linked technical notes on vulnerabilities, binary deltas, and architectural research for PlayStation 4 system software **Firmware 14.00** (Released September 16, 2026).

Lead Research Lab: **García Casado Security Research** (García Casado Cyber Lab)  
Primary Codebase: [adri22235/ps4-13xx-research](https://github.com/adri22235/ps4-13xx-research)

---

## Technical Status Snapshot (October 2026)

| Layer | Research Status | Technical Architecture & Caveats |
| :--- | :--- | :--- |
| **WebKit (Userland R/W)** | **Demonstrated (Native R/W)** | 29 verified ROP gadgets extracted from `libSceNKWebKit.sprx` (67 MB) with a `+0x4000` base shift from FW 13.50. Stack pivot established at `0x03F755A8`. |
| **ROP Dispatcher Frame** | **Verified (80 Bytes / 10 Qwords)** | System V AMD64 ABI compliant dispatcher frame (`80 mod 16 = 0`) modeled in `tests/test_rop_dispatcher_1400.c`. |
| **Sandbox Confinement** | **Active (AF_UNIX Restricted)** | WebKit execution is confined to browser sandbox; non-`AF_UNIX` network sockets restricted in 14.00. |
| **Kernel Candidates (KEX)** | **Active Investigation** | Celsius patched in 14.00. Priority focus on unpatched FreeBSD upstream vectors: **CVE-2026-101308** (`sound(4)` TOCTOU in `mmap`) and **CVE-2026-7270** (`execve` slab overflow). |
| **Kernel Symbol Offsets** | **Cataloged (GoldHEN Standard)** | `PRISON0` (`0x0111FA18`), `ROOTVNODE` (`0x02136E90`), `ALLPROC` (`0x01B28538`), `SYSENT` (`0x0110A760`). |

---

## Detailed Documentation

* [**Technical Research Notes (`RESEARCH.md`)**](RESEARCH.md) — Comprehensive technical dossier containing the 3-phase exploit architecture diagram, the complete 29 WebKit gadget table, ROP dispatcher specifications, ABI parameter passing notes (`RCX` vs `R10`), and the FreeBSD kernel vulnerability catalogue.
* [**Interactive WebKit Harness (`exploit_1400.html`)**](exploit_1400.html) — Interactive browser-based WebKit ROP and memory safety testing harness for FW 14.00.
* [**Primary Research Repository (`ps4-13xx-research`)**](https://github.com/adri22235/ps4-13xx-research) — Full repository containing gadget scanners, automated Python sentinels, and C verification harnesses.
* [**Sources and Claim Map (`SOURCES.md`)**](SOURCES.md) — Primary references, FreeBSD security advisories, and community trackers.

---

## Scope & Legal Framework

This repository operates strictly under the **ISO/IEC 29147 (Coordinated Vulnerability Disclosure)** and **ISO/IEC 30111** standards. No proprietary binary dumps or weaponized end-to-end payloads are hosted. All offsets and gadgets are reproducible by running the documented tools against legally obtained firmware dumps.

## License

Unless otherwise noted, notes and specifications in this repository are dedicated to the public domain under [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/).
