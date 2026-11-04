# HomePanel Studio v1 beta

## Install and open

Requires **Home Assistant OS with Supervisor** on an **amd64** or **aarch64** host.
For Raspberry Pi, install **64-bit Home Assistant OS**; a 64-bit processor running
a 32-bit OS is not enough. Initial mini PC/Pi 4 installations have been confirmed
by the developer; wider hardware/performance and actual Supervisor backup/restore
acceptance are ongoing. Home Assistant Container has no app store and needs the
separate standalone distribution, not this Supervisor image. This is not HACS.

1. Open **Settings → Apps → Install app** (older HA: **Settings → Add-ons → Add-on Store**).
2. Choose **⋮ → Repositories**, add the URL below, then close the dialog:

   ```text
   https://github.com/AceClaw519/homepanel-studio-apps
   ```

3. Refresh the store, find **HomePanel Studio v1** and choose **Install**.
   The matching amd64/aarch64 build is selected automatically.
4. Leave **Admin recovery request** (or **Reset HPS login** in newer wording)
   blank. This is a forgotten-login reset trigger, not an initial-password field.
5. Choose **Start**, then **Open Web UI**. Create your HPS administrator login
   in that web page; there is no default login. Enable **Start on boot** if desired.
6. Make a dashboard and save it. Other browsers/tablets use the same HPS host
   address and HPS login. The Android companion is a viewer, not another server.

The first beta opens directly at your HA host on port **3008** (configurable in the app Network options). Create a separate HPS administrator login. The connection to this Home Assistant instance is automatic; no long-lived HA token or HA URL is needed. HPS controls HA through the server-side Supervisor Core proxy. This grants Core administrator-equivalent access; the Supervisor credential never belongs in a browser or dashboard export.

Embedded ingress/sidebar display is **not supported in this beta**. For a tablet or Android viewer, use the direct HPS URL and HPS login; verify that device separately. Keep this HTTP endpoint on your trusted LAN; do not port-forward it to the internet. Remote TLS/proxy deployment is a separate configuration, not tested by ingress.

Start fresh. Import dashboard JSON exported from another installation if desired. Dashboard imports are not whole-installation backups and do not transfer administrator credentials or paid activation. Activate this new installation with your license key in HPS Settings. Original beta testers retain their granted Pro access for major version 1; this is not a temporary trial.

## Choose a different port

The default browser address is `http://<your-HA-host>:3008`. If that port is already in use, open this app's **Configuration → Network**, change the host port for **3000/tcp** to an unused port (for example **3010**), save, and restart. The internal port3000 stays unchanged. **Open Web UI** uses the chosen host port automatically; update any tablet/Android saved address or bookmarks to match.

Changing this mapping does not change your server-side dashboards, login account, installation identity or license. Browser-local preferences and unsaved drafts belong to the old URL/origin, however: save/export outstanding work before changing the port. A port conflict is handled in HA's Network settings; HPS will not silently choose a different port. Use your local HA address for this direct-port beta; HA Cloud remote access does not automatically expose this separate app.

## Persistence, updates and recovery

All installation state lives in the app's persistent `/data`: dashboards, uploaded media, settings, login credentials, installation ID and license state. Restarting or updating the app must retain it. Uninstalling and deleting app data is not an update.

To update, refresh the Apps store, open **HomePanel Studio v1**, read the changelog and use **Update** when offered. No Git checkout, Docker command or reinstall is required. Updating the repository alone does not update the installed app.

Before updating, save open editor changes, create an app backup and retain a downloadable/off-host copy with its backup encryption/recovery information. This app requests **cold** backups: Supervisor stops it while capturing data so SQLite and its WAL are not copied independently while changing. Confirm that the backup completed. Record the app version. Do not rely on only one automatically retained update backup.

To roll back an incompatible change, stop the app and restore the **matching pre-update app backup including its previous version and data** using HA backup restore. Then start it and verify dashboards, backgrounds, login, HA connection and license. Do not substitute only an older container image against newer-format data. There is no promised arbitrary previous-version selector; verify the retained backup offers the expected app version before restoring. Restoring replaces newer changes with the older snapshot. Keep old released images available; contact HPS support if HA cannot retrieve a matching image.

This v1 app does not silently update to a future paid v2 app. Perpetual purchased-major use is distinct from the duration of maintenance/support. Future-major migration requires a separate install, entitlement check, explicit consent and verified backup before modifying content. No future-major migrator ships in this beta. Never share a writable database between old and new instances. Unsupported newer-major/storage markers refuse startup without migrating data; use a matching image/data backup to recover, without buying a new major.

## Forgotten HPS login

The **Options** panel is part of Home Assistant's app configuration. Its **Reset HPS login** field is only for recovering a forgotten HPS username or password. **Leave it blank during normal use.** It is not a place to set or store your new password. The separate **Network** panel controls the browser port, not password recovery.

1. In Home Assistant, open **Settings → Apps → HomePanel Studio v1 → Configuration**. Older Home Assistant versions may call Apps **Add-ons**.
2. In **Reset HPS login** (called **Admin recovery request** in older HPS versions), enter a reset label such as `reset-1`. **Do not enter your password.** Use a different label for each reset, such as `reset-2` next time. Labels may contain 1–64 letters, numbers, dots, hyphens or underscores, with no spaces.
3. Choose **Save**, then **Restart** the HomePanel Studio app—not Home Assistant itself. Saving the field alone does not reset the login.
4. Choose **Open Web UI**. If an HPS page is already open, refresh it. HPS shows the setup screen; create a new administrator username and password there. The welcome/first-time-setup screen is expected after a login reset; it does not mean your dashboards were deleted.
5. Return to the app's **Configuration**, clear **Reset HPS login** and choose **Save**. Leave the field blank until you need another reset.

**What changes:** the old HPS administrator login is removed and all existing HPS sessions are signed out. Sign in again on your other browsers/tablets using the new login.

**What stays:** dashboards, uploaded media, the Home Assistant connection, installation identity and Pro/license state. Your Home Assistant username and password are not changed. No email recovery, SSH, terminal commands, uninstall or data deletion is needed.

**If the reset does not work:** check the app's **Log** for a storage/startup error. The last reset label is recorded before the reset runs, so a restart with the same label will not reset the replacement login again. Clearing the field does not make that last label reusable. After resolving an error, enter a different label, **Save** and **Restart** to retry. Do not uninstall the app or delete its data to recover a password.

## Feedback

Report the HPS version, HAOS/Supervisor/Core versions, host architecture and reproduction steps. Never post access tokens, purchase keys, or unredacted backups. This is an experimental beta, not an invitation-only access-controlled distribution.
