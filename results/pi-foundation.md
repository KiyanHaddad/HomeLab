# Pi foundation lab results

17 October 2026 · updated 20 October 2026

I can administer my Pi from my desktop over SSH. After following the reboot step, I opened a fresh connection and supplied terminal evidence showing recent uptime and a running SSH service. This is the working Linux foundation for the lab's first service.

The first Pi-hole exercise is also complete: **allowed → blocked → restored**, with the temporary test rules removed and the restored answer checked in both Windows and Query Log.

## What worked

| Action | Result | What it means |
| --- | --- | --- |
| Reconnect from the desktop | Returned to the Pi shell, reported during the session | A fresh remote session worked after the reboot step. |
| Run `uptime` | `up 17 min`, captured in the screenshot | The Pi had been running for 17 minutes since boot. |
| Run `systemctl is-active ssh` | `active`, captured in the screenshot | The SSH server was running at that moment. |
| Run `df -h /` | `235G` total, `3.9G` used, `222G` available, `2%` used | The root filesystem has ample room for the first service. |
| Run `ip -4 route` | Default route through household gateway on `eth0`, marked `proto dhcp`; connected `/22` network | Ethernet routing is recorded. |
| Save address reservations in Deco | Pi and Archer reservations saved, confirmed during the session | Current addresses are reserved according to my report; lease renewal has not been tested. |
| Inspect listening services | Capture shows SSH `22`, Avahi `5353` and other UDP ports, NetworkManager `546`; no listeners shown on `53`, `80`, `443` | No conflict with Pi-hole's usual ports was observed at this check. |
| Download the official installer | Final curl transfer reaches 100%, displaying `102k`, then returns to the prompt | The installer file was downloaded. |
| Choose upstream DNS | Quad9 (filtered, DNSSEC), without ECS, selected according to my session report | The choice is reported; an accepted-provider capture was not supplied. |
| Complete installation | I report Pi-hole finished installing and I saved the admin password privately | Completion is user-reported; complete configuration has not been read back. |
| Open the dashboard | I confirm being in the dashboard; subsequent Query Log and privacy controls are browser-observed | Web administration access works. At that stage, DNS filtering still needed the separate tests recorded below. |
| Query `example.com` from Windows — 18 October | Capture shows a Non-authoritative IPv4 answer: `172.66.147.243` and `104.20.23.154`, from the Pi's address | The explicit lookup received an answer. Blocked/restored results were pending at this check. |
| Investigate missing history — 18 October | Refreshed Query Log is empty; query-file logging is checked, but privacy level `3 — Anonymous mode` is selected | Active privacy mode explains absent Query Log history. The setting had not been corrected at this checkpoint. |
| Save privacy correction — 19 October | Codex applied level `0`, observed a success notification, reloaded and verified level `0` persisted, query-file logging enabled and Pi-hole Active | The saved setting is verified. |
| Inspect an allowed query — 19 October | My Query Log screenshot shows `A example.com` and `8.0 ms`; Codex expanded the row and observed `Forwarded`, reply from `9.9.9.9#53`, reply type `IP` | An allowed request is logged, with Quad9 shown as the answering upstream. No Windows terminal-answer capture was supplied for this allowed request. |
| Configure the test deny — 19 October | My screenshot shows one `example.com` row: Exact deny, Enabled, Default group, blank comment | The rule configuration is captured, separately from its DNS test. |
| Repeat the desktop lookup — 19 October | My terminal screenshot shows `example.com` returning `0.0.0.0` from the Pi's address | The blocked response is captured. |
| Inspect the blocked reason — 19 October | My expanded Query Log screenshot shows `A example.com`, `Blocked (exact)`, reply type `IP`, database ID `32`, and `0.3 ms` | The controlled block is verified with its exact-deny reason. |
| Clean up the test entries — 19 October | My captures show a temporary Exact allow alongside the Exact deny, then an empty table and two deletion-success notifications. Codex reloaded Domains with all filters checked and search blank: zero entries, Pi-hole Active | Both temporary rules are removed, including the allow detour. |
| Verify restoration — 19 October | My new terminal capture returns `172.66.147.243` and `104.20.23.154`; Codex expanded the newest log row: Forwarded, reply from `149.112.112.112#53`, IP reply, database ID `34`, `10.5 ms` | Normal resolution returned after cleanup. The controlled cycle is complete. |
| Save configuration backup — 20 October | I saved a private ZIP outside the GitHub draft. Codex verified CRC, parsed TOML and received `ok` from both in-memory SQLite quick checks; no temporary domain rules or query history were present | The export is structurally verified. A restore has not been tested. |

