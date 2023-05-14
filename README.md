![manjaro_logo_text.png](pics/manjaro_logo_text.png)

Ce tuto est à la base une doc personnelle.

Néanmoins, j'ai tenté autant que possible de la rédiger afin qu'elle soit utilisable par d'autres.

Les commentaires sont évidement les bienvenus ;)

# Post Installation

Bon... normalement, à ce stade, vous venez de terminer l'installation de base de **Manjaro Cinnamon** ;)

> **Remarque** : hormis quelques points propres à Cinnamon, cette doc est valable pour d'autres variantes de Manjaro.

## Outils de base

On va installer ce package, qui sera nottament nécessaire dans la construction de paquets `AUR`.

```shell
sudo pacman -S base-devel
```

## Configuration de base de Pacman

### Méthode 1

On configure le gestionnaire de paquet pacman pour utiliser un mirroir belge pour les dépots

```shell
nano /etc/pacman.d/mirrorlist
```

Et on garde

```shell
Server = http://archlinux.cu.be/$repo/os/$arch
```

### Méthode 2 (conseillée)

Passer via le gestionaire de paquet de Manjaro

![mirrors.jpeg](/pics/pamac_mirrors.jpeg)

## Ajout de dépôts additionnels dans Pacman

> **Remarque** : c'est le même principe que pour n'importe quel dépot de n'importe quelle distro, il faut **impérativement** n'ajouter que des dépots dans lesquels on a **confiance**. Et c'est idem ++ pour les paquets `AUR`.

Dans `/etc/pacman.conf`

On ajoute le support du 32bits en décommentant :

```shell
[multilib]
Include = /etc/pacman.d/mirrorlist
```

Et on ajoute les dépôts suivants :

```shell
[archstrike]
Server = https://mirror.archstrike.org/$arch/$repo

[herecura]
Server = https://repo.herecura.eu/$repo/$arch
```

On met les DBs des paquets à jour

```shell
sudo pacman -Syy
```

On importe la clé de Archstrike

> **Remarque** : je vous invite à vérifier ces clés sur le site de Archstrike et ne pas bêtement copier/coller ;)

```shell
sudo pacman-key --init
sudo dirmngr < /dev/null
sudo pacman-key -r 9D5F1C051D146843CDA4858BDE64825E7CBC0D51
sudo pacman-key --lsign-key 9D5F1C051D146843CDA4858BDE64825E7CBC0D51
```

On installe les paquets suivants (procédure spcifique à Archstrike)

```shell
sudo pacman -S archstrike-keyring
sudo pacman -S archstrike-mirrorlist
```

Et toujours dans `/etc/pacman.conf`, on remplace :

```shell
[archstrike]
Server = https://mirror.archstrike.org/$arch/$repo
```

Par

```shell
[archstrike]
Include = /etc/pacman.d/archstrike-mirrorlist
```

Et on met les DBs des paquets à jours à nouveau

```shell
sudo pacman -Syy
```

## Yay ou Trizen

> **Remarque** : étant sous Manjaro, l'outil maison `Pamac` gère également `AUR` (ainsi que `Snap` et `Flatpak`), mais `trizen` / `yay` restent intéressant, surtout pour les Archlinuxien qui veulent rester en terrain connu ;)

On installe `yay` ou `trizen` pour épauler `pacman` avec les paquets `AUR`.

Ce sont en fait des wrapper pour `pacman` qui ajoutent des fonctions en plus, comme la possibilité d'installer des paquets depuis `AUR`.

> **Remarque** : n'oubliez pas de lire les scripts d'installation à chaque fois, même lors de mises à jours !

Pour `yay`

```shell
sudo pacman -S yay
```

Pour `trizen`, manuellement

```shell
sudo pacman -S git
cd /tmp
git clone https://aur.archlinux.org/trizen.git
cd trizen
makepkg -si
```

Ou plus simplement via `yay`

```shell
yay -S trizen
```

## Polices de caractères

On rajoute quelques polices de caractères utiles/manquantes.

```shell
yay -S ttf-bitstream-vera
```

Malheureusement nécessaires...

```shell
yay -S ttf-ms-fonts
```

Et pour le japonais ;)

```shell
yay -S noto-fonts-cjk noto-fonts-emoji noto-fonts
```

## Bluetooth

On ajoute ce qui manque au support du bluetooth

```shell
yay -S bluez-tools bluez-plugins bluez-utils
```

Et on ajout ce paramètre pour éviter blueman d'être à la porte...

```shell
sudo nano /etc/bluetooth/main.conf
```

Et on ajoute/modifie le paramètre

```shell
AutoEnable=true
```

## Sources du Kernel

On installe le package contenant les sources du kernel indispensable pour pouvoir compiler des modules addtionnels avec `DKMS` (Virtualbox, ....)

> **Rermaque** : les sources à installer doivent bien entenu "matcher" avec le noyau installé

```shell
yay -S linux516-headers
```

# Package Managers additionnels

On va maintenant installer des package manager alternatifs, mais avant, passons par les paramètres de `Pamac`.

## Paramètres de Pamac

Histoire de garder un système propre, je vous conseille de cocher `Déinstaller les dépendances inutiles`

![pamac_settings.jpeg](/pics/pamac_settings.jpeg)

## AUR

Bon... même si vous avez installé `trizen` ou `yaourt`, si vous voulez intégrer les paquets AUR dans Pamac et donc bénéficier de la gestion unifiée que celui-ci fourni, vous devez activer les paquets `AUR` dans celui-ci.

> **Remarque** : les paquets `AUR` viennent en droite ligne de `AUR` (mouarf) et contrairement à `Flatpak` ou `Snap`, peuvent entrer en conflit avec les paquets de Manjaro. Je vous conseille de ne cocher `Vérifier les mises à jour` si et seulement si vous vous sentez l'âme de gérer les éventuels conflits. Sinon, effectuez les mises à jour via `trizen` seulement quand c'est nécessaire et pour un paquet en particulier.

