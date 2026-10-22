# Drop · Encrypted Clash Config

This repository serves an age-encrypted Clash/Mihomo configuration. The original plaintext configuration and the private key are intentionally not stored here.

- Subscription URL: https://tyu-mint.github.io/drop1/drop.yaml
- File: [drop.yaml](./drop.yaml)
- Format: age v1 X25519 ASCII armor, containing the original YAML after decryption.

## Client setup

Paste the subscription URL into a Mihomo-compatible client and provide the matching age secret key in the client's age-key field. Keep that secret key private. Never commit it to this repository or place it in a URL.

The Pages deployment workflow is in `.github/workflows/pages.yml`. If Pages has not been enabled for this repository, set **Settings → Pages → Build and deployment → Source → GitHub Actions** once, then re-run the workflow.
