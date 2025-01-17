# SSH Relay

This is a simple SSH server that accepts just port forwarding requests from any
SSH client, and forwards the connection to a target server. It is useful when
you want to expose a server that is behind a firewall or NAT, and don't want to
run a full OpenSSH server, with all the security implications that may come with
it.

## Install

```
go install github.com/prochac/sshrelay@latest
```

## Usage

If you want to set up users, you must use the `--public-key` or
`--public-key-path` to specify the public key file for the user. The number of
`--user` flags and `--public-key` or `--public-key-path` flags must be the same.

```
Usage:
  sshrelay [flags] [args]

Flags:
  -h, --help                      help for sshrelay
      --host string               Hostname or IP address to listen on (default "0.0.0.0")
      --host-key strings          Host key file (default [/etc/ssh/ssh_host_rsa_key])
      --port uint                 Port to listen on (default 22)
      --public-key strings        Public key file for user
      --public-key-path strings   Path to public key file for user
      --user strings              Allowed user
```

### systemd

If you want to use this with Systemd as a service, use this simple template.

```unit file (systemd)
[Unit]
Description=SSH Relay Service
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/sshrelay \
    --host-key /etc/ssh/ssh_host_rsa_key \
    --host-key /etc/ssh/ssh_host_ed25519_key \
    --port 2222 \
    --user bob \
    --public-key-path /home/bob/.ssh/id_rsa.pub
KillMode=process
Restart=always
SyslogIdentifier=sshrelay

[Install]
WantedBy=multi-user.target
```

## Client usage

Do as you would do with any other SSH server you want to use local port
forwarding with.

ex. forward `neverssl.com` to your local port

```
ssh -N -p {port} {user}@{host} -L 127.0.0.1:8080:neverssl.com:80
```