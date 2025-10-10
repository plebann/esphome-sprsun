# MODBUS Communication Protocol

**Version:** V1.2  
**Date:** 2022.07.05  
**Protocol:** RS485-CG248075-MODBUS

---

## 1. Communication Specifications

- **Bus Type:** RS485
- **Signal Format:** Asynchronous serial
  - 1 start bit
  - 8 data bits
  - 2 end bits (stop bits)
  - No parity check
- **Baud Rate:** 19200 bps

## 2. Protocol Compliance

- Complies with standard **MODBUS RTU** protocol
- 16-bit data structure
- 16-bit CRC check (low byte before high byte)

## 3. Device Addressing

- Unit addresses determined by dial code 2-4
- Address range: #1 to #8

## 4. Master-Slave Architecture

- **Master:** Upper computer (calling host)
- **Slave:** Controller

## 5. Communication Commands

### Command 03H - Read Holding Registers (4x)

**TX (Transmit):**
```
[Device Address] + [Command 03H] + [Starting Register Address High 8 bits] + [Lower 8 bits] + 
[Number of Registers High 8 bits] + [Lower 8 bits] + [CRC Low 8 bits] + [CRC High 8 bits]
```

**RX (Receive):**
```
[Device Address] + [Command 03H] + [Number of Bytes Returned] + [Data1] + [Data2] + ... + 
[Data n] + [CRC Low 8 bits] + [CRC High 8 bits]
```

### Command 06H - Write Single Register

**TX (Transmit):**
```
[Device Address] + [Command 06H] + [Register Address High 8 bits] + [Lower 8 bits] + 
[Data High 8 bits] + [Lower 8 bits] + [CRC Low 8 bits] + [CRC High 8 bits]
```

**RX (Receive):**
- If successful: Returns command as sent
- If failed: No response

### Command 10H - Write Multiple Registers

**TX (Transmit):**
```
[Device Address] + [Command 10H] + [Starting Register Address High 8 bits] + [Lower 8 bits] + 
[Number of Registers High 8 bits] + [Lower 8 bits] + [Number of Memory Bytes] + 
[Data 1 High 8 bits] + [Lower 8 bits] + ... + [Data N High 8 bits] + [Lower 8 bits] + 
[CRC Low 8 bits] + [CRC High 8 bits]
```

**RX (Receive):**
```
[Device Address] + [Command 10H] + [Starting Register Address High 8 bits] + [Lower 8 bits] + 
[Number of Registers High 8 bits] + [Lower 8 bits] + [CRC Low 8 bits] + [CRC High 8 bits]
```

## 6. Parameter Modification

- Modified parameters are on machine #1
- Other units can only query (read-only)

---

## Register Map

**Legend:**
- **R** = Read-only
- **RW** = Read/Write

### Status and Input Registers (0x0000 - 0x000D)

| Address | Access | Description | Bit Definitions |
|---------|--------|-------------|-----------------|
| 0x0000 | R | Invalid | - |
| 0x0001 | R | Invalid | - |
| 0x0002 | R | Switching Input Symbol | bit 0: A/C Linkage switch<br>bit 1: Linkage switch<br>bit 2: Heating linkage<br>bit 3: Cooling linkage<br>bit 4: Flow Switch<br>bit 5: High pressure switch<br>bit 6: Phase sequence detection<br>bit 7: Invalid |
| 0x0003 | R | Working Status Mark | bit 0: Hotwater demand<br>bit 1: Heating demand<br>bit 2: With/without heating<br>bit 3: With/without cooling<br>bit 4: Antilegionella on<br>bit 5: Cooling demand<br>bit 6: Alarm downtime<br>bit 7: Defrost |
| 0x0004 | R | Output Symbol 1 | bit 0: Compressor<br>bit 1: Invalid<br>bit 2: Invalid<br>bit 3: Invalid<br>bit 4: Invalid<br>bit 5: Fan<br>bit 6: 4-way valve<br>bit 7: High/low fan speed (0=low, 1=high) |
| 0x0005 | R | Output Symbol 2 | bit 0: Chassis heater<br>bit 1: Invalid<br>bit 2: Invalid<br>bit 3: Invalid<br>bit 4: Invalid<br>bit 5: Heating heater<br>bit 6: Three-way valve<br>bit 7: Hotwater heater |
| 0x0006 | R | Output Symbol 3 | bit 0: A/C PUMP<br>bit 1: Crank heater<br>bit 2: Invalid<br>bit 3: Invalid<br>bit 4: Invalid<br>bit 5: Assistant solenoid valve<br>bit 6: Pump<br>bit 7: Invalid |

