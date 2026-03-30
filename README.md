# owasp_waf

Ansible role to deploy OWASP CRS with ModSecurity on Apache2.

This role installs and configures ModSecurity for Apache2, downloads the latest OWASP CRS release from GitHub, deploys it under `/etc/apache2/modsec`, enables blocking mode, and sets paranoia level in `crs-setup.conf`.

## What this role does

- Installs Apache ModSecurity package
- Creates and maintains /etc/apache2/modsec
- Downloads and extracts the latest OWASP CRS release tarball
- Creates crs-setup.conf from crs-setup.conf.example
- Inserts a managed SecAction paranoia block after the default commented CRS sample
- Forces SecRuleEngine On
- Configures SecAuditLogParts via variable
- Creates pre and post CRS exclusion files from .example templates
- Writes /etc/apache2/mods-available/security2.conf with CRS include paths

## Requirements

- Ansible 2.9+
- Target OS: Ubuntu 24.04 or newer
- Apache installed on the target host (apache2 package)

Note: Nginx task file is currently a placeholder, so active configuration logic is Apache-focused.

## Role variables

Defined in defaults:

```yaml
OWASP_update_roles: false
```

Optional variable used by the role:

```yaml
OWASP_paranoia_level: 1
OWASP_audit_log_parts: ABIJDEFHZ
```

Variable details:

- OWASP_update_roles: when true, removes /etc/apache2/modsec before re-installing CRS content
- OWASP_paranoia_level: CRS blocking paranoia level injected into crs-setup.conf (default: 1)
- OWASP_audit_log_parts: value for SecAuditLogParts (default: ABIJDEFHZ)

## Example playbook

```yaml
---
- name: Deploy OWASP WAF on Apache hosts
  hosts: webservers
  become: true

  vars:
    OWASP_update_roles: false
    OWASP_paranoia_level: 2
    OWASP_audit_log_parts: ABIJDEFHZ

  roles:
    - role: balazspetik.owasp_waf
      tags: [owasp]
```

## Example inventory vars

```yaml
# group_vars/webservers.yml
OWASP_paranoia_level: 2
OWASP_audit_log_parts: ABIJDEFHZ
```

## Tags

- owasp

## Verify

Run Molecule scenario from the role directory:

```bash
molecule test
```