> **Remarque 2** : AUR (Arch User Repository) est, comme son nom l'indique, prévu pour Arch. Et bien que Manjaro soit basée sur Arch, elle fonctionne avec ses propres dépots (instable, test et stable), lesquelles ajoutent une "couche de stabilité" par rapport à Arch, l'instable de Manjaro correspondant au stable de Arch et les releases stables de Manjaro se faisant sous forme de "Snapshots" à intervals courts (excepté les MAJ de sécu qui peuvent être poussées en priorité). Ce qui du coup, peut entrainer des soucis (c'est rare, mais ça arrive) avec la mise à jours de certains packages AUR, lesquels reposent sur une version stable de Arch mais pas encore disponible dans le dépot stable de Manjaro. La plupart du temps, ça se passe très bien, et dans le cas pré-cité, cela se résume à une erreur de construction du paquet (donc l'ancienne version reste installée) et à simplement attendre le suivant snapshot stable de manjaro.

> **Remarque 2 bis** : et donc dans un idéal "safe", je recommanderai de ne PAS cocher l'option **Vérifier les mises à jours**, afin d'éviter qu'une telle erreur se produise lors d'une mise à jours complète du système.

![pamac_aur.jpeg](/pics/pamac_aur.jpeg)

## Flatpak

Ce fait via l'activation des paquets `Flatpak` dans `Pamac`.

Je vous invite à cocher `Vérifier les mises à jour` pour que celles soient effectuées via `Pamac`.

> **Remarque** : cela équivaut à un `flatpak update` et devrait être 100% safe pour votre système.

![pamac_flatpak.jpeg](/pics/pamac_flatpak.jpeg)

### Installation manuelle du plugin Flatpak

> **Remarque** : le plugin `libpamac-flatpak-plugin` à flatpak comme dépendence, donc l'installation manuelle de `flatpak` n'est nécessaire QUE si vous n'installer pas le support `Flatpak` dans `Pamac`

```shell
yay -S flatpak
```

## Snap

Ce fait via l'activation des paquets `Snap` dans `Pamac`.

> **Remarque** : `Snap` fonctionne avec un Store centralisé et géré par Canonical. D'un point de vue d'un libriste, je vous conseil fortement d'éviter autant que possible et privilégier `AUR` ou `Flatpak`.

![pamac_snap.jpeg](/pics/pamac_snap.jpeg)

### Installation manuelle du plugin Snap

> **Remarque** : le plugin `libpamac-snap-plugin` à `snapd` comme dépendence, donc l'installation manuelle de `snapd` n'est nécessaire QUE si vous n'installer pas le support `Snap` dans `Pamac`

```shell
yay -S snapd
```

## AppImage

Les `AppImage` ne sont pris en charge par `pamac` et bien qu'il existe un `AppImage` hub, cela ne repose pas sur un store ou des dépôts comme `Snap` ou `Flatpak`.

Du coup, ce petit utilitaire permet d'installer une `AppImage` dans `~/Applications` (default) et d'ajouter un raccourci dans le menu de façon simple.

On install donc

```shell
yay -S appimagelauncher
```

## NodeJS et NPM

On installe NodeJS + le package manager NodeJS

```shell
yay -S nodejs npm
```

On définit où seront installé les packages nodejs avec l'option `-g` de `npm`

```shell
npm config set prefix /home/<YOUR_USER_NAME>/.local
```

Dans `~/.bashrc`, on ajoute :

```shell
PATH=~/.local/bin/:$PATH
```

## PIP

On installe le package manager de python

```shell
yay -S python-pip
```

Et `pipx` pour installer les Apps python depuis PyPi dans un environement isolé

```shell
yay -S python-pipx
```

# Matos

Dans cette section, on va s'occuper de notre matos.

## Pilote graphique

### Activer Freesync pour AMD (amdgpu)

Pour AMD (pilote : amdgpu), on édite `/etc/X11/xorg.conf.d/20-amdgpu.conf`

```shell
sudo nano /etc/X11/xorg.conf.d/20-amdgpu.conf
```

Et on y colle ceci

```shell
Section "Device"
     Identifier "AMD"
     Driver "amdgpu"
     Option "VariableRefresh" "true"
     Option "TearFree" "true"
EndSection
```

### Activer le support Vulkan 32 bits pour AMD (steam, lutris, wine)

Les apps comme steam, lutris, wine, ... nécessites leurs pendants 32 bits de Vulkan pour fonctionner.

```shell
sudo pacman -S --needed lib32-mesa vulkan-radeon lib32-vulkan-radeon vulkan-icd-loader lib32-vulkan-icd-loader
```

> **Remarque** : pour les autres Intel/Nvidia : https://github.com/lutris/docs/blob/master/InstallingDrivers.md

## Dongle USB Bluetooth basé sur Realtek RTL8761B

> **Remarque (Juillet 2021)** : cette partie n'est plus nécessaire, rtl8761b ayant été ajouté dans linux-firmware dans les dépôts officiels
>
> Installer le module depuis `AUR`, suivi d'un branchement/débranchement du dongle ;)
>
> ```shell
> yay -S rtl8761b-fw
> ```

## Support LDAC dans Pulseaudio pour les Casques Bluetooth

Nous avons besoin de remplacer `pulseaudio-bluetooth` par `pulseaudio-modules-bt` pour avoir plus de choix niveau Codecs Bluetooth, dont **LDAC**, qui est un Codec Lossless.

```shell
yay -S pulseaudio-modules-bt
```

> **Remarque** : le paquet `pulseaudio-modules-bt` sur Manjaro est présent dans le dépôt `community` ET `AUR`, mais n'existe que dans `AUR` sous Arch

> **Remarque 2** : le dépôt github en amont n'est plus maintenu, donc à utiliser jusqu'à ce que ça casse !

Suivi d'un petit

```shell
pulseaudio -k
```

### Vérifier son utilisation

#### Via `pactl list`

Il est possible de vérifier s'il est pris en compte via la commande : `pactl list`.

Exemple de sortie