### Failure Symbols (0x0007 - 0x000D)

| Address | Access | Description | Bit Definitions |
|---------|--------|-------------|-----------------|
| 0x0007 | R | Failure Symbol 1 | bit 0: Hotwater temp<br>bit 1: Ambi temp<br>bit 2: Coil temp<br>bit 3: Invalid<br>bit 4: Outlet temp<br>bit 5: High pressure sensor failure<br>bit 6: Invalid<br>bit 7: Phase sequence |
| 0x0008 | R | Failure Symbol 2 | bit 0: Water flow failure<br>bit 1: Invalid<br>bit 2: High protection of heating water outlet<br>bits 3-7: Invalid |
| 0x0009 | R | Failure Symbol 3 | bit 6: Outlet gas temp failure<br>bits 0-5, 7: Invalid |
| 0x000A | R | Failure Symbol 4 | bit 0: Water inlet temp failure<br>bit 1: Exhaust temperature too high<br>bit 5: Low protection of cooling water outlet<br>bit 6: Inlet gas temp failure<br>bits 2-4, 7: Invalid |
| 0x000B | R | Failure Symbol 5 | bit 0: Low pressure protection<br>bit 1: High pressure protection<br>bit 2: Coil temperature too high<br>bit 6: High pressure sensor failure<br>bit 7: Low pressure sensor failure<br>bits 3-5: Invalid |
| 0x000C | R | Failure Symbol 6 | bit 4: Sec antifreeze<br>bit 5: One antifreeze<br>bits 0-3, 6-7: Invalid |
| 0x000D | R | Failure Symbol 7 | bit 1: Ambient temperature too low<br>bit 4: Frequency conversion module faulty<br>bit 5: 2# DC fan failure<br>bit 6: 1# DC fan failure<br>bits 0, 2-3, 7: Invalid |

### Temperature Sensors (0x000E - 0x0031)

| Address | Access | Description | Scaling |
|---------|--------|-------------|---------|
| 0x000E | R | Inlet temp | n × 0.1°C |
| 0x000F | R | Hotwater temp | n × 0.1°C |
| 0x0011 | R | Ambi temp | n × 0.5°C |
| 0x0012 | R | Outlet temp | n × 0.1°C |
| 0x0015 | R | Suct gas temp | n × 0.5°C |
| 0x0016 | R | Coil temp | n × 0.5°C |
| 0x001B | R | Exhaust temp | n × 1°C |
| 0x0022 | R | Driving temp | n × 0.5°C |
| 0x0028 | R | Evap. temp | n × 0.1°C |
| 0x0029 | R | Cond. temp | n × 0.1°C |

### Component Status (0x001C - 0x0031)

| Address | Access | Description | Note |
|---------|--------|-------------|------|
| 0x001C | R | EEV1 step | - |
| 0x001D | R | EEV2 step | - |
| 0x001E | R | Comp. frequency | - |
| 0x001F | R | Frequency conversion failure 1 | - |
| 0x0020 | R | Frequency conversion failure 2 | - |
| 0x0021 | R | DC bus voltage | - |
| 0x0023 | R | Comp. current | - |
| 0x0024 | R | Target frequency | - |
| 0x0026 | R | DC fan 1 speed | - |
| 0x0027 | R | DC fan 2 speed | - |
| 0x002A | R | Frequency conversion fault 8 bits higher | 0xFF = fault |
| 0x002B | R | Frequency conversion failure 8 bits lower | - |
| 0x002C | R | Controller Version | - |
| 0x002D | R | Display Version | - |
| 0x002E | R | DC pump speed | - |
| 0x002F | R | Suct. press | n × 0.1 bar |
| 0x0030 | R | Disch. press | n × 0.1 bar |
| 0x0031 | R | DC fan target | - |

