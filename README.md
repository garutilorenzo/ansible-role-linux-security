[![GitHub issues](https://img.shields.io/github/issues/garutilorenzo/ansible-role-linux-security)](https://github.com/garutilorenzo/ansible-role-linux-security/issues)
![GitHub](https://img.shields.io/github/license/garutilorenzo/ansible-role-linux-security)
[![GitHub forks](https://img.shields.io/github/forks/garutilorenzo/ansible-role-linux-security)](https://github.com/garutilorenzo/ansible-role-linux-security/network)
[![GitHub stars](https://img.shields.io/github/stars/garutilorenzo/ansible-role-linux-security)](https://github.com/garutilorenzo/ansible-role-linux-security/stargazers)

# Basic ansible role for Linux security

Ansible role used to configure basic security (iptables, sshd, fail2ban) for Linux servers

## Requirements

Download the role form GitHub:

```
ansible-galaxy install git+https://github.com/garutilorenzo/ansible-role-linux-security.git
```

## Role Variables

This role accept this variables:

| Var   | Required |  Default | Desc |
| ------- | ------- | ----------- |  ----------- |
| `system_user`       | `no`       | `ubuntu`       | Main system user used to connect to the server. Must exist on remote machine  |
| `sshd_permit_root_login`       | `no`       | `false`       | Set the value of PermitRootLogin in sshd_config, root login not allowed by default  |
| `sshd_pubkey_auth`       | `no`       | `true`       | Set the value of PubkeyAuthentication, allow auth via public ssh keys  |
| `sshd_password_auth`       | `no`       | `false`       | Set the value of PasswordAuthentication, password autentication disabled by default  |
| `sshd_challange_response_auth`       | `no`       | `false`       | Set the value of ChallengeResponseAuthentication, disable  'keyboard-interactive' authentication by default |
| `sshd_use_pam`       | `no`       | `false`       | Set the value of UsePAM, disable authentication via PAM by default  |
| `sshd_x11_forwarding`       | `no`       | `false`       | Set the value of X11Forwarding, disable X11 forwardning by dafault  |
| `sshd_print_motd`       | `no`       | `false`       |  Set the value of PrintMotd, Motd disabled by default  |
| `ssh_keys`       | `no`       | `[]`       |  List of public ssh keys to be enabled for the `system user`.  |
| `install_fail2ban`       | `no`       | `false`       |  Install and configure fail2ban (ssh jail) |
| `enable_firewall`       | `no`       | `true`       |  Enable and configure firewall |
| `iptables_mode`       | `no`       | `legacy`       |  Define the iptables mode: legacy or nfables |
| `firewall_ingress_rules`       | `no`       | `[]`       |  List of firewall rules in YAML dict format. See [below](#iptables-ingress-rules-yaml-format) for more details |
| `ssh_allow_cidr`       | `no`       | `0.0.0.0/0`       |  CIDR allowed by the firewall for the SSH service |

For all th sshd config please refer to `man sshd_config` or refer to the [online](https://man7.org/linux/man-pages/man5/sshd_config.5.html) doc.

### Docker specific configuration variables

If the server has a docker daemon configured, you might want to preserve the iptables docker rules. In this role I've included the scrips from the [iptables-docker](https://github.com/garutilorenzo/iptables-docker) repo.

Here the allowed variables in details:

| Var   | Required |  Default | Desc |
| ------- | ------- | ----------- |  ----------- |
| `docker_preserve`       | `no`       | `false`       | If set to `true` use the custom iptables script instad of ufw or firewalld  |
| `swarm_enabled`       | `no`       | `false`       | Set to `true` if docker is configured in swarm mode  |
| `swarm_cidr`       | `no`       | `192.168.1.0/24`       | If docker is configured in swarm mode, define the swarm CIDR  |
| `ebable_icmp_messages`       | `no`       | `true`       | Set to `false` if you want to disable ping via firewall  |

### IPTables ingress rules YAML format

Here an example of a firewall rule compatible with this Ansible role:

```yaml
firewall_ingress_rules:
  - rule: 'allow'
    proto: 'tcp'
    src: '1.2.3.4/32'
    port: '8080' # <- optional. If you omit the port, all ports are opened
    family: 'ipv4' # <- optional (only needed for firewalld). Default 'ipv4', allowed values 'ipv4' and 'ipv6'
```

## Using this role

To use this role you follow the example in the  [examples/](examples/) dir.