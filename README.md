 # Fedora 42 Post Install Guide
Things to do after installing Fedora 42

## RPM Fusion & Terra

* Fedora has disabled the repositories for a lot of free and non-free .rpm packages by default. Follow this if you want to use non-free software like Steam, Discord and some multimedia codecs etc. As a general rule of thumb it is advised to do this to get access to many mainstream useful programs.
* Enable third party repositories by pasting the following into the terminal: 
* `sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm`
* also while you're at it, install app-stream metadata by:
* `sudo dnf group upgrade core`
* `sudo dnf4 group update core`
  
## Update 
* Go into the software center and click on update. Alternatively, you can do:
* `sudo dnf -y update`
* Reboot

## Firmware
* If your system supports firmware update delivery through lvfs, update your device firmware by:
```
sudo fwupdmgr refresh --force
sudo fwupdmgr get-devices # Lists devices with available updates.
sudo fwupdmgr get-updates # Fetches list of available updates.
sudo fwupdmgr update
```

## Flatpak
* Fedora doesn't include all non-free flatpaks by default. The command below enables access to all the flathub flatpaks. Particularly useful for users of Fedora KDE and other spins since they do not get the "Enable Third Party Repositories" option on initial boot.
* `flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo`

## AppImage

* For Appimage support install fuse:
* `sudo dnf in fuse`
* You can also install an AppImage manager like [Gearlever](https://flathub.org/apps/it.mijorus.gearlever) for neater management. To do so, run the following command:
* `flatpak install it.mijorus.gearlever`

## Media Codecs
* Install these to get proper multimedia playback.
````
sudo dnf4 group upgrade multimedia
sudo dnf swap 'ffmpeg-free' 'ffmpeg' --allowerasing # Switch to full FFMPEG.
sudo dnf upgrade @multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin # Installs gstreamer components. Required if you use Gnome Videos and other dependent applications.Disable NetworkManager-wait-online.service

    Disabling it can decrease the boot time by at least ~15s-20s:
    sudo systemctl disable NetworkManager-wait-online.service

sudo dnf group install -y sound-and-video # Installs useful Sound and Video complementary packages.
````

## H/W Video Acceleration
* Helps decrease load on the CPU when watching videos online by alloting the rendering to the dGPU/iGPU. Quite helpful in increasing battery backup on laptops.

### H/W Video Decoding with VA-API 
* `sudo dnf install ffmpeg-libs libva libva-utils`

<details>
<summary>Intel</summary>
 
* If you have a recent Intel chipset (5th Gen and above) after installing the packages above., Do:
* `sudo dnf swap libva-intel-media-driver intel-media-driver --allowerasing`
* `sudo dnf install libva-intel-driver`
</details>

<details>
<summary>AMD</summary>No need to do this for intel integrated graphics. Mesa drivers are for AMD graphics, who lost support for h264/h245 in the fedora repositories in f38 due to legal concerns.
 
* If you have an AMD chipset, after installing the packages above do:
```
sudo dnf swap mesa-va-drivers mesa-va-drivers-freeworld
sudo dnf swap mesa-vdpau-drivers mesa-vdpau-drivers-freeworld
sudo dnf swap mesa-va-drivers.i686 mesa-va-drivers-freeworld.i686
sudo dnf swap mesa-vdpau-drivers.i686 mesa-vdpau-drivers-freeworld.i686
```
</details>

### OpenH264 for Firefox
* `sudo dnf install -y openh264 gstreamer1-plugin-openh264 mozilla-openh264`
* `sudo dnf config-manager setopt fedora-cisco-openh264.enabled=1`
* After this enable the OpenH264 Plugin in Firefox's settings.


## Optimizations
* The tips below can allow you to squeeze out a little bit more performance from your system.
  
### Disable NetworkManager-wait-online.service
Disabling it can decrease the boot time by at least ~15s-20s:
- `sudo systemctl disable NetworkManager-wait-online.service`

### Disable Mitigations 
* Increases performance in multithreaded systems. The more cores you have in your cpu the greater the performance gain. 5-30% performance gain varying upon systems. Do not follow this if you share services and files through your network or are using fedora in a VM. 
* Modern intel CPUs (above 10th gen) do not gain noticeable performance improvements upon disabling mitigations. Hence, disabling mitigations can present some security risks against various attacks, however, it still _might_ increase the CPU performance of your system.
* `sudo grubby --update-kernel=ALL --args="mitigations=off"`

### Use themes in Flatpaks
- `sudo flatpak override --filesystem=xdg-config/gtk-3.0`
- `sudo flatpak override --filesystem=xdg-config/gtk-4.0`
