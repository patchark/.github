# Removing the Quarantine Attribute (macOS)

When you download an app outside the Mac App Store, macOS tags it with the
`com.apple.quarantine` extended attribute. Gatekeeper uses this to block
unsigned / unnotarized apps, showing errors like:

- *"App" is damaged and can't be opened. You should move it to the Trash.*
- *"App" can't be opened because Apple cannot check it for malicious software.*
- *"App" cannot be opened because the developer cannot be verified.*

Removing the attribute tells macOS to stop quarantining the app.

> ⚠️ Only do this for apps you trust. Quarantine + Gatekeeper exist to protect you.

---

## 1. Inspect the attribute

Check whether an app is quarantined:

```bash
xattr "/Applications/Example.app"
```

If you see `com.apple.quarantine` in the output, it's quarantined.

See the full value:

```bash
xattr -p com.apple.quarantine "/Applications/Example.app"
```

---

## 2. Remove quarantine

### Single app (recursive)

```bash
xattr -dr com.apple.quarantine "/Applications/Example.app"
```

- `-d` deletes the attribute
- `-r` applies recursively to everything inside the `.app` bundle

### Remove from a `.dmg` mount or a folder

```bash
xattr -dr com.apple.quarantine "/path/to/folder"
```

### Clear ALL extended attributes (nuclear option)

```bash
xattr -cr "/Applications/Example.app"
```

`-c` clears every extended attribute, not just quarantine. Use only if the
targeted delete above doesn't work.

---

## 3. Verify

```bash
xattr "/Applications/Example.app"
```

`com.apple.quarantine` should be gone. Launch the app normally.

---

## Troubleshooting

### "Operation not permitted"

Grant your terminal **Full Disk Access**:

1. System Settings → Privacy & Security → Full Disk Access
2. Enable your terminal (Terminal.app, iTerm, Ghostty, etc.)
3. Restart the terminal and retry.

Or run with elevated privileges:

```bash
sudo xattr -dr com.apple.quarantine "/Applications/Example.app"
```

### Still says "damaged" after removing quarantine

The app may be **unsigned** and blocked by Gatekeeper independently. Re-sign it
with an ad-hoc signature:

```bash
sudo codesign --force --deep --sign - "/Applications/Example.app"
```

Then re-remove quarantine and try again.

### Allow once via Gatekeeper

If you'd rather not touch attributes, you can approve a blocked app after a
failed launch:

System Settings → Privacy & Security → scroll to *"App was blocked"* →
**Open Anyway**.

### Disable Gatekeeper globally (not recommended)

```bash
sudo spctl --master-disable   # turns off Gatekeeper entirely
sudo spctl --master-enable    # re-enable when done
```

This lowers system security across the board. Prefer per-app removal above.

---

## One-liner for a freshly mounted DMG

After dragging an app to `/Applications`:

```bash
APP="/Applications/Example.app"; xattr -dr com.apple.quarantine "$APP" && open "$APP"
```
