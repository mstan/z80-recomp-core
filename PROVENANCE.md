# Provenance

This repository was seeded on 2026-07-18 from
[`smsggrecomp`](https://github.com/mstan/smsggrecomp) feature commit `6d93016`.
That branch added the flat single-instruction output used by the Genesis sound
CPU experiment while retaining the same packed state and verified semantic
helpers used by SMS and Game Gear recompilations.

The initial shared files are:

- `include/sms_runtime.h`: author-owned state layout and generated host ABI,
  extracted without behavioral changes.
- `include/z80_ops.h`: semantic helpers derived from the MIT-licensed
  SuperZazu Z80 core by Nicolas Allemand. The original MIT notice is included
  at `LICENSES/SUPERZAZU-MIT.txt`.

The first consumers are `smsggrecomp:feature/genesis-z80-step` and
`segagenesisrecomp:feature/z80-static-apu`. Platform memory maps, schedulers,
interrupt controllers, interpreters, ROM captures, and generated game code
remain in the consumer repositories.


The shared frontend retains the qualified instruction decoder and transactional
explicit-PC emitter from revision `03a440a7864a87dcb582977dd1d40d9cf9a82d8a`
of the extracted Z80 candidate. Its decoder source and header are byte-identical
to `smsggrecomp` revision `35a84d724374edfd53a50fbb650bf8497d0fae92`.
The current SMS/GG WZ writes for mutable absolute and indexed memory operands
are retained alongside the qualified mutable jump and ALU shapes. These
frontend and integration changes are Matthew Stanley contributions under MIT;
all existing third-party notices remain applicable.
