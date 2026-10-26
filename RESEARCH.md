# Firmware 14.00 research notes

**Snapshot date:** 2026-10-26  
**Scope:** Publicly documented PS4 firmware vulnerabilities and security changes relevant to 14.00.  
**Method:** Cross-check PS4 Developer Wiki and ConsoleMods status pages; distinguish documented historical ranges from extrapolation to 14.00.

## Summary

The available public status references point to a boundary at 14.00: the Slopkit WebKit chain and the public AIO (“relapse”) kernel implementation are documented through 13.52, while 14.00 is reported to have patched Slopkit at least partly and may have fixed the AIO issue. The AIO patch claim is explicitly tentative in the source. The PS2 emulator research path mast1c0re/LuaC0re is separately reported as still present on 14.00, but user-mode execution alone is not a kernel privilege escalation or a complete jailbreak.

PS4 Dev Wiki also documents a new restriction on creating non-AF_UNIX domain sockets in non-privileged games from 14.00. The wiki itself flags the system browser's privilege status as unconfirmed. That is a security hardening change, not a vulnerability by itself.

ConsoleMods’ current exploit chart reports no public userland or kernel exploit for firmware 14.00 or higher. A chart is a community status summary; it cannot prove that no private or undisclosed bugs exist.

## Findings by component

### WebKit: Slopkit chain

- **Reported range:** PS4 11.00–13.52 in public implementations and status notes.
- **14.00 status:** PS4 Dev Wiki says the chain is patched at least partially in 14.00. It also says the implementation’s underlying analysis is still “to be documented,” so this source does not establish exactly which individual bug was fixed or whether every primitive is eliminated.
- **Assessment:** Treat Slopkit as unavailable on 14.00 unless a researcher publishes a tested port or a new primitive. Do not infer exploitability merely from shared WebKit components or matching offsets.

### Kernel: AIO “relapse”

- **Reported range:** Public PS4 implementation documented for 13.02–13.52.
- **14.00 status:** The wiki says it may be patched in PS4 14.00. This is explicitly uncertain.
- **Assessment:** A likely patch, not a confirmed one, pending a controlled comparison or device test. The published kernel chain for earlier firmware does not establish a 14.00 kernel exploit.

### PS2 emulator: mast1c0re / LuaC0re

- **Reported status:** PS4 Dev Wiki says the PS2-on-PS4 path remains unpatched as of PS4 14.00 and describes user-mode ROP execution and Lua interpreter use.
- **Impact boundary:** User-mode execution in the emulator does not itself grant kernel access. A complete jailbreak still requires a separate privilege-escalation path.
- **Assessment:** Relevant userland research lead; avoid describing it as a 14.00 jailbreak.

### Socket creation restriction

- **Reported change:** Since PS4 system software 14.00, non-privileged PS4 applications such as games cannot create non-AF_UNIX domain sockets.
- **Caveat:** The PS4 Dev Wiki says the Internet Browser may have different privileges, but this needs confirmation by testing.
- **Assessment:** Record as an observed/reported hardening behavior, with app privilege scope explicitly unresolved.

## What would strengthen these conclusions

1. A controlled before/after test on retail hardware for each claimed 14.00 patch, with model and exact firmware recorded.
2. A primary technical write-up or upstream fix reference identifying the specific Slopkit and AIO fixes.
3. Separate, reproducible tests for the socket restriction in games and the system browser.
4. For any claimed 14.00 exploit, clear evidence of each privilege boundary crossed; do not equate browser or emulator code execution with kernel access.

## Limitations

The main sources are community-maintained wikis. Some entries explicitly lack detailed analysis, and exploit status changes quickly. This snapshot is a source-based summary, not an independent hardware validation or a vulnerability disclosure report. No CVE is assigned here unless a source links the identifier to the PS4 firmware and affected component.
