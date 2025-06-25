# zDURILCAs: **D**ifferential **U**niveRsal **I**nte**L**legent **C**ompression **A**lgorithm for sequencing data

Reference-free compression tool for NGS data.

## Table of Contents
- [Installation](#install)
- [Usage](#usage)
- [Options](#options)
- [Limitations](#limitations)
- [Support & Contacts](#support)

## <a name="install"></a>Installation



You can download precompiled binaries directly from the [Releases](../../releases) page.

### Platform-specific Binaries

| Platform        | Binary Name      | 
| --------------- | ---------------- | 
| Linux (x86\_64) | `zDUR-x86-linux` | 
| Linux (ARM64)   | `zDUR-arm-linux` | 
| macOS (ARM64)   | `zDUR-arm-mac`   | 
| macOS (x86\_64) | `zDUR-x86-mac`   | 


---

### License Notice (Non-Commercial Use Only)

This software is released under a **non-commercial use license**. A valid license key is required to use the tool.

**If you intend to use this tool for academic or research purposes**, please [contact us](#support--contacts) to request a license. We offer free academic licenses valid for 6 months, which can be renewed upon request.

To help us process your request efficiently, please use your institutional email address and include the following information:

- Your full name

- Institution or organization name

- Department or research group (if applicable)

- Intended use (e.g. expected application scenario, data type, and approximate data volume)

**Commercial use is strictly prohibited** without explicit permission.

When you run the binary for the first time, you will be prompted to input your license key:

```bash
zDUR c-simtree
> Please enter your license key: 
```

To request a free academic license, please email: **[mgi_bit_eu@mgi-tech.com](mailto:mgi_bit_eu@mgi-tech.com)**

---

### Quick Setup

After downloading the binary for your platform:

```bash
# Give executable permission
chmod +x zDUR-<arch-platform>

# Rename to 'zDUR' for simplicity
mv zDUR-<arch-platform> zDUR
```

Then you can run it via:

```bash
zDUR --help        # Basic usage and common options
zDUR --help-all    # Full list of all parameters
```

---

### Requirements

* No installation needed.
* Dependencies: `zlib` and a standard system with **C++17-compatible libc/gcc**.




## <a name="usage"></a>Usage

zDUR has 3 compression commands:
- `c-simtree` should be preferred in most cases 
- `c-stereoseq` should be selected if one of two input files contains barcodes (short technical sequences), such as in Stereo Seq and Single Cell datasets
- `c-fast` works better in two cases: 
    - when sequences are too noisy and contain very little redundancy - in this case similarity search in sequences yields very little benefit, making computations unjustified;
    - when sequences are, in contrast, very redundant, such that entropy coding alone can achieve very high compression ratio (this case should be rare in practice)

NB: when using `c-simtree` and `c-stereoseq` commands, decompressed reads are reordered, but otherwise compression is lossless.

All commands can work with gzip-compressed input.

Examples for SE data:
```bash
# compress with Similarity Tree algorithm
./zDUR c-simtree --i1 reads.fastq --threads 4 -o archive.zdur

# compress with entropy coding
./zDUR c-fast --i1 reads.fastq --threads 4 -o archive.zdur 

# decompress to file 
./zDUR d --input archive.zdur --threads 4 -o decompressed_reads.fastq 

# decompress to stdout 
./zDUR d --input archive.zdur --threads 4
```

Examples for PE data and reads with barcodes:
```bash
# compress with Similarity Tree algorithm 
./zDUR c-simtree --i1 reads1.fastq --i2 reads2.fastq --threads 4 -o archive.zdur

# compress with Similarity Tree algorithm treating reads passed to --i1 as barcodes
./zDUR c-stereoseq --i1 reads1.fastq --i2 reads2.fastq --threads 4 -o archive.zdur

# compress with entropy coding
./zDUR c-fast --i1 reads1.fastq --i2 reads2.fastq --threads 4 -o archive.zdur

# decompress to files
./zDUR d --input archive.zdur --threads 4 --o1 decompressed_reads1.fastq --o2 decompressed_reads2.fastq

# decompress to stdout (reads are interleaved)
./zDUR d --input archive.zdur --threads 4
```

## <a name="options"></a>Options

Here we only list a few options which have noticeable impact; for all available options, please see `./zDUR --help-all`

### Options for all commands:
- `--safe-reading` - reduces performance but allows to report line number if zDUR discovers a problem in input data (such as file corruption or invalid fastq format); without this flag, zDUR usually reports the header of a nearby read.
- `--arbitrary-headers` - drops the assumption that all fastq headers in the dataset have exactly the same format; might decrease compression ratio, but allows using zDUR in cases such as compressing concatenated datasets or storing arbitrary information in the headers.

### Options for `c-simree` and `c-stereoseq`:
- `--no-mmap` - by default, zDUR uses available RAM to accelerate operations on temporary files, which improves performance when compressing large (several hundred gigabytes) datasets; `--no-mmap` flag disables this feature
- `--window-size` - lowering this value decreases running time but may reduce compression ratio (especially for Sequence part)
- `--cluster-size-limit-Mb` - lowering this value decreases memory requirement but may reduce compression ratio

### Specific options for `c-simtree`
- `--window-size-pe` - lowering this value decreases running time but may reduce compression ratio (especially for Sequence part)

## <a name="limitations"></a>Limitations

In addition to conforming to the fastq format, input files must meet the following restrictions:
- bases must be one of "ACGTN"
- all headers must have the same prefix and structure (i.e., order of fields), unless `--arbitrary-headers` is passed
- number of bytes occupied by a single fastq record (that is, sum of lengths of 4 lines, including newline characters) should not exceed the value of `--max-bytes-per-read` parameter (though the default value of this parameter should be suitable for short reads data).

## <a name="support"></a>Support & Contacts 

In case you experience problems with zDUR software or just want to chat, feel free to create an issue 
in [the official repository](https://gitee.com/MGI-EU-AF/zdurilcas) or contact developers directly at:
- [mgi_bit_eu@mgi-tech.com](mailto:mgi_bit_eu@mgi-tech.com)

We guarantee that any data provided to us will only be used for purpose of debugging zDUR.
