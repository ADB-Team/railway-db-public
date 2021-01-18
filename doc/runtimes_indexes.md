| Transaction          | min   | max   | avg   | diff |
|----------------------|-------|-------|-------|------|
| ***Without indexes***      |       |       |       |      |
| Transaction 1        | 0.472 | 0.638 | 0.583 |      |
| Transaction 3        | 3.1   | 3.907 | 3.679 |      |
| Transaction 5        | 5.738 | 5.967 | 5.871 |      |
| Transaction 7        | 1.967 | 2.098 | 2.033 |      |
| Backup Transaction 2 | 1.071 | 1.197 | 1.074 |      |
| Backup Transaction 4 | 1.735 | 1.74  | 1.661 |      |
| Backup Transaction 5 | 5.82  | 5.895 | 5.848 |      |
|                      |       |       |       |      |
| ***Hash Index***           |       |       |       |        |
| Transaction 1        | 0.471 | 0.582 | 0.502 | +0.081 |
| Transaction 3        | 3.168 | 4.315 | 3.804 | -0.126 |
| Transaction 5        | 4.29  | 4.677 | 4.417 | +1.455 |
| Transaction 7        | 2.027 | 2.734 | 2.25  | -0.217 |
| Backup Transaction 2 | 1.1   | 1.394 | 1.179 | +1.119 |
| Backup Transaction 4 | 2.229 | 2.966 | 2.422 | -0.688 |
| Backup Transaction 5 | 3.764 | 3.965 | 3.852 | +1.996 |
|                      |       |       |       |        |
| ***Btree Index***          |       |       |       |         |
| Transaction 1        | 0.415 | 0.739 | 0.538 | -0.045  |
| Transaction 3        | 3.126 | 3.988 | 3.602 | -0.067  |
| Transaction 5        | 4.305 | 4.866 | 4.554 | -1.317  |
| Transaction 7        | 2.05  | 2.777 | 2.299 | +0.2657 |
| Backup Transaction 2 | 1.098 | 1.264 | 1.161 | +0.0866 |
| Backup Transaction 4 | 2.16  | 2.357 | 2.227 | +0.493  |
| Backup Transaction 5 | 3.721 | 4.028 | 3.84  | -2.007  |
