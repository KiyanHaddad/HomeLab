# Pi setup and remote administration

16 October 2026

I started this build with a Pi 4 I already owned. The immediate goal was to get a Linux machine that I could manage from my desktop, then use it for real services. SSH login works, and the initial update process has finished. The next day's follow-up records a fresh connection after the reboot step.

## Getting access to the router

Before working on services, I had to resolve access to the Archer AX11000’s admin page. It was in AP mode behind the household Deco mesh. The page would not load while connected to the mesh, but a different management address was available when disconnected.

Codex helped inspect my desktop’s network connection and locate a responding web interface. It confirmed the AX11000 login page at the address used while connected to Deco. I then logged in myself.

Finding the current management address solved the access problem. I didn’t need to flash firmware or reset the router during that investigation. The address change fits DHCP and fallback-address behaviour, although I haven’t checked the DHCP configuration yet.

I also learned that AP mode bridges devices onto the existing LAN and exposes fewer routing controls. That is useful for this first build, but it is not the isolated network I eventually want for experiments.

## Getting into the Pi

The setup used Raspberry Pi Imager with SSH enabled. I chose Lite for the Pi’s role as an always-on server, rather than because a terminal-only system looks more impressive.

An initial SSH password attempt failed. After deciding to start over, I hit a changed host-key warning on the next connection attempt. Those were two different problems: the password authenticates my account, while the host key identifies the server.

I learned why Windows remembers server keys in `known_hosts`, and why a new installation can trigger a warning at an address I have used before. The recovery guidance was to compare the fingerprint on the Pi itself, then replace only the outdated record for that address. My notes don’t yet include a local fingerprint comparison.

I subsequently logged in over SSH. That gave me a remote Linux terminal on the Pi, rather than a terminal running commands on my Windows desktop.

## Establishing the starting point

I ran three commands to identify the current account, machine and operating system:

| Command | What it tells me |
| --- | --- |
| `whoami` | Which Linux account is running the command |
| `hostname` | Which machine I am administering |
| `cat /etc/os-release` | The installed operating system and release |

The captured output identifies Debian GNU/Linux 13, Trixie. Learning to check the target machine before making changes is a habit I want to carry into later work with multiple servers.

I then ran `sudo apt update`. `sudo` grants administrator privileges to that command; APT manages software packages. `update` refreshes the package catalogue rather than installing the upgrades. It reported 14 available upgrades.

The next maintenance step was `sudo apt full-upgrade`, which installs updates and can adjust supporting packages. The update process finished and returned me to the shell. I didn’t save the full package output this time.

## Reconnecting after the reboot step — 17 October 2026

I followed the guided reboot step. The SSH connection closed, I waited about a minute, and I connected again from my desktop. I reported reaching the Pi's shell prompt. This is a useful recovery check: I can open a fresh remote session after the restart step.

I then ran `uptime` and `systemctl is-active ssh` and supplied a terminal screenshot. It shows 17 minutes of uptime and the SSH service returning `active`. Together with the fresh connection, these results support recovery after the restart step. They describe this check, rather than proving long-term stability or that every available update was installed.

I also ran `df -h /` and supplied its output. The root filesystem showed `235G` total, `3.9G` used, `222G` available and `2%` used. `df` reports filesystem space, `-h` makes the display readable, and `/` selects the root filesystem. That confirms room for the first service; it is not a storage health test.

I ran `ip -4 route` next. The capture shows a default route through the household gateway on `eth0`, plus a directly connected `/22` network. The default route is marked `proto dhcp`: DHCP supplied that route. This does not establish whether an address reservation exists.

I then saved address reservations in Deco for both the Pi and the Archer's management interface, keeping their existing addresses. I clarified that I reserved the Archer address rather than changing Deco's LAN gateway. These changes are recorded from my session confirmation; no reservation screenshot or subsequent DHCP lease renewal has been captured yet.

A reservation tells the DHCP server to offer the same address to that device. I then ran `sudo ss -lntup` and supplied its output. It shows SSH on TCP port `22`, Avahi discovery on UDP `5353` and additional UDP ports, and NetworkManager on UDP `546`. No listeners are shown on `53`, `80` or `443` at that check. I did not stop existing services.

