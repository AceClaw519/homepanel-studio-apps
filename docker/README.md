# HomePanel Studio — Docker installation and recovery

**Standalone Docker early access — version 1.0.4.** Use the public, source-free
[installer bundle and checksum](https://github.com/AceClaw519/homepanel-studio-apps/releases/tag/docker-v1.0.4).
The bundle pins the public multi-platform image
`ghcr.io/aceclaw519/homepanel-studio-docker-v1@sha256:ebaebe52b90cff3a2cc9324ca84bf8fe1af7b753e5c8a71ecda9d5680ff8a3f4`.
No GitHub login, registry credentials, application source, Git or Node.js is needed.
Docker selects Linux amd64 or arm64 automatically. The bundle includes Linux and native Windows PowerShell maintenance helpers.

Home Assistant OS users can instead use the app-store method; this standalone
image is for a separate Docker host, including Home Assistant Container users.
Windows uses Docker Desktop with Linux containers, not a Windows-container image.
Native ARM/NAS performance and host-specific networking/reboot behavior vary;
architecture lifecycle checks under emulation are not hardware certification.

## Requirements and scope

This host-side helper targets Linux with Docker Engine, Docker Compose supporting
`up --wait --wait-timeout`, POSIX `sh`, `tar` and standard shell utilities. It does
not require Git, Node, npm, or the application source. Docker must already work
for your user; Docker installation and host boot configuration are separate.
Version 1.0.4 provides one public, versioned Docker index for Linux amd64 and arm64;
Docker selects the host platform automatically. ARM64 image/module/upgrade tests use emulation;
native ARM/NAS performance and native Windows/Docker Desktop acceptance remain
separate device-specific checks.
Use the Windows section below instead of translating POSIX helper commands.

HPS is intended for your trusted local network, not direct internet exposure.
No router port forwarding is needed. Tablets connect to the host's LAN address.
Docker's restart policy can restart HPS after Docker starts; it cannot ensure
that Docker, WSL or your host starts. Test your actual host after a reboot.

## Install

1. Download `hcp-1.0.4-install.tar.gz` and its adjacent `.sha256` file from the
   release above into a permanent local directory. With Docker already running,
   verify and extract:

   ```sh
   sha256sum -c hcp-1.0.4-install.tar.gz.sha256
   tar -xzf hcp-1.0.4-install.tar.gz
   cd hcp-1.0.4-install
   cp config.env.example .env
   ```

   Stop if the checksum does not match. Keep the extracted folder, including
   hidden `.hcp` state after installation.
2. Change into the extracted bundle directory containing `hcp.sh`, `hcp.ps1`
   and `compose.yaml`. Edit `.env` as needed, keeping the supplied immutable
   `HCP_IMAGE` and public activation selector unchanged. Choose an unused `HCP_PORT` (default **3004**).
3. Run `sh hcp.sh install` from a terminal. This pulls the image, checks its product
   labels, pins its local image ID, refuses existing project data, then waits for
   Docker health before reporting success.
4. Open `http://HOST-LAN-IP:3004` (use your chosen port), create your own admin
   password, and connect Home Assistant now or later in HPS Settings using its reachable
   URL and a long-lived access token from your HA profile. Unlike the Supervisor
   app, standalone Docker does not connect automatically. There is no default password.

`HCP_PROJECT_NAME` determines the data-volume namespace. Keep it stable for the
life of the installation. Different installations require different project names
and ports. Data must be on local SQLite-suitable storage, not a multi-writer share.
The host port maps to the fixed internal port 3000; a conflict is an error, not an
automatic port change. Choose another host port in `.env` and run `start` if the
initial install already pinned its image. Do not reinstall or delete the volume.
The helper records the resolved project name and rejects later accidental namespace
changes. Use a fresh bundle directory when intentionally creating another install.

`HCP_BIND_ADDRESS=0.0.0.0` allows LAN access; `127.0.0.1` restricts access to the
host. Firewall configuration depends on the host. Inside Docker, `localhost`
means the HPS container, not your Home Assistant server. Use HA's reachable URL.
For optional Pro access, enter your license key in **HPS Settings → License**.
Free features do not require a key. The supplied activation settings select public
trust already pinned in this release; no provider token or signing key belongs
in `.env`. Do not change them to connect a different licensing service.

For HTTPS reverse proxies, set `HCP_COOKIE_SECURE=true`. Leave
`HCP_LOGIN_PROXY_HEADER` blank unless you have deliberately configured a trusted
proxy that overwrites the header and prevents direct origin access.

Offline delivery: load a publisher-provided image archive with `docker load`,
then use `sh hcp.sh install --local`. No command downloads fonts at app startup.
An offline installation still needs a reachable HA server for live HA features.

## Daily maintenance

```sh
sh hcp.sh status
sh hcp.sh logs
sh hcp.sh backup
sh hcp.sh stop
sh hcp.sh start
```

A backup briefly stops writes and resumes a previously running installation even
if archive creation fails. A stopped installation stays stopped. Completed
checkpoints appear under `backups/hcp-TIMESTAMP-PID/` and contain `complete`,
`data.tar.gz`, exact `image.txt`, version metadata, and copies of the configuration,
maintenance helper and Compose file. A `.partial` archive is NOT a backup.

The data archive includes SQLite, uploads, HA credentials, admin/sessions,
installation identity and local license state. It is not encrypted. Keep the
whole checkpoint private and copy it off the Docker host. A canvas JSON export
is not a substitute: it does not include all uploaded media or installation state.
For offline recovery also save the retained image with `docker image save`; an
image ID by itself is not a portable download reference. Keep the published
repository digest/release bundle too. Do not prune retained images prematurely.

## Update

Read the release notes first, save current editor changes, then run:

```sh
sh hcp.sh update ghcr.io/aceclaw519/homepanel-studio-docker-v1@sha256:ebaebe52b90cff3a2cc9324ca84bf8fe1af7b753e5c8a71ecda9d5680ff8a3f4
```

This is the exact reference for **1.0.4**. If you already run that image, the
helper reports no change. For a later release, use its published immutable
reference instead; do not edit `.env` to force an update. The helper downloads before downtime,
checks HPS product major 1, pins the exact old image, creates a stopped-data
checkpoint and retains a recovery image tag before switching. It only reports
success after container health passes. Preserve `.env`, `.hcp`, project name and
volume. The persistent pin overrides `.env`'s original image: editing `HCP_IMAGE`
alone does not update an installed HPS. Use the helper, not bare `compose up`.
Loaded/offline images can be updated with `update IMAGE --local`.

A health check proves app HTTP liveness, not that HA connectivity, every widget,
or your restored media works. After updating verify login, canvases, media, HA
controls and license state. Minor updates stay within the purchased product
major. This helper rejects other product majors; paid-major upgrades require a
separate explicit process. It also rejects version downgrades. Release labels use
`1.MINOR.PATCH`, optionally suffixed `-alpha.N`, `-beta.N` or `-rc.N`; development
images without release metadata are not accepted by this customer helper.
There is no in-app updater or automatic update check
in this milestone, and the app container has no Docker socket access.

If update startup fails, the helper stops HPS and leaves `.hcp/pending-update`
pointing to its checkpoint. It does NOT automatically start old code on possibly
migrated data. Interruptions likewise retain the checkpoint/pin; inspect container
state and `pending-update` before proceeding. A power loss can leave `.hcp/lock`;
verify no maintenance process is running before removing that empty directory.
Do not bypass an incomplete update with bare Compose commands.

## Recovery / rollback into a separate project

Never downgrade code alone against data a new version may have migrated.

1. Stop the source installation (`sh hcp.sh stop`). Do not run source and recovered
   installations concurrently: recovery preserves the same license identity.
2. Extract a fresh copy of the checkpoint's matching release bundle in a DIFFERENT
   directory. Alternatively create a directory containing its saved `compose.yaml`,
   `hcp.sh`, and `config.env` copied to `.env`.
3. Set a NEW `HCP_PROJECT_NAME` and unused port in the new `.env`. Do not copy the
   old `.hcp` directory. Keep the original directory and volume intact.
4. Ensure the checkpoint's exact old image is available (retained locally or loaded
   from your image archive). Use the `image.txt` value, not the failed new version:

   ```sh
   sh hcp.sh restore /ABSOLUTE/PATH/TO/CHECKPOINT/data.tar.gz sha256:OLD_IMAGE_ID --local
   sh hcp.sh start
   ```

