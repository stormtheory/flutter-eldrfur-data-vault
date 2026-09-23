# Eldrfur Vault

An offline-first password and data vault. Your vault is encrypted on your
device with your master password, and it stays there unless you tell it to go
somewhere else.

Built by **Azimos Labs, LLC**.

**Downloads: [Latest release](../../releases/latest)**

---

## What this app does with your data

Worth reading before you install anything that holds your passwords.

- **No telemetry. None.** No usage tracking, no crash reporting, no analytics,
  no "anonymous statistics". Not now, and not added quietly in an update later.
- **No account.** There is nothing to sign up for. We do not have a server that
  knows you exist.
- **The app does not contact us, ever.** It has no address for us to be at.
- **Your vault is encrypted on your device**, with AES-256-GCM and an Argon2id
  key derived from your master password. We cannot read it. Neither can anyone
  who takes the file without the password.
- **Cloud sync is entirely opt-in and you pick the provider.** Proton Drive,
  Nextcloud, or any folder your own sync client already watches. Off by
  default. When it is on, your vault is encrypted *before* it leaves the
  machine, so your provider stores ciphertext.
- **There is an optional update check.** It is off unless you turn it on, it
  only ever tells you a version number, and it never downloads or installs
  anything by itself.

If any of that ever stops being true, it will be in the release notes in plain
words, not buried in a policy.

---

## Which file do I download?

Everything is on the [latest release](../../releases/latest) page, under
**Assets**.

| You have | Download | Notes |
|---|---|---|
| Windows 10 or 11, 64 bit | `EldrfurVault-<version>-windows-x64-setup.exe` | Normal installer. Recommended. |
| Windows, no installer wanted | `eldrfur_data_vault-<version>-windows-x64.zip` | Portable. Unzip and run. |
| Debian, Ubuntu, Mint, Pop!_OS | `eldrfur-vault_<version>_amd64.deb` | Normal package. |

Windows on ARM works through x64 emulation. A 32 bit machine will not run this.

---

## Windows: install

### With the installer, recommended

1. Download `EldrfurVault-<version>-windows-x64-setup.exe`.
2. Double click it.
3. **Windows will show a blue "Windows protected your PC" box.** This is
   expected, and the next section explains exactly why. Click **More info**,
   then **Run anyway**.
4. Follow the prompts. Finish.

It installs to your own user profile, at
`%LOCALAPPDATA%\Programs\Eldrfur Vault`, and **never asks for Administrator**.
That is deliberate. A password vault has no business holding an admin token,
and an install inside your own profile cannot be quietly modified by another
account on the machine.

### About that SmartScreen warning

The installer is **not code signed**. A code signing certificate has to be
bought from a certificate authority, and until we have one, Windows has no
publisher name to show you and defaults to warning. The warning is Windows
telling you the truth: it does not know who made this file.

We would rather say that plainly than have you wonder. If you want to verify
you got what we published, check the SHA-256 against the checksum listed on the
release page:

```powershell
Get-FileHash .\EldrfurVault-<version>-windows-x64-setup.exe -Algorithm SHA256
```

### Portable zip, no install

1. Download `eldrfur_data_vault-<version>-windows-x64.zip`.
2. Right click the file, **Properties**, tick **Unblock** at the bottom, **OK**.
   Windows marks downloaded archives and this saves you a warning per file.
3. Extract the whole folder somewhere you can write, for example
   `C:\Users\<you>\Apps\EldrfurVault`.
4. Run `eldrfur_data_vault.exe`.

**Extract the entire archive, not just the .exe.** The app needs the `data`
folder and the DLLs beside it. Running the exe on its own will not work.

### Where Windows keeps your data

```
%APPDATA%\com.azimoslabs\eldrfur_data_vault
```

Paste that into File Explorer's address bar to open it. Your vault database,
your settings and any cached attachments live there. **Back this folder up**,
or set up sync inside the app.

### Uninstalling on Windows

Settings, then Apps, then Installed apps, find **Eldrfur Vault**, Uninstall.
Or run the portable folder's own uninstaller if you used the installer.

The uninstaller **asks** before deleting your vault data, and the default
answer is No. Say No and your vault stays on disk, so reinstalling later picks
up where you left off. Say Yes only if you have a backup elsewhere. Deleting it
cannot be undone, and we do not have a copy.

---

## Linux: install

### Debian, Ubuntu, Mint, Pop!_OS

