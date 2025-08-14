# Zetako CLI Usage Guide

The Zetako CLI provides direct access to the compression engine from the terminal.  
It supports **lossless encode/decode operations**, batch processing, and logging.

---

## Basic Syntax

```bash
zetako-[platform] <command> [options]
Where:

zetako-[platform] is the binary for your OS/architecture (e.g. zetako-linux-x64, zetako-win-x64.exe, zetako-macos-arm64).

<command> is either encode or decode.

Commands
encode
Compress an input file or stream into the Zetako .zcaps format.

bash

./zetako-linux-x64 encode --input block.json --output block.zcaps
decode
Decompress a .zcaps file back to its original form.

bash

./zetako-linux-x64 decode --input block.zcaps --output block.json
Common Options
Flag    Description
--input <path>    Path to the input file (required).
--output <path>    Path for the output file (required).
--log <path>    Save a detailed process log to a file.
--threads <n>    Number of threads to use (auto by default).
--mode <profile>    Compression profile: lossless (default) or a custom profile name.
--overwrite    Overwrite output file if it already exists.
--quiet    Suppress all non-error console output.
--version    Display version and build info.
--help    Show command help.

Examples
1. Compress a blockchain block
bash

./zetako-macos-arm64 encode \
  --input ./data/solana/block_001.json \
  --output ./out/block_001.zcaps \
  --log ./logs/block_001.encode.log
2. Decompress and verify

bash

./zetako-macos-arm64 decode \
  --input ./out/block_001.zcaps \
  --output ./out/block_001.json \
  --log ./logs/block_001.decode.log
3. Batch process a directory

bash

for file in ./data/solana/*.json; do
  ./zetako-linux-x64 encode \
    --input "$file" \
    --output "./out/$(basename "$file").zcaps" \
    --threads 4
done

Logging

When --log <path> is provided, Zetako writes a detailed report including:

Configuration (mode, threads, buffer sizes)

File information (input/output names, sizes)

Performance metrics (encode/decode time, throughput, memory use)

Verification results (SHA256 match, compression ratio)

Example log excerpt:

Configuration:
 - Mode: Lossless
 - Threads: 4
 - Buffer Size: 4,194,304 bytes

File Info:
 - Input: block_001.json (1,049,233 bytes)
 - Output: block_001.zcaps (344,925 bytes)

Performance:
 - Encode: 78.12 MB/s
 - Decode: 73.33 MB/s
 - Reduction: 67.13%
 - SHA256 Match: Yes

Compression Profiles

The --mode flag can select different internal compression strategies:

lossless (default) — Full fidelity, byte-for-byte reversible.

fast — Prioritize speed over ratio.

max — Prioritize compression ratio over speed.

custom:<name> — Use a custom profile defined in config/ (if available).

Note: In the public binary, only lossless may be available.

Exit Codes
Code    Meaning
0    Success
1    General error
2    Invalid arguments
3    File not found
4    Verification failed (roundtrip mismatch)

Tips
Use SSD or fast storage for best throughput.

On large files, higher thread counts may improve speed (--threads auto uses all available cores).

For reproducible benchmarks, pin CPU frequency and disable background processes.

To compress multiple files, combine the CLI with shell scripts (Bash, PowerShell, etc.).

