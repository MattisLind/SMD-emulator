SMD-emulator
============

SMD emulator using SD card

This project aim to create an SMD disk drive emulator that uses a SD card as storage. It is supposed to be used with a [Emulex UD33 Unibus SMD controller](http://bitsavers.informatik.uni-stuttgart.de/pdf/emulex/UD3351002-G_UD33_Sep90.pdf) or the [Norsk Data ND-10/S system](http://www.datormuseum.se/computers/others/nord-10-s) and the [controller](https://dl.dropboxusercontent.com/u/96935524/Datormusuem/ND-11013-01A-EN.pdf) it uses. The soluton would be based on a STM32 family chip and some logic and line drivers and receivers.

SMD disc signals
----------------

The SMD interface uses two cables, A and B cable. The A cable is the control cable that attaches to all discs in a daisy-chain.
The B cable is the data cable and attaches to each of the disc drives.

#### A cable pinout

| Low pin | High pin | Signal name       | Used by emulation | Send / Receive  |
|---------|----------|-------------------|-------------------|-----------------|
|   1     |   31     | Tag 1             |         Y         |      Rx         |
|   2     |   32     | Tag 2             |         Y         |      Rx         |
|   3     |   33     | Tag 3             |         Y         |      Rx         |
|   4     |   34     | Bus 0             |         Y         |      Rx         |
|   5     |   35     | Bus 1             |         Y         |      Rx         |
|   6     |   36     | Bus 2             |         Y         |      Rx         |
|   7     |   37     | Bus 3             |         Y         |      Rx         |
|   8     |   38     | Bus 4             |         Y         |      Rx         |
|   9     |   39     | Bus 5             |         Y         |      Rx         |
|  10     |   40     | Bus 6             |         Y         |      Rx         |
|  11     |   41     | Bus 7             |         Y         |      Rx         |
|  12     |   42     | Bus 8             |         Y         |      Rx         |
|  13     |   43     | Bus 9             |         Y         |      Rx         |
|  14     |   44     | Open Cable Detect |         Y         |      Rx         |
|  15     |   45     | Fault             |         Y         |      Tx         |
|  16     |   46     | Seek Error        |         Y         |      Tx         |
|  17     |   47     | On Cylinder       |         Y         |      Tx         |
|  18     |   48     | Index             |         Y         |      Tx         |
|  19     |   49     | Unit Ready        |         Y         |      Tx         |
|  20     |   50     | (AM Found)  (1)   |         N         |      NA         |
|  21     |   51     | (Busy)  (2)       |         N         |      NA         |
|  22     |   52     | Unit Select Tag   |         Y         |      Rx         |
|  23     |   53     | Unit Select bit 0 |         Y         |      Rx         |
|  24     |   54     | Unit Select bit 1 |         Y         |      Rx         |
|  25     |   55     | Sector            |         Y         |      Tx         |
|  26     |   56     | Unit Select bit 2 |         Y         |      Rx         |
|  27     |   57     | Unit Select bit 3 |         Y         |      Rx         |
|  28     |   58     | Write Protected   |         Y         |      Tx         |
|  29     |          | Pick              |         N         |      NA         |
|         |   58     | Hold              |         N         |      NA         |
|  30     |   60     | (Tag 4) (3)       |         N         |      NA         |

1. Used only with soft sectored disc drives.
2. Used only with dual channel disc drives.
3. The SMD-E interface is supporting Tag 4 and Tag 5.

### B cable pinout

| Low pin | High pin | Signal name       | Used by emulation | Send / Receive  |
|---------|----------|-------------------|-------------------|-----------------|
|   2     |   14     | Servo Clock       |         Y         |      Tx         |
|   3     |   16     | Read Data         |         Y         |      Tx         |
|   5     |   17     | Read Clock        |         Y         |      Tx         |
|   6     |   19     | Write Clock       |         Y         |      Rx         |
|   8     |   20     | Write Data        |         Y         |      Rx         |
|  22     |    9     | Unit Selected     |         Y         |      Tx         |
|  12     |   24     | Index             |         Y         |      Tx         |
|  13     |   26     | Sector            |         Y         |      Tx         |
|   1     |          | GND               |         Y         |      NA         |
|   4     |          | GND               |         Y         |      NA         |
|   7     |          | GND               |         Y         |      NA         |
|  11     |          | GND               |         Y         |      NA         |
|  15     |          | GND               |         Y         |      NA         |
|  18     |          | GND               |         Y         |      NA         |
|  21     |          | GND               |         Y         |      NA         |
|  25     |          | GND               |         Y         |      NA         |

SMD interface
-------------

In total 21 signal are received and handled by 11 75110 chips and 13 signals are transmitted and handled by 7 75107 chips.


![Index Sector timing](http://i.imgur.com/uuZ3x6B.png "Index Sector Timing")

The Index and Sector sigals is generated by the emulation and provided both in the A and B cables. These signals is generated by chained timers inside the STM32 chip. The emulation also generate the 10 MHz Servo Clock signal and Read Clock signal using a timer inside the STM32.



The SMD interafce has foru tag signals, Unit Select tag, Tag 1, Tag 2 and Tag3. The tag signals act as strobe signals. Unit Select Tag is used to strobe the Unit Select Bit 0 - Unit Select Bit 3 into the drive. Tag 1 - Tag 3 is used to strobe in the data on the Bus 0 - Bus 9 signals. Tag 1 strobes the cylinder address, Tag 2 strobes the head address and Tag 3 strobes Write gate, Read gate, Servo Offset Plus, Servo Offset Minus, Fault Clear, Address Mark Enable, RTZ, Data Strobe Early, Data Strobe Late and Release.

This emulation would only use the Unit Select tag for unit selection,  Tag 1 for Track, Tag 2 for Head and Tag 3 for Write Gate, Read Gate, Fault Clear, RTZ.

The Index and Sector sigals is generated by the emulation and provided both in the A and B cables. These signals is generated by chained timers inside the STM32 chip. The emulation also generate the 10 MHz Servo Clock signal and Read Clock signal using a timer inside the STM32.

The emulation generate Write Protected, Unit Ready, On Cylinder, Seek Error and Fault signals on the A cable and Unit Selected on the B cable. It handles the Open Cable Detect signal on the A cable.

Not supported signals are Busy, AM Found, Pick, Hold, and Tag 4.

![Write clock and data timing](http://i.imgur.com/2zWGFky.png "Write clock and data timing")

Write Data is handled by one of the SPI peripherlas in the STM32. The clock signals used is the Write Clock signal from the controller which has been gated by the latched Write Gate signal. Gating takes place in an external 74LVC08 gate. Thus data is recived only when the Write gate is enabled.

![Read clock and data timing](http://i.imgur.com/oeRLdh0.png "Read clock and data timing")

Read Data is handled similarily by a SPI peripheral in the STM32. The clock signal is gated by the latched Read Gate signal as received from the controller and isthe 10 MHz Read Clock and Servo clock generated by a timer peripheral in the STM32 chip. Gating takes place in an external 74LVC08 gate.

![Format track timimg](http://i.imgur.com/1zJVYCS.png "Format track timing")

![Write sector timimg](http://i.imgur.com/4gdz9nc.png "Write sector timing")

![Read sector timimg](http://i.imgur.com/QNRXla0.png "Read sector timing")

The logic inside the STM32 program checks if a read SPI character is available, it then reads it and writes it to the RAM at an offset taken from the current byte position which is caclulated by the Index and Sector timer peripherals. It then chcks if there is the send buffer is empty. In that case it reads a memory buffer at same position and writes it to the send buffer. Then repeat.

The track is stored in raw format in RAm and then written back to flash in disk image format. The format of the track is:
![Sector Format](http://i.imgur.com/7pC46Qv.png "Sector format")

| Field Name                        | Size (Bytes) | Value |
|-----------------------------------|--------------|-------|
| Gap 1                             | 16           |       |
| VFO Sync                          | 11           | 00    |
| Sync Pattern Byte                 | 1            | 0E    |
| Flag Status and Logical Unit Byte | 1            |       |
| Upper Cylinder Address            | 1            |       |
| Lower Cylinder Address            | 1            |       |
| Head Address                      | 1            |       |
| Sector Address                    | 1            |       |
| CRC                               | 2            |       |
| Write Splice                      | 1            |       |
| VFO Sync                          | 13           | 00    |
| Sync Pattern                      | 1            | 09    |
| Data Area                         | n            |       |
| CRC                               | 2            |       |
| EOR Pad                           | 1            |       |
| Gap 3                             | 11           |       |

Interrupt based logic handles the Tag 1, Tag 2 and Unit Select Tag signals together with the Unit Select bits and Bus bits to selct a particular unit or to perform a seek. Bus signals is stored with Tag 3 as clock signal using an external 74LVC374 octal flip flop to handle Write Gate, Read Gate, Fault Clear and RTZ. Write Gate and Read gate signals is feed is directly gating the clock signal whereas the RTZ and Fault Clear is feed into the STM32 microcontroller which is using edge triggered interrupts to detect changes in these signals.

56 ohm terminatotion reistors is required for the B cable, In total four resistors are required. For the A cable termination resistors are only required if this is the last drive in the chain. Total 42 signals has to be terminated in the drive (including the non used signals)
