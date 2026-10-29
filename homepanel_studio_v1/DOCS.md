# HomePanel Studio v1 beta

## Install and open

Requires Home Assistant OS with Supervisor and an amd64 or 64-bit ARM (aarch64) host. ARM64 is newly available for real Raspberry Pi hardware testing; native performance and Supervisor backup/restore acceptance remain in progress. 32-bit ARM is not supported. Add `https://github.com/AceClaw519/homepanel-studio-apps` in the Apps store, install **HomePanel Studio v1**, start it, and choose **Open Web UI**.

The first beta opens directly at your HA host on port **3008** (configurable in the app Network options). Create a separate HPS administrator login. The connection to this Home Assistant instance is automatic; no long-lived HA token or HA URL is needed. HPS controls HA through the server-side Supervisor Core proxy. This grants Core administrator-equivalent access; the Supervisor credential never belongs in a browser or dashboard export.

Embedded ingress/sidebar display is **not supported in this beta**. For a tablet or Android viewer, use the direct HPS URL and HPS login; verify that device separately. Keep this HTTP endpoint on your trusted LAN; do not port-forward it to the internet. Remote TLS/proxy deployment is a separate configuration, not tested by ingress.

Start fresh. Import dashboard JSON exported from another installation if desired. Dashboard imports are not whole-installation backups and do not transfer administrator credentials or paid activation. Activate this new installation with your license key in HPS Settings. Original beta testers retain their granted Pro access for major version 1; this is not a temporary trial.

## Choose a different port

The default browser address is `http://<your-HA-host>:3008`. If that port is already in use, open this app's **Configuration → Network**, change the host port for **3000/tcp** to an unused port (for example **3010**), save, and restart. The internal port3000 stays unchanged. **Open Web UI** uses the chosen host port automatically; update any tablet/Android saved address or bookmarks to match.

Changing this mapping does not change your server-side dashboards, login account, installation identity or license. Browser-local preferences and unsaved drafts belong to the old URL/origin, however: save/export outstanding work before changing the port. A port conflict is handled in HA's Network settings; HPS will not silently choose a different port. Use your local HA address for this direct-port beta; HA Cloud remote access does not automatically expose this separate app.

## Persistence, updates and recovery

All installation state lives in the app's persistent `/data`: dashboards, uploaded media, settings, login credentials, installation ID and license state. Restarting or updating the app must retain it. Uninstalling and deleting app data is not an update.

Before updating, create an app backup and retain a downloadable/off-host copy with its backup encryption/recovery information. This app requests **cold** backups: Supervisor stops it while capturing data so SQLite and its WAL are not copied independently while changing. Confirm that the backup completed. Record the app version. Do not rely on only one automatically retained update backup.

To roll back an incompatible change, stop the app and restore the **matching pre-update app backup including its previous version and data** using HA backup restore. Then start it and verify dashboards, backgrounds, login, HA connection and license. Do not substitute only an older container image against newer-format data. There is no promised arbitrary previous-version selector; verify the retained backup offers the expected app version before restoring. Restoring replaces newer changes with the older snapshot. Keep old released images available; contact HPS support if HA cannot retrieve a matching image.

This v1 app does not silently update to a future paid v2 app. Perpetual purchased-major use is distinct from the duration of maintenance/support. Future-major migration requires a separate install, entitlement check, explicit consent and verified backup before modifying content. No future-major migrator ships in this beta. Never share a writable database between old and new instances. Unsupported newer-major/storage markers refuse startup without migrating data; use a matching image/data backup to recover, without buying a new major.

## Forgotten HPS login

In Home Assistant, open this app's **Configuration**, set **Admin recovery request** to a new unique value (letters, digits, hyphens; for example a descriptive label with today's date), save, then restart the app. Open HPS and create a replacement administrator login. Clear the request afterward. This resets HPS login/sessions only; it preserves dashboards, uploads, identity and license. Each value is consumed once before resetting; restarting with the same value does not reset the replacement login. If recovery fails, resolve the storage/startup error and use a new value. No host SSH or terminal is required.

## Feedback

Report the HPS version, HAOS/Supervisor/Core versions, host architecture and reproduction steps. Never post access tokens, purchase keys, or unredacted backups. This is an experimental beta, not an invitation-only access-controlled distribution.
