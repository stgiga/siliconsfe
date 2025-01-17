# SiliconSFe

SiliconSFe is the unofficial successor of the SiliconSF format used by legacy SF2.04.

This repository contains an early implementation of a SiliconSFe header for testing purposes. Due to poor documentation of SiliconSF in `SFSPEC24.PDF`, this header is not completely accurate.

### How to use the SiliconSFe header

The SiliconSFe header (`sample_header.bin`) is intended to be used to build a SiliconSFe ROM file. To do this, you will need:

- the header (`sample_header.bin`)
- a sine wave sample (`sine8192.bin`)
- a copy of The Fixed Jummbox SoundFont by stgiga (available for download [here](https://musical-artifacts.com/artifacts/2722)).

To build a ROM file, you need to write the header and then zeros until address 0x3147e6. Then write the sine wave sample at address 0x3147e6, and then after that the JBSF data at 0x3187e6. 

When this is done, you can then use the SiliconSFe ROM file to test your SiliconSFe code.

Because Jummbox is FOSS, and stgiga has released JBSF under a FOSS license (CC-BY-SA 4.0), we are allowed to distribute these samples. No copyrighted samples are used.

### Not implemented

Because the documentation publicly released by Creative is incomplete, some parts are not implemented in these test ROM files:
- "id" attribute
    - It is apparently 4 bytes and corresponds to irom, but irom can be up to 256 bytes in legacy SF2.04. It doesn't make any sense.
- "checksum" and "checksum2sComplement" attributes
    - These are "short" (2 byte) values but it is unclear what checksum was being used at the time.
    - This may have been decided by the implementer of the SiliconSF format.
- "sampleCompType"
    - It is unclear what each of the "sample precompensation" type numbers are.
    - This may also have been decided by the implementer of the SiliconSF format.