```shell
pactl list

[...]

Destination #2
 État : RUNNING
 Nom : bluez_sink.94_DB_56_9D_4C_38.a2dp_sink
 Description : WH-1000XM4
 Pilote : module-bluez5-device.c
 Spécification de l'échantillon : s16le 2ch 44100Hz
 Plan des canaux : front-left,front-right
 Module du propriétaire : 26
 Sourdine : non
 Volume : front-left: 37933 /  58% / -14,25 dB,   front-right: 37933 /  58% / -14,25 dB
         balance 0,00
 Volume de base : 65536 / 100% / 0,00 dB
 Source du moniteur : bluez_sink.94_DB_56_9D_4C_38.a2dp_sink.monitor
 Latence : 31109 usec, configuré 30804 usec
 Marqueurs : HARDWARE DECIBEL_VOLUME LATENCY
 Propriétés :
  bluetooth.protocol = "a2dp_sink"
  bluetooth.a2dp_codec = "LDAC"
  device.description = "WH-1000XM4"
  device.string = "94:DB:56:9D:4C:38"
  device.api = "bluez"
  device.class = "sound"
  device.bus = "bluetooth"
  device.form_factor = "headset"
  bluez.path = "/org/bluez/hci0/dev_94_DB_56_9D_4C_38"
  bluez.class = "0x240404"
  bluez.alias = "WH-1000XM4"
  device.icon_name = "audio-headset-bluetooth"
  device.intended_roles = "phone"
 Ports :
  headset-output: Headset (type: Unknown, priority: 0, available)
 Port actif : headset-output
 Formats :
  pcm

[...]

Carte #3
 Nom : bluez_card.94_DB_56_9D_4C_38
 Pilote : module-bluez5-device.c
 Module propriétaire : 26
 Propriétés :
  device.description = "WH-1000XM4"
  device.string = "94:DB:56:9D:4C:38"
  device.api = "bluez"
  device.class = "sound"
  device.bus = "bluetooth"
  device.form_factor = "headset"
  bluez.path = "/org/bluez/hci0/dev_94_DB_56_9D_4C_38"
  bluez.class = "0x240404"
  bluez.alias = "WH-1000XM4"
  device.icon_name = "audio-headset-bluetooth"
  device.intended_roles = "phone"
 Profils :
  headset_head_unit: Headset Head Unit (HSP/HFP) (sinks: 1, sources: 1, priority: 30, available: oui)
  a2dp_sink_sbc: High Fidelity Playback (A2DP Sink: SBC) (sinks: 1, sources: 0, priority: 40, available: oui)
  a2dp_sink_aac: High Fidelity Playback (A2DP Sink: AAC) (sinks: 1, sources: 0, priority: 40, available: oui)
  a2dp_sink_aptx: High Fidelity Playback (A2DP Sink: aptX) (sinks: 1, sources: 0, priority: 40, available: non)
  a2dp_sink_aptx_hd: High Fidelity Playback (A2DP Sink: aptX HD) (sinks: 1, sources: 0, priority: 40, available: non)
  a2dp_sink_ldac: High Fidelity Playback (A2DP Sink: LDAC) (sinks: 1, sources: 0, priority: 40, available: oui)
  off: Off (sinks: 0, sources: 0, priority: 0, available: oui)
 Profil actif : a2dp_sink_ldac
 Ports :
  headset-output: Headset (type: Unknown, priority: 0, latency offset: 0 usec, available)
   Partie du(des) profil(s) : headset_head_unit, a2dp_sink_sbc, a2dp_sink_aac, a2dp_sink_aptx, a2dp_sink_aptx_hd, a2dp_sink_ldac
  headset-input: Headset (type: Unknown, priority: 0, latency offset: 0 usec, availability unknown)
   Partie du(des) profil(s) : headset_head_unit
```

## Udev

Certains périphériques USB nécessitent des règles `udev` spécifiques pour qu'ils soient accessibles.

### Périphs Android

Afin de pouvoir utiliser les outils du SDK Android, comme `adb` ou `fastboot`, on doit donner un accès au smartphone via USB.

Vous avez 2 possibilités :

#### Manuellement, on créer le fichier `/etc/udev/rules.d/99-android.rules` et on ajoute

