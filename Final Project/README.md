Khan 2101 Final Project
-----------------------------------------------------------------------------------------
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

| State | Q2 Q1 Q0 |

|---|---|

| S0 | 0 0 0 |

| S1 | 0 0 1 |

| S2 | 0 1 0 |

| S3 | 0 1 1 |

| WA | 1 0 0 |

| WB | 1 0 1 |

| X | 1 1 0 |

| X | 1 1 1 |



Light output table & equations

| Q2 Q1 Q0 | State | RA YA GA RB YB GB |
| 0 0 0 | S0 | 0 0 1 1 0 0 |
| 0 0 1 | S1 | 0 1 0 1 0 0 |
| 0 1 0 | S2 | 1 0 0 0 0 1 |
| 0 1 1 | S3 | 1 0 0 0 1 0 |
| 1 0 0 | WA | 1 0 0 1 0 0 |
| 1 0 1 | WB | 1 0 0 1 0 0 |
| 1 1 0 | X | 1 0 0 1 0 0 |
| 1 1 1 | X | 1 0 0 1 0 0 |

Initially I ignored the two rows (110, 111) as they did not represent any states. As rule 3 states, the two lights can never be green or yellow together. Setting each light to red for each row was safe.

RA = Q2 + Q1
YA = Q2'Q1'Q0
GA = Q2'Q1'Q0' = (Q2 + Q1 + Q0)' (Demorgan's, replaced with NOR)
RB = Q1' + Q2
YB = Q2'Q1Q0
GB = Q2'Q1Q0'


Next-state table & equations

| Q2 Q1 Q0 R | N2 N1 N0 |
| 0 0 0 0 | 0 0 1 |
| 0 0 0 1 | 0 0 1 |
| 0 0 1 0 | 0 1 0 |
| 0 0 1 1 | 1 0 0 |
| 0 1 0 0 | 0 1 1 |
| 0 1 0 1 | 0 1 1 |
| 0 1 1 0 | 0 0 0 |
| 0 1 1 1 | 1 0 1 |
| 1 0 0 0 | 0 1 0 |
| 1 0 0 1 | 0 1 0 |
| 1 0 1 0 | 0 0 0 |
| 1 0 1 1 | 0 0 0 |
| 1 1 0 0 | 0 0 0 |
| 1 1 0 1 | 0 0 0 |
| 1 1 1 0 | 0 0 0 |
| 1 1 1 1 | 0 0 0 |

Rows (0, 1, 4, 5, 8, 9 , 10, 11, 12, 13, 14, 15) are unaffected by R, the request. This is important because it enforces rule 5, the walking states can only occur after the moving light's yellow cycle ends, and both lights remain red for 10 seconds. The values for N2, N1, N0 were determined based on how the 3 bit number represented by Q must continue. 

N2 = Q2'Q0R
N1 = Q0'(Q2'Q1 + Q2Q1') + Q2'Q1'Q0R'
N0 = Q2'Q0' + Q2'Q1Q0R = Q2'Q0' + Q2'Q1R


Timer


Display conversion


Request latch


Testing


Adjustments

Output and next-state logic was easiest with equations

7seg common anode decoder req, distorted board display.

Active on low KEY_0, KEY_1 requires NOT gates. Alters simulation.

Bit extension, tens and ones calculation

STEP, Q, N relation in StateReg subcirc






Final board mapping

I let the Hex digit display on Logisim unmapped, as I wanted the circuit simulation to retain its accuracy. Instead, I used tunnels before splitters to connect the display logic outputs to the Logisim Hex display, and used the splitter fans as inputs for the segment decoder, with outputs representing each segment to map individually to the DE10-Lite. 