The earlier OS identification capture showed Debian 13, Trixie. The initial update process finished according to my session report; I did not capture the complete package transaction. The Archer is in AP mode on the household LAN. Pi-hole installation is user-reported; dashboard access has since been browser-observed. The controlled DNS cycle, rule cleanup and structural configuration-backup checks are complete. Restore testing, network isolation and household rollout remain future work.

The [evidence extract](../evidence/pi-terminal-evidence.md) has the output and its limits. The [build journal](../journal/pi-foundation.md) explains the router access and login problems along the way.

## Remember the maintenance cycle

**Update → restart → reconnect → verify**

Restarting closes the remote session. Reconnecting tests the access path; uptime and service status provide additional checks. A successful login is useful, but different commands answer different questions.

## A short recall check

Try answering aloud before opening each answer. One sentence is enough. These are learning prompts; no result has been recorded for them.

<details>
<summary>Why did rebooting close SSH, and what evidence shows the Pi came back?</summary>

Restarting stops the remote operating system and its connection. I reconnected successfully, then captured 17 minutes of uptime and SSH `active`.

</details>

<details>
<summary>What is the difference between uptime and SSH active?</summary>

`uptime` reports elapsed time since boot. `systemctl is-active ssh` checks whether the SSH service is running now. Neither alone certifies system security or long-term stability.

</details>

<details>
<summary>Does apt update install upgrades?</summary>

No. `apt update` refreshes the package catalogue. `apt full-upgrade` installs upgrades and resolves dependency changes, which can include removing packages.

</details>

For a starting review rhythm, try the questions now, recall them briefly next session, and apply them during a real Pi check about a week later. Adjust when recall is difficult or already easy. These intervals are a practical suggestion, not a proven optimum or scheduled reminder.

