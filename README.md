# CopperheadOS-Docker
Docker container for building CopperheadOS. Still very much a work in progress

## Enviroment Variables

### `USE_CCACHE`
Enable or disable using `ccache`. Can significantly speed up later builds. All
cache files are saved to `CCACHE_DIR`. Defaults to the value of 1.

### `CCACHE_SIZE`
If `USE_CCACHE` is true this variable determines the size of the ccache.
Defaults to the value of "50G".

### `SIGNATURE_SPOOFING`
If set, privileged apps will be allowed to spoof their signature. This is needed
for packages like Microg or FakeStore to spoof being Google Play Services or the
Google Play Store. Defaults to "no".

### `DEVICE`
The codename of the device the build will be for. Currently supported is all the
Google Pixel devices (sailfish, marlin, walleye, taimen). Defaults to "walleye".

### `BUILD_TAG`
The release version of [CopperheadOS](https://github.com/CopperheadOS/android-prepare-vendor/releases).
Defaults to "OPM2.171019.029.2018.04.02.21".

### `BUILD_ID`
The Build ID of the build. Defaults to "OPM2.171019.029".

### `NUM_OF_THREADS`
The number of threads/processes that will be used during the various stages of
the build. Defaults to 8.

## Volumes

### `/srv/src`
Location of where all the repositories will be downloaded.

### `/srv/ccache`
Location of where the ccache files are saved.

### `/srv/keys`
Location of where the release keys are located. If the directory is empty the
script will generate keys for you based on the device.

### `/srv/tmp`
Location of the temporary directory.

### `/srv/local_manifests`
Location of any custom manifests that will be included when syncing repos.

### `/srv/zips`
Location of where the output zips and xz files will be copied to after the
build process is complete.

## Example Commands

### Basic CopperheadOS
If you want just plain CoppperheadOS just provide the enviroment variables.

```
$ sudo docker run \
    -v /media/hdd/copperheados/src:/srv/src \
    -v /media/hdd/copperheados/ccache:/srv/ccache \
    -v /media/hdd/copperheados/keys:/srv/keys \
    -v /media/hdd/copperheados/tmp:/srv/tmp \
    -v /media/hdd/copperheados/zips:/srv/zips \
    -e USE_CCACHE=1 \
    -e CCACHE_SIZE="50G" \
    -e SIGNATURE_SPOOFING="no" \
    -e DEVICE="walleye" \
    -e BUILD_TAG="OPM2.171019.029.2018.04.02.21" \
    -e BUILD_ID="OPM2.171019.029" \
    -e NUM_OF_THREADS=8 \
    bflux/copperheados-docker
```

### Microg
[Microg](https://microg.org/) is a FOSS implementation of most of the Google
Play Services Libraries. Most importantly it supports GCM and Account
Authentication. Inorder to support microg `SIGNATURE_SPOOFING` patch must be
applied. In addation `CUSTOM_PACKAGES` should include `GmsCore`, `GsfProxy` and
`FakeStore`. `FakeStore` is needed since most apps that rely on Google Play
Services will get upset if they don't see an app with the `com.android.vending`
package id installed on the system.

```
$ sudo docker run \
    -v /media/hdd/copperheados/src:/srv/src \
    -v /media/hdd/copperheados/ccache:/srv/ccache \
    -v /media/hdd/copperheados/keys:/srv/keys \
    -v /media/hdd/copperheados/tmp:/srv/tmp \
    -v /media/hdd/CopperheadOS-Docker/local_manifest/:/srv/local_manifests \
    -v /media/hdd/copperheados/zips:/srv/zips \
    -e USE_CCACHE=1 \
    -e CCACHE_SIZE="50G" \
    -e SIGNATURE_SPOOFING="yes" \
    -e DEVICE="walleye" \
    -e BUILD_TAG="OPM2.171019.029.2018.04.02.21" \
    -e BUILD_ID="OPM2.171019.029" \
    -e NUM_OF_THREADS=8 \
    -e CUSTOM_PACKAGES="GmsCore GsfProxy MozillaNlpBackend NominatimNlpBackend com.google.android.maps FakeStore" \
    bflux/copperheados-docker
```

### Microg with Google Play Store
If you are a complete newb like I am and aren't ready to give up the Google Play
Store quite yet then simply swap out the `FakeStore` package for the `Phonesky`
package.

```
$ sudo docker run \
    -v /media/hdd/copperheados/src:/srv/src \
    -v /media/hdd/copperheados/ccache:/srv/ccache \
    -v /media/hdd/copperheados/keys:/srv/keys \
    -v /media/hdd/copperheados/tmp:/srv/tmp \
    -v /media/hdd/CopperheadOS-Docker/local_manifest/:/srv/local_manifests \
    -v /media/hdd/copperheados/zips:/srv/zips \
    -e USE_CCACHE=1 \
    -e CCACHE_SIZE="50G" \
    -e SIGNATURE_SPOOFING="yes" \
    -e DEVICE="walleye" \
    -e BUILD_TAG="OPM2.171019.029.2018.04.02.21" \
    -e BUILD_ID="OPM2.171019.029" \
    -e NUM_OF_THREADS=8 \
    -e CUSTOM_PACKAGES="GmsCore GsfProxy MozillaNlpBackend NominatimNlpBackend com.google.android.maps Phonesky" \
    bflux/copperheados-docker
```
