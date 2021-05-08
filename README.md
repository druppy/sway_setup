# Sway setup notes

I have a few systems running Wayland and SwayWM on a Debian Bullseye, and here is a few hints, mostly to myself and anyone interested.

Note that with XWayland installed, most programs works as expected, but the scaling can be really bad, depending on your monitor. 

With HiDPI monitors using fractional scaling, and also using GPU (Intel and AMD), these things make everything work really beautiful.

## LightDM 

When booting sway works out of the box, but some tuning will be good to make sure sway boot is clean and to make sure some environment values are in place.

When using LightDM the `$HOME/.xsessionrc` is used for booting, and to be able to detect the sway boot we bac detect on the `$STARTUP` environment values that is set to `sway` when starting sway.

Add this to the `$HOME/.xsessionrc`

```bash
if [ "$STARTUP" = "sway" ]
then
   export MOZ_ENABLE_WAYLAND=1
   export MOZ_X11_EGL=1
   export GDK_BACKEND=wayland
   export SDL_VIDEODRIVER=wayland
fi 
```

This will make GTK+3 programs, SDL games, Firefox and Thunderbird work as expected in Wayland.

## Sublime text 4

Sublime text 4 (still somewhat beta) works really nice directly in Wayland, and if you have a nice GPU (again Intel or AMD), it is possible to allow sublime to use OpenGL, and become an even faster editor.

Add to following to `Preferences.sublime-settings` 

```json
"hardware_acceleration": "opengl",
```

## Chromium

Chromium have an alternative render platform (that is found in Electron) called Ozone, that have nice Wayland support, and we just need a way to enable this.

For Chromium this can be done by adding the proper startup flags to the `~/.config/chromium-flags.conf` file, like this 

```bash
--enable-features=UseOzonePlatform
--ozone-platform=wayland
```

This will be loaded every time you start an Chromium browser, and you can now enjoy fast rendering, and sharp fonts.

## VSCode (1.56+)

VSCode have just recently updated to Electron 12, that again is based on Chromium, and therefor will this only work for VScode 1.56 or later.

As I did not find anywhere in the setup where we can control settings for VScode startup, I ended up making a copy of the `code.desktop` file and then added the same flags as in the chromium flags file.

I added the file `~/.local/share/applications/code.desktop` to the system, with the following content

```ini
[Desktop Entry]
Name=Visual Studio Code (Wayland)
Comment=Code Editing. Redefined.
GenericName=Text Editor
Exec=/usr/share/code/code --enable-features=UseOzonePlatform --ozone-platform=wayland --no-sandbox --unity-launch %F
Icon=com.visualstudio.code
Type=Application
StartupNotify=false
StartupWMClass=Code
Categories=Utility;TextEditor;Development;IDE;
MimeType=text/plain;inode/directory;application/x-code-workspace;
Actions=new-empty-window;
Keywords=vscode;

X-Desktop-File-Install-Version=0.26

[Desktop Action new-empty-window]
Name=New Empty Window
Exec=/usr/share/code/code --enable-features=UseOzonePlatform --ozone-platform=wayland --no-sandbox --new-window %F
Icon=com.visualstudio.code
```

The downside here is that you need to start VSCode using the desktop file (read menu). If anyone find another and better way, please let me know.

## Sway setup

Debian bullseye comes with SwayWM 5.1, and it is quite stable at least for me, and I use it on a large AMD CPU/GPU stationary and my Intel laptop.

I use `wofi` for as launch menu, as it is xdg menu aware, works natively in Wayland and has nice icons.

For terminal I ended up using `alacritty`, as it was native Wayland and OpenGL, fast and written in Rust. The downside is, it needs to be build by hand.

For menu i use `waybar` and a bit for special styling for my own taste. I am missing a bit of graphs for CPU and network load, but it is mostly nice to haves.

I luck my screen using `swayidle` that works really good, and look cool too.

I also added basic sound control keys, that works directly on pulse audio.

I use 1.6 scaling, as I use HiDPI on my laptop, and danish/english (+chinese as I try to learn this too) keyboard.

Move [this](sway/config) to `.config/sway/config`, for the sway setup, and to use waybar copy the content of [this](waybar) to `.config/waybar`.

### Alacritty

Install rust using [rustup](https://rustup.rs), and checkout there [github](https://github.com/alacritty/alacritty).

To build it, run `cargo install alacritty --force`. This will build it and place it in the local users home dir. Make sure you have `$HOME/.cargo/bin` in your search path.
