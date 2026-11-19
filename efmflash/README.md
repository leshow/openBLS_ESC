# Flashing OpenBLS_ESC EFM8BB10 via the factory UART bootloader

## Setup

```bash
cd efmflash
python3 -m venv venv
./venv/bin/pip install crcmod pyserial intelhex
```

- `efm8load/` — https://github.com/fishpepper/efm8load (vendored here)
- `S_L_50_REV16_7.HEX` — BLHeli_S firmware for this chip (EFM8BB1 family)

## Wiring per channel (separate TX/RX, NOT tied together)

Adapter TX → the channel's RC_IN pad. Adapter RX → pad 18 / the dedicated
bootloader-TX test point. GND → board GND. Power board at around 4V and I set the DC power to 0.25A

| Channel | MCU (board position) | TX → (RC_IN) | RX → (pad 18 net) | Motor pads     |
| ------- | -------------------- | ------------ | ----------------- | -------------- |
| 1       | U201 (top-right)     | TP103        | BL_TX1            | PHASE 1A/1B/1C |
| 2       | U301 (bottom-right)  | TP104        | BL_TX2            | PHASE 2A/2B/2C |
| 3       | U401 (top-left)      | TP105        | BL_TX3            | PHASE 3A/3B/3C |
| 4       | U501 (bottom-left)   | TP106        | BL_TX4            | PHASE 4A/4B/4C |

## Commands

Run from the `efmflash` directory:

```bash
./venv/bin/python3 efm8load/efm8load.py -i                        # identify
./venv/bin/python3 efm8load/efm8load.py -w S_L_50_REV16_7.HEX     # write+verify
```

I just ran this until I got a pin on pad 18, then used gator clips for everything else.

```bash
until ./venv/bin/python3 efm8load/efm8load.py -i; do sleep 1; done
./venv/bin/python3 efm8load/efm8load.py -w S_L_50_REV16_7.HEX
```

esc-configurator didn't seem to work but I soldered a motor to each phase output and it did indeed start with the sound, so it's workin'.
