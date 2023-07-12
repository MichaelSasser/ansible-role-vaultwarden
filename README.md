# Ansible Role: Vaultwarden

An Ansible role, which installs and manages a Vaultwarden Docker container.

## Requirements

1. This role doesn't install Docker on the host. You need to make sure the
   docker is installed and running on your system. You can do that e.g. with
   [ericsysmin's collection](https://galaxy.ansible.com/ericsysmin/docker)
   (which only contains the docker role).
2. This role does not install or configure the optional feature `fail2ban`. It
   only makes sure the Vaultwarden logs are accessible, and it adds the
   Vaultwarden filter and jail to `fail2ban`. You can install `fail2ban` for
   example with the role
   [robertdebock.fail2ban](https://github.com/robertdebock/ansible-role-fail2ban).
   Make sure to set the correct `vaultwarden_config.IP_HEADER` for your reverse
   proxy. For example, `x-forwarded-for`, when you are using `envoy` as reverse
   proxy. Make sure to configure your reverse proxy to append that header.

## Role Variables

| Variable                             | Default Value                         | Description                                                                       |
| ------------------------------------ | ------------------------------------- | --------------------------------------------------------------------------------- |
| `vaultwarden_docker_image`           | `vaultwarden/server`                  | The docker image, which is used to pull the container.                            |
| `vaultwarden_docker_image_tag`       | `alpine`                              | The tag of the image.                                                             |
| `vaultwarden_container_name`         | `vaultwarden`                         | The container name.                                                               |
| `vaultwarden_networks`               | `- name: "{{ docker_network_name }}"` | The network the container will be connected to.                                   |
| `vaultwarden_container_memory_limit` | `null`                                | A memory limit for the container                                                  |
| `vaultwarden_upgrade`                | `no`                                  | if set to `yes` the container will be updated to the latest version (of the tag). |
| `vaultwarden_config`                 | `null`                                | The Vaultwarden config (see below)                                                |
| `vaultwarden_fail2ban_enabled`       | `false`                               | Enable or disable the fail2ban jail for vaultwarden.                              |
| `vaultwarden_fail2ban_config`        |                                       | Config options for the Vaultwarden fail2ban config (see below).                   |
| `vaultwarden_host_logdir`            | `/var/log/vaultwarden`                | The directory, where the logfile will be stored in.                               |
| `vaultwarden_logfile_name`           | `vaultwarden.log`                     | The filename of the logfile.                                                      |

There is one specialty for `vaultwarden_networks`. I Usually deploy one
application per (cloud) server. Therefore I often need just one docker network
per server. To save some time `vaultwarden_networks` is set to a
`docker_network_name` network name. This network name is configured in in my
playbook's vars and is most likely not configured on yours. On a more "complex"
setup, I set `vaultwarden_networks` to the network I need. Likewise you can use
`docker_network_name`, which only does make sense, if you configure multiple
container networks on the fly like that or set `vaultwarden_networks` in your
vars to your need.

### Vaultwarden Config

This role uses environment variables to configure Vaultwarden. You can configure
them through the `vaultwarden_config` variable.

All available environment variables are listed in the `.env.template` file
([direct-link](https://raw.githubusercontent.com/dani-garcia/vaultwarden/main/.env.template))
on the
[Vaultwarden repo](https://raw.githubusercontent.com/dani-garcia/vaultwarden).

The only thing you have to do is to use valid `yaml` syntax to define them
(replace `=` with `:`).

#### Example

```yaml
---
vaultwarden_config:
  DATABASE_URL: "postgresql://db_user:my_secret@postgres:5432/db"
  DATABASE_MAX_CONNS: "10"
  IP_HEADER: "X-Real-IP"
  SIGNUPS_ALLOWED: "false"
  SIGNUPS_VERIFY: "true"
  SIGNUPS_VERIFY_RESEND_TIME: "3600"
  SIGNUPS_VERIFY_RESEND_LIMIT: "6"
  SIGNUPS_DOMAINS_WHITELIST: "yourdomain.tld, anotherdomain.tld"
  SHOW_PASSWORD_HINT: "false"
  DOMAIN: "https://pw.yourdomain.tld"
  SMTP_HOST: "mail.yourdomain.tld"
  SMTP_FROM: "service@yourdomain.tld"
  SMTP_FROM_NAME: "Something - Service"
  SMTP_PORT: "587"
  SMTP_USERNAME: "service@yourdomain.tld"
  SMTP_PASSWORD: "your_smtp_app_password"
```

### The Vaultwarden fail2ban Config

The role variables are a `dict` in `vaultwarden_fail2ban_config`:

| Variable         | Default Value | Description                                                       |
| ---------------- | ------------- | ----------------------------------------------------------------- |
| `findtime`       | `14400`       | The time difference in which the tries must happen to get banned. |
| `bantime`        | `14400`       | The time a user gets banned.                                      |
| `maxretry`       | `3`           | The numbers of allowed tries.                                     |
| `external_ports` | `[80, 443]`   | The ports used by the reverse proxy.                              |

#### Example

```yaml
---
vaultwarden_fail2ban_config:
  findtime: 14400
  bantime: 14400
  maxretry: 3
  external_ports:
    - 80
    - 443
```

## License

Copyright &copy; 2021 Michael Sasser <Info@MichaelSasser.org>. Released under
the GPLv3 license.
