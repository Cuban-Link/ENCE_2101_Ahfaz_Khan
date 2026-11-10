Khan 2101 Final Project
-----------------------------------------------------------------------------------------

Logisim Evolution 4.1 Simulation
---
**Traffic intersection circuit**

Set clock frequency at 2Hz

The DE10-Lite requires each pedestrian walk button to be passive on press, however this setting must be changed for each button in order to see the correct simulation on Logisim. 

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
| H0a | HEX0, a |
| H0b | HEX0, b |
| H0c | HEX0, c |
| H0d | HEX0, d |
| H0e | HEX0, e |
| H0f | HEX0, f |
| H0g | HEX0, g |
| H1a | HEX1, a |
| H1b | HEX1, b |
| H1c | HEX1, c |
| H1d | HEX1, d |
| H1e | HEX1, e |
| H1f | HEX1, f | 
| H1g | HEX1, g |
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

The assignment describes five phases: Road A moving, Road A stopping, Road B moving, Road B stopping, Walk after press. 

As rule 8 requires, each walk cycle is followed by the light that was initially red, and neither light repeats a green-red cycle after a walk cycle. To make this work, I split the walking phase into two states, WA, WB. The assignment also names the light state RA, YA, GA, and I found this harder to read than AR, AY, AG.


With a Moore Machine, output depend on the current state. With two walk cycles, each can be made to follow a button press and to be followed by the next light's cycle. 

State table for Q2, Q1, Q0:

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
| 0 | 0 | 0 | S0 | 0 | 0 | 1 | 1 | 0 | 0 |
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

Rows (0, 1, 4, 5, 8, 9 , 10, 11, 12, 13, 14, 15) are unaffected by R, the request. This is important because it enforces rule 5, the walking states can only occur after the moving light's yellow cycle ends. The values for N2, N1, N0 were determined based on how each Q must continue to change states. For the values of Q that are not associated with a state, the next state is set to 0 0 0 to begin from S0.

N2 = Q2'Q0R

N1 = Q0'(Q2'Q1 + Q2Q1') + Q2'Q1'Q0R'

N0 = Q2'Q0' + Q2'Q1Q0R = Q2'Q0' + Q2'Q1R

Request

Since the walk button's state must be retained even before it reaches a clock edge, I connected both walk buttons to an OR gate to keep it to one circuit, using an AND gate as a makeshift enable pin to output 1 when a button is pressed, and when Q2 is active, the flip-flop's Set pin receives 0 and the Reset pin receives 1. I did not realise I should have routed D to Q on this flip-flop, and I tried to diagnose the issue with a AI prompt and realized there that I needed to account for Q copying D.


Timer & Display logic

At first I considered a down counter as the foundation of the countdown. The issue this presented was that the display needs to start at 3, and I would need additional logic for loading the numbers 3 and 10 based on Q, and stopping the count at 0 instead of it restarting to 15 as is a mistake I have previously encountered with using down counters for a seven-segment display. Out of laziness, I used an up counter with a subtractor. Multiplexers and comparators in my timer and display logic subcircuits made it easy to produce outputs unique to the state and whether a walk cycle is active. The tens digit is only 1 if the subtractor output is equal to (A)_16 or 10, and the bit extension gave the hex display a 4 bit input. At first I did not change the bit extension from sign extend and an F was displayed, but since 1 is the only number displayed on the tens digit I selected zero extend and it showed 1. Similarly the ones digit is only 0 when the tens digit is 1.

The timer subcircuit also outputs 1 bit labelled CUE, acting as a pulse timed to each state instead of a fixed frequency. I realized this would be helpful when making the state register and had an issue with the state updating.


State register

For the state register I used 3 flip-flops and 3 multiplexers. 3 flip-flops to represent each bit of Q corresponding to a state.

The flip flops represent the current state and output bits for Q. The problem I encountered was that I wired the next-state values to the D pins of each flip-flop, and the timing didn't work and the state changed with each tick of the clock signal. With the multiplexers, the CUE signal from the timer's output dictates whether the flip-flops maintain their value from the previous tick or if they are updated with the next-state values.


Unmentioned adjustments

My light output and next-state subcircuits worked on the first try as I derived the circuit from my equations. Using a NOR for Light A's green light was intentional and preferred, however when finding equations from the state table I wrote the NOR expression first and didn't need to reduce. XOR was a reduction I had to look up Boolean rules to recall. 


Board

After the Logisim simulation worked and met all the rules, I hastily uploaded to the DE10-Lite board and did not recognize the pattern of the LEDs or the active segments of the displays. I noticed the LEDs were on red for both traffic lights for 10 seconds, and when both KEY buttons were held the pattern worked as expected. This also explained the display as I forgot to account for the common anode segment display the board uses. To fix this I put a NOT gate in front of each pedestrian walk button, and I used the combinational analysis tool to assign values to the display segments and create a seven segment decoder. I decided to keep the tunnels from my display logic subcircuit to keep the digital simulation working, and assigned each segment for each display as an output to individually map to the board.


Final board mapping

After fixing the board display, I wanted my Logisim file to show the correct simulation. I let the Hex digit display on Logisim unmapped. Instead, I used tunnels before splitters to connect the display logic outputs to the Logisim hex display, and used the splitter fans as inputs for the segment decoder, with outputs representing each segment to map individually to the DE10-Lite. I also removed the NOT gates and realized I could just set each button to passive on high.