This helped separate device addresses from service ports: discovery port `5353` is different from Pi-hole's DNS port `53`. I then downloaded the official installer with `curl -fL -o basic-install.sh https://install.pi-hole.net`. The supplied screenshot shows the final transfer reaching 100%, displaying `102k`, then returning to the shell prompt.

Downloading and running an installer are separate actions. After the guided start step, I reported reaching the upstream DNS selection screen. At that point installation was in progress, with no completion result yet supplied.

Upstream DNS is the resolver Pi-hole asks for allowed names it cannot answer from cache. I paused at that screen to ask why one provider was being recommended over the others and supplied its screenshot. I then confirmed selecting **Quad9 (filtered, DNSSEC)**, the option without ECS. This selection is recorded from my report, rather than a capture of the accepted provider.

I chose the additional [known-malicious-domain filtering from Quad9](https://docs.quad9.net/services/) for the eventual household role. Pi-hole's own list and its upstream resolver provide separate filtering layers; a controlled block-and-restore test will help demonstrate Pi-hole's contribution.

The screenshot showed StevenBlack's Unified Hosts List with **Yes** focused. I subsequently confirmed accepting it and reaching query logging. The base list combines adware and malware domains; optional category extensions are separate. [StevenBlack's list documentation](https://github.com/StevenBlack/hosts) explains those variants. One starting list keeps troubleshooting manageable; it does not guarantee zero false positives or removal of every ad.

I confirmed choosing **Yes** for query logging. The installer privacy screenshot showed **0 — Show everything** selected before Continue. We recommended 0 for our controlled test so I can trace the requesting device and domain; that screenshot did not establish the saved setting. Raw history stays private, and public evidence will use deliberate test queries.

Pi-hole finished installing, and I saved the admin password privately. This completion is based on my session report, without a completion screenshot or captured final status output. I then confirmed getting into the dashboard from my desktop. Dashboard access is user-reported; no dashboard screenshot has been supplied. It establishes access to web administration, rather than proving working DNS filtering.

The proposed DNS check used a new Command Prompt or PowerShell window on my desktop while leaving SSH open:

```text
nslookup -type=A example.com YOUR_PI_IP
```

`YOUR_PI_IP` is a public-guide placeholder to replace with the Pi's reserved address. `nslookup` asks a DNS server for a record; `-type=A` requests an IPv4 address, and the second argument explicitly chooses the Pi as the resolver. This one lookup does not change Windows or household DNS settings. [Microsoft's command reference](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup) explains the syntax.

At this checkpoint, the lookup and its matching Query Log entry were still pending.

## First desktop DNS answer — 18 October 2026

I supplied a Windows terminal screenshot showing the command directed to the Pi's address. It returned a **Non-authoritative answer** for `example.com`, with IPv4 addresses `172.66.147.243` and `104.20.23.154`. These are the captured public answers, not permanent addresses to expect on every repeat. The private resolver address and local labels are omitted from the public evidence.

This confirms that the explicit lookup received an IPv4 answer from the Pi's address. It does not yet prove blocking, a fresh query to Quad9, or that my desktop's other traffic uses Pi-hole.

After I signed in, Codex refreshed Query Log and still found no data. In Privacy settings, **Log DNS queries/replies** was checked. Switching only the settings view from Basic to Expert exposed the selected **3 — Anonymous mode** radio button. The page states that this mode saves no query history to the database and displays none in Query Log. This explains the missing history despite a working DNS answer; the installer image had shown a selection, not verified saved configuration. Codex did not save a privacy change.

At the end of this check, the proposed next action was to select **0 — Show everything and record everything**, then **Save & Apply**. The privacy correction and new matching entry were still pending on 18 October. Earlier anonymous queries cannot reappear after changing the level.

## Saving and checking the privacy correction — 19 October 2026

I reported saving the privacy change. Codex reloaded the page and still found **3 — Anonymous mode** selected. Codex then selected **0 — Show everything and record everything**, clicked **Save & Apply**, and observed a success notification. After another reload, level **0** remained selected, query-file logging was enabled and Pi-hole showed **Active**. We have not established why the earlier save did not take effect.

This verified the saved setting. The next check was whether a new desktop query appeared in Query Log.

## Seeing an allowed request in Query Log — 19 October 2026

I supplied a Query Log screenshot showing an `A` query for `example.com` from the desktop and a displayed response time of **8.0 ms**. Codex filtered for the domain and expanded that row in the authenticated browser. Its status was **Forwarded**, the reply came from **Quad9 at `9.9.9.9#53`**, and its reply type was **IP**. The private client identifier is omitted here.

