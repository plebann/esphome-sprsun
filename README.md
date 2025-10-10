# SPRSUN Heat Pump ESPHome Package

[![ESPHome](https://img.shields.io/badge/ESPHome-2024.6+-blue.svg)](https://esphome.io/)
[![GitHub Actions](https://github.com/plebann/esphome-packages/workflows/ESPHome%20Validation/badge.svg)](https://github.com/plebann/esphome-packages/actions)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)

A comprehensive ESPHome package for integrating SPRSUN heat pump units via Modbus RTU communication. This package provides monitoring, control, and diagnostics capabilities with full variable customization support.

## Quick Start

### Installation via ESPHome Git Integration

Add the following to your ESPHome configuration:

```yaml
packages:
  sprsun_package:
    url: https://github.com/plebann/esphome-packages
    files:
      - path: packages/sprsun/complete.yaml
        vars:
          sprsun_device_prefix: "test"
          sprsun_device_name: "TEST"
          sprsun_uart_rx_pin: "GPIO14"
          sprsun_uart_tx_pin: "GPIO15"
          sprsun_uart_baud: "19200"
          sprsun_uart_stop_bits: "2"
          sprsun_modbus_address: "1"
          sprsun_modbus_send_wait: "200ms"
          sprsun_update_interval: "5s"
          sprsun_setup_priority: "-10"
    ref: main
    refresh: 1d
```

### Alternative Installation Methods

#### GitHub Shorthand (Simplest)
```yaml
packages:
  sprsun: github://plebann/esphome-packages/packages/sprsun/complete.yaml@main
```

#### Selective Component Loading
```yaml
packages:
  sprsun_sensors:
    url: https://github.com/plebann/esphome-packages
    files: 
      - packages/sprsun/modbus_controller.yaml
      - packages/sprsun/sensors.yaml
      - packages/sprsun/binary_sensors.yaml
    ref: main
```

## Package Components

- **complete.yaml** - Master package template including all components
- **binary_sensors.yaml** - Status monitoring with automated counter tracking
- **sensors.yaml** - Temperature, pressure, power, and other measurements
- **text_sensors.yaml** - Complex status parsing with lambda functions
- **controls.yaml** - Select and number controls for operation parameters
- **globals.yaml** - Global variables for counters and state tracking
- **modbus_controller.yaml** - UART and Modbus controller setup

## Configuration Variables

### Required Variables

All variables must be provided when including the package:

| Variable | Example | Description |
|----------|---------|-------------|
| `sprsun_device_prefix` | `"sprsun"` | Prefix for all entity IDs |
| `sprsun_device_name` | `"SPRSUN Heat Pump"` | Display name for entities |
| `sprsun_uart_rx_pin` | `"GPIO14"` | UART RX pin for Modbus communication |
| `sprsun_uart_tx_pin` | `"GPIO15"` | UART TX pin for Modbus communication |
| `sprsun_uart_baud` | `"19200"` | UART baud rate |
| `sprsun_uart_stop_bits` | `"2"` | UART stop bits |
| `sprsun_modbus_address` | `"1"` | Modbus device address |
| `sprsun_modbus_send_wait` | `"200ms"` | Wait time between Modbus commands |
| `sprsun_update_interval` | `"5s"` | Sensor update interval |
| `sprsun_setup_priority` | `"-10"` | Component setup priority |

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

## Examples

See the [examples/](examples/) directory for complete configuration examples:

- **[boneio-integration.yaml](examples/boneio-integration.yaml)** - Integration with BoneIO board
- **[basic-esp32.yaml](examples/basic-esp32.yaml)** - Standard ESP32 setup
- **[advanced-features.yaml](examples/advanced-features.yaml)** - Full feature demonstration
- **[selective-components.yaml](examples/selective-components.yaml)** - Individual component loading

## Migration from Local Package

If you're currently using a local package installation, see [MIGRATION.md](MIGRATION.md) for step-by-step migration instructions.

## Contributing

Contributions are welcome! Please read our contributing guidelines and submit pull requests for any improvements.

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

## Support

- **Issues**: Report bugs and feature requests via [GitHub Issues](https://github.com/plebann/esphome-packages/issues)
- **Discussions**: Community support via [GitHub Discussions](https://github.com/plebann/esphome-packages/discussions)
- **ESPHome**: Official documentation at [esphome.io](https://esphome.io/)

## Acknowledgments

- ESPHome team for the excellent framework
- SPRSUN for manufacturing reliable heat pump systems
- Community contributors for testing and feedback