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

## Electrical Engineering

[Jupyter Notebook](display.ipynb)