```shell
SUBSYSTEM=="usb", ATTR{idVendor}=="0502", MODE="0666", OWNER="shakasan" # Acer
SUBSYSTEM=="usb", ATTR{idVendor}=="0b05", MODE="0666", OWNER="shakasan" # Asus
SUBSYSTEM=="usb", ATTR{idVendor}=="413c", MODE="0666", OWNER="shakasan" # Dell
SUBSYSTEM=="usb", ATTR{idVendor}=="0489", MODE="0666", OWNER="shakasan" # Foxconn
SUBSYSTEM=="usb", ATTR{idVendor}=="04c5", MODE="0666", OWNER="shakasan" # Fujitsu
SUBSYSTEM=="usb", ATTR{idVendor}=="04c5", MODE="0666", OWNER="shakasan" # Fujitsu-Toshiba
SUBSYSTEM=="usb", ATTR{idVendor}=="091e", MODE="0666", OWNER="shakasan" # Garmin-Asus
SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", MODE="0666", OWNER="shakasan" # Google-Nexus
SUBSYSTEM=="usb", ATTR{idVendor}=="201E", MODE="0666", OWNER="shakasan" # Haier
SUBSYSTEM=="usb", ATTR{idVendor}=="109b", MODE="0666", OWNER="shakasan" # Hisense
SUBSYSTEM=="usb", ATTR{idVendor}=="0bb4", MODE="0666", OWNER="shakasan" # HTC
SUBSYSTEM=="usb", ATTR{idVendor}=="12d1", MODE="0666", OWNER="shakasan" # Huawei
SUBSYSTEM=="usb", ATTR{idVendor}=="8087", MODE="0666", OWNER="shakasan" # Intel
SUBSYSTEM=="usb", ATTR{idVendor}=="24e3", MODE="0666", OWNER="shakasan" # K-Touch
SUBSYSTEM=="usb", ATTR{idVendor}=="2116", MODE="0666", OWNER="shakasan" # KT Tech
SUBSYSTEM=="usb", ATTR{idVendor}=="0482", MODE="0666", OWNER="shakasan" # Kyocera
SUBSYSTEM=="usb", ATTR{idVendor}=="17ef", MODE="0666", OWNER="shakasan" # Lenovo
SUBSYSTEM=="usb", ATTR{idVendor}=="1004", MODE="0666", OWNER="shakasan" # LG
SUBSYSTEM=="usb", ATTR{idVendor}=="22b8", MODE="0666", OWNER="shakasan" # Motorola
SUBSYSTEM=="usb", ATTR{idVendor}=="0e8d", MODE="0666", OWNER="shakasan" # MTK
SUBSYSTEM=="usb", ATTR{idVendor}=="0409", MODE="0666", OWNER="shakasan" # NEC
SUBSYSTEM=="usb", ATTR{idVendor}=="2080", MODE="0666", OWNER="shakasan" # Nook
SUBSYSTEM=="usb", ATTR{idVendor}=="0955", MODE="0666", OWNER="shakasan" # Nvidia
SUBSYSTEM=="usb", ATTR{idVendor}=="2257", MODE="0666", OWNER="shakasan" # OTGV
SUBSYSTEM=="usb", ATTR{idVendor}=="10a9", MODE="0666", OWNER="shakasan" # Pantech
SUBSYSTEM=="usb", ATTR{idVendor}=="1d4d", MODE="0666", OWNER="shakasan" # Pegatron
SUBSYSTEM=="usb", ATTR{idVendor}=="0471", MODE="0666", OWNER="shakasan" # Philips
SUBSYSTEM=="usb", ATTR{idVendor}=="04da", MODE="0666", OWNER="shakasan" # PMC-Sierra
SUBSYSTEM=="usb", ATTR{idVendor}=="05c6", MODE="0666", OWNER="shakasan" # Qualcomm
SUBSYSTEM=="usb", ATTR{idVendor}=="1f53", MODE="0666", OWNER="shakasan" # SK Telesys
SUBSYSTEM=="usb", ATTR{idVendor}=="04e8", MODE="0666", OWNER="shakasan" # Samsung
SUBSYSTEM=="usb", ATTR{idVendor}=="04dd", MODE="0666", OWNER="shakasan" # Sharp
SUBSYSTEM=="usb", ATTR{idVendor}=="054c", MODE="0666", OWNER="shakasan" # Sony
SUBSYSTEM=="usb", ATTR{idVendor}=="0fce", MODE="0666", OWNER="shakasan" # Sony Ericsson
SUBSYSTEM=="usb", ATTR{idVendor}=="0fce", MODE="0666", OWNER="shakasan" # Sony Mobile Communications
SUBSYSTEM=="usb", ATTR{idVendor}=="2340", MODE="0666", OWNER="shakasan" # Teleepoch
SUBSYSTEM=="usb", ATTR{idVendor}=="0930", MODE="0666", OWNER="shakasan" # Toshiba
SUBSYSTEM=="usb", ATTR{idVendor}=="19d2", MODE="0666", OWNER="shakasan" # ZTE
```

#### Ou de manière automatique, via un paquet

```shell
yay -S android-udev
```

### Clé FIDO-U2F Key-ID

Idem, pour avoir accès à la clé FIDO U2F via USB.

On créer le fichier `/etc/udev/rules.d/70-u2f.rules` et on ajoute

```shell
# this udev file should be used with udev 188 and newer\nACTION!="add|change", GOTO="u2f_end"

# Key-ID FIDO U2F
KERNEL=="hidraw*", SUBSYSTEM=="hidraw", ATTRS{idVendor}=="096e", ATTRS{idProduct}=="0850|0880", TAG+="uaccess"

LABEL="u2f_end"
```

### Recharger les règles udev

Afin que ces règles `udev` soit prises en compte directement

```shell
sudo udevadm control --reload
```

## Apps liées au matos

### Logitech Unifying

Solaar permet de configurer les périphériques Logitech compatible Unifying.

```shell
yay -S solaar
```

### Logitech Gaming Mouses

Piper permet d'avoir accès aux différents paramètres des souris Gaming Logitech.

> **Remarque** : encore quelques bugs, mais vaut le détour et fait le taf ;)

```shell
yay -S piper
```

### Lecteur de carte

