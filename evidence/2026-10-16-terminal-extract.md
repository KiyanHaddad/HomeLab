# Pi setup terminal evidence

16 October 2026

This is an organized extract transcribed from the supplied terminal screenshot, with each command placed beside its result. Account and host names are omitted; the operating system lines below retain their original text. This is an excerpt rather than a complete session transcript.

```text
$ whoami
[account name omitted]

$ hostname
[host name omitted]

$ cat /etc/os-release
PRETTY_NAME="Debian GNU/Linux 13 (trixie)"
NAME="Debian GNU/Linux"
VERSION_ID="13"
VERSION="13 (trixie)"
VERSION_CODENAME=trixie
DEBIAN_VERSION_FULL=13.7
ID=debian
```

The following APT message was supplied separately as text after `sudo apt update`:

```text
14 packages can be upgraded. Run 'apt list --upgradable' to see them.
```

| Result | Evidence available |
| --- | --- |
| Successful SSH login | Reported during the session; subsequent terminal identification screenshot supplied |
| Initial update process finished | Reported during the session; final package transaction output not captured |
| Local SSH host-key comparison | Procedure explained; comparison result not recorded |
| Fresh SSH login following the guided reboot step | User reports disconnect, wait and successful return to the Pi shell on 17 October 2026; follow-up terminal capture supplied |
| Uptime and SSH service state | Follow-up screenshot shows 17 minutes and `active`, transcribed below |
| Root filesystem space | Separate screenshot shows `235G` total, `3.9G` used, `222G` available and `2%` used |
| IPv4 routes | Separate screenshot shows Ethernet default route, `proto dhcp`, and connected `/22` network; redacted extract below |
| Pi and Archer address reservations | User confirms saving both in Deco; reservation UI and DHCP lease renewal not captured |
| Pre-install listening ports | Screenshot supplied: SSH `22`; Avahi `5353` and high UDP ports; NetworkManager `546`; no listeners shown on `53`, `80`, `443` |
| Official installer download | Screenshot shows final curl transfer at 100%, displaying `102k`, followed by returned shell prompt |
| Upstream provider selection | User confirms selecting Quad9 (filtered, DNSSEC), the non-ECS option; accepted provider is not shown in the supplied capture |
| Installer progress | Blocklist and query logging Yes are user-confirmed; installer screenshot shows level 0 selected before Continue, not proof of its saved value. User subsequently reports installation completed and admin password saved privately. |
| Dashboard access | User confirms being in the dashboard; subsequent Query Log and privacy controls are browser-observed. |
| Desktop DNS baseline, 18 October | Screenshot shows an IPv4 answer for `example.com` from the explicitly specified Pi address. Blocking and restoration had not been tested at this checkpoint. |
| Active privacy setting, 18 October | Browser-observed `3 — Anonymous mode`; Query Log refreshed empty while Log DNS queries/replies is checked. No correction had been applied at this checkpoint. |
| Saved privacy correction, 19 October | After the learner reported saving, a reload still showed level 3. Codex selected level 0, clicked Save & Apply and observed success; another reload verified level 0 persisted, query-file logging remained enabled and Pi-hole was Active. |
| Allowed query recorded, 19 October | Learner screenshot shows `A example.com` from the desktop and `8.0 ms`. Codex expanded the row: status Forwarded, reply from `9.9.9.9#53`, reply type IP. No Windows terminal-answer capture was supplied for this allowed request. |
| Exact deny configured, 19 October | Learner screenshot shows one row: `example.com`, Exact deny, Enabled, Default group, blank comment. This is configuration evidence, separate from the following lookup. |
| Blocked desktop lookup, 19 October | Learner terminal screenshot shows the explicit `A example.com` query returning `0.0.0.0` from the Pi's address. |
| Exact blocked reason, 19 October | Learner expanded Query Log screenshot shows `A example.com`, status `Blocked (exact)`, reply type `IP`, database ID `32`, and `0.3 ms`. |
| Temporary allow and cleanup, 19 October | Learner captures show enabled Exact allow and Exact deny for the test domain, then an empty table and two deletion-success notifications. Codex reloaded Domains with all filters checked and search blank: zero entries and Pi-hole Active. |
| Restored desktop lookup, 19 October | Learner terminal capture returns public IPv4 answers for `example.com`; Codex's expanded latest Query Log row shows Forwarded, reply from `149.112.112.112#53`, reply type IP, database ID `34`, and `10.5 ms`. The controlled cycle is complete. |
| Private configuration backup, 20 October | Learner exported and saved a ZIP outside this draft. Read-only validation passed CRC, TOML parsing and both in-memory SQLite quick checks. Temporary domain rules and query history are absent; no restore was tested. |

