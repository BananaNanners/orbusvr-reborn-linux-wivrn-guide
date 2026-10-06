# OrbusVR: Reborn Community Edition on Linux with WiVRn

A tested setup for running **OrbusVR: Reborn Community Edition** on Linux using **Proton Experimental + OpenComposite + WiVRn**.

This guide was tested on **CachyOS (Arch Linux-based)** with an NVIDIA GPU, but the same approach should be useful on other modern Linux distributions using Steam/Proton and WiVRn.

There is also a **community-reported Steam Frame standalone setup** further down the page. That setup is separate from the WiVRn PC instructions.

## ✅ Working result

This setup successfully provides:

- VR world rendering
- Head tracking
- Motion controllers and buttons
- Game audio
- Character creation and selection
- Community server connectivity
- Normal world loading

### Known-good stack

```text
OrbusVR: Reborn
        ↓
Proton Experimental
        ↓
OpenComposite
        ↓
OpenXR
        ↓
WiVRn
```

> **Important:** On the tested system, xrizer could launch the menus and connect to the server, but entering the world resulted in a black screen. Explicitly forcing OpenComposite fixed the problem.

---

## 1. Download and extract OrbusVR

Download the Community Edition PC build:

```text
OrbusVR-Reborn-Community-PC.zip
```

Create an application directory:

```bash
mkdir -p ~/Applications/OrbusVR-Reborn-Community
```

Extract the archive:

```bash
7z x ~/Downloads/OrbusVR-Reborn-Community-PC.zip \
  -o"$HOME/Applications/OrbusVR-Reborn-Community"
```

The executable should be located at something similar to:

```text
~/Applications/OrbusVR-Reborn-Community/OrbusVR-Reborn-Community-PC/vrclient.exe
```

---

## 2. Add OrbusVR to Steam

In Steam:

**Games → Add a Non-Steam Game to My Library → Browse**

Select:

```text
~/Applications/OrbusVR-Reborn-Community/OrbusVR-Reborn-Community-PC/vrclient.exe
```

Then open:

**OrbusVR → Properties → Shortcut**

Set **Target** to the full path to `vrclient.exe`.

Set **Start In** to the directory containing `vrclient.exe`, for example:

```text
/home/YOUR_USER/Applications/OrbusVR-Reborn-Community/OrbusVR-Reborn-Community-PC/
```

---

## 3. Use Proton Experimental

Open:

**Steam → OrbusVR → Properties → Compatibility**

Enable:

**Force the use of a specific Steam Play compatibility tool**

Choose:

```text
Proton Experimental
```

---

## 4. Install OpenComposite

On Arch/CachyOS using an AUR helper:

```bash
paru -S opencomposite-git
```

On the tested system, the package installed its OpenVR client here:

```text
/opt/opencomposite/bin/linux64/vrclient.so
```

Verify it exists:

```bash
ls -lh /opt/opencomposite/bin/linux64/vrclient.so
```

---

## 5. Copy OpenComposite into your home directory

For this setup, placing OpenComposite inside the home directory made the Steam/Pressure Vessel override reliable:

```bash
mkdir -p ~/Applications
cp -a /opt/opencomposite ~/Applications/OpenComposite
```

Verify:

```bash
ls -lh ~/Applications/OpenComposite/bin/linux64/vrclient.so
```

The runtime path used below is:

```text
/home/YOUR_USER/Applications/OpenComposite
```

---

## 6. Configure WiVRn

Start WiVRn and connect your headset normally.

You do **not** need to launch SteamVR for this configuration.

OrbusVR will use OpenComposite as its OpenVR → OpenXR compatibility layer, and WiVRn will provide the OpenXR runtime.

---

## 7. Set the Steam launch options

Open:

**Steam → OrbusVR → Properties → General → Launch Options**

Use:

```bash
PRESSURE_VESSEL_IMPORT_OPENXR_1_RUNTIMES=1 VR_OVERRIDE=/home/YOUR_USER/Applications/OpenComposite %command%
```

Replace `YOUR_USER` with your Linux username.

### What these variables do

`PRESSURE_VESSEL_IMPORT_OPENXR_1_RUNTIMES=1`

Makes the host OpenXR runtime available inside Steam's Pressure Vessel container.

`VR_OVERRIDE=.../OpenComposite`

Explicitly tells Proton/OpenVR to use the OpenComposite runtime instead of another OpenVR compatibility implementation.

---

## 8. Launch the game

1. Start WiVRn.
2. Connect the headset.
3. Do **not** manually start SteamVR.
4. Launch OrbusVR from Steam.
5. Select your Community Edition server.
6. Create/select your character and enter the world.

### Community server used during testing

Reborn was tested with:

```text
Orbus.GarauntGames.com
```

This is a community-hosted server, not part of this guide/repository. Community server availability and addresses may change over time.

---

## 9. Final known-good configuration

```text
Game:
OrbusVR: Reborn Community Edition

VR runtime:
WiVRn

OpenVR compatibility:
OpenComposite

OpenComposite path:
/home/YOUR_USER/Applications/OpenComposite

Compatibility tool:
Proton Experimental

Steam launch options:
PRESSURE_VESSEL_IMPORT_OPENXR_1_RUNTIMES=1 VR_OVERRIDE=/home/YOUR_USER/Applications/OpenComposite %command%

SteamVR:
Not used

xrizer:
Not used for OrbusVR on the tested system
```

---

