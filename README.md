# nginxtune-enhance

**Version:** 0.3  
**Location:** `/opt/nginxtune-enhance/`  
**Author:** rdbf  

## Overview

nginxtune-enhance is an automated configuration management tool for Enhance hosting environments that fixes Nginx configuration issues and provides HTTP/3 protocol optimization with centralized security management. The script uses feature toggles to manage configurations reversibly while maintaining compatibility with Enhance's auto-generated files.

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
- **Server Hardening**: Security headers and attack protection measures  
- **CMS Protection**: WordPress-specific security rules and file access restrictions

### Performance Fixes
- **IPv6 Support**: Adds missing IPv6:80 listeners to default.conf
- **Reuseport Optimization**: Enables socket reuse for improved performance

## File Structure

```
/opt/nginxtune-enhance/
├── nginxtune-enhance          # Main executable script (v0.3)
├── config.json                 # Feature toggle configuration
├── debug_analysis.py           # Debug utility script
└── overrides/                  # Modular security configurations
    ├── ssl.conf               # SSL A+ security settings
    ├── hardening.conf         # Server hardening configuration
    └── cms.conf               # CMS/WordPress protection rules
```

## Configuration

The `config.json` file controls all features through boolean toggles:

- **config_fixes**: Adds IPv6:80 listeners and reuseport to default.conf for performance
- **http3_enable**: Enables full HTTP/3 support with QUIC listeners and Alt-Svc headers  
- **ssl_upgrade**: Includes SSL security configuration for modern TLS settings
- **server_hardening**: Applies security headers and server hardening measures
- **cms_protection**: Enables WordPress and CMS-specific security protections

All features are disabled by default except `config_fixes`. Enable features by setting them to `true` in the configuration file.

## Installation & Usage

### Setup
```bash
# Make script executable
chmod +x /opt/nginxtune-enhance/nginxtune-enhance

# Configure features (edit as needed)
nano /opt/nginxtune-enhance/config.json
```

### Automated Execution
Add to crontab for automatic management:
```bash
crontab -e

# Add this line to run every minute
* * * * * /opt/nginxtune-enhance/nginxtune-enhance >/dev/null 2>&1
```

### Manual Execution
```bash
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

## Exit Codes

- **0**: Success or no changes needed
- **1**: Error with successful rollback  
- **2**: Fatal error requiring manual intervention

## Requirements

- Root access required
- Enhance hosting environment
- Nginx with HTTP/3 support compiled in
- Python 3.6+ with standard libraries

## Version History

**0.3** - Feature toggle system, modular overrides, targeted include management, performance optimizations  
**0.2** - Unified add/remove logic, JSON configuration, improved state detection  
**0.1** - Initial HTTP/3 configuration automation with basic security features

---
