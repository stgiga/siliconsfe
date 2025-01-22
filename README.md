# SiliconSFe

SiliconSFe is the unofficial successor of the SiliconSF format used by legacy SF2.04.

This repository contains an early implementation of a SiliconSFe header for testing purposes. Due to poor documentation of SiliconSF in `SFSPEC24.PDF`, this header is not completely accurate.

### How to use the SiliconSFe header

The SiliconSFe header (`sample_header.bin`) is intended to be used to build a SiliconSFe ROM file. To do this, you will need:

- the header (`sample_header.bin`)
- a sine wave sample (`sine8192.bin`)
- a copy of The Fixed Jummbox SoundFont by stgiga (available for download [here](https://musical-artifacts.com/artifacts/2722)).

To build a ROM file/sample blob, start by extracting all of the samples from JBSF. 

After that, write the header and then zeros until address 0x3232bc. Then write the sine wave sample at address 0x3232bc, and then after that the JBSF samples at 0x3272bc.

Write the JBSF samples one after another, with no spaces. When this is done, you can then use the SiliconSFe ROM file to test your SiliconSFe code.

Because Jummbox is FOSS, and stgiga has released JBSF under a FOSS license (CC-BY-SA 4.0), we are allowed to distribute these samples. No copyrighted samples are used.

### Things to do

The checksums are calculated based on the actual JBSFv11 bank, and need to be recalculated based on the sample blob. 

### stgiga's prototypes

- stgiga themselves made a "prototype" build of it (basically, stgiga *thinks* it is correct, and hopes they got the endianess of their attempts to populate the `Not Implemented` fields right. Speaking of those fields, stgiga populated them with sensible values.
- The `id` attribute being 4 bytes *actually* is equivalent to `iver`, NOT the 256-byte `irom`, so stgiga put in a value of 11, hoping the endianess is correct. They also set the revision field to 11.
- The checksum values were populated with the CRC16/ARC of the actual SF2, which is a clean 0x2900, and the two's complement of it, 0x5C00 (stgiga hopes the endianess is correct.)
- stgiga researched what precompensation is, and it relates to sampling and DACs. These samples are synthetic, so that doesn't apply, and Zandro Reveille and William B. Santos, two co-authors, have completely dropped off the face of the Earth (or, at least, the Internet) so finding out *their* settings isn't possible, and on that note, JBSF is a project with multiple components, none of which conflict. The multiple components means there is no singular answer. Plus we don't exactly have a list of types. So the value has multiple good reasons to remain zero, a value that actually makes sense *here*.
- The "prototype" can be found [here](https://sourceforge.net/projects/stgigasoundfonts/files/soundfonts/SiliconJBSF.7z/download) or [here](https://sourceforge.net/projects/stgigasoundfonts/files/soundfonts/SiliJBSF.BIN/download) for those interested.
It's on SourceForge due to being WAY too large for Github. To put it on a chip, you need at minimum a 1GiB one. I say "at minimum" because *apparently* you aren't limited to one bank, given the spec. Think multiple coexisting cartridges or similar.
If you're using 1GiB memory that you can make show up as USB Mass Storage, if you're on Linux or macOS (potentially WSL could work according to stgiga, but the question is how much access it has), you can use `dd` to flash it, and using a graphical version is *strongly* advised for `very` good reasons. This isn't the only method.

The above prototype MAY be the first ever SiliconSF bank ever made, because looking at EMU's samplers from the era in search of EMU using SiliconSF wasn't exactly fruitful but we don't want to jump to any conclusions either. Either way, it's amazing that one can put SF2 banks on a chip AND that we revived an elusive format.

There's a lot of other things to ponder here, such as making one SF2 load a SiliconSF as a ROM sample source for the purposes of nested SF2s, and then seeing how SFe can take everything to the next level. It would also be really nice for an actual hardware usage of SiliconSF to happen. Also of note is that 1GiB memory chips were not impossible during the later end of the SF2 heyday, they were just expensive to a significant degree. 

If anything, JBSF is probably the best first test-case for SiliconSF, for multiple reasons, but there's a lot more potential for SiliconSF. We've *also* turned JBSF into a libre substitute for the actual onboard samples in an AWE32, allowing for one to replace any ROM samples in an SF2 with libre ones that work on anything that *isn't* a real SF2-capable card, as well as allowing players to play back SF2s that use ROM samples, *without* spreading around an actual AWE32 ROM, something that *isn't* libre. We've published it in another repo.

Also, the sine wave sample IS compliant with the way samples work, and the CRC16/ARC of the "prototype" is a clean number too.

stgiga humbly apologizes for the length of their section (starting from `stgiga themselves made a prototype...`) of this document. There are likely shorter ways to say it.
