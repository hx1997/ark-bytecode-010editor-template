# Ark Bytecode (.abc) 010Editor Template

010Editor template for .abc (Open/HarmonyOS Ark Bytecode) files

Note: this is NOT a disassembler for the bytecode, but a parser for the file format that contains the bytecode. (Think .dex vs Dalvik bytecode)

For a disassembler, see [https://gitee.com/openharmony/arkcompiler_runtime_core/blob/master/README.md#disassembler-ark_disasm](https://gitee.com/openharmony/arkcompiler_runtime_core/blob/master/README.md#disassembler-ark_disasm).

# Known issues
- Parsing of `LiteralArray` can be flawed due to poor documentation. By the documentation (see `References` section for a link), a `LiteralArray` has two fields: `num_literals` which indicates the number of literals this `LiteralArray` contains (somehow the number must be divided by 2 to get the actual number), and `literals` which is an array of `Literal`. The `Literal` struct, as it turns out, follows a Tag-Value format, which means the first field is a tag determining how the second field should be interpreted. Unfortunately, the tags are not documented at all. I had to extract them from the source code, but I'm not 100% sure if I understood everything in the relevant code correctly, especially the `LiteralTag::ARRAY_*` tags, given that this is such a huge project.
- Parsing of methods referred to by `MethodRegionIndex` is still problematic, so this part of code has been commented out.
- Parsing of <s>`Proto` and</s> `MethodHandle` is not implemented yet.
- Parsing of `LineNumberProgram` is flawed too, as the operands in a line number program are not handled at all, and as such a `0x0` operand would be misinterpreted as marking the end of the program.
- Should be fixed already (if I was looking at the right place in the code!). <s>Parsing of `Value` of `any` type is not implemented. The `Value` struct is used when a `Field`, in its `field_data`, has a `TaggedValue` with the tag `VALUE` (bit of a tongue twister). `Value` follows a Tag-Value format too (but the "tag" is indicated by a field named `type_idx` in `Field`), whose tag is - surprise surprise - poorly documented. Luckily, this time the code is simpler, except for the `0x0b` tag, which means the type of the value is `any`. I have no idea what this type means and how it should be interpreted.</s>

# References
- [Official documentation for the .abc format](https://gitee.com/openharmony/arkcompiler_runtime_core/blob/master/docs/file_format.md) (Careful, a few things in it are not accurate or up-to-date)
- `arkcompiler\runtime_core\libpandafile\literal_data_accessor.h`
- `arkcompiler\ets_runtime\ecmascript\jspandafile\js_pandafile.cpp`
- FieldTag: `arkcompiler\runtime_core\libpandafile\field_data_accessor.cpp`
- LiteralTag: `arkcompiler\runtime_core\libpandafile\literal_data_accessor-inl.h`
- [Official ABC static defect scanner](https://gitee.com/openharmony/arkcompiler_runtime_core/tree/master/libark_defect_scan_aux)

# Changelog
- 2024/7/27: Added support for abc version >= 12.0.1.0