Le nécessaire pour utiliser les cartes à puces (carde d'identité, ...)

```shell
yay -S pcsc-tools ccid
```

### eID Middleware

Nécessaire pour les services fédéraux, etc en lignes en Belgique, le middleware eID.

```shell
yay -S eid-mw
```

### Ledger-Live pour Nano S/X

On installe la nouvelle application de gestion/config du wallet hardware Ledger Nano S/X.

#### Méthode 1

> **Remarque** : c'est valable pour n'importe quel dépôt tier ou `AUR`, il faut **toujours** vérifier minitieusement ce que vous installez. Et dans le cas de `AUR`, vérifier les sources. Il s'agit d'un Wallet matériel pour Crypto-monnaies, c'est d'autant plus sensible !!

Depuis `AUR`

```shell
yay -S aur/ledger-udev aur/ledger-live-bin
```

Ou depuis le dépot `community`

```shell
yay -S ledger-live-bin
```

#### Méthode 2 (officielle)

On télécharge l'`AppImage` officielle via cette URL : <https://download-live.ledger.com/> et on l'install via `AppImageLauncher`.

> **Remarque** : je vous conseille de TOUJOURS télécharger ET vérifier le contenu d'un script AVANT de l'exécuter, comme c'est le cas dans la commande ci-dessous.

On install ensuite les règles `udev` depuis le dépôt github officiel de Ledger comme ceci :

```shell
wget -q -O - https://raw.githubusercontent.com/LedgerHQ/udev-rules/master/add_udev_rules.sh | sudo bash
```

Ou bien depuis `AUR` :

```shell
yay -S aur/ledger-udev
```

### Corsair

`ckb-next` permet de gérer les périphériques Corsair (touches spécials, rgb, ...)

Depuis AUR

```shell
yay -S ckb-next
```

Et démarrer/activer le service au démarrage

```shell
sudo systemctl start ckb-next-daemon.service
sudo systemctl enable ckb-next-daemon.service
```

### Iscan for EPSON V500 Photo

Application + 'driver' pour le scanner Epson V500 Photo

```shell
yay -S iscan-for-epson-v500-photo
```

# Apps

L'installation de "base" étant terminée, passons à l'ajout des applications.

> **Astuce** : si vous voulez des infos sur un paquet en particulier, en résumé `yay -Ss <nom_du_paquet>` et en détaillé `yay -Si <nom_du_paquet>`.

## Utilitaires

Depuis les dépôts et/ou AUR

```shell
yay -S detox qt5-styleplugins clamav clamtk acetoneiso2 alltray aria2 htop bmon screen bleachbit copyq dialog dosbox fcrackzip fdupes figlet guake idle3-tools mupdf mupdf-tools pdfcrack pdfgrep psensor pv screenfetch neofetch ulauncher terminator tilda pandoc stress odt2txt strace tilix etcher-bin lm_sensors pdfmixtool libva-utils xdotool tmsu-bin xcalib qtqr woeusb spectre-meltdown-checker inxi cryptkeeper s-tui linpack nano-syntax-highlighting kdocker rpi-imager duf iotop vorta edid-decode-git hw-probe mc sct fwupd nvtop qt5ct qt6ct geekbench sg3_utils nvme-cli smartmontools
```

Depuis `Flatpak`

```shell
sudo flatpak install com.github.tchx84.Flatseal
```

Depuis `NPM`

```shell
npm i -g coinmon nativefier
```

Depuis `PIP`

```shell
pipx install tldr
pipx install cheat
pipx install md2pdf
```

## Multimédia

Depuis des dépôts et/ou AUR

```shell
yay -S quodlibet vlc mpv xsane shotwell simplescreenrecorder lib32-simplescreenrecorder asunder audacious audacious-plugins audacity avidemux-cli avidemux-qt blender cheese cuetools darktable peek shotcut flacon gpick gpicview guvcview handbrake handbrake-cli hugin inkscape kodi krita milkytracker mkvtoolnix-cli mkvtoolnix-gui moc openshot picard pitivi rawtherapee shntool smplayer smplayer-{skins,themes} soundconverter id3v2 youtube-dl mac libdvdcss celluloid vidcutter font-manager blanket birdfont gcolor3 green-recorder xnviewmp-system-libs indicator-sound-switcher gifcurry flameshot webp-pixbuf-loader upscayl-appimage converter annotator
```

Depuis `Flatpak`

```shell
sudo flatpak install flathub com.spotify.Client com.github.marinm.songrec org.gnome.gitlab.YaLTeR.VideoTrimmer org.flozz.yoga-image-optimizer io.github.jliljebl.Flowblade
```

Depuis `PIP`

```shell
pipx install qobuz-dl
```

## Gimp + plugins

Depuis les dépôts et/ou AUR

```shell
yay -S gimp gimp-{help-fr,nufraw,plugin-gmic} gimp-{plugin-refocusit,plugin-pandora,plugin-layers-to-divs,plugin-export-layers}
```

## OBS

Depuis les dépôts et/ou AUR

```shell
yay -S obs-studio obs-websocket-bin obs-cli obs-websocket-compat
```

## eBook

Depuis les dépôts et/ou AUR

```shell
yay -S calibre sigil fbreader gnome-epub-thumbnailer bookworm foliate
```

## Graveur CD/DVD/BR

Depuis les dépôts et/ou AUR

```shell
yay -S brasero k3b xfburn
```

## Bureautique

Depuis les dépôts et/ou AUR

```shell
yay -S libreoffice-still libreoffice-still-fr scribus mate-calc
```

Depuis `Flatpak`

```shell
sudo flatpak install flathub com.jgraph.drawio.desktop org.nickvision.money
```

## Internet, Web

Depuis les dépôts et/ou AUR

```shell
yay -S firefox firefox-i18n-fr chromium slack-desktop thunderbird thunderbird-i18n-fr nextcloud-client digikam discord filezilla google-chrome hexchat icedtea-web links mumble teamspeak3 mypaint opera opera-ffmpeg-codecs pidgin pidgin-{libnotify,otr} purple-{facebook,plugin-pack,skypeweb} quiterss syncthing syncthing-gtk telegram-{desktop,qt} vivaldi vivaldi-ffmpeg-codecs skypeforlinux-stable zoom liferea cawbird signal-desktop caprine newsflash brave-browser element-desktop whalebird-bin tootle birdtray telegram-purple microsoft-edge-stable-bin torbrowser-launcher gallery-dl teams ferdium-bin hamsket-bin tangram webapp-manager
```

Depuis `Flatpak`

```shell
sudo flatpak install flathub com.microsoft.Teams com.github.IsmaelMartinez.teams_for_linux net.jami.Jami
```

Workaround pour Discord quand le nouveau paquet n'est pas encore dispo

Editer `~/.config/discord/settings.json` et ajouter ceci au fichier JSON :

```json
  "SKIP_HOST_UPDATE": true
```

## Réseau

Depuis les dépôts et/ou AUR

```shell
yay -S aircrack-ng ngrep networkmanager-openvpn dsniff wireshark-qt wireshark-cli iftop iptraf-ng nethogs whois keychain x11-ssh-askpass nmap bind-tools bluez vinagre remmina traceroute remmina-plugin-rdesktop sshs iperf3 aur/enum4linux enum4linux-ng-git ifmetric ipmitool ipcalc dnslookup bind-tools
```

Depuis `NPM`

```shell
npm i -g whatismyip
```

Depuis `PIP`

```shell
pipx install speedtest-cli
```

## Thunar + plugins

Depuis les dépôts et/ou AUR

```shell
yay -S thunar thunar-{archive-plugin,media-tags-plugin,volman}
```

## Wine

Depuis les dépôts et/ou AUR

```shell
yay -S wine winetricks playonlinux
```

## CAD

Depuis les dépôts et/ou AUR

```shell
yay -S kicad kicad-library kicad-library-3d librecad freecad
```

## AI

Depuis `Flatpak`

```shell
sudo flatpak install flathub io.github.Bavarder.Bavarder
```

## Jeux

### Jeux - Utilitaires

Depuis les dépôts et/ou AUR

```shell
yay -S joyutils lutris gnutls lib32-gnutls steam gamemode lib32-gamemode bottles
```

Et `protonup-qt` pour gérer les releases de Proton-GE pour Steam

Depuis `Flatpak`

```shell
sudo flatpak install flathub net.davidotek.pupgui2
```

#### Pour les joueurs de WoW

##### Battle.net via Lutris

Dépendances pour que Battle.net fonctionne

```shell
yay -S lib32-gnutls lib32-libldap lib32-libgpg-error lib32-sqlite lib32-libpulse lib32-alsa-plugins
```

> **Remarque** : pensez à désactiver l'accélération matérielle en cas de black screen via les paramètres de Battle.net

##### WowUp

Via une AppImage : <https://github.com/WowUp/WowUp/releases>

Ou un paquet AUR qui va build wowup :

```shell
yay -S wowup
```

### Quelques Jeux

Depuis les dépôts et/ou AUR

```shell
yay -S 0ad
```

Depuis `Flatpak`

```shell
sudo flatpak install flathub org.srb2.SRB2
```

## Gadgets

Depuis les dépôts et/ou AUR

```shell
yay -S no-more-secrets-git
```

## Perso

Depuis les dépôts et/ou AUR

```shell
yay -S wpfind
```

# Customization

Un système qui a du style, ça n'est pas le plus fondamental, mais quand même... ça fais du bien ;)

## Thèmes et icones

On ajoute quelques thèmes GTK et icones

Depuis les dépôts et/ou AUR

```shell
yay -S arc-{gtk-theme,solid-gtk-theme} matcha-gtk-theme numix-{gtk-theme-git,icon-theme-git} arc-icon-theme kvantum-theme-matcha kvantum-theme-orchis-git orchis-theme elementary-icon-theme kvantum-theme-materia materia-gtk-theme whitesur-gtk-theme-git yaru-gtk-theme aritim-dark-gtk-git qogir-gtk-theme-git adw-gtk3 adwaita-qt5 adwaita-qt6
```

## Widgets

### Conky

```shell
yay -S conky conky-manager
```

## Wallpapers

### HydraPaper

Cet utilitaire permet de configurer des wallpapers différents en configuration multi-écran OU de configurer un wallpaper couvrant l'ensemble de ceux-ci. Contrairement à `Nitrogen`, on ne doit pas sacrifier ses icones de bureau ;)

```shell
yay -S hydrapaper
```

## Docks

### Plank

Dock léger et bien fini en provenance de ElementaryOS.

```shell
yay -S plank
```

# Configuration / Customization

Il est maintenant temps d'un peu "fine tuner" comme dirait certains ;)

## SSD

On va activer le TRIM pour les disques SSD

```shell
sudo systemctl enable fstrim.timer
```

## Swappiness

Si l'on dispose de suffisament de RAM, on peut limiter l'usage du swap et donc ménager un peu le SSD

Dans `/etc/sysctl.d/99-sysctl.conf`, on ajoute :

```shell
vm.swappiness=10
```

## Mise à jours du Microcode du CPU

> **Remarque** : Cette étape est optionnelle, mais fortement recommandée (failles spectre, meltdown, ...) car elle corrige des failles majeures.

### On installe le nécessaire : INTEL

```shell
yay -S iucode-tool intel-ucode
```

Et on met à jour la configuration de `GRUB` pour qu'il en tienne compte

```shell
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

### On installe le nécessaire : AMD

```shell
yay -S iucode-tool amd-ucode
```

Et on met à jour la configuration de `GRUB` pour qu'il en tienne compte

```shell
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## Firewall

On installe les outils graphique/cli

```shell
yay -S ufw gufw
```

Si vous utilisez `syncthing`, on ouvre les ports pour celui-ci

```shell
sudo ufw allow syncthing
```

De même si vous faites l'install depuis une connexion SSH, on ouvre les ports SSH également. Et c'est important de le faire **avant** d'activer le firewall sous peine de se retrouver à la porte ;)

```shell
sudo ufw allow ssh
```

Et on active le firewall au boot.

```shell
sudo systemctl enable --now ufw
sudo ufw enable
```

## Unbound (cache DNS)

Pour accélérer la résolution DNS, on installe un cache DNS local

```shell
yay -S unbound
```

Et on active le service au boot

```shell
sudo systemctl start unbound.service
sudo systemctl enable unbound.service
```

## .bashrc

Dans `~/.bashrc`, on ajoute :

Un theme plus sympa pour `mocp`

```shell
alias mocp='mocp -T "darkdot_theme"'
```

Des couleurs et un affichage plus complet de la commande `ls`

```shell
alias ll='ls -lah --color'
```

On affiche `neofetch` à l'ouverture d'un terminal

```shell
neofetch
```

On configure `nano` comme éditeur cli par défaut

```shell
export EDITOR=nano
```

## Terminal par défaut

> **Remarque** : nécessaire pour certaines applications comme `rclonebrowser` par exemple

Dans `/etc/environment`, on ajoute

```shell
TERMINAL=/usr/bin/tilix
```

## Partages SMB/CIFS dans /etc/fstab

Exemple de configuration dans `/etc/fstab` pour monter automatiquement au boot un dossier partagé sur son NAS avec smb/cifs

```shell
//<VOTRE_ADRESSE_IP>/<NOM_DU_DOSSIER_PARTAGE>     /mnt/<NOM_DU_POINT_DE_MONTAGE_LOCAL>          cifs   vers=3.0,x-systemd.automount,x-systemd.idle-timeout=1min,_netdev,credentials=/etc/smbcreds,rw,iocharset=utf8,uid=1000,gid=1000 0 0
```

> **Remarque** : le `uid` et le `gid` sont a adapter si besoin à ceux de votre utilisateur

Et on regénère les règles `systemd` pour le nouveau point de montage smb/cifs

```shell
sudo systemctl daemon-reload
sudo systemctl restart remote-fs.target
sudo systemctl restart local-fs.target
```

## Grappe Raid 0 pour Logithèque Steam

Cet exemple décrit un RAID 0 (logiciel, via mdadm) de 2 disques : /dev/sdb et /dev/sdc, pour un volume monté via fstab.

### Considération Performance et Sécurité

> **Remarque** : la taille de chunk est de 512 par défaut et correspondais à mon besoin. La taille de celui-ci + les caractéristiques stride/stripe du système de fichier ext4 vont influencer fortement les performances en fonction de l'usage que vous aller faire de la grappe : jeux, documents, bases de données, .....
>
> **Astuce** : lisez cette section raid 0 avant de vous lancer pour l'adapter à vos besoins.
>
> **Doc Arch** : je vous invite à checker la doc de Arch pour plus de détails : [Arch Wiki : RAID](https://wiki.archlinux.org/index.php/RAID)
>
> **Remarque 2** : sans que ça devienne une doc sur le raid, je rappel que le Raid 0, c'est plus de performance mais aucune sécurité des données. Dans mon cas, il s'agit de mes jeux Steam, au pire, je dois simplement les re-downloader ;-)

### Préparation des disques

Pour éviter certains soucis un peu étranges... je vous conseil de faire un petit nettoyage des 2 disques

> **Remarque** : suivant la taille des disques et le type (HDD / SSD), cela peut prendre un certain temps

```shell
sudo dd if=/dev/zero of=/dev/sdb
sudo dd if=/dev/zero of=/dev/sdb
```

### Config du raid

On modifie `/etc/mkinitcpio.conf` et on ajoute `mdadm_udev` dans `HOOKS` juste avant `filesystems`:

```shell
HOOKS="base udev autodetect modconf block keyboard keymap plymouth plymouth-encrypt openswap resume mdadm_udev filesystems"
```

On créer le raid 0

> **Remarque** : cfr la partie performance et sécurité pour le paramètre `chunk`

```shell
sudo mdadm --create --verbose --level=0 --raid-devices=2 /dev/md0 /dev/sdb /dev/sdc
```

On créer la config en l'ajoutant à `/etc/mdadm.conf`

```shell
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm.conf
```

Ensuite, on créer un système de fichier sur le raid fraîchement créé

> **Remarque** : cfr la partie performance et sécurité pour les paramètres `stride` et `stripe`

```shell
sudo mkfs.ext4 -v -L monVolRaid -b 4096 -E stride=128,stripe-width=256 /dev/md0
```

On ajoute un point de montage dans `/etc/fstab`

```shell
/dev/md0  /mnt/monVolRaid  ext4  defaults,noatime 0 2
```

On monte le point de montage

```shell
sudo mount /mnt/monVolRaid
```

On re-genère le `mkinitpcio`

```shell
sudo mkinitpcio -P
```

### Vérification : raid, montage, [...]

```shell
$ lsblk
NAME                                          MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
sdb                                             8:16   1   1,8T  0 disk
└─md0                                           9:0    0   3,6T  0 raid0 /mnt/monVolRaid
sdc                                             8:32   1   1,8T  0 disk
└─md0                                           9:0    0   3,6T  0 raid0 /mnt/monVolRaid
```

```shell
$ cat /proc/mounts
[...]
/dev/md0 /mnt/mySSDRaid4T ext4 rw,noatime,stripe=256 0 0
[...]
```

```shell
$ sudo mdadm --detail /dev/md0
/dev/md0:
           Version : 1.2
     Creation Time : Fri Jan  8 18:42:20 2021
        Raid Level : raid0
        Array Size : 3906764800 (3725.78 GiB 4000.53 GB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

       Update Time : Fri Jan  8 18:42:20 2021
             State : clean
    Active Devices : 2
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 0

        Chunk Size : 512K

Consistency Policy : none

              Name : manjaro:0  (local to host manjaro)
              UUID : b71cae0e:5d8cc9ed:f56af4ee:eaf9a8b6
            Events : 0

    Number   Major   Minor   RaidDevice State
       0       8       16        0      active sync   /dev/sdb
       1       8       32        1      active sync   /dev/sdc
```

```shell
$ cat /proc/mdstat
Personalities : [raid0]
md0 : active raid0 sdb[0] sdc[1]
      3906764800 blocks super 1.2 512k chunks

unused devices: <none>
```

```shell
$ df -h
Sys. de fichiers Taille Utilisé Dispo Uti% Monté sur
[...]
/dev/md0           3,6T    984G  2,5T  29% /mnt/monVolRaid
[...]
```

## Environnement QT5/QT6

**TODO** : rework !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

Utilisation de l'utilitaire Kvantum

![kvantum.jpeg](/pics/kvantum.jpeg)

> **Remarque** : en lieu et place de `qt5ct` + variable `QT_QPA_PLATFORMTHEME=qt5ct`

# Outils de Dev

Pour cette partie, je passe certaines explications étant donné le publique cible ^^

## Outis de base

```shell
yay -S gdb ghex jq meld
```

## Langages

### Javascript

#### NVM

```shell
yay -S nvm
```

#### Yarn

```shell
yay -S yarn
```

Ou bien

```shell
npm i -g yarn
```

#### Gulp

```shell
yay -S gulp
```

#### Angular CLI

```shell
npm i -g @angular/cli
```

Et utiliser `yarn` à la place de `npm` (AngularCLI >= 6)

```shell
ng config -g cli.packageManager yarn
```

#### Ionic & Cordova

```shell
npm i -g ionic cordova
```

> A besoin d'être mis à jour ;) :')

