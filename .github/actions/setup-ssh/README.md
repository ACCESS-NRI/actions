# SSH Action

This action sets up `ssh` to use a given key.

> [!IMPORTANT]  
> If the `private-key-passphrase` input is not provided, the SSH key (`private-key`) must have been created with no (empty) passphrase, otherwise SSH authentication will fail.

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| hosts | string | Space- or newline-separated list of SSH hosts added to the SSH config. Each entry can optionally include an alias using the format `host:alias`, allowing connection with `ssh user@myalias` instead of `ssh user@myhost.org.au`. | YES | N/A | `myhost.org.au`, `myhost.org.au myotherhost.com:myotherhostalias` |
| private-key | string | Raw private key to add | YES | N/A | '---- OPENSSH PRIVATE KEY ---- ...' |
| user | string | Default user for SSH connections. When provided, it is added to the SSH config to allow connection with `ssh myhost.org.au` instead of `ssh user@myhost.org.au`. | NO | N/A | `myuser` |
| private-key-name | string | Name of the ephemeral private key file to store in `~/.ssh` | NO | `priv.key` | `key.pem` | 
| private-key-passphrase | string | Passphrase of the private key. If a passphrase is not provided, the private-key must have been created with no (empty) passphrase for the `ssh` command not to fail | NO | N/A | `mysecretpassphrase` |

## Outputs

| Name | Type | Description | Example |
| ---- | ---- | ----------- | ------- |
| private-key-path | string | Path to the generated private key file for use in the job | `~/.ssh/priv.key` |

## Example usage

<details>
<summary><b>Basic usage</b></summary>

```yaml
# ...
steps:
  - id: ssh
    uses: access-nri/actions/.github/actions/setup-ssh@main
    with:
      hosts: host1.example.com
      private-key: ${{ secrets.PRIVATE_KEY }} # Key with empty passphrase
  - run: |
      ssh user@host1.example.com -i ${{ steps.ssh.outputs.private-key-path }} /bin/bash <<'EOT'
      echo "Hello from host1!"
      EOT
```

</details>

<details>
<summary><b>With passphrase</b></summary>

```yaml
# ...
steps:
  - id: ssh
    uses: access-nri/actions/.github/actions/setup-ssh@main
    with:
      hosts: |
        host1.example.com
        host2.example.com
      private-key: ${{ secrets.PRIVATE_KEY }}
      private-key-passphrase: ${{ secrets.PRIVATE_KEY_PASSPHRASE }}
  - run: |
      ssh user@host2.example.com /bin/bash <<'EOT'
      echo "Hello from host2!"
      EOT
```

</details>

<details>
<summary><b>Host with alias</b></summary>

```yaml
# ...
steps:
  - id: ssh
    uses: access-nri/actions/.github/actions/setup-ssh@main
    with:
      hosts: |
        host1.example.com:myalias
        host2.example.com
      private-key: ${{ secrets.PRIVATE_KEY }}
  - run: |
      ssh user@myalias /bin/bash <<'EOT'
      echo "Hello from host1!"
      EOT
```

</details>

<details>
<summary><b>Host with alias and user</b></summary>

```yaml
# ...
steps:
  - id: ssh
    uses: access-nri/actions/.github/actions/setup-ssh@main
    with:
      hosts: |
        host1.example.com
        host2.example.com:myotheralias
      user: myusername
      private-key: ${{ secrets.PRIVATE_KEY }}
  - run: |
      ssh myotheralias /bin/bash <<'EOT'
      echo "Hello from host2!"
      EOT
```

</details>