Retrieval, spaced review and explanatory questions are recommended in [public U.S. Department of Education guidance](https://ies.ed.gov/ncee/wwc/PracticeGuide/1). [Roediger and Karpicke's experiments](https://www.psychologicalscience.org/journals/psychological-science/j.1467-9280.2006.01693.x/) found benefits from retrieval for delayed recall compared with repeated study.

## How the DNS exercise worked

I confirmed selecting **Quad9 (filtered, DNSSEC), without ECS**. [Quad9's filtered service](https://docs.quad9.net/services/) adds known-malicious-domain blocking upstream, alongside Pi-hole's local filtering. Our controlled test used a harmless domain that resolved before adding the temporary rule.

I confirmed accepting **StevenBlack's Unified Hosts List** and **Yes** for query logging. The installer screenshot showed **0 — Show everything** selected before Continue; this was not proof of its saved value. The later browser inspection found active level **3**, described below. Installation is reported complete, and the admin password is saved privately.

The [base list combines adware and malware domains](https://github.com/StevenBlack/hosts); optional category extensions for gambling, pornography and social media are separate. One starting list makes it easier to investigate a broken site. It cannot guarantee zero false positives or block every ad.

[DNS history can include domains, clients, timestamps and query outcomes](https://docs.pi-hole.net/database/query-database/#query-table). It does not contain HTTPS page contents or full URL paths. Keep raw history private and publish only deliberate test evidence. [Query-file logging](https://docs.pi-hole.net/ftldns/configfile/#querylogging) is separate from FTL privacy and database retention; choosing No alone does not disable all history.

[Level 0 preserves detailed FTL statistics](https://docs.pi-hole.net/ftldns/privacylevels/), helping me trace a test domain to its requesting device. Higher levels hide domains, then clients, or retain only anonymous statistics. We will review privacy and retention before any household rollout. These FTL controls are separate from query-file logging.

I confirmed being in the dashboard from the desktop. Codex subsequently inspected Query Log and privacy settings in the authenticated browser. Web access alone does not establish working DNS filtering; that requires a separate controlled test.

On 18 October I supplied a Windows screenshot of this command, shown here with the Pi's private address replaced by a placeholder:

```text
nslookup -type=A example.com YOUR_PI_IP
```

Replace `YOUR_PI_IP` with the Pi's reserved address. `nslookup` requests a DNS record; `-type=A` asks for an IPv4 address, and the final argument directs this query to the Pi. It does not change Windows or router DNS settings. [Microsoft's command reference](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup) documents the syntax.

The capture shows a **Non-authoritative answer** for `example.com`: `172.66.147.243` and `104.20.23.154`. These public addresses are the observed answer, not a fixed future expectation. The response came from the Pi's address. It does not prove blocking, fresh contact with Quad9, or that all desktop applications use Pi-hole.

**Problem — 18 October:** after I signed in, a refreshed Query Log still showed no data. Browser inspection found **Log DNS queries/replies** checked but active privacy **3 — Anonymous mode** selected. The page states that anonymous mode records no database query history and shows none in Query Log. Switching Basic to Expert only exposed the control; no setting was saved at that check.

**Correction and verification — 19 October:** after I reported saving the change, Codex reloaded and still found level **3**. Codex selected **0 — Show everything and record everything**, clicked **Save & Apply**, saw success, then reloaded again. Level **0** persisted, query-file logging remained enabled and Pi-hole showed **Active**. We have not established why the earlier save did not take effect.

**Allowed-query result — 19 October:** I supplied a Query Log screenshot showing an `A` query for `example.com` from the desktop and **8.0 ms**. Codex expanded that row and observed **Forwarded**, a reply from **Quad9 at `9.9.9.9#53`**, and reply type **IP**. This confirms a recorded allowed request after the privacy correction. I have not supplied a new Windows command-output capture or new answer IP addresses; the addresses above belong to the 18 October terminal test. The row's **Deny** button is an available action, not its result. Earlier anonymous history cannot be recovered by changing privacy.

**Test rule captured — 19 October:** my screenshot shows one `example.com` row with type **Exact deny**, status **Enabled**, group **Default**, and no comment. The suggested `Controlled DNS test` comment was not entered. This is screenshot evidence of configuration; Codex has not independently read back the live rule at this checkpoint.

**Blocked result — 19 October:** my terminal screenshot shows the repeated explicit lookup returning **`0.0.0.0`** for `example.com` from the Pi's address. My subsequent expanded Query Log screenshot shows **Blocked (exact)**, reply type **IP**, database ID **32**, and **0.3 ms**. These capture the blocked response and its exact-deny reason. The displayed timestamp is transcribed in the evidence notes, without treating it as a clock check.

**Cleanup detour — 19 October:** a later screenshot showed both an enabled **Exact allow** and **Exact deny** for `example.com` in Default. The allow comment was `Added from Query Log`; the screenshots do not record every creation click. [Exact allow takes priority over Exact deny](https://docs.pi-hole.net/database/domain-database/), so an override would not demonstrate removal of the test configuration. My next screenshot showed an empty Domains table and two deletion-success notifications. Codex reloaded with all filters checked and search blank, confirming **0 entries** and Pi-hole **Active**.

**Restored result — 19 October:** my new desktop terminal capture returned a Non-authoritative answer for `example.com`: **`172.66.147.243`** and **`104.20.23.154`**. Codex expanded the latest Query Log row: **Forwarded**, reply from **`149.112.112.112#53`**, reply type **IP**, database ID **34**, and **10.5 ms**. The older blocked and allowed rows remained visible. This verifies restoration after both test entries were removed.

**Exercise complete:** allowed → blocked → restored is verified. No further DNS test is needed for this checkpoint. Household DNS and Deco's DHCP service remain unchanged; GitHub publication has not occurred. The test rules' Default group was not inherently limited to the desktop.

**Configuration backup — 20 October:** I exported and privately saved a **24,334-byte ZIP with 5 members**. Codex's read-only checks passed ZIP CRC, TOML parsing and `quick_check` for the gravity and reduced FTL SQLite databases, with expected configuration tables present. Temporary domain rules and query history were absent. The archive was unchanged; nothing was extracted or restored. Codex captured the Teleporter page afterward without clicking Export or Import.

This verifies backup structure, not recovery. The private export does not replace backups of the operating system, SSH configuration or router DHCP reservations. Next is reviewing the proposed public files and author identity before the first local commit. GitHub Desktop review, commit and upload remain pending; no remote is configured.

This was a guided build with Codex helping explain commands, troubleshoot and draft notes. I performed the Pi actions. [Return to the project overview](../README.md).