### Control Parameters (0x0032 - 0x0036)

| Address | Access | Parameter | Bit Definitions | Reference |
|---------|--------|-----------|-----------------|-----------|
| 0x0032 | RW | Parameter Marker | bit 0: 0=OFF, 1=ON (bit add: 0x0320)<br>bit 1: MV mode: 0=Auto, 1=Manual (0x0321) (A45)<br>bit 2: Manual frequency selection (0x0322)<br>bit 5: AV MODE: 0=Auto, 1=Manual (0x0325) (B01)<br>bit 6: AV init outlet stp (0x0326) (B93)<br>bits 3-4, 7: Invalid | - |
| 0x0033 | RW | Control Mark 1 | bit 0: Constant freq adj: 0=NO, 1=YES (0x0330) (R29)<br>bit 1: Pressure switch enable: 1=use, 0=Unuse (0x0331) (F01)<br>bit 2: AV cool enable: 0=use, 1=Unuse (0x0332) (B74)<br>bit 3: AV control mode: 0=Enhan, 1=Exhau (0x0333) (B92)<br>bit 4: DCfan 1 enable: 0=Unuse, 1=use (0x0334) (D01)<br>bit 5: DCfan 2 enable: 0=Unuse, 1=use (0x0335) (D02)<br>bit 6: Parameters reset: 0=Unuse, 1=use (0x0336)<br>bit 7: Failure reset: 0=Unuse, 1=use (0x0337) | - |
| 0x0034 | RW | Control Mark 2 | bit 0: Antilegionella Enable: 0=Unuse, 1=use (0x0340)<br>bit 1: Two/Three function: 0=Unuse, 1=use (G01) (0x0341)<br>bits 2-7: Invalid | - |
| 0x0035 | RW | Timeband | bit 0: Timeband 1 Enable (0x0350)<br>bit 1: Timeband 2 Enable (0x0351)<br>bit 2: Timeband 3 Enable (0x0352) | - |
| 0x0036 | RW | P06 Unit Mode | 0=DHW<br>1=Heating<br>2=Cooling<br>3=Heating+DHW<br>4=Cooling+DHW | - |

### Defrost Parameters (0x0037 - 0x003D)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x0037 | RW | H01 Defrost freq | 30-120 Hz | - |
| 0x0038 | RW | H08 Defrost period 1 | 10-120 MIN | - |
| 0x0039 | RW | H11 Defrost period 2 | 10-120 MIN | - |
| 0x003A | RW | H14 Defrost period 3 | 10-120 MIN | - |
| 0x003B | RW | H15 Comp total run | 1-60 | n × 5 min |
| 0x003C | RW | H16 Comp continuous run | 5-90 MIN | - |
| 0x003D | RW | H05 Defrost time | 5-20 MIN | - |

### MV (Manual Valve) Parameters (0x003E - 0x0061)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x003E | RW | A01 MV period | 20-90 S | - |
| 0x003F-0x0046 | RW | A14-A21 MV heat initial 0-7 | 0-240 | n × 2P |
| 0x0047-0x004A | RW | A22-A25 MV cool initial 0-3 | 0-240 | n × 2P |
| 0x004B-0x0052 | RW | A26-A33 MV water init 0-7 | 0-240 | n × 2P |
| 0x0053-0x005A | RW | A34 MV heat lower 0-7 | 0-240 | n × 2P |
| 0x005B | RW | A43 MV defrost open | 0-240 | n × 2P |
| 0x005C | RW | A44 MV water lower | 25-75 | n × 2P |
| 0x005D | RW | A46 MV manual open | 10-225 | n × 2P |
| 0x005E | RW | A47 MV SH ratio | 1-6 | - |
| 0x005F | RW | A48 MV SH diff | 1-180 | - |
| 0x0060 | RW | A50 MV comp keep time | 0-250 S | n × 2S |
| 0x0061 | RW | A51 MV def keep time | 0-250 S | n × 2S |

