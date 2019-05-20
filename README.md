# Ansible Role: BorgBackup

Tis is an Ansible Role that sets up automated remote backups on the target machine.
It uses [BorgBackup](https://borgbackup.readthedocs.io/en/stable/) with systemd timers and service files.
Currently tested on Debian.

## Role Variables

### Required Arguments
- `borg_repository`: Full path to repository.
- `borg_source_directories`: List of local folders to back up.
- `borg_passphrase`: Password to use for repokey.

### Optional Arguments
- `borg_rsh`: Remote shell options.
- `borg_time`: systemd time pattern See [documentation](https://www.freedesktop.org/software/systemd/man/systemd.time.html#) for more.
- `borg_exclude_from`: Read exclude patterns from a files.
- `borg_one_file_system`: Don't cross file-system boundaries. Defaults to `false`
- `borg_exclude_patterns`: List of paths or patterns to exclude from backup. See [official documentation](https://borgbackup.readthedocs.io/en/stable/usage/help.html#borg-help-patterns) for more.
- `borg_scripts_directory`: Location where the backup scripts should be stored.

Most of the variables can be global and also variable for the individual backupjobs.

## Example Playbook

```
- hosts: webservers
  roles:
  - role: borgbackup-customizable
    borg_scripts_directory: "/root/borg-scripts"
	borg_rsh: "ssh -i ~/.ssh/backup_key -oBatchMode=yes"
	borg_time: "*-*-* 06:00:00"
	borg_exclude_from: "/etc/borg_excludelist"

    borgbackup_jobs:
      - name: "borgHome"
        borg_passphrase: CHANGEME
        borg_repository: backupserver:/backupSpace/{{ ansible_hostname }}/borgHome
        borg_source_directories:
         - /home
        borg_exclude_patterns:
          - "*/Cache"
          - "*/cache"
        borg_rsh: "ssh -i ~/.ssh/backup_key -oBatchMode=yes"
```

## License

MIT

## Author

Conrad Sachweh.
