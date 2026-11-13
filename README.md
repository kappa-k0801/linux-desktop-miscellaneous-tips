# Miscellaneous Tips for Linux Desktop

## Organizing the "Open Files" list in Krita software
- Krita has a problem where too many execution options appear in GNOME's "Open File" because individual associations exist for each extension. Therefore, you must hide everything except the launcher.
- [org.kde.krita-desktop.tar.gz](https://raw.githubusercontent.com/kappa-k0801/linux-desktop-miscellaneous-tips/main/org.kde.krita-desktop.tar.gz) -> Download This file and copy it to ~/.local/share/applications/
- Result
![example 01](https://raw.githubusercontent.com/kappa-k0801/linux-desktop-miscellaneous-tips/main/example01.png)

## Safe sudo
- If it is not a system command, a virus scan is performed first before executing the command.
- $HOME/.local/bin/safesudo
```
#!/bin/sh

[[ z$1 == "z" ]] || [[ $1 == "--help" ]] && /usr/bin/sudo --help && exit 0

if [[ $(whereis $1 | awk '{print $2}' | grep '/usr/bin' | wc -l) != "1" ]]
then
        clamscan --no-summary $1
        if [[ $? == "1" ]]
        then
                exit 1
        else
                echo ""
        fi
fi

/usr/bin/sudo "$@"
```

## Configuring automatic updates to default kernel boot settings in a systemd-boot environment
- Add script : /usr/lib/kernel/install.d/99-bootctl-update.install
```
#!/bin/sh

COMMAND="$1"
KERNEL_VERSION="$2"

MACHINE_ID=`cat /etc/machine-id`

case "$COMMAND" in
    add)
        bootctl set-default ${MACHINE_ID}-${KERNEL_VERSION}.conf
        echo "Now default kernel version is ${MACHINE_ID}-${KERNEL_VERSION}.conf"
    ;;
    remove)
	CURRENT_KERNEL_VERSION=`uname -r`
        bootctl set-default "${MACHINE_ID}-${CURRENT_KERNEL_VERSION}.conf"
        echo "Now default kernel version is ${MACHINE_ID}-${CURRENT_KERNEL_VERSION}.conf"
    ;;
esac

exit 0
```
-> Installed: Set New Kernel
-> remove: Set Current Kernel