### AV (Automatic Valve) Parameters (0x0062 - 0x00C5)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x0062 | RW | B02 AV manual open | 10-225 | n × 2P |
| 0x0063 | RW | B04 AV exhaust ratio | 1-6 | - |
| 0x0064 | RW | B05 AV exhaust diff | 0-180 | - |
| 0x0065 | RW | B06 AV SH ratio | 1-6 | - |
| 0x0066 | RW | B07 AV SH diff | 0-180 | - |
| 0x0067 | RW | B08 AV period | 10-20 S | - |
| 0x0068-0x0069 | RW | B19-B20 AV heat initial 0-1 | 0-240 | Outlet temp ≤ B93, n × 2P |
| 0x0070-0x0071 | RW | B27-B28 AV water initial 0-1 | 0-240 | Outlet temp ≤ B93, n × 2P |
| 0x0078-0x007F | RW | B35-B42 AV heat lower 0-7 | 0-240 | n × 2P |
| 0x0080 | RW | B43 AV defrost open | 0-240 | n × 2P |
| 0x0081 | RW | B44 AV cool open | 0-240 | n × 2P |
| 0x0098-0x009F | RW | B45-B52 AV water lower 0-7 | 0-240 | n × 2P |
| 0x00A0-0x00A7 | RW | B53-B60 AV heat exhaust 0-7 | 50-125°C | Temperature ranges by ambient |
| 0x00A8-0x00AF | RW | B61-B68 AV water exh 0-7 | 50-125°C | Temperature ranges by ambient |
| 0x00B0-0x00B3 | RW | B69-B72 AV cool exhaust 0-3 | 50-125°C | Temperature ranges by ambient |
| 0x00B4 | RW | B73 AV open delay | 0-180 S | - |
| 0x00B5 | RW | B75 AV off exh diff | 0-30 | - |
| 0x00B6-0x00BD | RW | B76-B83 AV heat exh diff 0-7 | 0-125°C | Temperature ranges by ambient |
| 0x00BE-0x00C5 | RW | B84-B91 AV water exh diff 0-7 | 0-125°C | Temperature ranges by ambient |

### Pressure and Temperature Control (0x0082 - 0x00D3)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x0082 | RW | F02 HP protect value | 0-250 | (n+250) × 0.1 bar |
| 0x0083 | RW | F03 HP recover value | 0-250 | (n+250) × 0.1 bar |
| 0x0084 | RW | F04 LP protect value | 0-200 | n × 0.1 bar |
| 0x0085 | RW | F05 LP recover value | 0-200 | n × 0.1 bar |
| 0x00C6 | RW | P03 Temp diff | 2-18°C | - |
| 0x00C7 | RW | F39 AC constant temp diff | 1-10°C | - |
| 0x00C8 | RW | P05 Temp diff | 2-18°C | - |
| 0x00C9 | RW | F40 WT constant temp diff | 1-10°C | - |
| 0x00CA | RW | P04 Hotwater setp | 10-55 | n × 0.5°C (Read only lower 8 bits) |
| 0x00CB | RW | P02 Cooling setp | 12-30 | n × 0.5°C (Read only lower 8 bits) |
| 0x00CC | RW | P01 Heating setp | 10-55 | n × 0.5°C (Read only lower 8 bits) |
| 0x00CD | RW | F44 WT temp calibration | -30 to +30 | - |
| 0x00CE | RW | F45 Inlet temp calibration | -30 to +30 | - |
| 0x00CF | RW | F46 Outlet temp calibration | -30 to +30 | - |
| 0x00D0 | RW | F49 WT temp offset stp | 30-55 | - |
| 0x00D1 | RW | F50 Inlet temp offset stp | 30-55 | - |
| 0x00D2 | RW | F42 Water temp offset | 0-50 | - |
| 0x00D3 | RW | F43 Inlet temp offset | 0-50 | - |

