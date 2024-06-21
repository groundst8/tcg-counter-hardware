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
- I2C GPIO Expander
- NFC I2C to general MCU with > 14 GPIOs


