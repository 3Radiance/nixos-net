{ config, pkgs, ... }:

let
  mozillaOverlay = import (builtins.fetchTarball "https://github.com/mozilla/nixpkgs-mozilla/archive/master.tar.gz");
in
{
  imports = [
    ./hardware-configuration.nix
  ];

  # Подключение оверлея Mozilla
  nixpkgs.overlays = [ mozillaOverlay ];
  nixpkgs.config.allowUnfree = true;

  # =========================================================================
  # SYSTEM & BOOT
  # =========================================================================

  boot.loader.systemd-boot.enable = true;
  boot.loader.efi.canTouchEfiVariables = true;
  boot.kernelParams = [ "nvidia-drm.modeset=1" ];

  networking.hostName = "nixos";
  networking.networkmanager.enable = true;
  networking.networkmanager.dns = "none";
  networking.nameservers = [ "127.0.0.1" ];

  time.timeZone = "YOUR-TIME";

  i18n.defaultLocale = "en_US.UTF-8";
  i18n.extraLocaleSettings = {
    LC_ADDRESS = "ru_RU.UTF-8";
    LC_IDENTIFICATION = "ru_RU.UTF-8";
    LC_MEASUREMENT = "ru_RU.UTF-8";
    LC_MONETARY = "ru_RU.UTF-8";
    LC_NAME = "ru_RU.UTF-8";
    LC_NUMERIC = "ru_RU.UTF-8";
    LC_PAPER = "ru_RU.UTF-8";
    LC_TELEPHONE = "ru_RU.UTF-8";
    LC_TIME = "ru_RU.UTF-8";
  };

  # =========================================================================
  # USER & SECURITY
  # =========================================================================

  users.users."YOU-USER" = {
    isNormalUser = true;
    description = "YOU";
    extraGroups = [ "networkmanager" "wheel" "wireshark" "adbusers" "plugdev" ];
  };

  security.polkit.enable = true;
  security.rtkit.enable = true;
  services.gnome.gnome-keyring.enable = true;
  security.pam.services.YOUR-USER.enableGnomeKeyring = true;

  # =========================================================================
  # GRAPHICS & WAYLAND / HYPRLAND
  # =========================================================================

  hardware.graphics.enable = true;

  services.xserver.enable = false;
  services.xserver.videoDrivers = [ "nvidia" ];

  hardware.nvidia = {
    modesetting.enable = true;
    powerManagement.enable = false;
    powerManagement.finegrained = false;
    open = false;
    nvidiaSettings = true;
  };

  programs.hyprland = {
    enable = true;
    xwayland.enable = true;
  };

  xdg.portal = {
    enable = true;
    extraPortals = [ pkgs.xdg-desktop-portal-hyprland ];
  };

  environment.sessionVariables = {
    NIXOS_OZONE_WL = "1";
    LIBVA_DRIVER_NAME = "nvidia";
    GBM_BACKEND = "nvidia-drm";
    __GLX_VENDOR_LIBRARY_NAME = "nvidia";
    WLR_NO_HARDWARE_CURSORS = "1";
    HYPRCURSOR_ENABLED = "0";
    XDG_CURRENT_DESKTOP = "Hyprland";
    XDG_SESSION_TYPE = "wayland";
    XDG_SESSION_DESKTOP = "Hyprland";
    QT_QPA_PLATFORM = "wayland;xcb";
  };

  # =========================================================================
  # SOUND & PRINTING
  # =========================================================================

  services.printing.enable = true;
  services.pulseaudio.enable = false;
  services.pipewire = {
    enable = true;
    alsa.enable = true;
    alsa.support32Bit = true;
    pulse.enable = true;
  };

  # =========================================================================
  # PROGRAMS & SERVICES
  # =========================================================================

  programs.firefox.enable = true;
  programs.wireshark = {
    enable = true;
    package = pkgs.wireshark;
  };

  programs.thunar = {
    enable = true;
    plugins = with pkgs; [
      thunar-archive-plugin
      thunar-volman
    ];
  };

  services.gvfs.enable = true;
  services.udisks2.enable = true;

  # Flatpak
  services.flatpak.enable = true;

  systemd.services.flatpak-repo = {
    wantedBy = [ "multi-user.target" ];
    path = [ pkgs.flatpak ];
    script = ''
      flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
    '';
  };

  # Nix-LD для запуска сторонних динамических бинарников
  programs.nix-ld.enable = true;
  programs.nix-ld.libraries = with pkgs; [
    stdenv.cc.cc.lib
    openssl
    zlib
    libGL
    libx11
    libxrandr
    libxcursor
    libxinerama
    libxi
    libxxf86vm
    icu
    fontconfig
    freetype
    expat
    glib
    gtk3
    pango
    cairo
    atk
    gdk-pixbuf
    libpng
    libjpeg
    libtiff
    libwebp
    lcms2
    libxkbcommon
    mesa
    libxrender
    libxext
    libxdamage
    libxfixes
    libxcomposite
    libice
    libsm
  ];

  # =========================================================================
  # CUSTOM SYSTEMD SERVICES & NETWORK TUNNELS
  # =========================================================================

  # Zapret DPI bypass
  services.zapret = {
    enable = true;
    qnum = 300;
    params = [
      "--hostlist=/etc/zapret/hosts.txt"
      "--dpi-desync=fake,multisplit"
      "--dpi-desync-split-pos=1"
      "--dpi-desync-split-seqovl=681"
      "--dpi-desync-fooling=ts"
      "--dpi-desync-repeats=2"
      "--dpi-desync-fake-tls-mod=rnd,dupsid,sni=cloudflare.com"
      "--bind-fix4"
      "--bind-fix6"
    ];
  };

  # Локальный DoH-резолвер
  systemd.services.doh-stub = {
    description = "Custom Rust DoH Resolver";
    after = [ "network.target" ];
    wantedBy = [ "multi-user.target" ];
    serviceConfig = {
      ExecStart = "/YOUR/PATH/doh-stub-rust -p 53 -d https://1.1.1.1/dns-query";
      Restart = "always";
      RestartSec = "3s";
      User = "root";
    };
  };

  # Tunnel
  systemd.services.wstunnel-ssh = {
    description = "wstunnel SSH tunnel service";
    after = [ "network-online.target" ];
    wants = [ "network-online.target" ];
    wantedBy = [ "multi-user.target" ];
    environment = {
      HTTP_PROXY = "http://127.0.0.1:10808";
    };
    serviceConfig = {
      ExecStart = "${pkgs.wstunnel}/bin/wstunnel client --http-upgrade-path-prefix YOUR/PATH -L tcp://127.0.0.1:2222:127.0.0.1:YOUR-SSH-PORT wss://YOUR-DOMAIN";
      Restart = "always";
      RestartSec = "5s";
      User = "YOUR-USER";
    };
  };

  # =========================================================================
  # FONTS & PACKAGES
  # =========================================================================

  fonts.packages = with pkgs; [
    nerd-fonts.jetbrains-mono
    nerd-fonts.symbols-only
    font-awesome
  ];

  environment.variables = {
    C_INCLUDE_PATH = "${pkgs.openssl.dev}/include";
    LIBRARY_PATH = "${pkgs.openssl.out}/lib";
  };

  environment.systemPackages = with pkgs; [
    # Системные тулы
    git
    curl
    wget
    (btop.override { cudaSupport = true; })
    htop
    iptables
    iproute2
    unzip
    traceroute
    termius
    wstunnel
    vscode
    android-tools
    scrcpy
    usbutils
    yazi
    adwaita-icon-theme
    xfce4-settings
    file-roller
    polkit_gnome

    # Сборка ядра и разработка
    rustc
    cargo
    rustfmt
    clippy
    gnumake
    gcc
    clang
    flex
    bison
    bc
    elfutils
    binutils
    openssl
    openssl.dev
    pkgsCross.aarch64-multiplatform.buildPackages.gcc
    pkgsCross.armv7l-hf-multiplatform.buildPackages.gcc
    pkg-config
    libelf
    ncurses
    python3
    dtc

    # Браузер
    latest.firefox-nightly-bin

    # Hyprland окружение
    waybar
    rofi
    dunst
    awww
    kitty
    grim
    slurp
    swappy
    wl-clipboard
    pavucontrol
  ];

  system.stateVersion = "26.05";
}
