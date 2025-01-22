## 9.2 Header format

### 9.2.1 About the header format

The SiliconSFe header format is almost identical to legacy SF2.04, however an explanation is provided here due to poor documentation of SiliconSF.

Here is the SiliconSFe header format:

```c
typedef struct romHdrType{
    DWORD romRsrc;
    DWORD romByteSize;
    CHAR interleaveIndex;
    CHAR revision[3];
    CHAR id[4];
    SHORT checksum;
    SHORT checksum2sComplement;
    CHAR bankFormat;
    CHAR product[16];
    BYTE sampleCompType;
    CHAR filler1[2];
    CHAR style[16];
    CHAR copyright[80];
    DWORD sampleStart;
    DWORD sineWaveStart;
    DWORD filler2[124];
    SHORT sineWave[SINEWAVESIZE];
} romHdr;
```

### 9.2.2 romRsrc

In the legacy SF2.04 specification, Creative declared this "unused", however it is defined in SiliconSFe as the FourCC used by the chunk header type used by the integrated SF bank, for example `RIFF`, `RF64`, `RIFD`, etc.

### 9.2.3 romByteSize

This is an UNSIGNED `DWORD` value with the size of the SiliconSFe ROM blob in bytes. It is limited to 4 GiB. Signed integers are prohibited.

### 9.2.4 interleaveIndex

This is used for interleaved ROMs. You can interleave up to 256 ROMs with one SiliconSFe blob.

### 9.2.5 revision

This is a revision identifier as an integer. It is 3 bytes long.

### 9.2.6 id

This corresponds to the `iver` value in the integrated SF bank. In Creative's documentation, it is erroneously listed as corresponding to the `irom` value.

### 9.2.7 checksum

This stores the `CRC-16 (ARC)` checksum of the integrated SF bank.

### 9.2.8 checksum2sComplement

This stores the twos-complement of the value found in `checksum`.

### 9.2.9 bankFormat

In the legacy SF2.04 specification, Creative declared this "unused", but it seems to correspond to the `wMajor` value in `ifil`. For an unknown or other format, this value is `0`.

### 9.2.10 product

This stores the product name, conventionally `SiliconSFe`. It is a UTF-8 string.

### 9.2.11 copyright

This stores copyright information about the SiliconSFe blob. It is a UTF-8 string.

### 9.2.12 sampleStart

This stores the location in the SiliconSFe blob where the bank samples start.

### 9.2.13 sineWaveStart

This stores the location in the SiliconSFe blob where the test sine wave sample starts.

### 9.2.14 sineWave

This contains `WORD` values that correspond to a sine wave sample.
