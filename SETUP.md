# DNS Records — Setup Guide

This repo is the single source of truth for all DNS across 100+ domains,
managed via OctoDNS → Cloudflare. Changes pushed to `main` auto-sync via
GitHub Actions.

## First-time setup

### 1. Cloudflare
1. Create account at cloudflare.com (free)
2. Go to: My Profile → API Tokens → Create Token
3. Use template: **Edit zone DNS** — select All zones
4. Also note your **Account ID** (right sidebar on the Cloudflare dashboard homepage)

### 2. GitHub Secrets (required for Actions to work)
Go to: https://github.com/chattydogmagazine/dns-records/settings/secrets/actions

Add:
- `CLOUDFLARE_API_TOKEN` — your Cloudflare API token
- `CLOUDFLARE_ACCOUNT_ID` — your Cloudflare account ID

### 3. GoDaddy API Keys (for domain inventory + NS migration)
For each of your 3 GoDaddy accounts:
1. Log into the account at developer.godaddy.com
2. Click "Create New App"
3. Select **Production** environment
4. Copy the Key and Secret

These go into the DNS MCP env file on the DO droplet — not in this repo.

### 4. DNS MCP server (mcp5.ushersoft.com)
The MCP handles all API operations. Credentials live in:
`/opt/ppc/mcp/dns.env` on the DO droplet.

MCP endpoint: https://mcp5.ushersoft.com/mcp
OAuth password: ppc-dns-2026

### 5. Migrating a domain to Cloudflare
1. `add_zone_to_cloudflare` — adds domain, imports existing DNS, returns CF nameservers
2. Go to GoDaddy/Namecheap for that domain → update nameservers to Cloudflare ones
3. Wait up to 24h for propagation
4. `export_zone_to_github` — captures current state to this repo
5. Add domain to `config.yaml` zones section

## Workflow for DNS changes
1. Edit the zone YAML file directly, or use the MCP tool `create_dns_record`
2. MCP changes go live immediately in Cloudflare
3. Run `export_zone_to_github` to sync the current state back to this repo
4. Alternatively: edit YAML in GitHub → push → Actions auto-syncs to Cloudflare

## Quick reference
| Domain action      | Tool                        |
|--------------------|-----------------------------|
| List all domains   | `list_all_domains`          |
| Check CF status    | `check_nameserver_status`   |
| Migrate to CF      | `set_domain_nameservers`    |
| Add to Cloudflare  | `add_zone_to_cloudflare`    |
| Add DNS record     | `create_dns_record`         |
| Add redirect       | `create_redirect`           |
| Check MCP slots    | `list_mcp_subdomains`       |
| Next free MCP slot | `get_next_mcp_slot`         |
| Export to Git      | `export_zone_to_github`     |
| Trigger sync       | `trigger_dns_sync`          |
