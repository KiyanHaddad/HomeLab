# IT Homelab

I’m building a home IT lab with hardware I already own. I want useful services I can keep running, alongside practical experience with Linux, networking, automation and eventually cloud infrastructure.

The first working service is Pi-hole on a Raspberry Pi 4 that I administer over SSH. I verified an allowed request through Quad9, an exact deny returning `0.0.0.0` with `Blocked (exact)` in Query Log, and restored resolution after removing the test entries. These explicit desktop lookups left household DNS settings unchanged.

The evidence distinguishes captured output, reported actions and pending checks. Private identifiers are omitted from the project record.

## Current setup

```text
Home mesh and gateway
├── Desktop used for administration
└── TP-Link Archer AX11000 in access point mode
    └── Raspberry Pi 4 used as the Linux server
```

The Pi has 8 GB of RAM and a 256 GB microSD card, based on my hardware inventory. The current AP setup shares the household LAN. It does not create an isolated lab network.

My reused laptop and GL.iNet GL-X3000 Spitz AX are available for later work. They are not part of the completed server build yet.

## Progress

| Milestone | Current result |
| --- | --- |
| Archer management access | Working at its connected management address |
| Desktop to Pi SSH login | Working |
| Account, hostname and OS identification | Terminal output captured |
| Initial software updates | Reported finished; full transaction output not captured |
| Fresh SSH connection after the reboot step | Reported successful on 17 October; terminal capture shows uptime 17 minutes and SSH active |
| Root filesystem capacity | Capture shows `235G` total, `3.9G` used, `222G` available and `2%` used |
| IPv4 route inspection | Ethernet default route and connected `/22` network captured |
| Stable device addresses | Pi and Archer reservations saved in Deco, user-confirmed; lease renewal not tested |
| Pre-install port inspection | Capture shows SSH on `22`; no listeners shown on `53`, `80` or `443` |
| Pi-hole installer download | Completed transfer captured; installation subsequently started |
| Pi-hole installation | Completed and admin password saved privately, user-reported; complete configuration has not been read back |
| Pi-hole dashboard | Login user-reported; subsequent Query Log and privacy controls browser-observed |
| Controlled-client DNS verification | **Allowed → blocked → restored**, verified across terminal captures and Query Log. The restored lookup returned public IPv4 answers; its log row shows `Forwarded` with a reply from Quad9 at `149.112.112.112#53`. |
| Query history troubleshooting | Level `3 — Anonymous mode` explained the empty Query Log. On 19 October Codex applied level `0`, reloaded and verified it persisted, with query-file logging enabled and Pi-hole Active. A subsequent allowed test query is now visible in Query Log. |
| Test-rule cleanup | A temporary Exact allow appeared alongside the Exact deny. Both test entries were deleted; learner capture and Codex's reloaded Domains view show zero entries. |
| Pi-hole configuration backup — 20 October | I exported and saved a private ZIP. Codex's read-only checks passed ZIP CRC, TOML parsing and both SQLite quick checks; the export has no temporary domain rules or query history. This is structural verification, not a tested restore. |
| Household DNS filtering | Not changed during this exercise; review privacy and recovery before any wider rollout |

![Pi-hole Query Log evidence for the completed DNS cycle](evidence/images/dns-cycle.png)

The rows are newest first: **restored at the top, blocked in the middle, allowed at the bottom**. Private identifiers are removed from this selected proof image.

The configuration backup remains private, outside this project draft. It is not a complete Pi image or a backup of SSH, the operating system or router DHCP reservations. A restore test remains future work.

## Follow the build

Start with the [Pi-hole project report](docs/pi-hole-project-report.md) for the architecture, decisions, command explanations and verified DNS results. A [PDF version](docs/pi-hole-project-report.pdf) is available for download. The report uses selected sanitized evidence and credits the guidance behind this build.

The [build journal](journal/2026-10-16-pi-foundation.md) and [terminal evidence](evidence/2026-10-16-terminal-extract.md) preserve the detailed process. The [Pi foundation results](results/2026-10-17-pi-foundation.md) and [local recall page](results/pi-foundation.html) include short learning prompts. The longer illustrated personal guide stays outside this repository.

I’m doing this as a guided build, using Codex for explanations, troubleshooting assistance and documentation. The journal distinguishes my actions from the diagnostics Codex performed.
