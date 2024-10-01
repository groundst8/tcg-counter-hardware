# TCG Counter Hardware

## Components

[TI RF430FRL152H](https://www.ti.com/lit/ds/symlink/rf430frl152h.pdf?ts=1718074976833&ref_url=https%253A%252F%252Fwww.ti.com%252Fproduct%252FRF430FRL152H)
- ISO 15693
- NfcV
- 1.5V passivey powered
- 8 bit GPIO
- +/- 400 uA GPIO current, 500 uA max all I/O 0.15V voltage drop

[Ynvisible D0432G02V01](https://assets-global.website-files.com/5f7f21e12602d912658dce12/62207f04e89fc72369fa582a_D0432G02V01-V1.0.pdf)
- 7 Segment E-Paper Display
- +/- 1.5V operating voltage
- 0.81 uA steady state current, all segments on
- 2.92 mA peak current (all segments?) 417uA / segment?

## v2 Design Tradeoffs

The v1 hardware will only interface to a single 7 segment display. Representing 1-9 counters is useful and a good proof of concept for getting hardware working. I think the value would be improved significantly by adding a second 7 segment display to be able to represent counter values from 1-99. While a single chip solution is possible for driving a single 7 segment display, driving two will require some additional chips because as far as I am aware the most GPIOs on an integrated MCU NFC chip is 12. 

Options:
- 2:1 (SPDT) 8-Channel Switch, would have to tie common electrode to ground (slower off switching) due to limited gpios and one needed to select between displays
- 16 Channel Demultiplexer, each segment addressable with 4 GPIOs, 1 for value (on/off), one for common electrode (on/off)
- I2C GPIO Expander (TCAL9539)
- NFC I2C to general MCU with > 14 GPIOs

## Development Board Prototyping

![dev_board](https://github.com/user-attachments/assets/842c668a-7c41-4d38-ab9c-c0872c784d76)

- GoodTag11
  - Open source hardware design
  - PCB & SMT assembly by JLCPCB
  - Through hole headers hand soldered by yours truly
- Ynvisible E-Paper Display Kit Adapter

## Electrical Engineering

[Jupyter Notebook](display.ipynb)

```python
import numpy as np
import matplotlib.pyplot as plt

# Given parameters
I_peak = (2.92/7)*1000        # Peak current in uA
I_steady = (0.00081/7)*1000   # Steady-state current in uA
taus = [40, 50, 60]           # Different time constants in ms

# Time array from 0 to 300 ms
t = np.linspace(0, 300, 1000)  # Time in ms

plt.figure(figsize=(8, 5))

# Plotting the decay function for each time constant
for tau in taus:
    I_t = I_steady + (I_peak - I_steady) * np.exp(-t / tau)
    plt.plot(t, I_t, label=f'τ = {tau} ms')

plt.title('Current Decay During Electrochromic Display Switching')
plt.xlabel('Time (ms)')
plt.ylabel('Current (uA)')
plt.grid(True)
plt.legend()
plt.show()

```