### GO

```shell
yay -S go
```

### Lua

```shell
yay -S lua luajit
```

### PHP

```shell
yay -S composer php
```

### Python

```shell
yay -S python-{pyqt5,pyqt6,pipenv} tk
```

```shell
pipx install pylint
pipx install autopep8
pipx install pycodestyle
pipx install flake8
pip install --user pipdeptree
pip install --user pip-autoremove
```

### QT5/QT6

```shell
yay -S qtcreator qt5-tools
```

### GTK

```shell
yay -S glade gtk4 python-gobject gtk3 gobject-introspection
```

### Ruby

```shell
yay -S ruby
```

**TODO** : rework ?????????????????????????????????????????????????????

Dans `~/.bashrc`, ajouter

```shell
PATH=$PATH:~/.gem/ruby/2.7.0/bin
```

### Rust

```shell
yay -S rustup
rustup default stable
```

### Java

Installer OpenJDK 18

```shell
yay -S jre-openjdk-headless jre-openjdk
```

Utiliser OpenJDK 18 par défaut

```shell
sudo archlinux-java set java-18-openjdk
```

## Base de données

### MongoDB + Compass

#### Client

```shell
yay -S mongodb-compass
```

#### Server

```shell
yay -S mongodb-bin mongodb-tools-bin
```

