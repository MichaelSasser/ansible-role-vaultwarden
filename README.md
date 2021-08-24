# Ansible Role: Vaultwarden

An Ansible role, which installs and manages a Vaultwarden Docker container.

## Requirements

This role doesn't install Docker on the host. You need to make sure the docker
is installed and running on your system. You can do that e.g. with [ericsysmin's collection](https://galaxy.ansible.com/ericsysmin/docker) (which only contains the docker role).

## Role Variables


| Variable                             | Default Value                           | Description                                                                       |
| ----------------------------------   | --------------------------------------- | --------------------------------------------------------------------------------- |
| `vaultwarden_docker_image`           | vaultwarden/server                      | The docker image, which is used to pull the container.                            |
| `vaultwarden_docker_image_tag`       | alpine                                  | The tag of the image.                                                             |
| `vaultwarden_container_name`         | vaultwarden                             | The container name.                                                               |
| `vaultwarden_networks`               | `- name: "{{ docker_network_name }}"`   | The network the container will be connected to.                                   |
| `vaultwarden_container_memory_limit` | null                                    | A memory limit for the container                                                  |
| `vaultwarden_upgrade`                | no                                      | if set to `yes` the container will be updated to the latest version (of the tag). |
| `vaultwarden_config`                 | null                                    | The Vaultwarden config (see below)                                                |

### Vaultwarden Config

This role uses environment variables to configure Vaultwarden.
You can configure them through the `vaultwarden_config` variable.

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

## License

Copyright &copy; 2021 Michael Sasser <Info@MichaelSasser.org>. Released under
the GPLv3 license.
