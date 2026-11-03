# HomePanel Studio apps

Install **HomePanel Studio v1** on **Home Assistant OS** through the Apps store
(called Add-ons in older Home Assistant versions). This is an experimental beta
for **x86-64 / amd64** and **64-bit ARM / aarch64**. A 32-bit OS is not supported.
Initial mini PC and Raspberry Pi 4 use has been confirmed by the developer;
broader native hardware, performance and backup/restore acceptance remain ongoing.

## Install

1. Open **Settings → Apps → Install app** in Home Assistant. On older versions,
   open **Settings → Add-ons → Add-on Store**.
2. Open the store's **⋮ → Repositories**, add the URL below, and close the dialog:

   ```text
   https://github.com/AceClaw519/homepanel-studio-apps
   ```

3. Refresh the store if necessary. Select **HomePanel Studio v1**, read its
   **Documentation** tab, then choose **Install**. Home Assistant selects the
   matching architecture automatically; there is no separate Pi download.
4. Leave the login-recovery option blank during normal setup. Choose **Start**,
   then **Open Web UI**, and create your separate HPS administrator login.
   Enable **Start on boot** if desired.
5. The HA connection is automatic. Open HPS from another browser/tablet using
   your HA host's LAN address and port **3008**, or your configured host port.

[Full installation, port configuration, updates, backups and login recovery](homepanel_studio_v1/DOCS.md).

## Which installation is this?

- **Home Assistant OS:** use the app-store steps above. No HACS, terminal,
  application source or manual Home Assistant token is needed.
- **Home Assistant Container / separate Linux or Windows host:** the standalone
  Docker distribution is separate. Use the [standalone Docker guide](docker/README.md)
  and its source-free Linux/Windows installer bundle. The public multi-platform
  image selects amd64 or arm64 automatically. Do not run the Supervisor app image
  as a generic standalone container.
- **Viewing devices:** browsers and the Android companion connect to an existing
  HPS host. Installing a viewer does not install the dashboard server.

## Updates and data

Back up the app in Home Assistant before updating and keep a downloadable copy
with its recovery information. Refresh the app store, read the changelog, and use
**Update**. Do not uninstall or delete app data to update or recover a login.
The default browser connection is direct LAN access, not embedded HA ingress;
Home Assistant Cloud does not automatically expose it. Do not port-forward HPS.

Only installer metadata and documentation live here; application source remains
private. Free features do not require Pro. Pro requires a valid license activated
in HPS Settings. V1 purchases cover that purchased major version. A future paid
major is a separate app/update stream and an explicit upgrade choice, not a silent
v1 update. A canvas export is not a full installation backup.