5. Verify login, canvases, uploads, HA connection and Pro entitlement, then change
   tablet URLs if the port changed. Only after acceptance consider retiring the
   failed installation. The helper does not delete either volume.

Restore accepts only your own trusted backups and refuses any existing container
or nonempty target data. A partial/failed restore is left for inspection, not
silently retried over partially written data. A `pending-restore` marker blocks
startup after failed/interrupted extraction. Use another fresh recovery project.
Restoring loses edits made AFTER that checkpoint; the original volume is retained
so those later changes can be investigated separately.

For lost admin passwords, use the release's host-access admin recovery procedure;
it resets admin/sessions, not canvases. If using the pinned helper configuration:

```sh
docker compose --env-file .env --env-file .hcp/image.env -f compose.yaml exec hcp npm run auth:reset
```

Immediately reopen HPS and create a new admin. Do not expose password reset as an
unauthenticated web endpoint. Never use `docker compose down -v` to troubleshoot:
that deletes the installation's persistent data.

## Windows / Docker Desktop

Use a currently supported Windows version with Docker Desktop, its WSL2 backend,
and **Linux containers**. The HPS image is the same Linux image used above, not a
Windows-container build. No manually managed Ubuntu, WSL shell, Git Bash, Node or
application source is required to run this PowerShell helper. Do not install a
second Docker engine into WSL as part of these customer instructions.

