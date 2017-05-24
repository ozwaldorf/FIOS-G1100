# FIOS-G1100
All the current information on reverse engineering the FIOS-G1100 Quantum Gateway router

Most of the original info here is from the [binwalk issue thread](https://github.com/devttys0/binwalk/issues/256)

### Hardware information
The cpu is an ARMv7 `Cortina G4`

link found when searching the model number [WPCS7542E A1](http://www.cortina-access.com/dhp/16-products/996-cs7542-cs7522)

The chip is a CS7542

Documentation: [450337 CS7542/CS7522 Product Brief](http://www.cortina-access.com/dhp/download/203/996/61)

### Current method of getting a root console ([creds](https://github.com/jameshilliard))
You have to enable ssh using tr-069 on the WAN side(there's a built in remote activate-able root ssh backdoor), I set up a local genieacs server to do that. Redirecting the router to a local acs server is a bit tricky though, I originally tried to mitm it but that's not possible since the router verifies the acs server ssl certificate.

You can however change the config file to disable ssl and point it at your own acs server, the config file is aes encrypted but I have some python scripts that can decrypt and re-encrypt the config file so that it can be edited(I had to get some help with reversing the encryption scheme from the assembly for that).

These are the config file encryption/decryption scripts I'm using:
- [gwdecrypt.py](https://gist.github.com/jameshilliard/7112235b62dd929d69d7980c979ae7c0)
- [gwencrypt.py](https://gist.github.com/jameshilliard/99191b2a2877220041dc8789fa07339a)

### Information on serial console
The debug console is disabled for the UART pins on the router board.

In the uBoot logs, the router seems to be opening a rw console on UART0. There are apparently multiple serial ports named; UART0, UART1, UART2, and UART3

### Rolling back your firmware ([creds](https://github.com/Brandonv101))
I also found a few hidden firmware rollback and update links assuming that the router is using the 192.168.1.1 IP: http://192.168.1.1/#/advanced/fwupgrade & http://192.168.1.1/#/advanced/fwrestore

### Firmware images/dumps
Currently nobody has a NAND dump of the older firmware that could hold the decryption/encryption keys/methods. Please make a pull request with the dump if you do!

Some dumps?
- https://github.com/paulhkhsu/bhr4
- https://github.com/jameshilliard/bhr4_release_0-17-51
- https://github.com/jameshilliard/bhr4_release_1.3.0.47.64
- https://github.com/jameshilliard/bhr4_release_01_04_00_10

The firmware images are both signed and encrypted with PGP, the [signing](https://pgp.mit.edu/pks/lookup?op=vindex&search=0xC32877552D7B4FA1) key is also different from the encryption key.

I managed to find, extract and decrypt the pgp decryption key on the router for one of the firmware images(bhr4_release_01.03.02.02-FTR_firmwareupgrade.bin.signed). You can grab the key from https://github.com/The5heepDev/FIOS-G1100/blob/master/decryptionkey-release_01.03.02.02.pgp

### Other potentially useful links etc
- http://snapon.lab.bufferbloat.net/~d/verizon_firmware/
- http://myplace.frontier.com/~firmware/
