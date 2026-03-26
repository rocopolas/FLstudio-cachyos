# 🎹 Installation Guide: FL Studio 21 on CachyOS (Wine + Pipewire + WineASIO)

his repository provides a battle-tested workflow for running FL Studio 21 on CachyOS (Arch-based Linux) with near-native performance. 

### Phase 1: Preparing the Ground
It's crucial not to mix FL Studio with your Linux system files. We are going to create its own isolated "Windows" environment.

**1. Install base tools:**
```bash
sudo pacman -S --needed wine winetricks
```

**2. Create a clean Wine Prefix:**
This creates a hidden folder (`.flstudio_wine`) that acts as our FL Studio's C: drive.
```bash
WINEPREFIX=~/.flstudio_wine winecfg
```
*(In the window that opens, make sure the Windows version is set to **Windows 10** and click Apply).*

**3. Install Windows fonts:**
This prevents invisible or glitchy text in the UI.
```bash
WINEPREFIX=~/.flstudio_wine winetricks corefonts
```

---

### Phase 2: Installing the Program and Patch

**1. Run the official installer:**
Navigate in your terminal to the folder where you have the installer and run it:
```bash
WINEPREFIX=~/.flstudio_wine wine installer_name.exe
```
*(Follow the normal installation steps, but **DO NOT** open the program when it finishes).*

**2. Apply the patch if you are a pirate (.reg file):**
* **⚠️ Common Error:** Trying to run the `.reg` directly throws a *ShellExecuteEx* error. Wine doesn't run registry files as if they were `.exe` files.
* **✅ Solution:** Use `regedit` through Wine. 

Go to your patch folder and run:
```bash
WINEPREFIX=~/.flstudio_wine wine regedit FLRegkey.reg
```
*(If the patch includes a `.dll` or `.exe` file, copy it and manually replace it in `~/.flstudio_wine/drive_c/Program Files/Image-Line/FL Studio 21/`).*

---

### Phase 3: Configuring Low-Latency Audio (The Final Boss)

To ensure the audio doesn't cut out and responds instantly, we need to bridge WineASIO with Pipewire.

**1. Grant yourself Realtime permissions:**
Without this, the audio will suffer from clicks and pops.
```bash
sudo usermod -aG realtime $USER
```
*(Important: **Reboot your PC** after this step so the group actually activates).*

**2. Install JACK support for Pipewire:**
* **⚠️ Common Error:** Package conflict between the old `jack` and `pipewire-jack`.
* **✅ Solution:** Force the replacement. Type "y" (yes) to remove the old `jack` when pacman asks.
```bash
sudo pacman -S pipewire-jack lib32-pipewire-jack
```

**3. Install WineASIO:**
In CachyOS/Arch, this package is in the AUR, not in the standard repositories.
```bash
paru -S wineasio
```

---

### Phase 4: Launching FL Studio at the speed of light

So you don't have to type the whole path every time you want to produce, let's create an alias in your terminal. Since you use **fish**, we save it like this:

```bash
alias flstudio='WINEPREFIX=~/.flstudio_wine wine "C:/Program Files/Image-Line/FL Studio 21/FL64.exe"'
funcsave flstudio
```

**Final steps inside the program:**
1. Open your terminal and type `flstudio`.
2. Inside FL Studio, go to **Options > Audio Settings**.
3. In the **Device** drop-down menu, select **WineASIO**.