## Follow-up terminal capture on 17 October 2026

The user supplied this screenshot after reporting a fresh SSH login. Account, hostname, last-login source and banner details are omitted from this public extract. The command results retain their original text.

```text
$ uptime
 14:05:45 up 17 min, 1 user, load average: 0.02, 0.07, 0.08
$ systemctl is-active ssh
active
```

`17 min` is the elapsed time since boot. `active` is the SSH service state at this check. Neither output establishes that every package was upgraded or that SSH is configured to start at every future boot.

## Root filesystem capture on 17 October 2026

The command output below is transcribed from the separate storage screenshot, with the account and host prompt omitted.

```text
$ df -h /
Filesystem      Size  Used Avail Use% Mounted on
/dev/mmcblk0p2   235G  3.9G  222G   2% /
```

`Avail` is the space available on the mounted root filesystem. This capture does not test the SD card's health or continuous operation.

## IPv4 route capture on 17 October 2026

The user supplied the result of `ip -4 route`. The extract below replaces actual LAN addresses with labelled placeholders; it is not a verbatim transcript. The interface, prefix length, route origins and metrics retain their observed values.

```text
$ ip -4 route
default via [household-gateway] dev eth0 proto dhcp src [pi-address] metric 100
[local-network]/22 dev eth0 proto kernel scope link src [pi-address] metric 100
```

The default route uses the household gateway; the local subnet is directly connected over `eth0`. `proto dhcp` identifies a DHCP-installed route. It does not itself prove the DHCP server's identity, an address reservation, functioning DNS or Internet connectivity. The later reservation result is user-reported, separately from this route capture.

## Listening service capture on 17 October 2026

The user supplied the result of `sudo ss -lntup`. This table is an organized extract, omitting account details, network addresses, process IDs and file descriptors.

| Protocol | Local port | Owning process shown |
| --- | --- | --- |
| TCP, IPv4 and IPv6 | `22` | `sshd` |
| UDP, IPv4 and IPv6 | `5353` | `avahi-daemon` |
| UDP, IPv4 | `46570` | `avahi-daemon` |
| UDP, IPv6 | `55279` | `avahi-daemon` |
| UDP, IPv6 | `546` | `NetworkManager` |

No listening socket is shown on `53`, `80` or `443` in this snapshot. This is a local socket inventory, not a firewall or client-reachability test.

## Installer download capture on 17 October 2026

The screenshot shows this command:

```bash
curl -fL -o basic-install.sh https://install.pi-hole.net
```

The final transfer line displays 100% and `102k`, and the shell prompt returns. This records download completion. The downloaded file's contents and exact revision have not been independently inspected on the Pi.

## Installer choices on 17 October 2026

An earlier screenshot shows the upstream DNS screen with Google highlighted. That records a cursor position, not an accepted choice. The user later explicitly confirms selecting **Quad9 (filtered, DNSSEC)**, the non-ECS option. The provider choice is therefore user-reported.

The blocklist screenshot showed **StevenBlack's Unified Hosts List**, with **Yes** focused. The user subsequently confirms accepting the list and **Yes** for query logging. The installer screenshot shows **0 — Show everything** selected before Continue; it does not establish the saved value. The later 18 October browser observation records the active setting separately below.

The user reports installation finished and the admin password was saved privately, then confirms being in the dashboard. These are user-reported results. No completion screenshot, dashboard screenshot, final installer output or service/configuration readback was supplied. At this 17 October checkpoint, controlled-client DNS tests were pending. No password is recorded here.

