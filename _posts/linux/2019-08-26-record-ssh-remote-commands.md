---
layout: post
title: "Record SSH Remote Commands to .bash_history for all users"
date: 2019-08-26 13:19:00 +0800
categories: linux
---

Sometimes it is useful to record every command that executed by remote ssh. Administrators can use those records to audit system security issues. This post is used to configure ssh and .bash_history, then commands that executed by remote ssh can be recorded in .bash_history in dedicated format.

## 1. Configure /etc/ssh/sshd_config

- Add below command to /etc/ssh/sshd_config

```bash
ForceCommand if [[ -z $SSH_ORIGINAL_COMMAND ]]; then bash; else printf "$SSH_ORIGINAL_COMMAND\n" >> .bash_history; bash -c "$SSH_ORIGINAL_COMMAND"; fi
```

- Restart sshd service

```bash
systemctl restart sshd
```

## 2. Configure .bash_history

- Add below configurations to /etc/bashrc

```bash
export HISTTIMEFORMAT="%Y-%m-%d %H:%M:%S "
export HISTSIZE=10000
export HISTFILESIZE=10000
```

### Referecens

[Logging ssh remote commands to bash_history](https://unix.stackexchange.com/questions/122649/logging-ssh-remote-commands-to-bash-history)<br>
[7 Tips – Tuning Command Line History in Bash](https://www.shellhacks.com/tune-command-line-history-bash/)