Pour démarrer le serveur

```shell
sudo systemctl start mongodb.service
```

Et si besoin, le démarrer au boot

```shell
sudo systemctl enable mongodb.service
```

### MariaDB

#### Serveur

On install MariaDB

```shell
yay -S mariadb
sudo mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
```

On démarre le service

```shell
sudo systemctl start mariadb.service
```

On effectue la post installation

```shell
sudo mysql_secure_installation
```

Et éventuellement on démarre MariaDB au boot si besoin

```shell
sudo systemctl enable mariadb.service
```

#### Client

```shell
yay -S mysql-workbench
```

### SQLite + SQLiteBrower

```shell
yay -S sqlite sqlitebrowser
```

## IDE

### Android Studio

```shell
yay -S android-tools android-studio
```

OU utiliser l'app Toolbox (conseillé)

```shell
yay -S jetbrains-toolbox
```

#### Android SDK Tools

Pour avoir accès aux outils du SDK : adb, fastboot, ... (installés via Android Studio et le SDK Manager)

Ajouter cette ligne à votre `.bashrc`

```shell
PATH=$PATH:/home/shakasan/Android/Sdk/platform-tools
```

### Arduino IDE

```shell
yay -S arduino arduino-docs arduino-avr-core
sudo usermod -a -G uucp shakasan
sudo usermod -a -G lock shakasan
```

