# patchark

Patched macOS apps + guides for getting them to run.

macOS flags apps downloaded outside the App Store with a **quarantine** attribute
(`com.apple.quarantine`). Gatekeeper then refuses to open them, often with:

> "App" is damaged and can't be opened. You should move it to the Trash.

The app is usually **not** damaged — it's quarantined. Removing the attribute lets it launch.

---

## Install via Homebrew

Apps are distributed through a private Homebrew tap — quarantine is removed automatically on install.

```bash
brew tap patchark/casks https://github.com/patchark/homebrew-casks
brew trust patchark/casks
brew install --cask patchark/casks/<app>
```

See [homebrew-casks](https://github.com/patchark/homebrew-casks) for setup and available casks.

## Manual fix

```bash
xattr -dr com.apple.quarantine "/Applications/Example.app"
```

Full guide with troubleshooting → [QUARANTINE.md](QUARANTINE.md)

---

## Disclaimer

Provided for educational and personal-use purposes. You are responsible for
complying with the license terms of any software you use. Only remove quarantine
from apps you trust.
