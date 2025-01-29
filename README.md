# 3-phase-power-meter-STM32-CJ
3-phase power metering with STM32F4

### Design specification
- 3-phase power
    - 415V phase voltage 
    - 240V line voltage 
    - Three wires (R,Y,B)
- Measure line current 
- Measure line voltage
- STM32F401CCU6
- 12-bit Unipolar ADC 
- ADC Ref Voltage 3.3V
- OLED display screen
- Non-Intrusive measurement 
- No galvanic isolation
- 

### Power calculation
The total power for the 3-phase system is given by:

``` P = SQRT(3) * V_LINE * I_LINE * POWER_FACTOR ```

Where power factor is between 0 and 1, depenfing on the quality of the supply. 
Please note that this design does not provide power factor correction algorithm or hardware.

### Choice of measurement components 
#### Current measurement
Current measurement is done using a current transformer. The current is transformed by the secondary transformer, passed through a burden resistor and sampled by the STM32 ADC. Below shows the circuit for this purpose:

#### Voltage measurement 
Line voltage measurement is done using the ZMPT101B voltage transformer. From the datasheet of ZMPT101B, the rated voltage is 240V, and at this voltage, 2mA can be produced on the output.
Having this info, the following calculations were done:

Limiting restor calculation
```
R'(limiting resistor) = V/I  
I = 2mA from datasheet, for Vl > 220V
R = (240V)/(2mA) = 120kOhm

```

Sampling resistor calculation

```
Rs = (Voutput) / I = [(Voutput max) / V input max]*R'

Vadc max = 3.3V
Vout max rms = 3.3 / 2.(sqrt(2)) =  1.16V

Voutput max = 1.16V 
R' = 120kOhm
Rs = [1.16 / 240] * 120000
Rs = 580R

```

These values were then used to design the circuit below: 

### Sampling and ADC measurement
STM32 is a 3.3V 12-bit unipolar ADC. Using this the current and voltage measurement circuits above had to be within this range. So DC biasing method using a potential divider is provided. The circuit is shown below.  
This circuit sets the voltage midpoint to around 1.65V when the current or voltage sensor is not measuring any value. 


