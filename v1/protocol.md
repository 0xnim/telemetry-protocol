
# Rocket Telemetry Protocol Definition

## 1. Header Structure

The header consists of 64 bits (8 bytes) structured as follows:
```
  1 2 3 4 5 6 7 8   1 2 3 4 5 6 7 8   1 2 3 4 5 6 7 8   1 2 3 4 5 6 7 8
+ - - - - - - - - + - - - - - - - - + - - - - - - - - + - - - - - - - - +
|     version     | tp |     id     |                len                |
+ - - - - - - - - + - - - - - - - - + - - - - - - - - + - - - - - - - - +
|                                 time                                  |
+ - - - - - - - - + - - - - - - - - + - - - - - - - - + - - - - - - - - +
|      Data... (variable length based on message type)                  |
+ - - - - - - - - + - - - - - - - - + - - - - - - - - + - - - - - - - - +
```

1.1. Version (8 bits)
    - Bits 1-8: Protocol version

1.2. Type (2 bits)
    - Bits 9-10: Message type (tp)

1.3. ID (6 bits)
    - Bits 11-16: Message ID

1.4. Length (16 bits)
    - Bits 17-32: Total length of the message in bytes, including the header

1.5 Time (32 bits)
    - Bits 33-64: Unix Timestamp

## 2. Data Fields

Data fields follow the header. Each field starts with a 1-byte identifier, followed by the data. The length of the data depends on the field type.

2.1. Altitude (ID: 0x01)
    - Length: 2 bytes
    - Unit: Meters

2.2. Pressure (ID: 0x02)
    - Length: 2 bytes
    - Unit: hPa

2.3. Rotation X (ID: 0x03)
    - Length: 2 bytes
    - Unit: Degrees per second

2.4. Rotation Y (ID: 0x04)
    - Length: 2 bytes
    - Unit: Degrees per second

2.5. Rotation Z (ID: 0x05)
    - Length: 2 bytes
    - Unit: Degrees per second

2.6. Acceleration X (ID: 0x06)
    - Length: 2 bytes
    - Unit: m/s²

2.7. Acceleration Y (ID: 0x07)
    - Length: 2 bytes
    - Unit: m/s²

2.8. Acceleration Z (ID: 0x08)
    - Length: 2 bytes
    - Unit: m/s²

2.9. Temperature (ID: 0x09)
    - Length: 2 bytes
    - Unit: Celsius

2.10. GPS Latitude (ID: 0x0A)
    - Length: 4 bytes
    - Unit: Degrees (float)

2.11. GPS Longitude (ID: 0x0B)
    - Length: 4 bytes
    - Unit: Degrees (float)

2.12. GPS Altitude (ID: 0x0C)
    - Length: 2 bytes
    - Unit: Meters

2.13. Ground Speed (ID: 0x0D)
    - Length: 2 bytes
    - Unit: m/s

2.14. Heading (ID: 0x0E)
    - Length: 2 bytes
    - Unit: Degrees

2.15. Battery Voltage (ID: 0x0F)
    - Length: 2 bytes
    - Unit: Volts (multiplied by 100 for 2 decimal precision)
/*
2.16. Data Transmission Status (ID: 0x10)
    - Length: 1 byte
    - Values: 0 = Failure, 1 = Success

2.17. GPS Lock Status (ID: 0x11)
    - Length: 1 byte
    - Values: 0 = No Lock, 1 = Lock

2.18. Recovery System Deployment Status (ID: 0x12)
    - Length: 1 byte
    - Values: 0 = Not Deployed, 1 = Deployed

2.19. Camera Feed Status (ID: 0x13)
    - Length: 1 byte
    - Bit field for different cameras (1 = active, 0 = inactive)

*/ // can be combined into a single byte in the future aka 0 0 0 0 0 0 0 0
2.20. Sensor Status (ID: 0x14)
    - Length: 2 bytes
    - Bit field for various sensors (1 = operational, 0 = not operational)

2.21. Error Codes (ID: 0x15)
    - Length: 2 bytes
    - Specific integer codes for different errors

2.22. Elapsed Time (ID: 0x16)
    - Length: 4 bytes
    - Unit: Seconds since launch

2.23. Vibration Data (ID: 0x17) //questionable
    - Length: Variable (recommend 2 bytes per axis, 6 bytes total)
    - Raw data from vibration sensors

2.24. Payload Data (ID: 0x18)
    - Length: Variable
    - Custom data format depending on payload type

## 3. Example Message Structure
```
  1 2 3 4 5 6 7 8   1 2 3 4 5 6 7 8   1 2 3 4 5 6 7 8   1 2 3 4 5 6 7 8
+ - - - - - - - - + - - - - - - - - + - - - - - - - - + - - - - - - - - +
|     version     | tp |     id     |                len                |
+ - - - - - - - - + - - - - - - - - + - - - - - - - - + - - - - - - - - +
|                                 time                                  |
+ - - - - - - - - + - - - - - - - - + - - - - - - - - + - - - - - - - - +
|   Field ID      |           Field Data (variable length)              |
+ - - - - - - - - + - - - - - - - - + - - - - - - - - + - - - - - - - - +
|   Field ID      |           Field Data (variable length)              |
+ - - - - - - - - + - - - - - - - - + - - - - - - - - + - - - - - - - - +
|                              ...                                      |
+ - - - - - - - - + - - - - - - - - + - - - - - - - - + - - - - - - - - +

```
Note: The ID byte (used for checks in controller software of GO/NOGO, if there is any rocket nearby with same ID) is incorporated into the header structure as part of the 6-bit ID field.
