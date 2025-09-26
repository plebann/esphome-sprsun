# SPRSUN Heat Pump Integration Package

## Package Overview
This package provides comprehensive integration for SPRSUN heat pump units via Modbus RTU communication. It includes monitoring, control, and diagnostics capabilities.

## Substitutions Reference

### Required Substitutions
The following substitutions must be defined in your main configuration:

```yaml
substitutions:
  # Device naming
  sprsun_device_name: "SPRSUN Heat Pump"
  sprsun_device_prefix: "sprsun"
  
  # UART Configuration
  sprsun_uart_rx_pin: "GPIO14"
  sprsun_uart_tx_pin: "GPIO15"
  sprsun_uart_baud: "19200"
  sprsun_uart_stop_bits: "2"
  
  # Modbus Configuration
  sprsun_modbus_address: "0x01"
  sprsun_modbus_send_wait: "200ms"
  sprsun_update_interval: "5s"
  sprsun_setup_priority: "-10"
```

## Package Components

- **globals.yaml** - Global variables for counters and state tracking
- **modbus_controller.yaml** - UART and Modbus controller setup
- **binary_sensors.yaml** - Status binary sensors with automations
- **text_sensors.yaml** - Complex status parsing with lambda functions
- **sensors.yaml** - Temperature, pressure, power, and other measurements
- **controls.yaml** - Select and number controls for operation parameters
- **complete.yaml** - Master template including all components

## Usage Example

```yaml
# In your main ESPHome configuration
substitutions:
  sprsun_device_name: "My Heat Pump"
  sprsun_uart_rx_pin: "GPIO14"
  sprsun_uart_tx_pin: "GPIO15"
  # ... other required substitutions

packages:
  sprsun_heatpump: !include
    file: packages/sprsun/complete.yaml
```

## Features

### Monitoring
- Temperature sensors (inlet, outlet, ambient, coil, exhaust, etc.)
- Pressure sensors (suction, discharge)
- Power consumption and compressor frequency
- Fan speed and pump operation
- System status and working modes

### Control
- Unit operation modes (Heating, Cooling, Hot Water)
- Temperature setpoints for heating and cooling
- Compressor operation modes (Normal, Eco, Night, Test)
- Pump control modes and settings
- Advanced frequency adjustment parameters
- Economic operation profiles

### Diagnostics
- Comprehensive failure detection and reporting
- Status parsing for all system components
- Counter tracking for compressor starts and defrost cycles
- Duty time tracking for heating and compressor operation

## Hardware Requirements

- ESP32-based controller with UART capability
- RS485 to TTL converter for Modbus communication
- Proper electrical isolation recommended
- Compatible SPRSUN heat pump with Modbus RTU interface

## Version History

- v1.0 - Initial package extraction from monolithic configuration