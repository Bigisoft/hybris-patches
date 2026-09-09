# Sailfish OS `sweet` port - patches

Branch `sweet-hybris-23.2` adds six patches that a hybris-23.2 port on an
Android 16 base needs. **None of them are device-specific** - they are all
consequences of Android 16 and any hybris-23.2 port will hit them:

- `system/core/0045` - `perform_apex_config` is a no-op on its second call once
  `SetupMountNamespaces()` is compiled out, so no APEX ships its init `.rc`,
  `ld.config.txt` stays empty and no `updatable` service can start. `bpfloader`
  then fails and its `reboot_on_failure` reboots the device ~121 s into boot.
- `system/core/0044` - libprocessgroup aborts every Android daemon that calls
  `SetTaskProfiles()` when cgroups are unavailable, which they are under systemd.
- `system/linkerconfig/0002` - maps `droid-hybris/vendor/bin` to the vendor
  linker section, so `vndservicemanager` gets the vendor `libbinder` and stops
  silently rejecting every vendor `addService()`.
- `frameworks/native/0007`, `0008` and `system/apex/0004` - servicemanager and
  apexd without SELinux.

Commit messages carry the full reasoning.
## The whole port

**Start here:** [droid-config-sweet](https://github.com/Bigisoft/droid-config-sweet) - the device adaptation, and the README that explains the device-specific parts.

| repo | branch | what |
|---|---|---|
| [droid-config-sweet](https://github.com/Bigisoft/droid-config-sweet) | `main` | the device adaptation |
| [droid-hal-version-sweet](https://github.com/Bigisoft/droid-hal-version-sweet) | `main` | version package |
| [droid-hal-sweet](https://github.com/Bigisoft/droid-hal-sweet) | `main` | tree-root `rpm/` spec and the `repo` local manifest |
| [hybris-patches](https://github.com/Bigisoft/hybris-patches/tree/sweet-hybris-23.2) | `sweet-hybris-23.2` | fork - patches an Android 16 base needs |
| [droidmedia](https://github.com/Bigisoft/droidmedia/tree/sweet-android16) | `sweet-android16` | fork - builds against Android 16 |
| [droid-hal-device](https://github.com/Bigisoft/droid-hal-device/tree/sweet) | `sweet` | fork - vendor-side binaries and C++17 helpers |
| [android_kernel_xiaomi_sm6150](https://github.com/Bigisoft/android_kernel_xiaomi_sm6150/tree/sweet-sailfish) | `sweet-sailfish` | fork - defconfig |

Device: Xiaomi Redmi Note 10 Pro (`sweet`), Snapdragon 732G / sm6150, aarch64.
Base: `hybris-23.2` on LineageOS 23.2 (Android 16).