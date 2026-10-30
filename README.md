# SOPS Utilities GitHub Action

[![action-shield]][action-url]

Multi-purpose SOPS utility action: install SOPS, load secrets into runner environment, or execute commands.

This GitHub Action simplifies the use of [Mozilla SOPS](https://github.com/getsops/sops) in your CI/CD workflows, particularly with `age` encryption.

## Quick Start - Using the GitHub Action

To use this action in your workflows, add one of the following steps depending upon your goal.

Pin the action to the full commit SHA of a release, not to a branch or tag. A branch or tag can move. Find the SHA with `git ls-remote https://github.com/Byont-Ventures/sops refs/tags/v1.1.0`.

The action checks the SHA-256 checksum of the SOPS binary before it installs it. The default version uses a checksum in `action.yml`. Other versions use the checksum file of the same SOPS release.

### 1. Install SOPS

Quickly installs a specified version of SOPS into the runner environment. This is useful if you only need the SOPS binary available for subsequent steps.

```yaml
- name: Install SOPS
  uses: byont-ventures/sops@<commit-sha> # v1.1.0
  with:
    operation: "install"
    # sops-version: "v3.10.2" # Optional, uses default if not specified
```

### 2. Load Secrets into Environment

Decrypts a SOPS-encrypted file and loads its key-value pairs as environment variables into the runner env. These variables are automatically masked in logs.

**Required:** You'll need to provide your `SOPS_AGE_KEY` as a GitHub secret.

```yaml
- name: Load Secrets from SOPS File
  uses: byont-ventures/sops@<commit-sha> # v1.1.0
  with:
    operation: "load-secrets"
    sops-age-key: ${{ secrets.SOPS_AGE_KEY }}
    sops-file-path: "path/to/your/secrets.sops.yaml" # e.g., .env.sops or secrets/production.sops.json
```

### 3. Execute a SOPS Command

Executes an arbitrary SOPS command. This is flexible for various use cases like encrypting files, decrypting specific fields, etc.

**Required (conditionally):** `sops-age-key` is needed if the command involves decryption or encryption operations.

```yaml
- name: Decrypt a specific value using SOPS
  uses: byont-ventures/sops@<commit-sha> # v1.1.0
  with:
    operation: "execute-command"
    sops-age-key: ${{ secrets.SOPS_AGE_KEY }}
    sops-args: '--decrypt --extract ''{"some_key"}'' secrets.enc.json'
```

## Detailed Action Configuration

The following inputs can be configured for the action:

| Input               | Description                                                                | Default   | Required (for operation)                          |
| ------------------- | -------------------------------------------------------------------------- | --------- | ------------------------------------------------- |
| `operation`         | The operation to perform: `install`, `load-secrets`, or `execute-command`. |           | **Yes** (All)                                     |
| `sops-version`      | The version of SOPS to install.                                            | `v3.10.2` | No                                                |
| `sops-age-key`      | The AGE private key for SOPS operations.                                   |           | `load-secrets`, `execute-command` (if key needed) |
| `sops-file-path`    | Path to the SOPS encrypted file.                                           |           | `load-secrets`                                    |
| `sops-args`         | Arguments to pass to the SOPS command.                                     |           | `execute-command`                                 |
| `working-directory` | The working directory to run the sops command in.                          | `.`       | No (`execute-command` only)                       |

## Local SOPS Setup and Usage (with 1Password Integration)

For managing SOPS-encrypted files locally, especially when using `age` keys secured by 1Password, this repository provides detailed guidance and a helpful `osops` shell function.

The local setup enhances your development workflow by making it easy to:

- Generate and securely store `age` keys using 1Password.
- Use the `osops` shell function (detailed in `sops.md`) to simplify SOPS commands by automatically sourcing the `SOPS_AGE_KEY` from 1Password.
- Perform common local commands like encrypting (`osops -e -i <file>`), decrypting (`osops -d <file>`), and interactively editing (`osops edit <file>`) sops-encrypted files.

➡️ **For comprehensive instructions, please refer to the [SOPS Local Setup Guide](./sops.md)**

## Branding

This action is configured with the following branding for the GitHub Marketplace:

- Icon: `shield`
- Color: `green`

## Contributing

Contributions are welcome! Please feel free to submit pull requests or open issues for bugs, feature requests, or improvements.

## License

This project is licensed under the MIT License. See the [LICENSE](./LICENSE) file for details (assuming a LICENSE file exists or will be added).

[action-shield]: https://img.shields.io/badge/GitHub_Action-SOPS_Utilities-green?style=for-the-badge&logo=githubactions
[action-url]: https://github.com/marketplace/actions/sops-utilities
