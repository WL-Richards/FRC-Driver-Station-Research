# Communication Protocols
A mapping and just general information dump of information regarding the communication protocols between FRC robots and the driver station

There is a better more complete mapping here...  https://frcture.readthedocs.io/en/latest/driverstation/ Unfortunately I had this spoiled and thus couldn't figure out the entire thing on my own
<br>

# General Overview:
- All packets involving Strings and other exact data being sent to/from the robot are encoded as TCP packets to assure that the data isn't lost.
- Constant UDP Packets of size 6 (DS -> Robot) and 8 (Robot -> DS) are sent across the network, looks like a heart beat of some kinda.
- Packet size increases on the DS side from 6 to 21 when a joystick is connected.
- The first 2 bytes of packets are an ever increasing packet ID. This ID is kept uniform on packets to the robot and from the robot. See Sample in `To Robot` Section.
<br>

# Technical Details

## Port Mappings

### <b>Driver Station -> Robot</b>
- UDP
    - Source Port: 60970
    - Destination Port: 1110
- TCP
    - Source Port: 55095
    - Destination Port: 1740

### <b>Robot -> Driver Station</b>
- UDP
    - Source Port: 37919
    - Destination Port: 1150
- TCP
    - Source Port:  1740
    - Destination Port: 51800
<br>

## Protocols
---
Information regarding which protocols communicate what information
<br>

### <u>To Robot:</u>
<br>

