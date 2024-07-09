# macos-docker-with-network

This script is made for macOS Docker container, ensuring that both network and mouse functions operate properly on Wayland.

Currently(2024-07-09) works perfectly on Arch KDE.

Setup:
```bash
docker run \
    --privileged -i \
    --net host \
    --device /dev/kvm \
    -p 50922:10022 \
    -e XDG_RUNTIME_DIR=/tmp \
    -e WAYLAND_DISPLAY=$WAYLAND_DISPLAY \
    -e QT_QPA_PLATFORM=wayland \
    -e GDK_BACKEND=wayland \
    -e CLUTTER_BACKEND=wayland \
    -v $XDG_RUNTIME_DIR/$WAYLAND_DISPLAY:/tmp/$WAYLAND_DISPLAY \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -e "DISPLAY=${DISPLAY:-:0.0}" \
    -e AUDIO_DRIVER=pa,server=unix:/tmp/pulseaudio.socket \
    -v "/run/user/$(id -u)/pulse/native:/tmp/pulseaudio.socket" \
    sickcodes/docker-osx:auto
```

Installs pre installed Catalina from [https://github.com/sickcodes/Docker-OSX](https://github.com/sickcodes/Docker-OSX)

After this when macOS start you will need to go to the docker container and change the following line for the mouse to work:

```bash
docker exec -it [container id] bash
```
edit `Launch.sh` 

```bash
vim Launch.sh
```

replace the lines:
```
-device qemu-xhci,id=xhci \ -device usb-kbd,bus=xhci.0 -device usb-tablet,bus=xhci.0 \ 
```
with:
```
-usb -device usb-kbd -device usb-tablet \
```
Restart container.

The final step is changing the username and the password for the mac instance.
