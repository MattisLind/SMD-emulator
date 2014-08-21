SMD-emulator
============

SMD emulator using SD card

This project aim to create an SMD disk drive emulator that uses a SD card as storage. The soluton would be based on a STM32 family chip
and some logic and line drivers and receivers.

SMD disc signals
----------------

The SMD interface uses two cables, A and B cable. The A cable is the control cable that attaches to all discs in a daisy-chain.
The B cable is the data cable and attaches to each of the disc drives.

#### A cable pinout

| Low pin | High pin | Signal name       |
|---------|----------|-------------------|
|   1     |   31     | Tag 1             |
|   2     |   32     | Tag 2             |
|   3     |   33     | Tag 3             |
|   4     |   34     | Bus 0             |
|   5     |   35     | Bus 1             |
|   6     |   36     | Bus 2             |
|   7     |   37     | Bus 3             |
|   8     |   38     | Bus 4             |
|   9     |   39     | Bus 5             |
|  10     |   40     | Bus 6             |
|  11     |   41     | Bus 7             |
|  12     |   42     | Bus 8             |
|  13     |   43     | Bus 9             |
|  14     |   44     | Open Cable Detect |
|  15     |   45     | Fault             |
|  16     |   46     | Channel Ready     |
|  17     |   47     | On Cylinder       |
|  18     |   48     | Index             |
|  19     |   49     | Unit Ready        |
|  20     |   50     | (AM Found)  (1)   |
|  21     |   51     | (Busy)  (2)       |
|  22     |   52     | Unit Select Tag   |
|  23     |   53     | Unit Select bit 0 |
|  24     |   54     | Unit Select bit 1 |
|  25     |   55     | Sector            |
|  26     |   56     | Unit Select bit 2 |
|  27     |   57     | Unit Select bit 3 |
|  28     |   58     | Write Protected   |
|  29     |          | Pick              |
|         |   58     | Hold              |
|  30     |   60     | (Tag 4) (3)       |

1. Used only with soft sectored disc drives.
2. Used only with dual channel disc drives.
3. The SMD-E interface is supporting Tag 4 and Tag 5.

### B cable pinout

| Low pin | High pin | Signal name       |
|---------|----------|-------------------|
|   2     |   14     | Servo Clock       |
|   3     |   16     | Read Data         |
|   5     |   17     | Read Clock        |
|   6     |   19     | Write Clock       |
|   8     |   20     | Write Data        |
|  22     |    9     | Unit Selected     |
|  12     |   24     | Index             |
|  13     |   26     | Sector            |
|   1     |          | GND               |
|   4     |          | GND               |
|   7     |          | GND               |
|  11     |          | GND               |
|  15     |          | GND               |
|  18     |          | GND               |
|  21     |          | GND               |
|  25     |          | GND               |