```bash
sudo apt install ./eldrfur-vault_<version>_amd64.deb
```

Use `apt install ./file.deb` rather than `dpkg -i`, because apt pulls in the
dependencies and dpkg does not. If you have already used `dpkg -i` and it
complained about dependencies, this fixes it:

```bash
sudo apt --fix-broken install
```

Then launch **Eldrfur Vault** from your applications menu, or run:

```bash
eldrfur-vault
```

### What it installs, and what it needs

| Path | What |
|---|---|
| `/usr/bin/eldrfur-vault` | Launcher |
| `/usr/lib/eldrfur-data-vault/` | The application and its libraries |
| `/usr/share/applications/` | Desktop menu entry |
| `/usr/share/icons/hicolor/` | Icons |

Dependencies are declared in the package, so apt handles them. They are the
usual desktop libraries (GTK 3, libsecret, SQLite) and nothing unusual.

**You do not need to install Go, Rust, or Flutter.** Those are build-time
tools. Everything the app needs at runtime ships inside the package or comes
from the dependencies apt installs.

### Where Linux keeps your data

```
~/.local/share/com.azimoslabs.eldrfur_data_vault/
```

**Back this folder up**, or set up sync inside the app.

### Uninstalling on Linux

```bash
sudo apt remove eldrfur-vault
```

This removes the application. It does **not** touch
`~/.local/share/com.azimoslabs.eldrfur_data_vault/`, so your vault survives.
Package managers do not delete files in your home directory, and we would not
want them to. If you want the vault gone as well, delete that folder yourself:

```bash
rm -rf ~/.local/share/com.azimoslabs.eldrfur_data_vault/
```

That is permanent and there is no copy anywhere else unless you made one.

---

## Verify what you downloaded

Every release lists SHA-256 checksums. Checking them confirms the file arrived
intact and is the one we published.

**Windows, PowerShell:**
```powershell
Get-FileHash .\<file> -Algorithm SHA256
```

**Linux:**
```bash
sha256sum <file>
```

Compare the output against the release page. If it does not match, do not run
it, and please open an issue.

---

## First run

1. Launch the app.
2. Create a vault and set a master password.
3. **Write that password down and put it somewhere safe.**

There is no recovery. No reset link, no support account, no back door. The
master password is what the encryption key is derived from, so without it the
vault is a file of random bytes to everyone including us. That is the point,
and it is also the risk. Please take it seriously.

Optional, from Settings: a PIN or biometric unlock for day to day use, screen
privacy, clipboard auto-clear, and cloud sync if you want it.

---

## Updating

Download the new release and install it over the top.

- **Windows:** run the new installer. It upgrades in place and leaves your
  vault alone.
- **Linux:** `sudo apt install ./eldrfur-vault_<newversion>_amd64.deb`

Your vault data is stored outside the application folder on both platforms
specifically so that updating cannot disturb it. Back it up before a major
version change anyway. It costs nothing to be careful with the one file that
has no spare.

---

## Trouble

**Windows: "Windows protected your PC"**
Expected, the app is unsigned. More info, then Run anyway. See above.

**Windows: the portable version will not start**
The archive was not fully extracted. The exe needs the `data` folder and the
DLLs next to it.

**Windows: it starts and immediately closes**
You are probably on a 32 bit Windows or an unsupported version. This needs
Windows 10 build 17763 or newer, 64 bit.

**Linux: "dependency problems prevent configuration"**
You used `dpkg -i`. Run `sudo apt --fix-broken install`.

**Linux: installed but not in the menu**
Log out and back in, or run `eldrfur-vault` from a terminal. Some desktop
environments cache the menu.

**Linux: it will not start from the terminal either**
Run `eldrfur-vault` and include the exact error text in an issue.

**I forgot my master password**
There is nothing anyone can do. Not us, not anyone. See "First run" above.

---

## Questions, bugs, feature requests

Open an issue on this repository. Please include your OS and version, the app
version, and what you expected to happen.

**Please do not paste your vault file, any part of it, or any password into an
issue.** Issues are public.

---

## What Azimos Labs is for

- **Freedom.** You decide what runs and what connects. You pick where your data
  goes.
- **Privacy.** Treated as a right, not a feature, in the spirit of the Fourth
  Amendment. Your data is your business until you say otherwise.
- **Zero telemetry.** No usage tracking, no phone home, no exceptions. Not now,
  not added quietly later.
- **Security by default**, not bolted on afterwards, and improved whenever
  there is a real reason to.
