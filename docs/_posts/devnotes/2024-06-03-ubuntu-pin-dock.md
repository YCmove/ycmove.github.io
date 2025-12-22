---
title: How to pin a program to the Ubuntu Dock Panel?
date: 2024-06-03 01:02:42 +0300
tags:
   - ubuntu
layout: tag

---

<!-- # How to pin a program to the Ubuntu Dock Panel? -->

## Create .desktop under /usr/share/applications/
Take Zotero for example:
Zotero offer the default Desktop Entry setting, other applications sometimes haveits .desktop locate in `~/.local/share/applications/`, or you can just create one if you couldn't find any existed .desktop file.
```
[Desktop Entry]
Name=Zotero
Exec=bash -c "$(dirname $(realpath $(echo %k | sed -e 's/^file:\/\///')))/zotero -url %U"
Icon=zotero.ico
Type=Application
Terminal=false
Categories=Office;
MimeType=text/plain;x-scheme-handler/zotero;application/x-research-info-systems;text/x-research-info-systems;text/ris;application/x-endnote-refer;application/x-inst-for-Scientific-info;application/mods+xml;application/rdf+xml;application/x-bibtex;text/x-bibtex;application/marc;application/vnd.citationstyles.style+xml
X-GNOME-SingleWindow=true
```

Change the `Exec` and `Icon`.

New setting:
```
$ vim Zotero-6.0.35_linux-x86_64/Zotero_linux-x86_64/zotero.desktop
[Desktop Entry]
Name=Zotero
Exec=bash {path_to_zotero}/Zotero_linux-x86_64/zotero
Icon={path_to_zotero}/Zotero_linux-x86_64/chrome/icons/default/default256.png
Type=Application
Terminal=false
Categories=Office;
MimeType=text/plain;x-scheme-handler/zotero;application/x-research-info-systems;text/x-research-info-systems;text/ris;application/x-endnote-refer;application/x-inst-for-Scientific-info;application/mods+xml;application/rdf+xml;application/x-bibtex;text/x-bibtex;application/marc;application/vnd.citationstyles.style+xml
X-GNOME-SingleWindow=true
NoDisplay=false
```

Create .desktop under /usr/share/applications/
```
$ sudo cp zotero.desktop /usr/share/applications/
```

## Set up the dock setting (gsettings)
```
$ gsettings get org.gnome.shell favorite-apps
['firefox_firefox.desktop', 'org.gnome.Nautilus.desktop', 'snap-store_ubuntu-software.desktop', 'google-chrome.desktop', 'org.gnome.Terminal.desktop', 'gnome-control-center.desktop', 'code_code.desktop', 'gimp_gimp.desktop']

```

Add `'zotero.desktop'` in the favorite-apps list. Don't forget the double quotes "[..., 'zotero.desktop']".
```
$ gsettings set org.gnome.shell favorite-apps "['firefox_firefox.desktop', 'org.gnome.Nautilus.desktop', 'snap-store_ubuntu-software.desktop', 'google-chrome.desktop', 'org.gnome.Terminal.desktop', 'gnome-control-center.desktop', 'code_code.desktop', 'gimp_gimp.desktop', 'zotero.desktop']"
```
