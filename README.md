# zfs-auto-snapshot

This is an Ansible role for installing and configuring [zfs-auto-snapshot](https://github.com/zfsonlinux/zfs-auto-snapshot).

## Requirements

- `cron` installed
- Python (for ansible)
- ZFS installed and `zfs` command in `$PATH`
- Root (or sudo) access
- A `global_cache_dir` variable where the git repository will be cloned to on the local machine
- The ZFS datasets need to exist, they won't get created
- Should run on any system that runs POSIX shell. Though tested only on Debian.

## Usage

Example usage:
```yaml
- hosts: zfs-servers
  vars:
    zfs_autosnap_datasets:
      tank/data/home:
        frequent: true
        daily: true
        monthly: true
      tank/data/public:
        hourly: true
        daily: true
  roles:
    - { role: zfs-auto-snapshot }
```

## Parameters

Advanced usage (with default values):
```yaml
- hosts: zfs-servers
  vars:
    zfs_autosnap_datasets:
      # No worries, the actual default is empty.
      tank/example/dataset:
        enabled: true
        frequent: false
        hourly: false
        daily: false
        weekly: false
        monthly: false

    zfs_autosnap_version: "upstream/1.2.4"

    # How often the 'frequent' snapshots occur. Can be anything from 1 to 59
    zfs_autosnap_keep_frequent_interval: 15

    # Count how many snapshots we can have before deleting.
    zfs_autosnap_keep_frequent: 4
    zfs_autosnap_keep_hourly: 24
    zfs_autosnap_keep_daily: 31
    zfs_autosnap_keep_weekly: 8
    zfs_autosnap_keep_monthly: 12

    # Enable different labels by default
    zfs_autosnap_default_enable: True
    zfs_autosnap_default_frequent: False
    zfs_autosnap_default_hourly: False
    zfs_autosnap_default_daily: False
    zfs_autosnap_default_weekly: False
    zfs_autosnap_default_monthly: False

    # The arguments to append to the script.
    zfs_autosnap_args: "--quiet --syslog --default-exclude"

  roles:
    - { role: zfs-auto-snapshot }
```

## Gotchas

The script provided by [zfs-auto-snapshot](https://github.com/zfsonlinux/zfs-auto-snapshot) by default snapshots **ALL** datasets it can find. Personally, I find this rather dangerous in terms of space consumption. So this playbook inverts the default behavior by adding `--default-exclude` to the arguments. This makes it necessary to explicitly define and enable the datasets in your playbook. You can change this back by removing the `--default-exclude` flag if you like (in which case you would define the datasets that should **not** get snapshot).

## Contributions

### Bugs

Please identify whether the behavior is caused by [zfs-auto-snapshot](https://github.com/zfsonlinux/zfs-auto-snapshot) itself before opening an issue here.

### Feature requests

Hmm, maybe. If I have time and motivation. Consider forking/+PR if you really need something more.

## Alternatives

zfs-auto-snapshot is simple and doesn't rely on additional dependencies other than cron.
If you prefer more control, check out [zfsnap](https://github.com/zfsnap/zfsnap) or [zfs-snap-manager](https://github.com/khenderick/zfs-snap-manager).