1. Start Docker Desktop, complete first-run setup and wait for **Engine running**.
2. Verify the published SHA256 with `Get-FileHash .\hcp-1.0.4-install.tar.gz -Algorithm SHA256`
   and compare it with the publisher's checksum. Extract the archive (for example
   `tar -xzf .\hcp-1.0.4-install.tar.gz`) into a permanent Windows directory. Copy
   `config.env.example` to `.env`, choose a stable project name and available port.
3. Open PowerShell in that directory and run:

   ```powershell
   powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\hcp.ps1 install
   ```

   This execution-policy setting applies to that process only, not machine policy.
   Use only a bundle you trust. The helper discovers Docker Desktop's CLI on PATH
   or in its per-user/all-users installation directories.
4. Open `http://localhost:3004` (or your configured port), then create your admin.
   Use the Windows host's LAN address on tablets, subject to your local firewall.

PowerShell commands below use `powershell.exe -NoProfile -ExecutionPolicy Bypass
-File .\hcp.ps1` followed by the operation:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\hcp.ps1 status
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\hcp.ps1 backup
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\hcp.ps1 update -Image ghcr.io/aceclaw519/homepanel-studio-docker-v1@sha256:ebaebe52b90cff3a2cc9324ca84bf8fe1af7b753e5c8a71ecda9d5680ff8a3f4
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\hcp.ps1 stop
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\hcp.ps1 start
```

For already-loaded/offline images, append `-LocalImage` to install/update/restore.
The default explicit context is `desktop-linux`; `-DockerContext NAME` selects a
different existing context per command without changing your global CLI context.
It must reach the Desktop Linux engine. Missing/stopped engines, Windows-container
mode, a changed installation project or a different pinned daemon are errors.
Do not reuse one installation directory between separate Docker engines.

The same checkpoint and recovery rules apply. Native binary streams preserve the
archive bytes even under Windows PowerShell 5.1; do not replace this with a text
pipeline such as `docker ... > backup.tar.gz`. Checkpoint directories disable
inherited permissions and grant access to the current Windows account, SYSTEM and
local Administrators. Keep them private and copy backups off the host.

For recovery, stop the source and use a fresh bundle directory, NEW project name
and available port. Use the checkpoint's exact old image (load its saved image
archive if needed), then:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\hcp.ps1 restore -Archive "C:\PRIVATE\CHECKPOINT\data.tar.gz" -Image sha256:OLD_IMAGE_ID -LocalImage
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\hcp.ps1 start
```

Only restore your own trusted archive. Restore remains stopped until `start`;
incomplete extraction blocks startup. Verify your session/login, canvases, media,
HA settings and license before retiring the original. For recovery without the
original release bundle, copy the checkpoint's saved `hcp.ps1`, `compose.yaml` and
`config.env` (renamed `.env`) into a new directory and change project/port.

For admin reset, use the same pinned Compose recovery command shown above in
PowerShell (use your Docker Desktop CLI/context); then immediately recreate admin.
No helper changes firewall rules, portproxy entries, Desktop startup settings or
Docker contexts. Desktop/Windows reboot and physical-tablet LAN acceptance remain
separate checks. Do not assume Desktop provides a pre-sign-in Windows service.