### DC Fan Control (0x0086 - 0x0097)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x0086 | RW | D03 Cool dcfan max speed | 0-100 | n × 10 rpm |
| 0x0087 | RW | D04 Cool dcfan min speed | 0-100 | n × 10 rpm |
| 0x0088 | RW | D05 Cool dcfan high press | 10-40 | - |
| 0x0089 | RW | D06 Cool dcfan open diff | 2-10 | n × 10 bar |
| 0x008A | RW | D07 Cool dcfan close diff | 2-10 | n × 10 bar |
| 0x008B | RW | D08 Cool dcfan init speed | 0-100 | n × 10 rpm |
| 0x008C | RW | D09 Heat dcfan max speed | 0-100 | n × 10 rpm |
| 0x008D | RW | D10 Heat dcfan min speed | 0-100 | n × 10 rpm |
| 0x008E | RW | D11 Heat dcfan low press | 5-15 | n × 10 bar |
| 0x008F | RW | D12 Heat dcfan open diff | 2-10 | n × 10 bar |
| 0x0090 | RW | D13 Heat dcfan close diff | 2-10 | n × 10 bar |
| 0x0091 | RW | D14 Heat DCfan init speed | 0-100 | n × 10 rpm |
| 0x0092 | RW | D15 DCfan adjust period | 0-30 | - |
| 0x0093-0x0097 | RW | D16-D20 DCfan adjust speed 1-5 | 0-100 | Various speed difference ranges |

### Defrost Settings (0x00D4 - 0x00DB)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x00D4 | RW | H03 Defrost in stp | -15 to -1°C | - |
| 0x00D5 | RW | H04 Defrost exit stp | 1-40°C | - |
| 0x00D6 | RW | H07 Def ambi coil 1 | 0-30°C | - |
| 0x00D7 | RW | H10 Def ambi coil 2 | 0-30°C | - |
| 0x00D8 | RW | H13 Def ambi coil 3 | 0-30°C | - |
| 0x00D9 | RW | H09 Defrost ambi stp 1 | -30 to +30°C | - |
| 0x00DA | RW | H12 Defrost ambi stp 2 | -30 to +30°C | - |
| 0x00DB | RW | H04 Defrost ambi stp | -30 to +30°C | - |

### MV Superheat Settings (0x00DC - 0x00E7)

| Address | Access | Parameter | Range | Ambient Temperature Range |
|---------|--------|-----------|-------|---------------------------|
| 0x00DC-0x00E3 | RW | A02-A09 MV heating SH 1-8 | -5 to +10°C | Various temp ranges |
| 0x00E4-0x00E7 | RW | A10-A13 MV cooling SH 1-4 | -5 to +10°C | Various temp ranges |

### AV Settings (0x00E8 - 0x00F3)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x00E8 | RW | A42 MV exhaust stp | 70-125°C | - |
| 0x00E9 | RW | B03 AV start ambi | 11-45°C | - |
| 0x00EA | RW | B09 AV exhaust stp | 70-120 | - |
| 0x00EB | RW | B10 AV off exh stp | 40-70 | - |
| 0x00EC-0x00F3 | RW | B11-B18 AV heating SH 1-8 | -10 to +10 | Temperature ranges by ambient |

