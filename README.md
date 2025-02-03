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
- SSD1306 OLED display screen
- Non-Intrusive measurement 
- No galvanic isolation

### Power calculation
The total power for the 3-phase system is given by:

``` P = SQRT(3) * V_LINE * I_LINE * POWER_FACTOR ```

Where power factor is between 0 and 1, depending on the quality of the supply. 
Please note that this design does not provide power factor correction algorithm or hardware.

### Choice of measurement components 
#### Current measurement
Current measurement is done using a current transformer. The current is transformed by the secondary transformer, passed through a burden resistor and sampled by the STM32 ADC. 

The current transformer used here is the CT1275-A1-RC. It has a turns ratio of 1:2500. It can measure a MAX input current of 100A and give an output of 40mA. 

The output current is transformed to voltage using a burden resistor, then this voltage is sampled and measured by the ADC.  


Below shows the circuit for this purpose:
[line-voltage](./images/phase_current_measurement.png)

The current transformers are conneted on the phase lines , where the phase line passes through the current transformer.


Here's the link to the project :
https://u.easyeda.com/join?type=project&key=e6ed463bb0bcfe54d6addecaed85d314&inviter=dd1664aa981942349eac2c5b2c113172

#### Voltage measurement 
Line voltage measurement is done using the ZMPT101B voltage transformer. From the datasheet of ZMPT101B, the rated voltage is 240V, and at this voltage, 2mA can be produced on the output.
Having this info, the following calculations were done:

Limiting resistor calculation
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

[line-voltage](./images/phase-voltage-measurement.png)

### Sampling and ADC measurement
STM32 is a 3.3V 12-bit unipolar ADC. Using this the current and voltage measurement circuits above had to be within this range. So DC biasing method using a potential divider is provided. The circuit is shown below.  
This circuit sets the voltage midpoint to around 1.65V when the current or voltage sensor is not measuring any value. 

### OLED Display 
I used the SSD1306 display for this task. This display uses 12C communication with the MCU. To carry out the connection, I initialized the I2C channel 2 on the STM and linked it with the display.
I2C for the screen is used in fast mode 