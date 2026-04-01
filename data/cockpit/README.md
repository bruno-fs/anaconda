# Cockpit Authentication Configuration

## Overview

This directory contains configuration for Cockpit authentication in the Anaconda installer.

## cockpit.conf

The `cockpit.conf` file configures Cockpit to use a custom authentication handler (`cockpit-pin-auth`) that supports two modes:

### 1. PIN Authentication Mode (default)

When the `WEBUI_LOCAL_SESSION` environment variable is **not set**, the handler requires PIN authentication:

```bash
# User will be prompted for PIN (currently hardcoded as "1234")
# Suitable for remote access scenarios
```

### 2. Local Session Mode (bypass authentication)

When the `WEBUI_LOCAL_SESSION` environment variable **is set**, authentication is bypassed:

```bash
# In systemd service or environment file:
Environment="WEBUI_LOCAL_SESSION=1"

# Or in shell:
export WEBUI_LOCAL_SESSION=1
```

## Integration with anaconda-webui

For the anaconda-webui project, update the systemd service to set the environment variable:

**For local installation (current behavior):**
```ini
[Service]
EnvironmentFile=/tmp/webui-cockpit-ws.env
Environment="COCKPIT_SUPERUSER=pkexec"
Environment="WEBUI_LOCAL_SESSION=1"
ExecStart=/usr/libexec/cockpit-ws ...
```

**For remote installation (PIN auth):**
```ini
[Service]
EnvironmentFile=/tmp/webui-cockpit-ws.env
Environment="COCKPIT_SUPERUSER=pkexec"
# WEBUI_LOCAL_SESSION not set - PIN auth will be required
ExecStart=/usr/libexec/cockpit-ws ...
```

Note: The cockpit.conf `Command` directive is respected regardless of how cockpit-ws is started.
