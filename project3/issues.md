# Project 3 - Issues & Troubleshooting

## Issue: Windows 11 setup demanded network drivers

**Symptom:** During Windows 11 install, setup got stuck asking to install network adapter drivers before it would proceed.

**Cause:** The VM's network adapter was set to VirtIO (paravirtualized), which Windows has no built-in driver for.

**Fix:** Shut down the VM, changed the network adapter model in Proxmox from VirtIO to **Intel E1000**, restarted the VM. Windows recognized the adapter natively with no driver install needed.

---

## Issue: Couldn't get past OOBE without a Microsoft account

**Symptom:** Windows 11 setup insisted on an internet connection and Microsoft account sign-in, with no visible offline/local account option.

**Cause:** Newer Windows 11 builds hide the "I don't have internet" option unless the device genuinely can't reach the internet.

**Fix:** Disconnected the VM's network adapter in Proxmox before starting setup, which forced the network check to fail and exposed the "I don't have internet" → "Continue with limited setup" path, letting a local account be created. (The `oobe\bypassnro` command via Shift+F10 is an alternative if you're already past that screen.)

---

## Issue: RDP couldn't find the Windows 11 VM by hostname

**Symptom:** Connecting via `Workspace.homelab.local` from the Mac RDP client failed with "Unable to connect... the PC can't be found" (error 0x104).

**Cause:** The Mac isn't part of the `homelab.local` domain and doesn't use the DC for DNS, so it has no way to resolve that hostname to an IP address.

**Fix:** Used the VM's actual IP address (from `ipconfig`) in the RDP client instead of the domain hostname.

---

## Issue: RDP login rejected valid domain admin credentials

**Symptom:** Attempting to join the Windows 11 VM to the domain failed even with the correct `HOMELAB\Administrator` credentials.

**Cause:** The Windows 11 VM's DNS server was not yet pointed at the domain controller, so it couldn't locate `homelab.local` on the network at all — this produces a failure that looks like a credentials issue but is actually a name resolution issue.

**Fix:** Set the Windows 11 VM's preferred DNS server to the DC's static IP before attempting the domain join again.

---

## Issue: Logged in as the wrong "freddy" account

**Symptom:** After joining the domain and logging in with just the username `freddy` (no domain prefix), `whoami` showed `workspace\freddy` instead of `homelab\freddy`.

**Cause:** Windows defaulted to the local account of the same name instead of the intended domain account, since no domain was explicitly specified at login.

**Fix:** Signed out, selected **Other user** at the login screen (which explicitly showed "Sign in to: HOMELAB"), and logged in as `freddy` in that context to authenticate against the domain instead of the local account.

---

## Issue: RDP as a standard domain user failed entirely

**Symptom:** Could not RDP into the Windows 11 VM as `HOMELAB\freddy`, even with correct credentials.

**Cause:** Only local Administrators have RDP access by default; a standard domain user isn't automatically granted remote access permissions.

**Fix:** Added `freddy` to the local **Remote Desktop Users** group on the Windows 11 VM (via Settings → Remote Desktop → Remote Desktop users, or `net localgroup "Remote Desktop Users" HOMELAB\freddy /add`).

---

## Issue: Wallpaper GPO showed an "Activate Windows" prompt instead of a clear confirmation

**Symptom:** After applying the desktop wallpaper GPO and running `gpupdate /force`, attempting to change the wallpaper manually triggered a message about needing to activate Windows, rather than a clear "this setting is managed by your organization" notice.

**Cause:** The VM was not activated (installed from ISO without a product key), which is unrelated to Group Policy but happened to surface alongside the locked personalization settings.

**Fix:** Not a real blocker — confirmed the GPO was actually applying by checking the desktop wallpaper visually and running `gpresult /r` to verify `Set-Desktop-Wallpaper` appeared under Applied Group Policy Objects. Activation status doesn't affect AD/GPO functionality for lab purposes.
