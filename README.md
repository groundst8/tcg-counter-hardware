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
- ~3.25 mA (datasheet says 2.92 mA) peak current (all segments?) ~1.25 mA peak / segment

![Picture1](https://github.com/user-attachments/assets/8b8edc19-059e-45b4-8ac7-fa8590120311)

[TI TCAL9539](https://www.ti.com/lit/ds/symlink/tcal9539.pdf)
- Low-Voltage 16-Bit I2c Expander
- I/Os backpowered protected

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

## Electrical Engineering

[Jupyter Notebook](display.ipynb)
