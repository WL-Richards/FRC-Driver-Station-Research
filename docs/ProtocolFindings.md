# Communication Protocols
### A mapping and just general information dump of information regarding the communication protocols between FRC robots and the driver station
<br>

# Ports

### Driver Station -> Robot
- UDP
- Source Port: 60970
- Destination Port: 1110

<br>

# Protocols
### Information regarding which protocols communicate what information

- It would appear that when a controller is plugged in a TCP packet is sent to the Robot with the windows based controller name
- - ```
    0000   f8 d9 b8 13 19 02 f8 34 41 65 8c 1d 08 00 45 00   .......4Ae....E.
    0010   00 b1 ff a6 40 00 80 06 8f 37 0a 40 2b e7 0a 40   ....@....7.@+..@
    0020   2b 02 d7 37 06 cc bf ab 35 7e 2c 0c 96 84 50 18   +..7....5~,...P.
    0030   02 01 67 c8 00 00 00 29 02 00 00 ff 21 43 6f 6e   ..g....)....!Con
    0040   74 72 6f 6c 6c 65 72 20 28 58 62 6f 78 20 4f 6e   troller (Xbox On
    0050   65 20 46 6f 72 20 57 69 6e 64 6f 77 73 29 00 00   e For Windows)..
    0060   00 00 29 02 01 00 ff 21 43 6f 6e 74 72 6f 6c 6c   ..)....!Controll
    0070   65 72 20 28 58 62 6f 78 20 4f 6e 65 20 46 6f 72   er (Xbox One For
    0080   20 57 69 6e 64 6f 77 73 29 00 00 00 00 08 02 02    Windows).......
    0090   00 ff 00 00 00 00 00 08 02 03 00 ff 00 00 00 00   ................
    00a0   00 08 02 04 00 ff 00 00 00 00 00 08 02 05 00 ff   ................
    00b0   00 00 00 00 00 06 07 00 00 00 00 00 00 01 0e      ...............

    * Controller (Xbox One For Windows) *
    ```

<br>

# Additional Analysis 
### The source of these observations are derived from the `FRC_RobotCAP.pcapng` file within this directory
