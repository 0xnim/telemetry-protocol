
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
    - Bits 9-10: Message type (tp), (telemetry, command, status, response)

1.3. ID (6 bits)
    - Bits 11-16: Rocket ID

1.4. Length (16 bits)
    - Bits 17-32: Total length of the message in bytes, including the header

1.5 Time (32 bits)
    - Bits 33-64: Unix Timestamp

## Type - Telemetry
###  2. Data Fields

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

2.15. Error Codes (ID: 0x0F)
    - Length: 2 bytes
    - Specific integer codes for different errors

2.16. Elapsed Time (ID: 0x10)
    - Length: 4 bytes
    - Unit: Seconds since launch

2.17. Vibration Data (ID: 0x11) //questionable
    - Length: Variable (recommend 2 bytes per axis, 6 bytes total)
    - Raw data from vibration sensors

2.18. Payload Data (ID: 0x12)
    - Length: Variable
    - Custom data format depending on payload type

## Command

Commands are sent from the ground station to the rocket for various operations. Each command consists of a **Command ID** followed by the relevant data for that command.

The Rocket ID is used.
#### 3. Command Fields:

**3.1. Toggle Telemetry Stream (ID: 0x01)**

- **Length:** 1 byte
- **Description:** Start the telemetry data stream.
- **Field Data:** `0x01` (1 = Start, 0 = Stop)

**3.2. Manually Deploy Recovery System (ID: 0x02)**

- **Length:** 1 byte
- **Description:** Deploy the recovery system (e.g., parachute).
- **Field Data:** `0x01` (1 = Deploy, 0 = Do not deploy(Override))

**3.3. Reset System (ID: 0x03)**

- **Length:** 1 byte
- **Description:** Reset the rocket’s onboard systems.
- **Field Data:** `0x01` (1 = Reset)

**3.4. Manually Activate Payload (ID: 0x04)**

- **Length:** 1 byte
- **Description:** Activate a payload on the rocket.
- **Field Data:** `0x01` (1 = Activate, 0 = Deactivate)

**3.5. Manually Activate Camera Feed (ID: 0x05)**

- **Length:** 1 byte
- **Description:** Turn on/off camera feed.
- **Field Data:** Bit field for different cameras (e.g., `0b00000011` means cameras 1 and 2 are activated) up to 8 cameras.
## Status

The status message provides information about the rocket's current condition, operational states, and onboard systems.
#### 4. Status Fields:

**4.1. System Health (ID: 0x01)**

- **Length:** 1 byte
- **Description:** General health status of the rocket's onboard systems.
- **Field Data:**
    - `0x01` = Healthy
    - `0x00` = Fault Detected

**4.2. Recovery System Status (ID: 0x02)**

- **Length:** 1 byte
- **Description:** Status of the recovery system.
- **Field Data:**
    - `0x01` = Deployed
    - `0x00` = Not Deployed

**4.3. Telemetry Stream Status (ID: 0x03)**

- **Length:** 1 byte
- **Description:** Whether the telemetry stream is active.
- **Field Data:**
    - `0x01` = Active
    - `0x00` = Inactive

**4.4. GPS Lock Status (ID: 0x04)**

- **Length:** 1 byte
- **Description:** Whether the rocket has a GPS lock.
- **Field Data:**
    - `0x01` = Locked
    - `0x00` = Not Locked

**4.5. Battery Voltage (ID: 0x0F)**

- Length: 2 bytes
- Unit: Volts (multiplied by 100 for 2 decimal precision)


**4.6. Sensor Operational Status (ID: 0x35)**

- **Length:** 2 bytes
- **Description:** Status of individual sensors (bit field).
- **Field Data:** Each bit represents a sensor (1 = Operational, 0 = Not operational).
    - Bit 0: Altimeter
    - Bit 1: Accelerometer
    - Bit 2: Gyroscope
    - etc.

## Response

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
