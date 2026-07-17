# Project 3 - Lessons Learned

## Proxmox VM Configuration

- **Windows 11 requires TPM 2.0, UEFI (OVMF), and Secure Boot.** These have to be configured in Proxmox *before* starting the install — switching BIOS mode (SeaBIOS ↔ OVMF) after Windows is already installed usually breaks the boot and forces a reinstall. CPU/RAM/network settings, by contrast, can be changed anytime with no issue.

- **VirtIO NICs need drivers Windows doesn't ship with.** Proxmox defaults to the VirtIO (paravirtualized) network adapter, which offers better performance but requires installing separate drivers inside Windows before it'll connect. Switching the adapter model to **Intel E1000** avoided this entirely — Windows has native drivers for it, no extra steps needed. For a lab like this, the performance tradeoff is irrelevant.

- **CPU type `host` passes through real CPU features**, which also helps satisfy Windows 11's hardware compatibility checks (virtualization extensions, etc.) more reliably than the generic `kvm64` type.

## Windows 11 Setup (OOBE)

- **Windows 11 Home cannot join a domain** — only Pro, Enterprise, and Education editions support domain join. This has to be decided at the ISO/edition level before installing.

- **Bypassing the Microsoft account requirement:** disconnecting the VM's network adapter in Proxmox *before* starting Windows Setup causes OOBE to fail its internet check and fall back to a local account path automatically — no need for the `oobe\bypassnro` command trick if you plan ahead. The command-line bypass is still useful as a backup if you're already past that screen.

## Networking & DNS

- **The domain controller needs a static IP set before promotion.** DNS and domain lookups depend on the DC being at a consistent address; letting it stay on DHCP risks breaking the domain later if the address changes.

- **A domain controller should point to itself for DNS** (`127.0.0.1` as the preferred DNS server) once promoted, since it becomes the authoritative DNS server for the domain.

- **Domain-joined machines must use the DC as their DNS server**, not a public DNS like 8.8.8.8 or the router's default — otherwise they can't resolve the domain name (`homelab.local`) at all, which blocks the domain join outright.

- **RDP by hostname (`machinename.homelab.local`) only resolves from machines that use the DC for DNS.** A Mac (or any device outside the domain) can't resolve that name and will fail with a "PC can't be found" error. Connecting by IP address avoids this entirely.

## Active Directory & Accounts

- **A domain-joined machine still has its original local account(s) sitting alongside the new domain account.** They're completely separate accounts (different SAM databases, different profiles) that just happen to share a username — logging in without specifying the domain can silently default to the local account instead of the intended domain one. Running `whoami` after login is the reliable way to confirm which one you're actually using (`hostname\user` = local, `domain\user` = domain).

- **Domain login format matters by context:** the local Windows login screen ("Sign in to: HOMELAB") only needs the plain username, but RDP client apps need the domain prefix explicitly (`HOMELAB\username`) since they don't have that same domain context pre-set.

- **Regular domain users don't get RDP access automatically** — only local Administrators can RDP into a machine by default. A user has to be explicitly added to the local **Remote Desktop Users** group on the target machine before they can connect remotely.

## Group Policy

- **GPOs applying successfully can show up in unexpected ways.** A wallpaper-lock policy caused Windows to surface an "activate Windows to personalize" message instead of a clear "this setting is managed by your organization" notice — the real confirmation came from checking the desktop directly and running `gpresult /r` to see the GPO listed under Applied Group Policy Objects.