### Bluefish

```shell
yay -S bluefish
```

### Codeblocks

```shell
yay -S codeblocks
```

### Intellij-idea (CE)

```shell
yay -S intellij-idea-community-edition
```

OU utiliser l'app Toolbox (conseillé)

```shell
yay -S jetbrains-toolbox
```

### Lazarus

```shell
yay -S lazarus-qt5 gdb
```

### Notepadqq

```shell
yay -S notepadqq
```

### Pulsar (Atom fork)

```shell
yay -S pulsar-bin
```

### PyCharm (CE)

```shell
yay -S pycharm-community-edition
```

OU utiliser l'app Toolbox (conseillé)

```shell
yay -S jetbrains-toolbox
```

### Visual Studio Code

```shell
yay -S visual-studio-code-bin
```

## Outils

### Ansible

```shell
yay -S ansible ansible-lint
```

### Joplin

```shell
yay -S joplin-desktop
```

### DBeaver

```shell
yay -S dbeaver
```

### Docker

```shell
yay -S docker docker-compose
```

On créer un groupe `docker` et on y ajoute son user

> **Remarque** : après cette étape, il est fortement recommandé de logout/login complètement

```shell
sudo groupadd docker
sudo usermod -aG docker <votre_user>
```

On check si `docker` tourne

```shell
sudo systemctl status docker
```

Et éventuellement on lance `docker` et on l'ajoute au boot si nécessaire

```shell
sudo systemctl start docker
sudo systemctl enable docker
```

### Portainer CE

```shell
docker volume create portainer_data
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```

### git

```shell
yay -S git
```

### packagecloud cli tool

```shell
gem install package_cloud
```

### Postman

```shell
flatpak install flathub com.getpostman.Postman
```

### Terraform

```shell
yay -S terraform
```

### Umbrello

```shell
yay -S umbrello
```

### Vagrant

```shell
yay -S vagrant
```

### VirtualBox

```shell
yay -S virtualbox virtualbox-host-dkms
```

Suivi d'un reboot ou bien

```shell
sudo modprobe vboxdrv
```

### Wordpress

```shell
yay -S wpscan
```

### Zeal

```shell
yay -S zeal
```

### Figma

Via une AppImage : <https://github.com/Figma-Linux/figma-linux/releases>

### bat

Un remplacement a `cat`, tout aussi simple, mais avec de vraies améliorations visuelles.

```shell
yay -S bat
```

On génère la config

```shell
bat --generate-config-file
```

On édite `~/.config/bat/config` et on ajoute/modifie :

- theme OnHalfDark pour les terminaux à fond sombre
- style full pour avoir les numéros de ligne, les entêtes et la grille

```toml
# This is `bat`s configuration file. Each line either contains a comment or
# a command-line option that you want to pass to `bat` by default. You can
# run `bat --help` to get a list of all possible configuration options.

# Specify desired highlighting theme (e.g. "TwoDark"). Run `bat --list-themes`
# for a list of all available themes

--theme="OneHalfDark"

# Enable this to use italic text on the terminal. This is not supported on all
# terminal emulators (like tmux, by default):
#--italic-text=always

# Uncomment the following line to disable automatic paging:
#--paging=never

# Uncomment the following line if you are using less version >= 551 and want to
# enable mouse scrolling support in `bat` when running inside tmux. This might
# disable text selection, unless you press shift.
#--pager="less --RAW-CONTROL-CHARS --quit-if-one-screen --mouse"

# Syntax mappings: map a certain filename pattern to a language.
#   Example 1: use the C++ syntax for Arduino .ino files
#   Example 2: Use ".gitignore"-style highlighting for ".ignore" files
#--map-syntax "*.ino:C++"
#--map-syntax ".ignore:Git Ignore"

--style="full"
```

Puis on modifie son `.bashrc` et on ajoute :

- loader la config
- remplacer via alias `cat` par `bat`

```shell
export BAT_CONFIG_PATH="~/.config/bat/config"
export cat='bat'
```

# Info & Licence

- Doc réalisée par Francois B (Makoto)
- Publiée sous les termes de la licence Creative Commons [CC-BY-NC-SA](https://creativecommons.org/licenses/by-nc-sa/2.0/be/)

[![cc-by-nc-sa](https://licensebuttons.net/l/by-nc-sa/2.0/be/88x31.png)](https://creativecommons.org/licenses/by-nc-sa/2.0/be/)
