# BlueCap

## Description

BlueCap provides a swift wrapper around Core Bluetooth with additional functionality that includes,

- Closure callbacks to replace protocol implementation for Central Manager peripheral, service and characteristic discovery, characteristic read and write and characteristic value update notifications. Similarly, for Peripheral Managers closures are provided for advertising and characteristic write callbacks.

- Connectorators and Scannerators provide management of peripheral scan and connection events. Timeouts for connection, scanning, read and write.

- A DSL for specification of GATT profiles. Bluetooth LE device manufactures can provide implementations of their GATT profiles for distribution to developer and developers can easily implement GATT profiles for their Bluetooth LE devices.

- Characteristic profile types encapsulating serialization and deserialization of values. Provided types include: Strings, Byte, Int8, UInt16, Int16 and Enum , Structs and more. New types can be added by users. Implementations of characteristic profiles for [TI Sensor tag](http://www.ti.com/ww/en/wireless_connectivity/sensortag/index.shtml?DCMP=PPC_Google_TI&k_clickid=1f619e48-1938-ba89-3b95-000078cf17fd), some [BLESIG GATT Profiles](https://developer.bluetooth.org/TechnologyOverview/Pages/Profiles.aspx) and more.

- A peripheral scanner application provides and example implementation of a Central Manager and a peripheral emulator an example implementation of a Peripheral Manager.

## Project Location

https://github.com/troystribling/BlueCap

## Team Members

- Troy Stribling - github: troystribling, twitter: @troystribling

## Updates

### July 3

- Peripheral scanning and discovery completed.
- Peripheral Connectorator completed
- Service Discovery completed.
- Characteristic Discovery completed.

### July 10

- Serialization/Deserialization and Endianness conversion interfaces completed with implementations for Enums and Ints.
- Characteristic profiles for Enums and Ints completed.

### July 22
- Characteristic read and write completed.
- Characteristic notifications completed.

### August 7
- Completed TI Sensor Tag Service Profile implementations and other examples.
- Completed struct, pair struct and String Characteristic Profile types.
- Completed Characteristic Profile browser.

### August 21
- Completed Peripheral Manager, Mutable Service and Mutable Characteristic implementations.
- Completed Peripheral Simulator.

### September 6
- Completed Location and Region managers.
- Started scan configuration. Completed views for scan mode configuration, scan services configuration and region initiated scan configuration.