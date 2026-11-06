# The 22H20 Tables

> Offsets are truth. Everything else is commentary.

iOS 18.7 (22H20) kernel offset derivations for the A12 generation, hash-anchored
to the signed IPSW kernelcaches they were derived from. This repository is the
proof. The tables are the product - they are not stored here, and this README
explains why.

## What this is

A complete, dual-path-verified offset set for the last iOS build Apple ever
shipped to the iPhone XS / XS Max / XR generation:

| item | content |
| --- | --- |
| boards | iPhone11,2_4_6 (XS / XS Max / XS Max CN) and iPhone11,8 (XR) |
| anchors | kernproc, kernel_task, mach_trap_table, bsd_syscall_table, mig_kern_subsystem, task_init |
| struct deltas | krw / task / sandbox / rwxfer / vm sets with per-field provenance |
| icmp6 | icmp6Filt = 0x148, soCount = 0x254, re-derived on both boards |
| format | flat JSON + C header with #define slot assignments |

Every value carries the method that produced it. Nothing in the tables is
guessed, copied from a third-party list, or rounded.

## The hash anchors

A derivation without the kernelcache hash attached is a claim. These are
anchored:

````
iPhone11,2_4_6  uuid 5cf7adf8633c3dd8b43ffdbe0bcc2990
iPhone11,8      uuid 5941a47dfef918192bd18b9f463f0cfe
````

The kernelcache sha256 values ship inside the dataset, next to every board.
Anyone holding the signed IPSW can verify the anchor in minutes and reproduce
any value in the tables independently. That reproducibility is the entire
point: these tables do not ask to be trusted.

## Why 18.7 is the interesting build

Public catalogs (ipsw.me, AppleDB, the OTA feed, web archives) list iOS 18.7
for exactly four device identifiers: iPhone11,2 / 11,4 / 11,6 / 11,8. Apple
shipped 18.7 only to the A12 generation - the devices that could not take
iOS 26. Every "22H20" row circulating for A13+ devices is a copied or
fabricated row; there is no IPSW behind it.

That makes the A12 pair the complete real-world 18.7 surface - and, as far as
we can verify, nobody has published a hash-anchored derivation for either
board. The published analyses of this build line stop at 18.6.2.

## Methodology

The pipeline is public tooling, run honestly:

1. `ipsw dl appledb --os iOS --device <id> --version 18.7 --kernel`
   (partialzip range extraction from Apple's CDN)
2. `ipsw kernel symbolicate --signatures blacktop/symbolicator --json`
   (3,069 / 3,871 symbols matched, 79.28%, on both boards)
3. Struct deltas via VERIFY-string anchor derivation against the XNU sources
   for 11417.140.69.700.6
4. kernproc / kernel_task via task_init store-site disassembly (rizin),
   pattern-cross-checked against the same method on the 18.7.1 kernel
5. Independent cross-path agreement: the disassembly-derived function address
   and the symbolication-resolved symbol land on the same instruction

Steps 4 and 5 are the parts that make the dataset worth reading: two
independent derivations agree, which is what separates a table from a rumor.

## Sample, redacted

The real values are redacted. The shape ships here so buyers know exactly
what they are evaluating:

````
kernproc_offset         0xfffffff007a5xxxx
kernel_task_offset      0xfffffff0079fxxxx
bsd_syscall_table       0xfffffff007a0xxxx
mach_trap_table         0xfffffff0079bxxxx
proc_p_pid_off          0x68
so_count_off            0x254
icmp6_filt_off          0x148
````

The struct offsets above are unredacted - they are build-wide public facts,
and any honest derivation agrees on them. The board-specific absolutes are
the part that took the work.

## Why the tables are not in this repository

The dataset is the result of real derivation work on kernels nobody else has
published for. Publishing the numbers would hand that work to every scraper,
mirror bot, and LLM training pipeline within the hour, with zero ability to
ever take it back. This repository demonstrates the work and the method
without donating the result.

The full dataset - flat JSON, C header, per-board provenance blocks, and the
verification logs - is available on request for security researchers,
detection engineers, and authorized test environments.

Contact: <your contact channel here>

## Author

YogSotho - BrokenSec

Derived from public IPSWs with public tools. Every number reproducible.
That is the standard, and this release meets it.