## Browser observation on 17 October 2026

Codex subsequently inspected the connected browser's authenticated Pi-hole Query Log. The page displayed **Active**, an empty visible query table, and Live update unchecked. Footer versions were Core v6.5, FTL v6.8 and Web interface v6.6.1. This is browser-observed evidence, not a supplied screenshot or a DNS lookup result. An empty visible table does not establish that no DNS requests have ever occurred. No configuration or block rules were changed.

## Desktop DNS baseline capture on 18 October 2026

The user supplied a Windows terminal screenshot showing the lookup below and an IPv4 answer. The public extract replaces the actual Pi address with `YOUR_PI_IP`; private local server labels and addresses are omitted. This is a redacted extract, not a verbatim complete transcript:

```text
nslookup -type=A example.com YOUR_PI_IP

[resolver identification omitted]

Non-authoritative answer:
Name:    example.com
Addresses:  172.66.147.243
          104.20.23.154
```

This requests an IPv4 (`A`) record from the explicitly named DNS server without changing the desktop's configured resolver. [Microsoft documents the command and server argument](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup). The capture establishes an answer from the Pi's address, not blocking, fresh upstream contact, or use of Pi-hole by every desktop application. The two public answer addresses are the observed result at this check and may change.

## Query history diagnosis on 18 October 2026

After the user signed in, Codex refreshed Query Log; it still displayed no data. Privacy settings showed **Log DNS queries/replies** checked. Codex switched the settings view from Basic to Expert without saving; the selected radio button was **3 — Anonymous mode**. The page explains that this mode saves no history to the database and shows none in Query Log. These are browser observations; no matching query row has been observed, and no privacy change was applied.

At this 18 October checkpoint, the proposed action was **0 — Show everything and record everything**, then **Save & Apply**. The correction, fresh lookup and new log entry were pending. Historical anonymous queries cannot be recovered by lowering the level. The earlier authenticated browser observation remains dated 17 October.

## Saved privacy correction on 19 October 2026

After the learner reported saving the change, Codex reloaded Privacy and still observed **3 — Anonymous mode**. Codex selected **0 — Show everything and record everything**, clicked **Save & Apply**, and saw a success notification. A subsequent reload verified level **0** persisted, **Log DNS queries/replies** remained enabled and Pi-hole showed **Active**. The reason the earlier save did not take effect was not established.

This verifies saved configuration. The subsequent query-row evidence is recorded separately below.

## Allowed Query Log entry on 19 October 2026

The learner supplied a Query Log screenshot showing an `A` query for `example.com`, the private desktop client and a displayed response time of **8.0 ms**. Codex then filtered for `example.com` in the authenticated browser and expanded the row:

| Field | Observed value |
| --- | --- |
| Query received, as displayed by Pi-hole | `2026-10-19 01:00:30.738` |
| Query status | `Forwarded` |
| Reply from | `9.9.9.9#53` |
| Reply | `IP` |
| Database ID | `1` |

The displayed timestamp is not an independent clock check. The private client address is omitted. The row records an allowed request and a reply from Quad9 after the privacy correction. The visible **Deny** control is an available action, not the query's status. No new Windows command-output capture or returned answer addresses were supplied for this request; the public IP addresses in the 18 October terminal extract belong to that earlier check.

## Exact deny configuration capture on 19 October 2026

The learner supplied a Domains screenshot showing exactly one row: domain `example.com`, type **Exact deny**, status **Enabled**, group **Default**, and a blank comment. The suggested comment `Controlled DNS test` is not present. Codex did not create this rule and has not independently read back the live browser state at this checkpoint.

The screenshot establishes the captured rule configuration. The following terminal result records its separate DNS test.

## Blocked desktop lookup capture on 19 October 2026

The learner supplied a Windows terminal screenshot showing the same explicit lookup returning **`0.0.0.0`** for `example.com`. The Pi's private address and local resolver identification are omitted from this public extract:

```text
nslookup -type=A example.com YOUR_PI_IP

[resolver identification omitted]

Name:    example.com
Address:  0.0.0.0
```

