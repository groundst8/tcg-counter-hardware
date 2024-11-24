# TCG Counter Hardware

## Components

[TI RF430FRL152H](https://www.ti.com/lit/ds/symlink/rf430frl152h.pdf?ts=1718074976833&ref_url=https%253A%252F%252Fwww.ti.com%252Fproduct%252FRF430FRL152H)
- ISO 15693
- NfcV
- 1.5V passivey powered
- 8 bit GPIO
- I2c
- +/- 400 uA GPIO current, 500 uA max all I/O 0.15V voltage drop

[Ynvisible D0432G02V01](https://assets-global.website-files.com/5f7f21e12602d912658dce12/62207f04e89fc72369fa582a_D0432G02V01-V1.0.pdf)
- 7 Segment E-Paper Display
- +/- 1.5V operating voltage
- 0.81 uA steady state current, all segments on
- ~3.25 mA (datasheet says 2.92 mA) peak current all segments; ~1.25 mA peak / segment

[TI TCAL9539](https://www.ti.com/lit/ds/symlink/tcal9539.pdf)
- Low-Voltage 16-Bit I2c Expander
- I/Os backpowered protected

## Constraints

### Harvested Power

Table 4 from [Frequently asked questions for RF430FRL15xH devices](https://www.ti.com/lit/an/sloa247c/sloa247c.pdf) shows the maximum harvested power available when using the using the TRF7970AEVM with a 5-V power supply as the tag reader. It is also mentioned that the current that can be supplied depends on the reader distance to the tag. 

![Screenshot 2024-10-27 at 3 35 25 PM](https://github.com/user-attachments/assets/0d3c73d3-ece4-41e6-90c6-07b050413aad)

## Ynvisible Display Testing

Twenty minute timelapse test to evaluation if the Ynvisible 7 segment display keeps readable contrast over a 20 minute period without power. Twenty minutes was chosen as the upper end of how long a counter would go without being updated. Twenty minutes is the length of the average Magic The Gathering standard format game. Commander games go longer but generally counters are updated over the course of the game which would provide the refresh without requiring additional effort.

[![YouTube](http://i.ytimg.com/vi/dayQ5Arc0Ek/hqdefault.jpg)](https://www.youtube.com/watch?v=dayQ5Arc0Ek)

## Development Board Prototyping

### Iteration 1

![dev_board](https://github.com/user-attachments/assets/842c668a-7c41-4d38-ab9c-c0872c784d76)

- GoodTag11
  - Open source hardware design
  - PCB & SMT assembly by JLCPCB
  - Through hole headers hand soldered by yours truly
- Ynvisible E-Paper Display Kit Adapter

#### Issues
- E-paper display backpowering RF430FRL152H via GPIO ESD diodes in no power state
  - GPIOs do not remain high-Z and display loses contrast
- TimerA0 low power delays not working in ROM patch operating mode
  - Temporarily doing delays on smartphone side
 
## Iteration 2

![PXL_20241019_153513104](https://github.com/user-attachments/assets/786ee0f7-d6f4-45c5-a042-7c8a554176ea)

- Put two 4 channel TMUX1511 analog switches between GPIOs and display
  - Enabled when have power and disabled when no power is applied
  - Fixes display contrast loss issue from Iteration 1

#### Issues
- GPIO Port1.7 is used by ROM code to indicate operating mode and doesn't seem available for display use in ROM patch mode
- Ultimately would be more useful with two 7 segment digits, could potentially use single chip TCAL9539 GPIO expanded that should also remain high-Z with no power
  - Would be fewer parts than the TMUX1511 solution and give greater capability
- Peak current consumption of display was more than anticiapted per segment
  - Look into handling larger peak current with appropriately sized capacitor

Ynvisible provided segment switching current decay curves which are shown below modeled with exponential decay functions. The raw data is available in 1vs7segs.xlsx. I was anticipating the single segment switching current to be lower so will need to do some additional testing and measurement to determine if enough energy will be harvested to do the switching.

![ynvisible](https://github.com/user-attachments/assets/937a3872-02f8-4a9d-9c7e-412e29af8169)

## Iteration 3

![PXL_20241124_011913071](https://github.com/user-attachments/assets/da9ca87d-5d6a-4206-8605-0dc292073559)

- Switched to RF430FRL152HEVM dev board
  - Can program with MSP-FET programmer via JTAG
  - Work around issue programming GoodTag11 with GoodV over the air not working with NFC only ROM firmware
- Switched from TMUX1511s to TCAL9539 for GPIO expansion and backpower protection
- Switched to Ynvisible 2 digit 7 segment display

#### Issues
- Currently powered from MSP-FET source
  - Need to do testing and see if can find solution to passively power from RF field in a way that is economically viable
- Need to get JTAG level transalation working so can switch back to GoodTag11 and custom PCB instead of dev board and use MSP-FET programmer

## Electrical Engineering

[Jupyter Notebook](display.ipynb)
