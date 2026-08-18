archlinux-repomanager
======================

Enables optional Arch Linux community repositories on top of a vanilla
pacman install. Currently supports:

- **CachyOS** — adds the `cachyos` repo plus an optional CPU-optimized
  variant (e.g. `cachyos-v3`, `cachyos-v4`).
- **BlackArch** — runs the official BlackArch strap script to add the
  `blackarch` repo.

Each repository is opt-in and independent of the other, so you can enable
one, both, or neither.

Requirements
------------

- Arch Linux (or an Arch-based distro using pacman).
- Internet access to fetch keys/mirrorlists from the CachyOS mirror and
  the BlackArch strap script.

The CachyOS mirror (`archrepomanager_cachyos_mirror`) is the live, rolling
repo directory — not a dated/versioned snapshot. The role always scrapes
that directory listing to grab whatever keyring/mirrorlist package is
currently newest (`grep` + `tail -1`), so no package version or date ever
needs to be pinned in the URL.

Role Variables
---------------

| Variable | Default | Description |
|---|---|---|
| `archrepomanager_cachyos_enabled` | `false` | Install/enable the CachyOS repository. |
| `archrepomanager_cachyos_variant` | `"v4"` | CPU-optimized variant to enable alongside the base `cachyos` repo (`v3`, `v4`, `znver3`, `znver4`). Set to `""` to install only the base repo. |
| `archrepomanager_cachyos_mirrorlist_map` | `{znver3: v3, znver4: v4}` | Maps a variant to the mirrorlist package/tier that actually backs it. CachyOS only publishes `cachyos-v3-mirrorlist` and `cachyos-v4-mirrorlist` packages — the `znverN` repo variants (per-microarch tuning) reuse one of those instead of shipping their own file, per the [CachyOS optimized repos docs](https://wiki.cachyos.org/features/optimized_repos/). Variants not listed here (`v3`, `v4`, or any future tier) fall back to using their own name as the mirrorlist tier. |
| `archrepomanager_cachyos_keyserver` | `keyserver.ubuntu.com` | Keyserver used to fetch the CachyOS signing key. |
| `archrepomanager_cachyos_key` | `F3B607488DB35A47` | CachyOS signing key ID. |
| `archrepomanager_cachyos_mirror` | `https://mirror.cachyos.org/repo/x86_64/cachyos` | CachyOS package mirror used to fetch keyring/mirrorlist packages. |
| `archrepomanager_blackarch_enabled` | `false` | Install/enable the BlackArch repository. |
| `archrepomanager_blackarch_strap_url` | `https://blackarch.org/strap.sh` | URL of the BlackArch strap script. |

Dependencies
------------

None.

Example Playbook
------------------

```yaml
- hosts: archlinux_hosts
  become: true
  roles:
    - role: spoljarevic.archlinux-repomanager
      archrepomanager_cachyos_enabled: true
      archrepomanager_cachyos_variant: v4
      archrepomanager_blackarch_enabled: true
```

Only CachyOS, without the CPU-optimized variant:

```yaml
- hosts: archlinux_hosts
  become: true
  roles:
    - role: spoljarevic.archlinux-repomanager
      archrepomanager_cachyos_enabled: true
      archrepomanager_cachyos_variant: ""
```

CachyOS tuned for Zen 4/Zen 5 CPUs (`znver4`, backed by the `v4` mirrorlist):

```yaml
- hosts: archlinux_hosts
  become: true
  roles:
    - role: spoljarevic.archlinux-repomanager
      archrepomanager_cachyos_enabled: true
      archrepomanager_cachyos_variant: znver4
```

License
-------

MIT-0

Author Information
--------------------

Luca Matteo Špoljarević <foss@spoljarevic.sh>
