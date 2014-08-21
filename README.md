SMD-emulator
============

SMD emulator using SD card

This project aim to create an SMD disk drive emulator that uses a SD card as storage. The soluton would be based on a STM32 family chip
and some logic and line drivers and receivers.

SMD disc signals
----------------

The SMD interface uses two cables, A and B cable. The A cable is the control cable that attaches to all discs in a daisy-chain.
The B cable is the data cable and attaches to each of the disc drives. 

| Low pin | High pin | Signal name       |
|---------|----------|-------------------|
|   1     |   31     | Tag 1             |
|   2     |   32     | Tag 2             |
|   3     |   33     | Tag 3             |
|   4     |   34     | Tag 1             |
|   5     |   35     | Bus 0             |
|   6     |   36     | Bus 1             |
|   7     |   37     | Bus 2             |
|   8     |   38     | Bus 3             |
|   9     |   39     | Bus 4             |
|  10     |   40     | Bus 5             |
|  11     |   41     | Bus 6             |
|  12     |   42     | Bus 7             |
|  13     |   43     | Bus 8             |
|  14     |   44     | Bus 9             |
|  15     |   45     | Bus 10            |
|  16     |   46     | Channel Ready     |
|  17     |   47     | On Cylinder       |
|  18     |   48     | Index             |
|  19     |   49     | Unit Ready        |
|  20     |   50     | (AM Found) *      |
|  21     |   51     | (Busy) *          |
|  22     |   52     | Unit Select Tag   |
|  23     |   53     | Unit Select bit 0 |
|  24     |   54     | Unit Select bit 1 |
|  25     |   55     | Sector |
|  26     |   56     | Unit Select bit 2 |
|  27     |   57     | Unit Select bit 3 |
|  28     |   58     | Write Protected   |
|  29     |          | Pick              |
|         |   58     | Hold              |
|  30     |   60     | (Tag 4)           |
