# Tunet (mora build)

Local Home Assistant add-on that builds Tunet from this fork's `mora` branch:
upstream `main` plus pull requests that are not released yet.

Install on Home Assistant OS (Terminal & SSH add-on):

```sh
mkdir -p /addons/tunet_mora && cd /addons/tunet_mora
for f in config.yaml Dockerfile run.sh icon.png logo.png translations/en.yaml; do
  mkdir -p "$(dirname "$f")"
  curl -fsSL "https://raw.githubusercontent.com/ozanmora/Tunet/mora/mora-addon/$f" -o "$f"
done
```

Then Settings → Add-ons → Add-on store → ⋮ → Check for updates, and install
"Tunet (mora build)" from Local add-ons. Use Rebuild to pick up new commits on `mora`.