- It would appear that when a controller is plugged in a TCP packet is sent to the Robot with the windows based controller name. There is also a large sum of additional data there. Which I am not sure of the meaning.
  - ```
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

- When looking closer at packets of length 6 being sent to the robot you can see that the first 2 bytes of data are an always increasing packet ID. While in this sample set the other 4 bytes appear static possibly representing the current robot states (Full File: `UDP_DS_To_Robot_6Bytes.txt`).
  - ```
      | Id    | Communication Type |
      |-------|--------------------| 
      | 0b f0 | 01                 | 00 10 04 
      | 0b f1 | 01                 | 00 10 04 
      | 0b f2 | 01                 | 00 10 04 
      | 0b f3 | 01                 | 00 10 04 
      | 0b f4 | 01                 | 00 10 04 
      | 0b f5 | 01                 | 00 10 04 
      | 0b f6 | 01                 | 00 10 04 
      | 0b f7 | 01                 | 00 10 04 
      | 0b f8 | 01                 | 00 10 04 
      | 0b f9 | 01                 | 00 10 04 
      | 0b fa | 01                 | 00 10 04 
      | 0b fb | 01                 | 00 10 04 
      | 0b fc | 01                 | 00 10 04 
      | 0b fd | 01                 | 00 10 04 
      | 0b fe | 01                 | 00 10 04 
      | 0b ff | 01                 | 00 10 04 
      | 0c 00 | 01                 | 00 10 04 
    ```
     The final packet within the capture looks like this:
    
    ```
      | ID    | Comm Version | Control Mode | Request | Alliance | Size | Data Type (Joystick) | Num Axis | Axes              | Num Buttons | 1 button = 1 bit| Num Hats | Hat 0 to 360, -1 if not pressed or FF FF |
      | 21 ef | 01           | 00           | 10      | 04       | 0e   | 0c                   | 06       | fb 02 00 00 fe 00 | 10          | 00 00           | 01       | ff ff                                    |
    ```

    While the packet is longer the increasing packet ID is still present.
<br>

- At a certain point the data being sent to the robot switches from being a more or less constant 6 bytes of data to 21 bytes. This would appear as though joystick data is possibly being sent over the network now.
  - This is further supported by the fact that the TCP packet sent right before this increase in data is a connection notification of a controller.
### <u>To Diver Station:</u>
<br>

- It also looks as though all messages that the Driver Station receives that are displayed in the console window are sent over TCP
    - ```
        0000   f8 34 41 65 8c 1d f8 d9 b8 13 19 02 08 00 45 00   .4Ae..........E.
        0010   00 e5 0a 08 40 00 3f 06 c5 a2 0a 40 2b 02 0a 40   ....@.?....@+..@
        0020   2b e7 06 cc ca 58 1f d4 8e 7e 86 bb bc e9 50 18   +....X...~....P.
        0030   03 b2 14 39 00 00 00 bb 0b 41 d5 ab d6 01 c7 00   ...9.....A......
        0040   01 00 00 00 01 00 00 4c 4a 6f 79 73 74 69 63 6b   .......LJoystick
        0050   20 42 75 74 74 6f 6e 20 31 20 6f 6e 20 70 6f 72    Button 1 on por
        0060   74 20 30 20 6e 6f 74 20 61 76 61 69 6c 61 62 6c   t 0 not availabl
        0070   65 2c 20 63 68 65 63 6b 20 69 66 20 63 6f 6e 74   e, check if cont
        0080   72 6f 6c 6c 65 72 20 69 73 20 70 6c 75 67 67 65   roller is plugge
        0090   64 20 69 6e 00 5b 65 64 75 2e 77 70 69 2e 66 69   d in.[edu.wpi.fi
        00a0   72 73 74 2e 77 70 69 6c 69 62 6a 2e 44 72 69 76   rst.wpilibj.Driv
        00b0   65 72 53 74 61 74 69 6f 6e 2e 72 65 70 6f 72 74   erStation.report
        00c0   4a 6f 79 73 74 69 63 6b 55 6e 70 6c 75 67 67 65   JoystickUnplugge
        00d0   64 57 61 72 6e 69 6e 67 28 44 72 69 76 65 72 53   dWarning(DriverS
        00e0   74 61 74 69 6f 6e 2e 6a 61 76 61 3a 31 32 34 36   tation.java:1246
        00f0   29 00 00                                          )..

      * Joystick Button 1 on port 0 not available, check if controller is plugged in[edu.wpi.first.wpilibj.DriverStation.reportJoystickUnpluggedWarning(DriverStation.java:1246) *
      ```
      <br>
- Abnormally Long UDP Packet Seemingly Right Before a RIO Reboot
  - ```
    0000   f8 34 41 65 8c 1d f8 d9 b8 13 19 02 08 00 45 00   .4Ae..........E.
    0010   00 57 c0 dc 40 00 3f 11 0f 51 0a 40 2b 02 0a 40   .W..@.?..Q.@+..@
    0020   2b e7 94 1f 04 7e 00 43 4a f0 1f 1f 01 00 31 0c   +....~.CJ.....1.
    0030   38 00 22 05 02 42 30 7d 13 00 00 00 00 00 00 00   8."..B0}........
    0040   00 41 01 be 22 41 c7 87 2a 00 00 00 00 00 00 00   .A.."A..*.......
    0050   00 41 25 68 f5 0f 0e 00 00 00 00 00 00 00 00 00   .A%h............
    0060   00 00 01 00 80                                    .....
    ```
<br>

- Packet seemingly containing timezone information as warranted by the string `PST8DT` within the packet
  - ```
    0000   f8 d9 b8 13 19 02 f8 34 41 65 8c 1d 08 00 45 00   .......4Ae....E.
    0010   00 37 ff 74 00 00 80 11 cf d8 0a 40 2b e7 0a 40   .7.t.......@+..@
    0020   2b 02 ee 2a 04 56 00 23 8c 06 13 7a 01 00 10 04   +..*.V.#...z....
    0030   0b 0f 00 03 77 c7 04 06 14 01 05 79 08 10 50 53   ....w......y..PS
    0040   54 38 50 44 54                                    T8PDT
    ```
<br>
<br>

# Additional Analysis 
The source of these observations are derived from the `FRC_RobotCAP.pcapng` file within this directory
- Capture Contains:
  - Enabled/disabled the robot
  - Connected a Joystick
  - Restarted RIO
  - Restarted Robot Code
  - Sent joystick inputs while enabled to the robot
