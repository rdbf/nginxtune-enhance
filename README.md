# nginxtune-enhance

**Version:** 0.3.5  
**Location:** `/opt/nginxtune-enhance/`  
**Author:** rdbf  

## Overview

nginxtune-enhance is an automated configuration management tool for Enhance hosting environments that fixes Nginx configuration issues and provides HTTP/3 protocol optimization with centralized security management. The script uses feature toggles to manage configurations reversibly while maintaining compatibility with Enhance's auto-generated files. Future Enhance updates might break the functionality, although checks are in place to prevent this.

## Objectives

1. **Fix Enhance Nginx Issues**: Resolve minor configuration issues in Enhance's auto-generated nginx files
2. **HTTP/3 Protocol Support**: Enable HTTP/3 with QUIC listeners and Alt-Svc headers
3. **Centralized Security**: Implement modular security configurations across all websites on one server

## Features

### Configuration Management
- **Reversible Changes**: All modifications can be undone by disabling feature toggles
- **Targeted Processing**: Only modifies files when changes are actually needed
- **State Detection**: Analyzes current configuration to determine required actions

### Features Managed
- **Listen Directives**: HTTP/2 and HTTP/3/QUIC listeners with proper IPv6 support
- **Protocol Configuration**: http2 on, http3 on, and quic_gso directives
- **HTTP/3 Compatibility**: Alt-Svc headers and FastCGI HTTP_HOST parameter
- **Performance Optimization**: reuseport and QUIC GSO
- **Security Modules**: SSL configuration, server hardening, and CMS protection includes
- **Logging Features**: Persistent logging and Cloudflare real IP detection

### Security Features
- **SSL Configuration**: Modern TLS protocols and secure cipher suites
- **Server Hardening**: Basic server hardening measures  
- **CMS Protection**: WordPress-specific security rules and file access restrictions

### Vhost Override Optimization
- **Eliminates Manual Copy/Paste**: Security configurations deploy automatically across all websites without repetitive setup
- **Minimal Override Files**: Keep vhost overrides focused only on site-specific customizations instead of repeating common security directives
- **Centralized Management**: Apply consistent security standards server-wide through simple feature toggles

## File Structure

```
/opt/nginxtune-enhance/
├── nginxtune-enhance          # Main executable script (v0.3.5)
├── config.json                # Feature toggle configuration
├── debug_analysis.py          # Debug utility script
└── overrides/                 # Modular security configurations
    ├── ssl.conf               # SSL A+ security settings
    ├── hardening.conf         # Server hardening configuration
    └── cms.conf               # CMS/WordPress protection rules
```

## Configuration

The `config.json` file controls all features through boolean toggles:

- **http3_enable**: Enables full HTTP/3 support with QUIC listeners, Alt-Svc headers, and automatic configuration fixes
- **quic_gso_enable**: Enables QUIC Generic Segmentation Offloading (requires http3_enable)
- **ssl_upgrade**: Includes SSL security configuration for modern TLS settings
- **server_hardening**: Applies server hardening measures
- **cms_protection**: Enables WordPress and CMS-specific security protections
- **persistent_logging**: Dual access logs (Enhance + persistent historical data)
- **real_ip_logging**: Cloudflare real visitor IP detection instead of edge server IPs
- **backup_retention_days**: Number of days to retain backup directories (default 30)

### Default Configuration
```json
{
  "features": {
    "http3_enable": false,
    "quic_gso_enable": false,
    "ssl_upgrade": false,
    "server_hardening": false,
    "cms_protection": false,
    "persistent_logging": false,
    "real_ip_logging": false
  },
  "backup_retention_days": 30
}
```

All features are disabled by default.

## Installation and Usage

### Requirements
- Root access required
- Enhance hosting environment
- Nginx with HTTP/3 support compiled in

### Quick Install
```bash
# Clone the repository
cd /opt
git clone https://github.com/rdbf/nginxtune-enhance.git

# Create config file
cd /opt/nginxtune-enhance
cp config.json.example config.json

# Test the script
/opt/nginxtune-enhance/nginxtune-enhance

# Add to cron for automatic management
crontab -e

# Add this commented line to run every minute:

# Run nginxtune-enhance
* * * * * /opt/nginxtune-enhance/nginxtune-enhance >/dev/null 2>&1
```

### Updates

**IMPORTANT**: If you get an error about "local changes would be overwritten by merge" when trying to update, run:

```bash
cd /opt/nginxtune-enhance
git reset --hard origin/main
cp config.json.example config.json
```

**Note**: This will replace your config.json with the new template format. You'll need to configure your desired features in config.json after updating.

Future updates will work normally:
```bash
cd /opt/nginxtune-enhance
git pull origin main
```

### Verification
```bash
# Check logs for operation status
tail -f /var/log/nginxtune-enhance.log

# Manual test run
/opt/nginxtune-enhance/nginxtune-enhance
```

## Logging

All operations are logged to `/var/log/nginxtune-enhance.log` with timestamps:
- Configuration changes and file processing status
- Error conditions and backup/restore operations  
- Nginx validation and reload results

### Persistent Logging
When the `persistent_logging` feature is enabled:
- Access logs are duplicated to `/var/log/nginx/<UUID>.log` (survives Enhance configuration reloads)
- Uses the same log format as Enhance-managed logs for consistency
- Real visitor IPs are logged when `real_ip_logging` is enabled (extracts Cloudflare visitor IPs instead of edge server IPs)

## Backup System

- **Automatic Backups**: Creates timestamped backups before any changes
- **Location**: `/etc/nginx/backups/YYYYMMDD_HHMMSS/`
- **Validation**: Tests nginx configuration before applying changes
- **Rollback**: Automatically restores from backup if validation fails
- **Retention**: Configurable cleanup of old backups (default 30 days)

## Version History

**0.3.5** - Added persistent logging and Cloudflare real IP detection features  
**0.3.4** - HTTP/3 and configuration fixes have been merged for cleaner logic  
**0.3.3** - Backup retention management  
**0.3.2** - Add FastCGI HTTP_HOST parameter for better HTTP3 compatibility with PHP  
**0.3.1** - Separate QUIC GSO toggle, improved reuseport handling  
**0.3.0** - Feature toggle system, modular overrides, targeted include management, performance optimizations  
**0.2.0** - Unified add/remove logic, JSON configuration, improved state detection  
**0.1.0** - Initial HTTP/3 configuration automation with basic security features
