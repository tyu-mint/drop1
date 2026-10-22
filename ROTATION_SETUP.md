# Set up automatic age-key rotation

The workflow `.github/workflows/rotate-age.yml` checks daily and rotates when 30 days have elapsed. It encrypts the public subscription file for both the current and new age recipients, verifies that both keys can decrypt it, publishes the ciphertext, updates the active key secret, and privately sends the new key through Telegram. The previous key remains usable for the transition window and is removed on the following successful rotation.

## Required repository Actions secrets

Open **Settings → Secrets and variables → Actions → New repository secret** and add:

- `AGE_CURRENT_KEY`: the current age secret-key line (from your existing `drop.age.key` file). Paste only the `AGE-SECRET-KEY-...` line; never commit it.
- `ROTATION_ADMIN_TOKEN`: a fine-grained GitHub personal access token restricted to this repository, with **Contents: Read and write** and **Actions: Read and write** permissions. The workflow uses it to push updates (so Pages deploys) and update `AGE_CURRENT_KEY`.
- `TELEGRAM_BOT_TOKEN`: the token from Telegram's official `@BotFather`.
- `TELEGRAM_CHAT_ID`: your private numeric chat ID with that bot.

Never put any of these values in a repository file, issue, commit message, or public workflow log.

## Telegram setup

1. Create a bot through `@BotFather` and copy its token directly into the repository secret `TELEGRAM_BOT_TOKEN`.
2. Open the bot's private chat and press **Start** (or send `/start`) before the first delivery.
3. Obtain your numeric Telegram chat ID using a trusted method and add it as `TELEGRAM_CHAT_ID`. Do not send the bot token to anyone.

## Initial validation

1. Confirm the four repository secrets exist and the current `AGE_CURRENT_KEY` decrypts the existing `drop.yaml`.
2. Open **Actions → Rotate age key → Run workflow**.
3. If you use the manual run, set `force` to `true`. This intentionally rotates immediately; the scheduled run otherwise waits until 30 days have elapsed.
4. Verify the workflow succeeds, the Telegram message arrives, and the subscription URL remains `https://tyu-mint.github.io/drop1/drop.yaml`.

## Transition behavior

The encrypted file temporarily accepts the current and new keys. The new key is sent only in the Telegram private message; the old key is kept as a recipient for one rotation interval so clients can be updated. At the next successful rotation, the older recipient is removed. If Telegram delivery fails, the workflow attempts to restore the old active key and leaves the rotation date unchanged so a later daily run can retry.

The workflow does not print private keys or Telegram responses to logs. GitHub-hosted runners are ephemeral; key files are created under the runner's temporary directory.
