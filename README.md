# ❄️ NixOS + Hyprland Dotfiles

Мой рабочий декларативный сетап NixOS с минималистичным Hyprland на Wayland, заточенный под сетевую разработку, обход блокировок и максимальную производительность на **NVIDIA**.

![NixOS](https://img.shields.io/badge/NixOS-26.05-blue?logo=nixos)
![Hyprland](https://img.shields.io/badge/WM-Hyprland-00F5D4?logo=hyprland)
![NVIDIA](https://img.shields.io/badge/GPU-NVIDIA_Proprietary-76B900?logo=nvidia)

---

##  Особенности конфига

* **Desktop Environment**: Hyprland (Wayland) с кастомными анимациями, фиксом под NVIDIA (`no_hardware_cursors`) и удобными биндами.
* **Network & DPI Bypass**:
  * Встроенный модуль **Zapret** (`nfqws`) с кастомными флагами десинхронизации (fake + multisplit).
  * Собственный **Rust DoH Resolver** (`doh-stub`) в виде системного демона `systemd`.
  * **WSTunnel over SOCKS5** — туннелирование SSH через WebSocket прямо из коробки.
  * **v2rayN** — пользовательский `systemd`-сервис для автоматического фонового запуска GUI-клиента v2rayN при старте графической сессии Hyprland.
* **Nix-LD**: Настроена полная совместимость с динамическими бинарниками (C/C++, Rust, Electron, GTK, Xorg), скомпилированными не под NixOS.
* **Тулчейн и Софт**:
  * **Dev**: Rust (`rustc`, `cargo`, `clippy`), Cursor AI, Git, Wireshark (с правами без root).
  * **Mobile / Hardware**: `android-tools` (ADB/Fastboot), `scrcpy`, `usbutils`.
  * **System**: `btop` с поддержкой CUDA, `yazi`, `kitty`, `pavucontrol`.


---

##  Аппаратное обеспечение (Hardware)

* **CPU**: Intel Core i3-12100F
* **GPU**: NVIDIA GeForce RTX 3060

---

## Запуск сессии

Дисплей-менеджеры (SDDM/GDM) отключены из-за извечных приколов NVIDIA + Wayland. 

Сессия поднимается вручную прямо из **TTY**:
```bash
Hyprland
