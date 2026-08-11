# ps5-linux-patches

## Compilation

```bash
git clone https://github.com/ps5-linux/ps5-linux-patches
git clone https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git
cd linux
git checkout "tags/v$(grep -m1 "^# Linux/" ../ps5-linux-patches/.config | awk '{print $3}')"
for p in ../ps5-linux-patches/*.patch; do git apply "$p"; done
cp ../ps5-linux-patches/.config .config
make -j$(nproc)
```

`linux.patch` is the base port. Any additional `*.patch` files are applied on
top of it, in glob order, which is also what `ps5-linux-image/build_image.sh`
does — so a fix kept in its own file needs no changes to `linux.patch` and does
not conflict when the base port is updated for a new kernel release.

| Patch | Purpose |
| --- | --- |
| `linux.patch` | the PS5 port |
| `ps5-hdmi-restore-output.patch` | restore video output after an EDID notification (fixes the black screen after the TV is switched off/on) |

## Installation

In the same `linux` folder after compilation, do:

```bash
sudo make modules_install
sudo make install
```

## TODO

- amdgpu smu driver to show correct gpu frequency and temperature
- hdmi converter improvments: hdr, rgb range, 120hz

## Bugs

- ~~screen save does not work properly~~ — fixed by `ps5-hdmi-restore-output.patch`.
  The symptom was a black screen with a live HDMI signal after the TV was
  switched off and on, or switched to another input and back; it was never a
  screensaver or a DPMS state. Set `hdmi.restore_output=0` to get the old
  behaviour back.
- hdmi audio output does not work on some monitors
- hdmi 1440p and 2160p video output does not work on some monitors
- monitor swap is not supported
- hdmi cec is not supported