We now have an allowed request recorded after the privacy correction, including which upstream answered it. This is different evidence from the 18 October terminal answer: I have not supplied a new Windows command-output capture or new returned IP addresses for this request. The visible **Deny** button is an available action, not a claim that this query was blocked.

## Setting up the controlled block — 19 October 2026

I supplied a screenshot showing one domain row: `example.com`, **Exact deny**, **Enabled**, assigned to **Default**. The comment is blank; the suggested `Controlled DNS test` comment was not entered. This captures the rule configuration, not a blocked DNS result. Codex has not independently read back the live rule at this checkpoint.

## Receiving the blocked answer — 19 October 2026

I repeated the explicit desktop lookup and supplied its terminal screenshot. This time `example.com` returned **`0.0.0.0`** from the Pi's address, matching Pi-hole's default IPv4 blocking response. This is an actual captured result, rather than the earlier prediction.

I then supplied an expanded Query Log screenshot for `A example.com`. It shows **Blocked (exact)**, reply type **IP**, database ID **32**, and a displayed response time of **0.3 ms**. Together, the terminal result and the logged reason demonstrate the controlled block. The screenshot's displayed timestamp is recorded in the evidence notes without treating it as an independently checked clock.

## Cleaning up a temporary allow and verifying restoration — 19 October 2026

A later screenshot showed both an enabled **Exact allow** and an enabled **Exact deny** for `example.com` in Default. The allow entry's comment was `Added from Query Log`. The screenshots establish the resulting entries rather than every click that created them. This was a useful detour: [Pi-hole gives an Exact allow priority over an Exact deny](https://docs.pi-hole.net/database/domain-database/), so adding an allow would not be the same cleanup as removing the test rules.

I supplied the next screenshot with an empty Domains table and two deletion-success notifications. Codex then reloaded Domains with every filter checked and the search blank: **No data**, **0 entries**, zero allow/deny badges, and Pi-hole **Active**. Both temporary entries were gone.

I repeated the desktop lookup and supplied its terminal output. `example.com` again returned a **Non-authoritative answer** with public IPv4 addresses **`172.66.147.243`** and **`104.20.23.154`**. Codex expanded the latest Query Log row and observed **Forwarded**, reply from **`149.112.112.112#53`**, reply type **IP**, database ID **34**, and a displayed **10.5 ms**. This verifies restoration after cleanup. The older allowed and blocked rows remained visible alongside it.

The controlled **allowed → blocked → restored** exercise is complete. Household DNS settings remain unchanged; the rule's Default group did not inherently make it desktop-only. The [results page](../results/pi-foundation.md) records the completed cycle.

## Saving a configuration backup — 20 October 2026

I exported Pi-hole's configuration and saved the ZIP privately, outside the GitHub draft. Codex checked the saved archive without changing it: **24,334 bytes**, **5 members**, ZIP CRC check passed, TOML parsed, and the gravity and reduced FTL SQLite databases each returned **`ok`** from an in-memory `quick_check`. Expected configuration tables were present; temporary domain rules and query history were absent.

These checks establish a readable, structurally consistent configuration export. They do not demonstrate a successful restore. Nothing was extracted or restored. Codex captured the Teleporter page after I saved the file; it did not click Export or Import. The export is separate from backing up the operating system, SSH configuration or router DHCP reservations.

Next is reviewing the public draft and author identity before the first local commit. GitHub Desktop review is still pending; no commit, remote or upload has been created for this draft. The raw backup stays private.

## Evidence and help

The [terminal evidence extract](../evidence/pi-terminal-evidence.md) separates captured output, reported results and pending checks. Codex helped explain commands, troubleshoot connectivity and draft these notes. I performed the Pi setup and SSH actions; the router diagnostics performed by Codex are credited above.

## References

- [Raspberry Pi setup documentation](https://www.raspberrypi.com/documentation/computers/getting-started.html)
- [Raspberry Pi software update guide](https://www.raspberrypi.com/documentation/computers/os.html#update-your-current-raspberry-pi-software)
- [OpenSSH key management manual](https://man.openbsd.org/ssh-keygen)
- [TP-Link AP management access guidance](https://community.tp-link.com/en/home/stories/detail/501250/)