### Fan and Exhaust Settings (0x00F4 - 0x00FA)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x00F4 | RW | D21 AC fan switch ambi 1 | -10 to +50°C | - |
| 0x00F5 | RW | D22 AC fan switch ambi 2 | -10 to +50°C | - |
| 0x00F6-0x00FA | RW | R16-R20 Exhaust high TP0-TP4 | 50-125°C | - |

### Frequency Settings (0x00FB - 0x0117)

| Address | Access | Parameter | Range | Ambient Temperature Range |
|---------|--------|-----------|-------|---------------------------|
| 0x00FB | RW | Manual frequency setting | - | - |
| 0x00FC-0x0103 | RW | Freq of water R00-R33 | 30-120 Hz | Various temp ranges |
| 0x0104-0x010B | RW | Freq of heat R04-R11 | 30-120 Hz | Various temp ranges |
| 0x010C-0x010F | RW | Freq of cool R12-R15 | 30-120 Hz | Various temp ranges |
| 0x0110-0x0117 | RW | Freq adj lower limit/upper R21-R28 | 0-125 Hz | - |

### Timeband Settings (0x0119 - 0x0124)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x0119-0x011C | RW | Timeband 1 ON/OFF hour/minutes | 0-23 / 0-59 | - |
| 0x011D-0x0120 | RW | Timeband 2 ON/OFF hour/minutes | 0-23 / 0-59 | - |
| 0x0121-0x0124 | RW | Timeband 3 ON/OFF hour/minutes | 0-23 / 0-59 | - |

### ECO Frequency Settings (0x012D - 0x0140)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x012D-0x0134 | RW | ECO freq of water R34-R41 | 30-120 Hz | - |
| 0x0135-0x013C | RW | ECO freq of heat R42-R49 | 30-120 Hz | - |
| 0x013D-0x0140 | RW | ECO freq of cool R50-R53 | 30-120 Hz | - |

### Night Frequency Settings (0x0141 - 0x0154)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x0141-0x0148 | RW | Night freq of water R54-R61 | 30-120 Hz | - |
| 0x0149-0x0150 | RW | Night freq of heat R62-R69 | 30-120 Hz | - |
| 0x0151-0x0154 | RW | Night freq of cool R70-R73 | 30-120 Hz | - |

### Test Frequency Settings (0x0155 - 0x0168)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x0155-0x015C | RW | Test freq of water R74-R81 | 30-120 Hz | - |
| 0x015D-0x0164 | RW | Test freq of heat R82-R89 | 30-120 Hz | - |
| 0x0165-0x0168 | RW | Test freq of cool R90-R93 | 30-120 Hz | - |

### Economic Mode Settings (0x0169 - 0x0180)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x0169-0x016C | RW | E01-E04 Economic heat ambi 1-4 | -30 to +50°C | - |
| 0x016D-0x0170 | RW | E05-E08 Economic water ambi 1-4 | -30 to +50°C | - |
| 0x0171-0x0174 | RW | E09-E12 Economic cool ambi 1-4 | -30 to +50°C | - |
| 0x0175-0x0178 | RW | E13-E16 Economic heat temp 1-4 | 10-55 | n × 0.5°C (Read only lower 8 bits) |
| 0x0179-0x017C | RW | E17-E20 Economic water temp 1-4 | 10-55 | n × 0.5°C (Read only lower 8 bits) |
| 0x017D-0x0180 | RW | E21-E24 Economic cool temp 1-4 | 12-30 | n × 0.5°C (Read only lower 8 bits) |

