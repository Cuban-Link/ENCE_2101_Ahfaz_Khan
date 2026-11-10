Khan 2101 Final Project
-----------------------------------------------------------------------------------------

Logisim Evolution 4.1 Simulation
---
**Traffic intersection circuit**

Set clock frequency at 2Hz

Pin Mapping:

| Logisim I/O | DE10-Lite |
| :---: | :---: |
| PedestrianA | KEY_0 |
| PedestrianB | KEY_1 |
| AG | LEDR5 |
| AY | LEDR4 |
| AR | LEDR3 |
| BG | LEDR2 |
| BY | LEDR1 |
| BR | LEDR0 |
| Oa | HEX0, a |
| Ob | HEX0, b |
| Oc | HEX0, c |
| Od | HEX0, d |
| Oe | HEX0, e |
| ONEf | HEX0, f |
| Og | HEX0, g |
| Ta | HEX1, a |
| Tb | HEX1, b |
| Tc | HEX1, c |
| Td | HEX1, d |
| Te | HEX1, e |
| Tf | HEX1, f | 
| Tg | HEX1, g |
| LED6 | LEDR6 |
| LED7 | LEDR7 |
| LED8 | LEDR8 |
| LED9 | LEDR9 |

As a purely visual choice, LEDR components 6-9 were wired to a constant 0 in Logisim to eliminate any light from unmapped pins.

---

Project constraints
------------------------------------------------------------------------------------------

1. Power-up: Light A is green, Light B is red, display shows 03. 

2. Green lasts for 3s. Yellow lasts for 3s. So essentially the other’s red lasts for 6s. 

3. One road moves at a time. Never green or yellow together. 

4. Remember every press even if long before next clock cycle 

5. Walk starts when moving road’s yellow ends. 6s wait max 

6. Both lights red for 10s when in Walk 

7. Ignore presses during walk.  

8. Cycle continues after walk, no road repeats. 

---------------------------------------------------------------------------------------------

Encoding

Each Q represents a state. State diagrams can be seen in [file name]

| State | Q2 | Q1 | Q0 |
| :---: | :---: | :---: | :---: |
| S0 | 0 | 0 | 0 |
| S1 | 0 | 0 | 1 |
| S2 | 0 | 1 | 0 |
| S3 | 0 | 1 | 1 |
| WA | 1 | 0 | 0 |
| WB | 1 | 0 | 1 |
| X | 1 | 1 | 0 |
| X | 1 | 1 | 1 |


Light output table & equations

| Q2 | Q1 | Q0 | State | RA | YA | GA | RB | YB | GB |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 0 | 0 | 0 | S0 | **0** | **0** | **1** | **1** | **0** | **0** |
| 0 | 0 | 1 | S1 | 0 | 1 | 0 | 1 | 0 | 0 |
| 0 | 1 | 0 | S2 | 1 | 0 | 0 | 0 | 0 | 1 |
| 0 | 1 | 1 | S3 | 1 | 0 | 0 | 0 | 1 | 0 |
| 1 | 0 | 0 | WA | 1 | 0 | 0 | 1 | 0 | 0 |
| 1 | 0 | 1 | WB | 1 | 0 | 0 | 1 | 0 | 0 |
| 1 | 1 | 0 | X | 1 | 0 | 0 | 1 | 0 | 0 |
| 1 | 1 | 1 | X | 1 | 0 | 0 | 1 | 0 | 0 |

Initially I ignored the two rows (110, 111) as they did not represent any states. As rule 3 states, the two lights can never be green or yellow together. Setting each light to red for each row was safe.

RA = Q2 + Q1
YA = Q2'Q1'Q0
GA = Q2'Q1'Q0' = (Q2 + Q1 + Q0)' (Demorgan's, replaced with NOR)
RB = Q1' + Q2
YB = Q2'Q1Q0
GB = Q2'Q1Q0'


Next-state table & equations

| Q2 | Q1 | Q0 | R | N2 | N1 | N0 |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 0 | 0 | 0 | 0 | 0 | 0 | 1 |
| 0 | 0 | 0 | 1 | 0 | 0 | 1 |
| 0 | 0 | 1 | 0 | 0 | 1 | 0 |
| 0 | 0 | 1 | 1 | 1 | 0 | 0 |
| 0 | 1 | 0 | 0 | 0 | 1 | 1 |
| 0 | 1 | 0 | 1 | 0 | 1 | 1 |
| 0 | 1 | 1 | 0 | 0 | 0 | 0 |
| 0 | 1 | 1 | 1 | 1 | 0 | 1 |
| 1 | 0 | 0 | 0 | 0 | 1 | 0 |
| 1 | 0 | 0 | 1 | 0 | 1 | 0 |
| 1 | 0 | 1 | 0 | 0 | 0 | 0 |
| 1 | 0 | 1 | 1 | 0 | 0 | 0 |
| 1 | 1 | 0 | 0 | 0 | 0 | 0 |
| 1 | 1 | 0 | 1 | 0 | 0 | 0 |
| 1 | 1 | 1 | 0 | 0 | 0 | 0 |
| 1 | 1 | 1 | 1 | 0 | 0 | 0 |

Rows (0, 1, 4, 5, 8, 9 , 10, 11, 12, 13, 14, 15) are unaffected by R, the request. This is important because it enforces rule 5, the walking states can only occur after the moving light's yellow cycle ends, and both lights remain red for 10 seconds. The values for N2, N1, N0 were determined based on how the 3 bit number represented by Q must continue. 

N2 = Q2'Q0R
N1 = Q0'(Q2'Q1 + Q2Q1') + Q2'Q1'Q0R'
N0 = Q2'Q0' + Q2'Q1Q0R = Q2'Q0' + Q2'Q1R


Timer


Display conversion


Request latch


Testing


Adjustments

My light output and next-state subcircuits worked on the first try as I derived the circuit from my equations. Using a NOR for Light A's green light was intentional and preferred, however when finding equations from the state table I wrote the NOR expression first and didn't need to reduce. XOR was one I had to look up Boolean rules to recall. 

StateReg:

STEP, Q, N relation in StateReg subcirc


MUX for each D

Swapped MUX inputs

Q tunnels for MUX

Needed both Q outputs and tunnels

Bit width

Comparators

Request

Display Logic

10 instead of a

MUX B input 0 not 1

Need for a bit extender

Zero extend

Numbers lasted 2s at 1Hz clock frequency

Frequency set to 2Hz


Board

After the Logisim simulation worked and met all the rules, I hastily uploaded to the DE10-Lite board and did not recognize the pattern of the LEDs or the active segments of the displays. I noticed the LEDs were on red for both traffic lights for 10 seconds, and when both KEY buttons were held the pattern worked as expected. This also explained the display as I forgot to account for the common anode segment display the board uses. To fix this I put a NOT gate in front of each pedestrian walk button, and I used the combinational analysis tool to assign values to the display segments and create a seven segment decoder. I decided to keep the tunnels from my display logic subcircuit to keep the digital simulation working, and assigned each segment for each display as an output to individually map to the board.


Final board mapping

I let the Hex digit display on Logisim unmapped, as I wanted the circuit simulation to retain its accuracy. Instead, I used tunnels before splitters to connect the display logic outputs to the Logisim Hex display, and used the splitter fans as inputs for the segment decoder, with outputs representing each segment to map individually to the DE10-Lite. 


