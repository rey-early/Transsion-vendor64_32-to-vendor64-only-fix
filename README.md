## Guide: Converting Vendor to 64-bit Only

### 1. Edit `vendor/odm/build.prop`

**Change the CPU ABI lists from:**
```properties
ro.odm.product.cpu.abilist=arm64-v8a,armeabi-v7a,armeabi
ro.odm.product.cpu.abilist32=armeabi-v7a,armeabi
ro.odm.product.cpu.abilist64=arm64-v8a
```

**To:**
```properties
ro.odm.product.cpu.abilist=arm64-v8a
ro.odm.product.cpu.abilist32=
ro.odm.product.cpu.abilist64=arm64-v8a
```

**Add these lines to the end of the file:**
```properties
ro.zygote=zygote64
dalvik.vm.dex2oat64.enabled=true
```

---

### 2. Update VINTF & Media Services

Remove the legacy OpenMAX (OMX) entry.

* **Edit** `vendor/etc/vintf/manifest.xml`: Remove the entry for `android.hardware.media.omx`.
* **Delete** the init script: `vendor/etc/init/android.hardware.media.omx@1.0-service.rc`.

---

### 3. Update Mediatek Media C2 Service

**Note:** Skip if already using `-64b`.

Check the initialization script located at `vendor/etc/init/android.hardware.media.c2@1.2-mediatek.rc`.

**Change from:**

```rc
service android-hardware-media-c2-hal-1-2 /vendor/bin/hw/android.hardware.media.c2@1.2-mediatek

```

**To:**

```rc
service android-hardware-media-c2-hal-1-2 /vendor/bin/hw/android.hardware.media.c2@1.2-mediatek-64b

```
### 4. **Copy and replace** all files in the target directory.
**Append** the necessary configurations to `system_file_contexts` and `system_fs_config`

---

### 5. Fix for triple slot sim (2 sim + 1 sd)
**Add to build.prop**
```properties
ro.boot.product.hardware.sku=tsts
persist.radio.multisim.config=tsts
ro.telephony.sim.count=3
telephony.active_modems.max_count=3
persist.vendor.radio.msimmode=tsts
ro.vendor.radio.max.multisim=tsts
```