This captures a blocked response matching Pi-hole's default IPv4 blocking mode. The following screenshot records the separate Query Log reason.

## Exact blocked reason capture on 19 October 2026

The learner supplied an expanded Query Log screenshot for `A example.com`:

| Field | Observed value |
| --- | --- |
| Query received, as displayed by Pi-hole | `2026-10-19 23:16:52.531` |
| Query status | `Blocked (exact)` |
| Reply | `IP` |
| Database ID | `32` |
| Displayed response time | `0.3 ms` |

The private client address is omitted, and the displayed timestamp is not an independent clock check. Combined with the Windows `0.0.0.0` answer, this verifies the controlled block with its exact-deny reason. The green **Allow** control is an available action, not a query status; a later screenshot captures a temporary allow entry as described below.

## Temporary allow and cleanup captures on 19 October 2026

The learner supplied a Domains screenshot showing both enabled **Exact allow** and **Exact deny** entries for `example.com`, assigned to **Default**. The allow comment reads `Added from Query Log`. This records the resulting configuration, not a complete click trace. [The documented priority order places Exact allow before Exact deny](https://docs.pi-hole.net/database/domain-database/); that temporary allow could override the test deny.

The next learner screenshot shows an empty table with two deletion-success notifications. Codex then reloaded Domains and observed every filter checked, a blank search, **No data**, **0 entries**, zero allow/deny badges, and Pi-hole **Active**. Both temporary test entries were removed.

## Restored lookup and Query Log on 19 October 2026

The learner supplied a new Windows terminal screenshot. This redacted extract replaces the explicit Pi address with `YOUR_PI_IP` and omits private resolver identification:

```text
nslookup -type=A example.com YOUR_PI_IP

[resolver identification omitted]

Non-authoritative answer:
Name:    example.com
Addresses:  172.66.147.243
            104.20.23.154
```

Codex filtered Query Log for `example.com` and expanded all three visible rows. The latest restored row showed:

| Field | Observed value |
| --- | --- |
| Query received, as displayed by Pi-hole | `2026-10-19 23:22:29.504` |
| Query status | `Forwarded` |
| Reply from | `149.112.112.112#53` |
| Reply | `IP` |
| Database ID | `34` |
| Displayed response time | `10.5 ms` |

The displayed timestamp is not an independently verified clock. The middle row retained **Blocked (exact)**, database ID **32**; the oldest retained the initial allowed request, database ID **1**. The terminal answer, empty Domains readback and latest forwarded reply verify **allowed → blocked → restored** with the test rules cleaned up. The selected sanitized proof image is included in the project overview, ordered newest first.

Household DNS settings were unchanged during these explicit lookups, and the Default-group rules were not inherently desktop-only. No further DNS test is needed to complete this exercise. GitHub publication and household rollout have not been performed.

## Private configuration backup verification on 20 October 2026

The learner exported Pi-hole configuration and saved one ZIP privately, outside the GitHub draft. Codex inspected the saved archive read-only:

| Check | Result |
| --- | --- |
| Saved archive size and members | `24,334` bytes; `5` members |
| ZIP CRC validation | Passed |
| TOML configuration | Parsed successfully |
| Gravity SQLite database | In-memory `quick_check` returned `ok` |
| Reduced FTL SQLite database | In-memory `quick_check` returned `ok` |
| Expected configuration tables | Present |
| Temporary domain rules | Absent |
| Query history | Absent |
| Archive changes, extraction or restore | None |

Codex captured the current Teleporter page after the save; it did not click Export or Import. The archive remained unchanged, and its filename, location, binary contents, authentication details and network values are excluded from this public record.

This is **structural backup verification**, not a tested restore. A Pi-hole configuration export does not replace an operating-system image or separate records for SSH and router DHCP reservations. Restore testing remains future work. Next is reviewing the proposed public files and author identity before a first local commit; GitHub Desktop review, a commit and upload are still pending, with no remote configured.

The original screenshots are retained with the private setup notes. This public draft omits private network addresses, MAC addresses, account identifiers and the SSH fingerprint. Public DNS service and test-domain answer addresses are retained where they explain a result.
