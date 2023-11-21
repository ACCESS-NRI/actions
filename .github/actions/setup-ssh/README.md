# SSH Action

This action sets up `ssh` to use a given key, and adds to `~/.ssh/known_hosts`.

## Inputs

| Name | Type | Description | Required | Default | Example |
| ---- | ---- | ----------- | -------- | ------- | ------- |
| hosts | string | Newline-separated list of hosts for ssh-keyscan lookup | true | N/A | 'localhost' |
| private-key | string | Raw private key to add | true | N/A | '---- OPENSSH PRIVATE KEY ---- ...' |
| private-key-name | string | Name of the ephemeral private key file to store in `~/.ssh` | false | 'priv.key' | 'key.pem' | 

## Outputs

| Name | Type | Description | Example |
| ---- | ---- | ----------- | ------- |
| private-key-path | string | Path to the generated private key file for use in the job | '~/.ssh/priv.key' |

## Example usage

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
  - run: |
      ssh user@host1.example.com -i ${{ steps.ssh.outputs.private-key-path }} /bin/bash <<'EOT'
      echo "Hello from host1!"
      EOT
```
