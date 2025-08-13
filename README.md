# nginxtune-enhance

**Version:** 0.3.1  
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

### HTTP/3 Support
- **QUIC Listeners**: Adds dedicated HTTP/3 listeners (443 quic, [::]:443 quic)
- **Alt-Svc Headers**: Implements HTTP/3 advertisement headers for server and PHP locations
- **Protocol Directives**: Manages http2 on, http3 on, and quic_gso on settings
- **Listen Directive Management**: Properly handles HTTP/2 and HTTP/3 listen configurations

### Security Features
- **SSL Configuration**: Modern TLS protocols and secure cipher suites
- **Server Hardening**: Basic server hardening measures  
- **CMS Protection**: WordPress-specific security rules and file access restrictions

### Performance Fixes
- **Quic GSO**: Utilize quic_gso, a commonly used option that provides Generic Segmentation Offloading
- **Reuseport Optimization**: Enables socket reuse for improved performance

### Vhost Override Optimization
- **Eliminates Manual Copy/Paste**: Security configurations deploy automatically across all websites without repetitive setup
- **Minimal Override Files**: Keep vhost overrides focused only on site-specific customizations instead of repeating common security directives
- **Centralized Management**: Apply consistent security standards server-wide through simple feature toggles

## File Structure

```
/opt/nginxtune-enhance/
├── nginxtune-enhance          # Main executable script (v0.3.1)
├── config.json                # Feature toggle configuration
├── debug_analysis.py          # Debug utility script
└── overrides/                 # Modular security configurations
    ├── ssl.conf               # SSL A+ security settings
    ├── hardening.conf         # Server hardening configuration
    └── cms.conf               # CMS/WordPress protection rules
```

## Configuration

The `config.json` file controls all features through boolean toggles:

- **config_fixes**: Adds IPv6:80 listeners and reuseport to default.conf
- **http3_enable**: Enables full HTTP/3 support with QUIC listeners and Alt-Svc headers  
- **quic_gso_enable**: Enables QUIC Generic Segmentation Offloading (requires http3_enable)
- **ssl_upgrade**: Includes SSL security configuration for modern TLS settings
- **server_hardening**: Applies server hardening measures
- **cms_protection**: Enables WordPress and CMS-specific security protections

### Default Configuration
```json
{
  "features": {
    "config_fixes": true,
    "http3_enable": false,
    "quic_gso_enable": false,
    "ssl_upgrade": false,
    "server_hardening": false,
    "cms_protection": false
  }
}
```

All features are disabled by default except `config_fixes`.

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

# Test the script
/opt/nginxtune-enhance/nginxtune-enhance

# Add to cron for automatic management
crontab -e

# Add this commented line to run every minute:

# Run nginxtune-enhance
* * * * * /opt/nginxtune-enhance/nginxtune-enhance >/dev/null 2>&1
```

### Updates
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

## Backup System

- **Automatic Backups**: Creates timestamped backups before any changes
- **Location**: `/etc/nginx/backups/YYYYMMDD_HHMMSS/`
- **Validation**: Tests nginx configuration before applying changes
- **Rollback**: Automatically restores from backup if validation fails

## Version History

**0.3.1** - Separate QUIC GSO toggle, improved reuseport handling  
**0.3** - Feature toggle system, modular overrides, targeted include management, performance optimizations  
**0.2** - Unified add/remove logic, JSON configuration, improved state detection  
**0.1** - Initial HTTP/3 configuration automation with basic security features
