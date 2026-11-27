# z80-recomp-core

Current licensing and the grant for existing owner contributions are documented in [LICENSE-SCOPE.md](LICENSE-SCOPE.md).

Shared Zilog Z80 static-recompiler runtime contract and verified instruction
semantics used to support:

- [SMS/GG Recomp](https://github.com/mstan/smsggrecomp), where the Z80 is the
  console's main CPU.
- [Sega Genesis Recomp](https://github.com/mstan/segagenesisrecomp), where the
  Z80 runs the cartridge sound driver as the audio coprocessor.

The initial extraction deliberately preserves the validated generated-code ABI:

- `include/sms_runtime.h` defines the packed `Z80State`, flag layout, register
  accessors, and host callback/global surface used by generated code.
- `include/z80_ops.h` provides header-only ALU, flag, rotate, shift, bit, and
  miscellaneous instruction semantics.

The `sms_*` callback names are retained for generated-source compatibility;
they describe host-provided Z80 memory/I/O operations and are not a requirement
that the consumer emulate Master System hardware. Genesis supplies those hooks
with its Z80 RAM, YM2612, PSG, BUSREQ, reset, and interrupt scheduler.

Consumers add this repository as a submodule and either link its interface
target or add `include/` to the generated target:

```cmake
add_subdirectory(external/z80-recomp-core)
target_link_libraries(my_generated_z80 PRIVATE z80-recomp-core::core)
```

The `z80-recomp-core::frontend` static library owns instruction decoding and
explicit-PC native instruction emission. Link it from a recompiler; generated
code still consumes only the header-only runtime contract.

- `z80_decode` reports length, prefix, operands and control flow.
- `z80_emit_step_body` emits one decoded instruction with explicit PC, refresh
  register and cycle settlement. The `_with_scratch` form reuses a caller-owned
  seekable update stream throughout image generation.
- `z80_emit_live_operand_case` emits guarded, statically decoded shapes whose
  mutable operands are read live. Unsupported shapes leave output unchanged.
  Opcode mismatches do not retire an instruction or change CPU state.

Consumers retain image validation, function discovery, bank mapping, image
alternatives, whole-image dispatch assembly and scheduling. The provider
contains no ROM, captured RAM or generated game source. SMS/GG retains its
function-oriented control-flow lowering; its flat-step path uses this shared
explicit-PC emitter.

Configure this repository with CMake and run CTest to check header compatibility,
frontend rejection and scratch handling, generated native execution, mutable
operands, WZ observation and ordered 16-bit bus reads. Embedding the provider
does not add these standalone tests to the consumer build.

## Licensing and provenance

The core is MIT licensed. SuperZazu-derived semantic helpers retain Nicolas
Allemand's attribution and the complete notice in `LICENSES/SUPERZAZU-MIT.txt`.
See [LICENSE-SCOPE.md](LICENSE-SCOPE.md) and [PROVENANCE.md](PROVENANCE.md).
