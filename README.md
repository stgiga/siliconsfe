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

### Things to do

The checksums are calculated based on the actual JBSFv11 bank, and need to be recalculated based on the sample blob. 

### stgiga's prototypes

- stgiga themselves made a "prototype" build of it (basically, stgiga *thinks* it is correct, and hopes they got the endianess of their attempts to populate the `Not Implemented` fields right. Speaking of those fields, stgiga populated them with sensible values.
- The `id` attribute being 4 bytes *actually* is equivalent to `iver`, NOT the 256-byte `irom`, so stgiga put in a value of 11, hoping the endianess is correct. They also set the revision field to 11.
- The checksum values were populated with the CRC16/ARC of the actual SF2, which is a clean 0x2900, and the two's complement of it, 0x5C00 (stgiga hopes the endianess is correct.)
- stgiga researched what precompensation is, and it relates to sampling and DACs. These samples are synthetic, so that doesn't apply except there's filtering, and Zandro Reveille and William B. Santos, two co-authors, have completely dropped off the face of the Earth (or, at least, the Internet) so finding out *their* settings isn't possible, and on that note, JBSF is a project with multiple components, none of which conflict. The multiple components means there is no singular answer. Plus we don't exactly have a list of types. So the value has multiple good reasons to remain zero, a value that actually makes sense *here*, but in light of discovering filters count, it's set to a boolean of 0x01. Check #1.
- The "prototype" can be found [here](https://sourceforge.net/projects/stgigasoundfonts/files/soundfonts/SiliconJBSF.7z/download) or [here](https://sourceforge.net/projects/stgigasoundfonts/files/soundfonts/SiliJBSF.BIN/download) for those interested.

For the version with ALL unused fields populated, go [here](https://sourceforge.net/projects/stgigasoundfonts/files/soundfonts/bleedingedge/SiliJBSF.DAT/download).

It's on SourceForge due to being WAY too large for Github. To put it on a chip, you need at minimum a 1GiB one. I say "at minimum" because *apparently* you aren't limited to one bank, given the spec. Think multiple coexisting cartridges or similar.
If you're using 1GiB memory that you can make show up as USB Mass Storage, if you're on Linux or macOS (potentially WSL could work according to stgiga, but the question is how much access it has), you can use `dd` to flash it, and using a graphical version is *strongly* advised for `very` good reasons. This isn't the only method.

The above prototype MAY be the first ever SiliconSF bank ever made, because looking at EMU's samplers from the era in search of EMU using SiliconSF wasn't exactly fruitful but we don't want to jump to any conclusions either. Either way, it's amazing that one can put SF2 banks on a chip AND that we revived an elusive format.

There's a lot of other things to ponder here, such as making one SF2 load a SiliconSF as a ROM sample source for the purposes of nested SF2s, and then seeing how SFe can take everything to the next level. It would also be really nice for an actual hardware usage of SiliconSF to happen. Also of note is that 1GiB memory chips were not impossible during the later end of the SF2 heyday, they were just expensive to a significant degree. 

If anything, JBSF is probably the best first test-case for SiliconSF, for multiple reasons, but there's a lot more potential for SiliconSF. We've *also* turned JBSF into a libre substitute for the actual onboard samples in an AWE32, allowing for one to replace any ROM samples in an SF2 with libre ones that work on anything that *isn't* a real SF2-capable card, as well as allowing players to play back SF2s that use ROM samples, *without* spreading around an actual AWE32 ROM, something that *isn't* libre. We've published it in another repo.

Also, the sine wave sample IS compliant with the way samples work, and the CRC16/ARC of the "prototype" is a clean number too.

The unused fields were a bit wack. The romRsc field *seemed* like an additional version number to stgiga initially, but then they searched "resource" in the spec and found that "RIFF" means Resource Interchange File Format, and the RIFF magic number is 4 bytes like this field is. So it got filled with `RIFF`, though SFe64 would support `RF64`. The *other* field marked unused, the one-byte bankFormat value would most likely refer to what version of SoundFont is involved. Remember ifil and iver can go beyond 2.x though no official third format existed. If we go by the logic that zero is blank/unknown/undefined and we are committed to the idea that bankFormat determines whether a SiliconSF is based on SBK or SF2, a value of 0x01 would be an SBK-based SiliconSF, and a value of 0x02 would be SF2-based.
So that's the *unused* fields.

InterleaveIndex may not be relevant with modern memory, but the index part of it would indicate that it isn't just a "this bank is interleaved" bit. This approach helps disambiguate portions. It can also be helpful if spanning a bank across multiple memory chips. 

Also, nothing says a SiliconSF has to *fill* a chip. Remember, *we* tell it where a bank and sine are, and *we* tell it how much data to expect. Meaning you could *theoretically* use that for multiple banks on a single chip, or even do that AND span multiple chips if you threw in InterleaveIndex. You'd probably want this if doing banks that aren't as clean-sized as JBSF. 

Also for those interested, the sine data is loaded directly (at least according to the spec), and ideally you want it to have some zeroes after it because of the padding requirements for samples in the SF2 sample chunk. SiliconJBSF *does* do this. In fact we have like 3MiB of zeroes. Oh and the sine is a test signal. The spec doesn't seem to ask much of it. It seemingly makes no inquiries to rate, sm24 use, loops, or key. The spec just asks for the sine's start location and at that location the data. It otherwise doesn't care. This would imply that the test mode calls the shots for the test tone, as it should. Apparently the checksums are ALSO used for diagnostics. Honestly, SiliconSF's features could have actually improved SF2 for everyone. Creative had some rather useful ideas here but never actually tried them, at least from what we know. SFe could actually benefit from incorporating SiliconSF components, and this is something we're digging into. And of course, documenting even what unused fields can do is helpful.

Oh and Creative was nice enough to make SiliconSF headers extensible. You have two bytes after sampleCompType, and 124 DWORDs after sineWaveStart to do what you want with.

Oh and we do advise making sure your buffers and memory are handled properly given the header telling how much data to expect and where to look. Don't be the next 2GiB limit, or the next `PAT` tag issue (which is wild and also non-SF). Code smart.
Also the sine is important to do right too.

stgiga humbly apologizes for the length of their section (starting from `stgiga themselves made a prototype...`) of this document. There are likely shorter ways to say it.
