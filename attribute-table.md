# Attribute Table

Handle | Type (hex)     | Type (description)                  | Value                   | Value (description)       | Permissions
-------|----------------|-------------------------------------|-------------------------|-------------------------- |-------------
0x0001 | 0x2800         | GATT Primary Service Declaration    | 0x1800                  | Generic Access Service    | r
0x0002 | 0x2803         | GATT Characteristic Declaration     | 02:03:00:00:2a          | Device Name (2a00)        | r
0x0003 | 0x2a00         | Device Name                         |                         |                           | r
0x0004 | 0x2803         | GATT Characteristic Declaration     | 02:05:00:01:2a          | Appearance (2a01)         | r
0x0005 | 0x2a01         | Appearance                          | 00:00                   |                           | r
0x0006 | 0x2803         | GATT Characteristic Declaration     | 0a:07:00:02:2a          | PPF (2a02)                | r
0x0007 | 0x2a02         | Peripheral Privacy Flag             | 00                      |                           | r/w
0x0008 | 0x2803         | GATT Characteristic Declaration     | 08:09:00:03:2a          | Recon. Addr. (2a03)       | r
0x0009 | 0x2a03         | Reconnection Address                |                         |                           | w
0x000a | 0x2803         | GATT Characteristic Declaration     | 02:0b:00:04:2a          | PPCP (2a04)               | r
0x000b | 0x2a04         | Periph. Preferred Connection Params | 50:00:a0:00:00:00:e8:03 |                           | r
0x000c | 0x2800         | GATT Primary Service Declaration    | 0x1801                  | Generic Attribute Service | r
0x000d | 0x2803         | GATT Characteristic Declaration     | 20:0e:00:05:2a          | Device Changed (2a05)     | r
0x000e | 0x2a05         | Service Changed                     |                         |                           | i? 
0x000f | 0x2902         | Client Characteristic Configuration | 00:00                   | Write 1 to enable notif.? | r/w
0x0010 | 0x2800         | GATT Primary Service Declaration    | 0xf371                  | X-Light Service           | r
0x0011 | 0x2803         | GATT Characteristic Declaration     | 0a:12:00:f1:ff          |                           | r
0x0012 | 0xfff1         | ???                                 |                         |                           | w
0x0013 | 0x2901         | Characteristic User Description     | "C1"                    |                           | r
0x0014 | 0x2803         | GATT Characteristic Declaration     | 0a:15:00:f2:ff          |                           | r
0x0015 | 0xfff2         | ???                                 |                         |                           | w
0x0016 | 0x2901         | Characteristic User Description     | "C2"                    |                           | r
0x0017 | 0x2803         | GATT Characteristic Declaration     | 0a:18:00:f3:ff          |                           | r 
0x0018 | 0xfff3         | ???                                 |                         |                           | w
0x0019 | 0x2901         | Characteristic User Description     | "C3"                    |                           | r
0x001a | 0x2803         | GATT Characteristic Declaration     | 0a:1b:00:f4:ff          |                           | r
0x001b | 0xfff4         | ???                                 |                         |                           | w
0x001c | 0x2901         | Characteristic User Description     | "C4"                    |                           | r
0x001d | 0x2803         | GATT Characteristic Declaration     | 0a:1e:00:f5:ff          |                           | r
0x001e | 0xfff5         | ???                                 | ? 18 bytes              |                           | r
0x001f | 0x2901         | Characteristic User Description     | "C5"                    |                           | r 
0x0020 | 0x2803         | GATT Characteristic Declaration     | 0a:21:00:f6:ff          |                           | r 
0x0021 | 0xfff6         | ???                                 | ? 18 bytes              |                           | r  
0x0022 | 0x2901         | Characteristic User Description     | "C6"                    |                           | r 
0x0023 | 0x2803         | GATT Characteristic Declaration     | 0a:24:00:f7:ff          |                           | r 
0x0024 | 0xfff7         | ???                                 | ? 18 bytes              |                           | r
0x0025 | 0x2901         | Characteristic User Description     | "C7"                    |                           | r
0x0026 | 0x2803         | GATT Characteristic Declaration     | 0a:27:00:f8:ff          |                           | r 
0x0027 | 0xfff8         | ???                                 | ? 18 bytes              |                           | r
0x0028 | 0x2901         | Characteristic User Description     | "C8"                    |                           | r
0x0029 | 0x2803         | GATT Characteristic Declaration     | 10:2a:00:f9:ff          |                           | r
0x002a | 0xfff9         | ???                                 | ? 18 bytes              |                           | r
0x002b | 0x2902         | Client Characteristic Configuration | 00:00                   |                           | r/w?
0x002c | 0x2901         | Characteristic User Description     | "C9"                    |                           | r

**Note:** Permissions for X-Light characteristics were guessed based on whether they were *able* to be read - ones that are recorded as 'r' may in fact be 'r/w'. 