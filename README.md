# TWRP recovery.img builder — EEBBK P21H170 (Unisoc UMS512)

This repository builds a TWRP `recovery.img` for the EEBBK X3/X5/X6a/A6/A7 (P21H170,
Unisoc UMS512 / Tiger T610) using GitHub Actions — no local Linux machine required.

## Layout

- `device/EEBBK/P21H170/` — the TWRP device tree (kernel `prebuilt/Image`, `dtb.img`,
  `dtbo.img`, recovery ramdisk overlay, fstab, AVB signing keys).
- `.github/workflows/build.yml` — CI pipeline.

## How the build works

1. Syncs the TWRP minimal manifest `minimal-manifest-twrp/platform_manifest_twrp_aosp`
   branch `twrp-11` (Android 11.0.0_r29).
2. Copies `device/EEBBK/P21H170` into `device/EEBBK/P21H170`.
3. Runs `lunch twrp_P21H170-eng` and `mka recoveryimage`.
4. Uploads `out/target/product/P21H170/recovery.img` as the artifact `recovery-P21H170`.

## Run it

- Push to `main`/`master`, or trigger **Actions → Build TWRP recovery.img (EEBBK P21H170) → Run workflow**.
- Download the artifact `recovery-P21H170` when the job finishes (~1–2 h).

## Kernel note

The bundled `prebuilt/Image` still contains the `BBK:recovery.img is destroyed` string.
The stock UMS512 kernel panics when a non-official recovery is loaded. If this image has
not been patched, the built recovery will not boot until the integrity check in
`drm_open` is bypassed (NOP the branch, ARM64 NOP = `0xD503201F`).

## Flash

```bash
fastboot flash recovery recovery.img
```