### System Settings (0x0181 - 0x019E)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x0181 | RW | G08 Comp delay | 1-60 min | - |
| 0x0182 | RW | G06 Comp delay | 1-60 min | - |
| 0x0183 | RW | G07 Hotwater heater Ext | -30 to +30°C | - |
| 0x0184 | RW | G05 Heating heater Ext | -30 to +30°C | - |
| 0x0185 | RW | G03 Start internal | 1-120 min | - |
| 0x0186 | RW | F13 DC pump mode | 0=manual / 1=auto | - |
| 0x0187 | RW | F14 DC pump cycle | 10-120 s | - |
| 0x0188 | RW | F15 DC pump freq set | 10-100% | - |
| 0x0189 | RW | F16 DC pump max freq | 10-100% | - |
| 0x018A | RW | F17 DC pump min freq | 10-100% | - |
| 0x018B | RW | F18 DC pump scale factor | 1-10 | - |
| 0x018C | RW | F19 DC pump diff | 0-100 | - |
| 0x018D | RW | G04 Delta temp set | 5-30°C | - |
| 0x018E | RW | F21 Comp freq scale factor | 1-10 | - |
| 0x018F | RW | F22 Comp freq diff | 0-100 | - |
| 0x0190 | RW | F23 Compressor mode | 0=NOR, 1=ECO, 2=Night, 3=Test | - |
| 0x0191 | RW | G09 Enable switch | NO linkage / YES amb | - |
| 0x0192 | RW | G10 Ambtemp switch setp | -20 to +30°C | - |
| 0x0193 | RW | G11 Ambtemp diff | 1-10°C | - |
| 0x0194 | RW | F27 Limit freq temp diff | 5-20°C | - |
| 0x0195 | RW | F28 Dec freq temp diff | 5-20°C | - |
| 0x0196 | RW | F29 Limit freq outlet low | 0-20°C | - |
| 0x0197 | RW | F30 Dec freq outlet low | 0-20°C | - |
| 0x0198 | RW | F31 Limit freq outlet high | 30-80°C | - |
| 0x0199 | RW | F32 Dec freq outlet high | 30-80°C | - |
| 0x019A | RW | Temp set point of antilegionella | 30-70 | - |
| 0x019B | RW | Weekday of antilegionella | 0 (Sun) - 6 (Sat) | - |
| 0x019C | RW | Start timer of antilegionella | 0-23 | - |
| 0x019D | RW | End timer of antilegionella | 0-23 | - |
| 0x019E | RW | G02 Pump work | 0=Interval, 1=Normal, 2=Demand | - |

### Cycle and Protection Settings (0x019F - 0x01A3)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x019F | RW | F35 Antifreeze cycle | 1-60 min | - |
| 0x01A0 | RW | F36 Three-way valve cycle | 1-60 hour | - |
| 0x01A1 | RW | F37 Pump cycle in error | 1-120 min | - |
| 0x01A2 | RW | F38 Comp freq cycle | 20-60 s | - |
| 0x01A3 | RW | F41 Air too low stp | -40 to 0 | - |

### Timeband Temperature Settings (0x01A4 - 0x01AC)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x01A4 | RW | Hotwater Set temp of Timeband 1 | 56-120 | n × 0.5°C (Read only lower 8 bits) |
| 0x01A5 | RW | Heat Set temp of Timeband 1 | 30-100 | n × 0.5°C (Read only lower 8 bits) |
| 0x01A6 | RW | Cool Set temp of Timeband 1 | 14-60 | n × 0.5°C (Read only lower 8 bits) |
| 0x01A7 | RW | Hotwater Set temp of Timeband 2 | 56-120 | n × 0.5°C (Read only lower 8 bits) |
| 0x01A8 | RW | Heat Set temp of Timeband 2 | 30-100 | n × 0.5°C (Read only lower 8 bits) |
| 0x01A9 | RW | Cool Set temp of Timeband 2 | 14-60 | n × 0.5°C (Read only lower 8 bits) |
| 0x01AA | RW | Hotwater Set temp of Timeband 3 | 56-120 | n × 0.5°C (Read only lower 8 bits) |
| 0x01AB | RW | Heat Set temp of Timeband 3 | 30-100 | n × 0.5°C (Read only lower 8 bits) |
| 0x01AC | RW | Cool Set temp of Timeband 3 | 14-60 | n × 0.5°C (Read only lower 8 bits) |

