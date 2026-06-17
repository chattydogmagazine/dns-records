# DNS Zone Files

One YAML file per domain. Format: `{domain}.yaml`

## Adding a new domain

1. Add the zone to your Cloudflare account
2. Run the `export_zone_to_github` tool on the DNS MCP to auto-generate the YAML
3. Add the domain to `config.yaml` under `zones:`
4. Push to `main` — GitHub Actions will validate and sync

## Manual zone file format

```yaml
# zones/example.com.yaml
'@':
  A:
    ttl: 300
    values:
      - 159.65.217.116
  MX:
    ttl: 3600
    values:
      - priority: 10
        value: mail.example.com.

www:
  CNAME:
    ttl: 300
    value: example.com.

_dmarc:
  TXT:
    ttl: 3600
    values:
      - 'v=DMARC1; p=none; rua=mailto:admin@example.com'
```
