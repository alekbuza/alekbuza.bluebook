# Bluebook Ansible Role - Managed Linux kernel parameters at runtime

Ansible role for managed Linux kernel parameters at runtime.

Supported platforms:
* GNU/Linux

## Requirements

No requirements.

## Role Variables

| Name                                            | Type   | Defaults | Required | Description                                  |
|-------------------------------------------------|--------|----------|----------|----------------------------------------------|
| bluebook_sysctl_enable                          | `bool` | `false`  | `No`     | Enable sysctl module                         |
| bluebook_sysctl_config                          | `dict` | `{}`     | `No`     | List of sysctl.conf variables                |
| bluebook_sysctld_config_enable                  | `bool` | `false`  | `No`     | Enable sysctl.d module                       |
| bluebook_sysctld_config_backup                  | `bool` | `false`  | `No`     | Enable sysctl.d backup before change         |
| bluebook_sysctld_config_files                   | `list` | `[]`     | `No`     | List of sysctl.d files and variables         |
| bluebook_sysctld_config_delete_unmanaged        | `bool` | `false`  | `No`     | Enable deletion for unmanaged sysctl.d files |
| bluebook_sysctld_config_delete_unmanaged_except | `list` | `[]`     | `No`     | Exception for deletion sysctl.d files        |

## Dependencies

No dependencies.

## Example Playbook

The best practice is to use as `host_vars` and `group_vars` in Ansible project or collection.

### Example with `vars`

```yml
- hosts: servers

  vars:
    - bluebook_sysctl_enable: true
    - bluebook_sysctl_config:
        net.ipv4.ip_forward: 1

    - bluebook_sysctld_config_enable: true
    - bluebook_sysctld_config_backup: true
    - bluebook_sysctld_config_delete_unmanaged: true
    - bluebook_sysctld_config_delete_unmanaged_except:
      - README.sysctl
      - protect-links.conf
      - 99-sysctl.conf

    - bluebook_sysctld_config_files:
        # Kernel security
        - filename: kernel-security
          order: 10
          config:
            kernel.sysrq: 0
            kernel.core_uses_pid: 1
            kernel.maps_protect: 1
            kernel.pid_max: 65535
            kernel.exec-shield: 1
            kernel.randomize_va_space: 2
            kernel.msgmnb: 65535
            kernel.msgmax: 65535
            fs.suid_dumpable: 0
            kernel.kptr_restrict: 1

        # Improve system memory management
        - filename: memory-management
          order: 20
          config:
            vm.swappiness: 30
            vm.dirty_ratio: 30
            vm.dirty_background_ratio: 5
            vm.mmap_min_addr: 4096
            vm.overcommit_ratio: 50
            vm.overcommit_memory: 0
            kernel.shmmax: 268435456
            kernel.shmall: 268435456
            vm.min_free_kbytes: 65535

        # Network security
        - filename: network-security
          order: 30
          config:
            net.ipv4.tcp_syncookies: 1
            net.ipv4.tcp_syn_retries: 2
            net.ipv4.tcp_synack_retries: 2
            net.ipv4.tcp_max_syn_backlog: 4096
            net.ipv4.ip_forward: 0
            net.ipv4.conf.all.forwarding: 0
            net.ipv4.conf.default.forwarding: 0
            net.ipv6.conf.all.forwarding: 0
            net.ipv6.conf.default.forwarding: 0

  roles:
      - bluebook.sysctl
```

## License

ISC

## Author Information

Aleksandar Buza <me@aleksandarbuza.com>