### Timeband Week Settings (0x01AD - 0x01AF)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x01AD | RW | Week set of Timeband 1 | 0-6 | - |
| 0x01AE | RW | Week set of Timeband 2 | 0-6 | - |
| 0x01AF | RW | Week set of Timeband 3 | 0-6 | - |

### Advanced Settings (0x01B0 - 0x01BB)

| Address | Access | Parameter | Range | Note |
|---------|--------|-----------|-------|------|
| 0x01B0 | RW | H17 Def fan high press | 20-40 bar | - |
| 0x01B1 | RW | H18 Def fan speed | 20-100 | n × 10 rpm |
| 0x01B2 | RW | F47 High press adjust | -50 to +50 | - |
| 0x01B3 | RW | F48 Low press adjust | -50 to +50 | - |
| 0x01B4 | RW | F51 Air too low diff | 1-10°C | - |
| 0x01B5 | RW | B93 AV init outlet stp | 20-60°C | - |
| 0x01B6 | RW | F53 Outlet temp offset stp | 30-55°C | - |
| 0x01B7 | RW | F52 Outlet temp offset | 0-50°C | - |
| 0x01B8 | RW | F54 Heat upper limit stp | 10-80 | - |
| 0x01B9 | RW | F55 Cool lower limit stp | 7-30 | - |
| 0x01BA | RW | F56 Model selection | 1-10 | 1=A102508<br>2=A202508<br>3=A103008<br>4=A203008<br>5=A104008<br>6=A204008<br>7=A105008<br>8=A205008<br>9=A106008<br>10=A206008 |
| 0x01BB | RW | R94 Oil return freq | 10-70 | - |

---

## Notes

1. **Temperature Scaling:**
   - When a parameter shows "n × 0.1°C", multiply the register value by 0.1 to get actual temperature
   - When a parameter shows "n × 0.5°C", multiply the register value by 0.5 to get actual temperature
   - When a parameter shows "n × 1°C", the register value equals the actual temperature

2. **Pressure Scaling:**
   - When showing "n × 0.1 bar", multiply the register value by 0.1 to get actual pressure
   - For HP protect/recover: formula is (n+250) × 0.1 bar

3. **Bit Operations:**
   - Bit values are typically 0 or 1
   - Bit addressing format: 0xXXYY where XX is register, YY is bit position
   - Read bit status from the corresponding register and mask the specific bit

4. **Temperature Ranges by Ambient:**
   - T ≥ 14°C
   - [9, 14)°C
   - [4, 9)°C
   - [-5, 4)°C
   - [-10, -5)°C
   - [-16, -10)°C
   - [-23, -16)°C
   - T < -23°C

5. **Invalid Registers:**
   - Registers marked as "Invalid" should not be used
   - Reading these may return undefined values

6. **Write Permissions:**
   - Only machine #1 can modify RW (read/write) parameters
   - Other units (machines #2-#8) can only read these parameters

---

## Example Usage

### Reading a Temperature Value (Register 0x000E - Inlet Temperature)

**Request (03H command):**
```
Device Address: 01
Function Code: 03
Start Address: 00 0E
Number of Registers: 00 01
CRC: [calculated]
```

**Response:**
```
Device Address: 01
Function Code: 03
Byte Count: 02
Data: [High Byte] [Low Byte]
CRC: [calculated]
```

To convert: If data = 0x00FA (250 decimal), actual temperature = 250 × 0.1 = 25.0°C

### Writing a Single Parameter (Register 0x00CA - Hotwater Setpoint)

**Request (06H command):**
```
Device Address: 01
Function Code: 06
Register Address: 00 CA
Data Value: [High Byte] [Low Byte]
CRC: [calculated]
```

To set 45°C: 45 ÷ 0.5 = 90 (0x005A)
Only lower 8 bits are read, so send: 0x005A

---

## Document Information

- **Protocol Version:** 1.2
- **Release Date:** July 5, 2022
- **Document Code:** RS485-CG248075-MODBUS-V1.2-EN

**End of Document**