## Steam Frame standalone (community reported)

A Steam Frame tester reported that the **OrbusVR Reborn Community PC client works directly on the headset**.

> **Status:** Community reported. This has not yet been personally verified by the repo author.

### Simple setup

From **Steam Frame Desktop Mode**:

1. Download and extract `OrbusVR-Reborn-Community-PC.zip`.
2. Open Steam.
3. Choose **Games → Add a Non-Steam Game to My Library**.
4. Select `vrclient.exe`.
5. Open **Properties → Compatibility**.
6. Enable **Force the use of a specific Steam Play compatibility tool**.
7. Select **Proton Experimental**.
8. Launch OrbusVR.

That's it. SteamOS handles the Windows compatibility work automatically.

### Reported result

- ✅ Runs standalone on Steam Frame
- ✅ No gaming PC required
- ✅ No PC streaming required
- ✅ Proton Experimental works
- ⚠️ **Low graphics settings** are currently recommended

### Do not copy the WiVRn launch options to Steam Frame

The Steam Frame setup does **not** need the PC-specific launch option from earlier in this guide.

Do not add:

```bash
PRESSURE_VESSEL_IMPORT_OPENXR_1_RUNTIMES=1 VR_OVERRIDE=/home/YOUR_USER/Applications/OpenComposite %command%
```

For the reported Steam Frame setup, leave **Launch Options empty** unless you are troubleshooting a separate problem.

You also do not need to set up WiVRn or OpenComposite for this standalone method.

> **WiVRn on Steam Frame?** Not needed — the VR PC is literally on your face. 😆

### About FEX

You may see Steam Frame discussions mention **FEX**. You do not need to install or select it separately.

Just choose **Proton Experimental** in Steam. SteamOS handles the rest in the background.

---

## Black screen after selecting a character

This was the main issue encountered during testing.

### Symptoms

The following worked normally:

- Game startup
- VR menus
- Server connection
- Character creation
- Character selection
- Controller input
- Audio

But after selecting a character, both the headset and desktop mirror became black while the game continued running.

The Orbus client was still receiving normal world/server traffic and world audio could sometimes be heard.

xrizer also reached a healthy OpenXR state:

```text
READY
SYNCHRONIZED
VISIBLE
FOCUSED
```

So the headset/OpenXR session itself was alive.

### WineD3D test

Testing WineD3D with xrizer resulted in:

```text
Unsupported texture type: DirectX
```

So this was not a usable solution.

### Fix

Force OpenComposite explicitly with:

```bash
VR_OVERRIDE=/home/YOUR_USER/Applications/OpenComposite
```

The full working launch option is:

```bash
PRESSURE_VESSEL_IMPORT_OPENXR_1_RUNTIMES=1 VR_OVERRIDE=/home/YOUR_USER/Applications/OpenComposite %command%
```

After forcing OpenComposite, the world rendered normally and controllers/audio continued working.

---

## Things that were NOT required for the WiVRn PC setup

The working configuration does **not** require:

- SteamVR
- `PROTON_USE_WINED3D=1`
- Additional DLL overrides
- Special DXVK variables
- Special NVIDIA environment variables

Keep the setup simple unless you have a different problem.

---

## Back up your Community Edition identity

Community Edition characters are tied to the client's `identity.dat` file.

For a Steam Non-Steam shortcut under Proton, it will normally be somewhere inside that shortcut's compatdata prefix, for example:

```text
~/.local/share/Steam/steamapps/compatdata/<APPID>/pfx/drive_c/users/steamuser/AppData/LocalLow/Orbus Online, LLC/OrbusVR/identity.dat
```

Find it with:

```bash
find ~/.local/share/Steam/steamapps/compatdata \
  -path '*/AppData/LocalLow/Orbus Online, LLC/OrbusVR/identity.dat' \
  -print
```

Then copy it somewhere safe:

```bash
mkdir -p ~/Documents/OrbusVR-Backups
cp "/path/to/identity.dat" ~/Documents/OrbusVR-Backups/reborn-identity.dat
```

Keep this file backed up before recreating or deleting a Proton prefix.

---

## Reborn vs. Preborn / Classic

The Reborn and Preborn/Classic Community Editions use separate game clients.

Do not assume a Preborn server can be used correctly from the Reborn client simply by changing the port. Use the appropriate Community Edition client for the version you want to play.

---

## Troubleshooting checklist

If the game opens but does not enter the world correctly, verify:

```bash
ls -lh ~/Applications/OpenComposite/bin/linux64/vrclient.so
```

Confirm Steam is using:

```text
Proton Experimental
```

Confirm your launch options contain:

```bash
PRESSURE_VESSEL_IMPORT_OPENXR_1_RUNTIMES=1
```

and:

```bash
VR_OVERRIDE=/home/YOUR_USER/Applications/OpenComposite
```

Also make sure SteamVR is not being launched unnecessarily.

---

## Tested result: CachyOS + WiVRn

With the WiVRn PC configuration above:

- ✅ OrbusVR launches in VR
- ✅ The world renders normally
- ✅ Controllers work
- ✅ Head tracking works
- ✅ Audio works
- ✅ Community server connection works
- ✅ Characters can enter the world

---

## Notes

This is a community Linux compatibility guide based on one tested setup. It is not an official OrbusVR, WiVRn, Valve, or OpenComposite guide.

If you test this on another Linux distribution, GPU, headset, or Proton version, reports and improvements are welcome.
