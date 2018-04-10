# This is manifest to download AGL build system with meta-synopsys layer

## Build procedure
1. Download necessary files using [repo](https://source.android.com/setup/downloading#installing-repo) utility
```
  repo init -u https://github.com/EvgeniiDidin/agl_manifest_synopsys
  repo sync -j4 
```
2. Initialize build directory by using template files
```
  source meta-agl/scripts/aglsetup.sh -m hsdk
```
3. Build synopsys image (currently, the rootfs is used as initramfs)
```
  bitbake agl-image-minimal
```
