# ❄️ NixOS + Hyprland Dotfiles

Мой рабочий декларативный сетап NixOS с минималистичным Hyprland на Wayland, заточенный под сетевую разработку, кросс-компиляцию ядра, обход DPI и максимальную производительность на **NVIDIA**.

![NixOS](https://img.shields.io/badge/NixOS-26.05-blue?logo=nixos)
![Hyprland](https://img.shields.io/badge/WM-Hyprland-00F5D4?logo=hyprland)
![NVIDIA](https://img.shields.io/badge/GPU-NVIDIA_Proprietary-76B900?logo=nvidia)

---

##  Особенности конфига

* **Desktop Environment**: Hyprland (Wayland) с оптимизациями под NVIDIA (`nvidia-drm`, Ozone WL), баром `waybar`, уведомлениями `dunst`, меню `rofi` и обоями через `awww`.
* **Browser**: **Firefox Nightly** (`latest.firefox-nightly-bin`), подключенный декларативно через оверлей `nixpkgs-mozilla`.
* **Network & DPI Bypass**:
  * Встроенный модуль **Zapret** (`nfqws`) с кастомными флагами десинхронизации (`fake,multisplit`).
  * Собственный **Rust DoH Resolver** (`doh-stub`) в виде системного демона `systemd` на порту 53.
  * **WSTunnel over SOCKS5** — туннелирование SSH через WebSocket в виде системной службы.
* **Nix-LD**: Настроена полная совместимость с динамическими бинарниками (C/C++, Rust, Electron, GTK, Xorg), скомпилированными не под NixOS.
* **Тулчейн и Разработка**:
  * **Kernel & Embedded**: `gcc`, `clang`, `gnumake`, `flex`, `bison`, `libelf`, `ncurses`, `dtc`, а также кросс-компиляторы под `aarch64` и `armv7l`.
  * **Dev**: Rust (`rustc`, `cargo`, `clippy`), VS Code / Cursor AI, Git, Wireshark (с правами без root).
  * **Mobile / Hardware**: `android-tools` (ADB/Fastboot), `scrcpy`, `usbutils`.
  * **System**: `btop` с поддержкой CUDA, `yazi`, `kitty`, `pavucontrol`, `flatpak`.

---

##  Аппаратное обеспечение (Hardware)

* **CPU**: Intel Core i3-12100F
* **GPU**: NVIDIA GeForce RTX 3060

---

##  Запуск сессии

Дисплей-менеджеры (SDDM/GDM) отключены из-за извечных приколов NVIDIA + Wayland. 

Сессия поднимается вручную прямо из **TTY**:
```bash
Hyprland
