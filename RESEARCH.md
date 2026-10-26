# Firmware 14.00 research notes

**Snapshot date:** 2026-10-26  
**Scope:** Publicly documented PS4 firmware vulnerabilities and research leads relevant to 14.00.  
**Source repo:** [adri22235/ps4-13xx-research](https://github.com/adri22235/ps4-13xx-research)  
**Method:** Review its README, `vuln_research_1400.md`, `webkit_1400_analysis.md`, `webkit_gadgets_1400.js`, scanner script and test inventory; cross-check upstream FreeBSD CVEs against FreeBSD advisories. The scanner is reproducible with a lawfully obtained firmware dump. The proprietary decrypted module is not hosted for copyright/DMCA compliance; no console hardware test was performed for this update.

## Summary

The source repository reports a catalogue of kernel-exploit candidates derived from FreeBSD advisories, WebKit gadget offsets for the 14.00 `libSceNKWebKit.sprx`, and kernel data offsets. These are candidate observations and researcher-reported values, not confirmation of a working 14.00 kernel exploit.

The two requested FreeBSD examples need particular care:

- **CVE-2026-7270 (`execve`)** is a real upstream FreeBSD local privilege-escalation issue. FreeBSD's advisory says “all supported versions” and lists fixes for stable/releng 13.5, 14.3/14.4 and 15.0. It does not establish vulnerability in FreeBSD 9 or Sony's fork. Catalog it as a priority candidate for static/dynamic analysis, subject to verification in hardware. The claim in the source repo that it is present in the FreeBSD 9 base still needs code-level evidence; an upstream version label or CVE alone is insufficient.
- **CVE-2026-101308 (`sound(4)`)** is a real upstream race involving sound-buffer resize and `mmap`. The published FreeBSD affected-release list covers 14.4, 14.5 and 15.1 release branches. It does not establish that the same implementation or vulnerable path exists in Orbis. Catalog it as a priority candidate for static/dynamic analysis, subject to verification in hardware; applicability also depends on the driver/device and permissions being present and reachable.
- **WebKit analysis:** the source repo reports a constant +0x4000 difference for eight sample gadget offsets between 13.50 and 14.00. This is a set of relative code offsets, not proof that the entire binary has a single base shift. The scanner script `scripts/find_webkit_gadgets_1400.py` and ROP dispatcher test are present in the source repo. The proprietary `libSceNKWebKit.sprx` is not distributed there for copyright/DMCA compliance; the authors report that extraction can be reproduced from a legally obtained firmware dump. This repo did not independently run that extraction. The listed test checks the dispatcher, not whether each byte pattern occurs in the right firmware binary.
- **Kernel offsets:** PRISON0, ROOTVNODE and ALLPROC below are copied as reported values. The source README attributes 14.00 kernel offsets to the GoldHEN kpayload format documented by the scene. PRISON0 refers to prison/credential context, ROOTVNODE to the root vnode, and ALLPROC to the process list. Treat the numeric values as source-reported until matched against an authenticated 14.00 kernel image.

## FreeBSD-derived kernel candidate catalogue

The entries below are leads collected in `vuln_research_1400.md`, not demonstrated PS4 vulnerabilities. “Upstream confirmed” means the FreeBSD issue has an advisory; it does not mean it affects PS4. For a credible Orbis claim, compare the vulnerable and fixed code against the relevant Sony firmware, establish the interface is reachable in the target process, and document device/sandbox constraints.

| Candidate | Upstream component / class | PS4 14.00 assessment |
| --- | --- | --- |
| [CVE-2026-7270](https://lists.freebsd.org/archives/freebsd-security/2026-April/000448.html) | `execve(2)`; argument-buffer overflow from an operator-precedence error. | Priority candidate for static/dynamic analysis, subject to verification in hardware. Orbis applicability and FreeBSD 9 code presence remain unconfirmed. |
| [CVE-2026-101308](https://security.freebsd.org/advisories/FreeBSD-SA-26:71.sound.asc) | `sound(4)`; TOCTOU/UAF during buffer resize and mapping. | Priority candidate for static/dynamic analysis, subject to verification in hardware. Orbis implementation, device access and vulnerable path remain unverified. |
| [CVE-2026-58087](https://security.freebsd.org/advisories/FreeBSD-SA-26:54.sysvsem.asc) | SysV `semctl(2)`; out-of-bounds access. | Candidate in source repo; PS4 code and reachability unverified. |
| [CVE-2026-58098](https://security.freebsd.org/advisories/FreeBSD-SA-26:64.sysvsem.asc) | SysV `semop(2)`; out-of-bounds access. | Candidate in source repo; PS4 code and reachability unverified. |
| [CVE-2026-58093](https://security.freebsd.org/advisories/FreeBSD-SA-26:62.tty.asc) | TTY `TIOCSCTTY`; race/UAF. | Candidate in source repo; PS4 code, device access and reachability unverified. |
| [CVE-2026-58094](https://security.freebsd.org/advisories/FreeBSD-SA-26:63.posixshm.asc) | POSIX shared memory; synchronization/TOCTOU. | Candidate in source repo; affected facility and PS4 code unverified. |
| [CVE-2026-45251](https://security.freebsd.org/advisories/FreeBSD-SA-26:19.file.asc) | `poll(2)`/`select(2)`; descriptor lifecycle race/UAF. | Candidate in source repo; PS4 code and reachability unverified. |
| [CVE-2026-101307](https://security.freebsd.org/advisories/FreeBSD-SA-26:70.tcp.asc) | TCP RACK PCM integer overflow. | Conditional lead in source repo; whether the relevant module/code exists and is enabled in Orbis is unknown. |
| [FreeBSD-SA-26:55.elf](https://security.freebsd.org/advisories/FreeBSD-SA-26:55.elf.asc) | ELF core-dump heap overflow (as named in source repo). | Candidate only; configuration and vulnerable code presence unverified. |
| CVE-2026-49415 | `execve` SUID/TOCTOU race (as described in source repo). | Candidate only; source repo notes SUID target availability as a constraint. Orbis applicability unverified. |


The source report also contains detailed mechanisms and proposed exploitation paths. This summary intentionally records candidate identity and validation status only; it does not reproduce trigger sequences or exploit instructions.

## WebKit: reported 14.00 gadget data

The source file `webkit_gadgets_1400.js` attributes the offsets to a byte-pattern search in `libSceNKWebKit.sprx.decrypted`, generated 2026-10-23. The linked research note compares selected values against 13.50. These are source-reported locations and pattern matches, not independent binary verification by this repository.

### Reported +0x4000 examples (13.50 → 14.00)

| Gadget | FW 13.50 | FW 14.00 | Reported delta |
| --- | ---: | ---: | ---: |
| LEAVE_RET | `0x182F7` | `0x1C2F7` | `+0x4000` |
| POP_RAX_RET | `0x10504` | `0x14504` | `+0x4000` |
| POP_RBX_RET | `0x79E8` | `0xB9E8` | `+0x4000` |
| POP_RCX_RET | `0x1BADE` | `0x1FADE` | `+0x4000` |
| POP_RDI_RET | `0x5C480` | `0x60480` | `+0x4000` |
| POP_RDX_RET | `0x12C5BA` | `0x1305BA` | `+0x4000` |
| POP_RSI_RET | `0x6E45E` | `0x7245E` | `+0x4000` |
| POP_R12_RET | `0x2426B15` | `0x242AB15` | `+0x4000` |

### Gadget addresses listed by the source file for FW 14.00

| Name | Reported offset | Source annotation |
| --- | ---: | --- |
| RET | `0x4032` | `ret` |
| LEAVE_RET | `0x1C2F7` | `leave; ret` |
| POP_RAX_RET | `0x14504` | `pop rax; ret` |
| POP_RBX_RET | `0xB9E8` | `pop rbx; ret` |
| POP_RCX_RET | `0x1FADE` | `pop rcx; ret` |
| POP_RDX_RET | `0x1305BA` | `pop rdx; ret` |
| POP_RSI_RET | `0x7245E` | `pop rsi; ret` |
| POP_RDI_RET | `0x60480` | `pop rdi; ret` |
| POP_RBP_RET | `0x40B6` | `pop rbp; ret` |
| POP_RSP_RET | `0x73017` | `pop rsp; ret` |
| POP_R8_RET | `0x230CBE1` | `pop r8; ret` |
| POP_R12_RET | `0x242AB15` | `pop r12; ret` |
| POP_R14_RET | `0xA9E91` | `pop r14; ret` |
| POP_R15_RET | `0x6047F` | `pop r15; ret` |
| MOV_RAX_RDI_RET | `0x23320` | `mov rax, rdi; ret` |
| MOV_RDX_RAX_RET | `0x1CEBA9` | `mov rdx, rax; ret` |
| XCHG_RAX_RSP_RET | `0x3F755A8` | `xchg rax, rsp; ret` |
| MOV_QWORD_RDI_RAX_RET | `0x948B` | `mov [rdi], rax; ret` |
| MOV_RAX_QWORD_RAX_RET | `0x14021` | `mov rax, [rax]; ret` |
| MOV_RAX_QWORD_RDI_RET | `0xE2EA0` | `mov rax, [rdi]; ret` |
| ADD_RAX_RCX_RET | `0x84F67` | `add rax, rcx; ret` |
| SUB_RAX_RCX_RET | `0x98736C` | `sub rax, rcx; ret` |
| XOR_RAX_RAX_RET | `0x23D14F6` | `xor rax, rax; ret` |
| INC_RAX_RET | `0xC5E51C` | `inc rax; ret` |
| JMP_RAX | `0x4082` | `jmp rax` |
| CALL_RAX | `0x4041` | `call rax` |
| JMP_RCX | `0x12A2A` | `jmp rcx` |
| POP_R13_POP_R14_POP_R15_RET | `0x6047B` | three-register pop; ret |
| POP_RBX_POP_RBP_RET | `0xAD0F` | two-register pop; ret |

The analysis note says “29/38 verified,” while the checked-in JS contains 29 named entries including compound gadgets. The labels and count are therefore author-reported; byte-pattern counts in comments are not a substitute for checking the correct decrypted module, segment mapping and firmware build.

## Kernel data offsets reported for FW 14.00

| Symbol | Reported offset | Provenance / validation |
| --- | ---: | --- |
| PRISON0 | `0x0111FA18` | Source repo attributes the offset set to Al-Azif / Scene-Collective; not checked against an authenticated 14.00 kernel image here. |
| ROOTVNODE | `0x02136E90` | Same provenance and limitation. |
| ALLPROC | `0x01B28538` | Same provenance and limitation. |

The source repo contains inconsistent SYSENT values in its README and gadget-file comments, so SYSENT is deliberately omitted pending clarification. This is a useful example of why offsets need a single versioned artifact, binary hash and symbol-level validation.

## Other public status context

- PS4 Dev Wiki reports Slopkit at least partially patched in 14.00, and the AIO (“relapse”) kernel implementation documented through 13.52 with a tentative note that it may be patched in 14.00.
- The same wiki reports mast1c0re/LuaC0re user-mode research as still present on 14.00; user-mode execution alone is not kernel privilege escalation.
- ConsoleMods’ exploit chart reports no public complete userland/kernel jailbreak chain for 14.00 or newer.

These are community-maintained status snapshots and can change.

## Evidence needed to promote a candidate

1. Identify the exact vulnerable code or an equivalent implementation in an authenticated 14.00 kernel image.
2. Confirm relevant syscalls, drivers and device nodes are present and reachable from the intended process/sandbox.
3. Compare firmware-specific code with the upstream fix, including Sony divergence and backports.
4. Record controlled test conditions and results. A crash, upstream PoC, or matching subsystem name alone does not prove privilege escalation on PS4.
5. For binary offsets, publish the module/kernel hash, extraction provenance, segment-relative address convention, and an independently repeatable verification record.

## Limitations

This is a source audit, not an independent console test. The main PS4 details are researcher-reported by the linked repository. Upstream CVE validity must not be confused with applicability to Sony's older, modified kernel. The source repository contains inconsistent metadata (including SYSENT addresses and the claimed gadget verification count); conflicts are called out rather than silently resolved.


## 14.00 exploit architecture (proposed; not validated)

The diagram records the proposed three-stage architecture reported for research. It does not claim a working FW 14.00 chain: the WebKit primitive, dispatcher behavior, kernel-candidate applicability, and payload compatibility still require firmware-specific verification and hardware testing.

```mermaid
flowchart LR
    A["Phase 1 — Userland: WebKit memory corruption / heap spray (reported hypothesis)"] --> B["Stack pivot: gadget offset 0x03F755A8 (reported; verify module-relative convention)"]
    B --> C["Phase 2 — ROP dispatcher: proposed 10 qwords / 80 bytes"]
    C --> D{"Candidate kernel path; separate activation semantics"}
    D -->|sound(4): buffer resize / mmap race| E["CVE-2026-101308"]
    D -->|execve: argument-processing overflow| F["CVE-2026-7270"]
    E --> G["Phase 3 — Kernel / GoldHEN kpayload"]
    F --> G
    G --> H["Reported offsets: PRISON0 0x0111FA18; ROOTVNODE 0x02136E90"]
    H --> I["Jail / credential effects and FW 14.00 payload compatibility require hardware validation"]
```

### Phase boundaries and status

1. **Userland / WebKit:** Heap spraying, memory corruption, and the pivot at `0x03F755A8` are source-reported design elements, not independently reproduced in this repository. Confirm the gadget's module-relative address against the legally obtained FW 14.00 dump.
2. **ROP dispatcher:** The proposed dispatcher size is 10 qwords (80 bytes). This document records the proposed size and stage boundary only; it does not supply qword values or assert the dispatcher has been demonstrated. The `sound(4)` and `execve` candidates have different activation paths and must not be treated as interchangeable syscall targets.
3. **Kernel / GoldHEN kpayload:** PRISON0 and ROOTVNODE are reported offsets in the scene's GoldHEN kpayload format. Any jail or credential effects, the ALLPROC mapping listed above, and compatibility with FW 14.00 remain subject to validation against the target kernel and on hardware.

