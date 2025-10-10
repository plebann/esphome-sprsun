# Migration Guide: Local to Git Package Integration

This guide helps you migrate from local SPRSUN package inclusion to Git-based integration using ESPHome's native Git package support.

## Current Local Setup

If you're currently using the SPRSUN package locally, your configuration likely looks like this:

```yaml
# OLD - Local package inclusion
packages:
  sprsun_package: !include
    file: packages/sprsun/complete.yaml
    vars:
      sprsun_device_prefix: "sprsun"
      sprsun_device_name: "SPRSUN Heat Pump"
      sprsun_uart_rx_pin: "GPIO14"
      sprsun_uart_tx_pin: "GPIO15"
      # ... other variables
```

## New Git-Based Setup

Replace the local inclusion with Git integration:

```yaml
# NEW - Git package integration
packages:
  sprsun_package:
    url: https://github.com/plebann/esphome-packages
    files: [packages/sprsun/complete.yaml]
    ref: main
    refresh: 1d
    vars:
      sprsun_device_prefix: "sprsun"
      sprsun_device_name: "SPRSUN Heat Pump"
      sprsun_uart_rx_pin: "GPIO14"
      sprsun_uart_tx_pin: "GPIO15"
      sprsun_uart_baud: "19200"
      sprsun_uart_stop_bits: "2"
      sprsun_modbus_address: "1"
      sprsun_modbus_send_wait: "200ms"
      sprsun_update_interval: "5s"
      sprsun_setup_priority: "-10"
```

## Step-by-Step Migration

### Step 1: Update Package Configuration

1. **Replace the packages section** in your ESPHome configuration file
2. **Add new required variables** that were previously implicit:
   - `sprsun_uart_baud`
   - `sprsun_uart_stop_bits`
   - `sprsun_modbus_send_wait`
   - `sprsun_update_interval`
   - `sprsun_setup_priority`

### Step 2: Variable Mapping

Map your existing variables to the new format:

| Old Variable | New Variable | Notes |
|--------------|--------------|-------|
| `sprsun_device_prefix` | `sprsun_device_prefix` | Same |
| `sprsun_device_name` | `sprsun_device_name` | Same |
| `sprsun_uart_rx_pin` | `sprsun_uart_rx_pin` | Same |
| `sprsun_uart_tx_pin` | `sprsun_uart_tx_pin` | Same |
| `sprsun_modbus_address` | `sprsun_modbus_address` | Same |
| *implicit* | `sprsun_uart_baud` | **NEW** - Default: "19200" |
| *implicit* | `sprsun_uart_stop_bits` | **NEW** - Default: "2" |
| *implicit* | `sprsun_modbus_send_wait` | **NEW** - Default: "200ms" |
| *implicit* | `sprsun_update_interval` | **NEW** - Default: "5s" |
| *implicit* | `sprsun_setup_priority` | **NEW** - Default: "-10" |

### Step 3: Remove Local Files (Optional)

Once you've verified the Git integration works:

1. You can remove the local `packages/sprsun/` directory
2. Clean up any references to local package files
3. The Git package will be automatically cached by ESPHome

### Step 4: Validate Configuration

1. Run `esphome config your-config.yaml` to validate syntax
2. Test compilation with `esphome compile your-config.yaml`
3. Upload and verify all sensors and controls work as expected

## Example Migration: BoneIO Integration

### Before (Local)
```yaml
packages:
  sprsun_package: !include
    file: packages/sprsun/complete.yaml
    vars:
      sprsun_device_prefix: "sprsun"
      sprsun_device_name: "SPRSUN"
      sprsun_uart_rx_pin: "GPIO14"
      sprsun_uart_tx_pin: "GPIO15"
      sprsun_modbus_address: 1
```

### After (Git)
```yaml
packages:
  sprsun_package:
    url: https://github.com/plebann/esphome-packages
    files: [packages/sprsun/complete.yaml]
    ref: main
    refresh: 1d
    vars:
      sprsun_device_prefix: "sprsun"
      sprsun_device_name: "SPRSUN"
      sprsun_uart_rx_pin: "GPIO14"
      sprsun_uart_tx_pin: "GPIO15"
      sprsun_uart_baud: "19200"
      sprsun_uart_stop_bits: "2"
      sprsun_modbus_address: "1"
      sprsun_modbus_send_wait: "200ms"
      sprsun_update_interval: "5s"
      sprsun_setup_priority: "-10"
```

## Benefits of Git Integration

1. **Automatic Updates**: Package stays current with `refresh` setting
2. **Version Control**: Pin to specific versions using `ref`
3. **No Local Maintenance**: No need to manually update package files
4. **Shared Access**: Easy sharing between multiple devices
5. **Backup Independence**: Configuration works from any location

## Troubleshooting

### Issue: Variables Not Recognized

**Problem**: ESPHome reports undefined variables
**Solution**: Ensure all new required variables are provided in your `vars` section

### Issue: Compilation Errors

**Problem**: Package doesn't compile after migration  
**Solution**: Check variable types - ensure strings are quoted where required

### Issue: Git Access Problems

**Problem**: ESPHome can't access the repository
**Solution**: Check internet connectivity and repository URL

### Issue: Entity ID Changes

**Problem**: Home Assistant entities have different IDs
**Solution**: Verify `sprsun_device_prefix` matches your previous configuration

## Support

If you encounter issues during migration:

1. Check the [examples/](../examples/) directory for working configurations
2. Open an issue at: https://github.com/plebann/esphome-packages/issues
3. Include your configuration and error messages