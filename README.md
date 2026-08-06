# Lily58 ZMK configuration

This repository is configured for the Typeractive Lily58 Wireless Choc build
with two nice!nano v2 controllers, two nice!views, and wireless communication
between the halves. The left half is the central half. The initial setup uses a
stock-style QWERTY keymap and leaves the optional encoder off.

## What is included

- Reproducible builds pinned to the stable ZMK `v0.3` line.
- GitHub Actions builds for nice!view-equipped `lily58_left`, `lily58_right`,
  and `settings_reset` firmware.
- ZMK Studio support over USB and Bluetooth on the left/central half.
- A three-layer starter keymap, five Bluetooth profiles, and three additional
  layers reserved for ZMK Studio.
- Explicit USB/Bluetooth output controls and an optional EC11 encoder toggle.

## Build the firmware

1. Commit these files and push them to GitHub.
2. Open the repository's **Actions** tab and select **Build ZMK firmware**.
   A push starts the workflow automatically; **Run workflow** can also start it
   manually.
3. After the workflow succeeds, download and extract its firmware artifact.
   It contains separate UF2 images for the left half, right half, and settings
   reset.

The controller and shield combinations are defined in [`build.yaml`](build.yaml)
and match Typeractive's Lily58-with-nice!view configuration.

## First flash

1. Turn both keyboard halves off.
2. Connect the right half by USB and double-tap its reset button to mount the
   controller's bootloader drive.
3. Copy the right-half UF2 to that drive, then disconnect it.
4. Repeat for the left half using the left-half UF2. Leave the left half
   connected if you want to test USB or use ZMK Studio.
5. Power both halves and reset them at roughly the same time. They should pair
   with each other automatically. Pair the host with **Lily58** over Bluetooth.

The UF2 filenames include the artifact names `lily58_left`, `lily58_right`, and
`settings_reset`, so verify the side before copying a file.

> Never plug or unplug a TRRS/TRS interconnect while either half is powered.
> This configuration expects the halves to communicate wirelessly, so no
> interconnect cable is needed.

## If the halves will not pair

Persistent settings survive ordinary firmware updates. To return both
controllers to a known state:

1. Flash the `settings_reset` UF2 to the right controller.
2. Flash the same `settings_reset` UF2 to the left controller.
3. Flash the normal right firmware to the right half.
4. Flash the normal left firmware to the left half.
5. Forget **Lily58** on the host, power/reset both halves together, and pair it
   again.

Settings reset erases Bluetooth hosts and the bond between the halves.

## Customize the keyboard

- Edit [`config/lily58.keymap`](config/lily58.keymap) for source-controlled
  key assignments and layers.
- Edit [`config/lily58.conf`](config/lily58.conf) for hardware and firmware
  features.
- Or connect the left half by USB and open [ZMK Studio](https://zmk.studio/) in
  Chrome or Edge to remap keys live. Firefox is not a supported USB/Web Serial
  path. Studio locking is disabled in `build.yaml`, so the keyboard is
  immediately editable after connecting.

ZMK Studio changes are stored on the controller. After saving a Studio change,
later edits to `lily58.keymap` will not take effect until **Restore Stock
Settings** is used in Studio.

That also applies to the layout already saved on your Typeractive firmware. A
normal flash preserves it. To make this repository's keymap the new baseline,
flash the new firmware and then use **Restore Stock Settings** over USB. Use the
full `settings_reset` procedure only when you also want to erase Bluetooth
pairings and re-pair the two halves.

### USB and Bluetooth output

The Lower layer has explicit output controls on its three rightmost top-row
keys:

- **Lower + 9:** prefer USB
- **Lower + 0:** prefer Bluetooth
- **Lower + Grave:** toggle USB/Bluetooth

This is useful when the left half is connected by USB for charging but should
still send keystrokes over Bluetooth, or when ZMK Studio needs the active output
to match its connection type.

### Native ZMK Studio over Bluetooth

The web app is the USB route. Bluetooth editing requires the native ZMK Studio
application. A normal Bluetooth HID connection by itself does not prove the
firmware exposes the Studio GATT service; the firmware must have ZMK Studio
enabled, as this configuration does.

After first flashing this firmware on Windows:

1. Close the web version of Studio and disconnect USB.
2. Remove/forget **Lily58** in Windows Bluetooth settings.
3. Hold **Lower** and press **Esc** to clear Bluetooth profile 1 on the
   keyboard.
4. Reset the left half, pair **Lily58** again in Windows, and verify that it
   types normally.
5. Open the latest native ZMK Studio application and refresh its device list.

If it still does not appear, use the reliable USB route in Chrome/Edge. There
is an open upstream Windows report where a working ZMK keyboard does not appear
in the native Studio device list.

### Optional hardware

For an EC11 encoder, uncomment both `CONFIG_EC11` lines in
`config/lily58.conf`. The nice!views are already enabled in `build.yaml`; do
not enable the Lily58 SSD1306 display option for this build.

## Keymap overview

- **Base:** QWERTY, with momentary Lower and Raise thumb keys.
- **Lower:** Bluetooth profiles, output selection, function keys, symbols, and
  external power controls.
- **Raise:** number/function duplication, arrows, and bracket/operator keys.

ZMK configuration reference: <https://zmk.dev/docs/>.
