# NXP NFC: restore PN8x HIDL build discovery

This fork restores one missing Soong traversal entry in the LineageOS 23.2
NXP NFC tree.

## Why this exists

The source tree still contains the PN8x HIDL implementation, including the
`pn8x/` Android build modules. However, LineageOS commit
`22876a989e80a3c28367fc439a5a103b03a21be7` removed `pn8x` from the top-level
`Android.bp` `subdirs` list. As a result, a product that selects the PN8x
HIDL service cannot discover and build the required module.

This fork adds only:

```bp
"pn8x",
```

to the existing Soong subdirectory list. It does not select PN8x for every
device and does not change the NFC driver, GPIOs, DTBO or firmware.

## Compatibility

The change targets:

- LineageOS 23.2 / Android 16;
- source base `367cff59c3a23d29c3978cf54ddc390b561c9c0a`;
- products that explicitly request `android.hardware.nfc@1.2-service`.

The OnePlus 7 Pro `guacamole` device change in the companion repository is
the tested consumer. This is a shared NXP repository, so other products must
still be built and checked for module-name conflicts.

## Build usage

Use this repository at `hardware/nxp/nfc` together with the companion device
change:

```xml
<project name="DurkaEbanaya/android_hardware_nxp_nfc"
    path="hardware/nxp/nfc"
    remote="github"
    revision="1fa6707c56eafc536d673fcbd162570bf922cdc3" />
```

This repository is source code, not a flashable image or an installable NFC
module. Build it as part of a complete LineageOS tree and package the output
with the matching device-side HIDL manifest/service selection.

## Validation

The required HIDL service and runtime files were verified on `guacamole` with
the companion device change. The tested runtime registered:

```text
android.hardware.nfc@1.2::INfc/default
vendor.nxp.nxpnfc@1.0::INxpNfc/default
```

The source change itself was checked against the exact LineageOS base with
`git diff --check`. A complete shared-tree build for unrelated NXP products
is still the appropriate downstream integration gate.

## Related review

- NXP build change: [LineageOS Gerrit 496804](https://review.lineageos.org/c/496804)
- Device consumer: [LineageOS Gerrit 496805](https://review.lineageos.org/c/496805)

## Safety

Do not flash this repository or its source files directly. It only changes
which Soong packages are visible during an Android build. Use a complete,
consistently signed OTA and retain a rollback image set.
