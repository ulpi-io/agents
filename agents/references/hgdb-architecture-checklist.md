# hgDB Architecture Review Checklist

For use by `rust-architecture-reviewer`. Focus: system structure, design choices, spec alignment, testing strategy. NOT line-level bugs — those belong to the correctness reviewer.

---

## 1. Crate Boundaries & Dependency Direction

- [ ] Dependencies flow downward only: storage at bottom, server at top
- [ ] No circular dependencies between crates
- [ ] hgdb-storage has zero DataFusion/Arrow dependency (DataFusion-agnostic)
- [ ] hgdb-doc has zero DataFusion dependency (ScalarUDFs registered in hgdb-sql)
- [ ] Catalog (CatalogProvider, SchemaProvider, TableProvider) lives in hgdb-sql, not hgdb-storage
- [ ] New crates created only when they own real code (no empty stubs per CLAUDE.md)

## 2. Spec Compliance

- [ ] Any new LogicalType variant appears in `docs/hg-type-system.md` section 5
- [ ] Any new DiskType has correct `disk_to_exec()` widening per spec section 1.2
- [ ] BMAP/BARR format changes reflected in spec sections 2/3
- [ ] Coercion rule changes reflected in spec section 12
- [ ] WAL entry types listed in spec WAL Design section
- [ ] Engine hint additions documented in CLAUDE.md WAL section
- [ ] Header sizes, overhead calculations in spec match code

## 3. Testing Policy (from docs/testing.md)

- [ ] Storage & types changes have proptest model test with reference implementation
- [ ] WAL/segment/MVCC changes have SimEnv crash test (or justification for deferral)
- [ ] Plan tree changes have insta snapshot test
- [ ] Bug fixes include regression test (failing first, then fix)
- [ ] Per-crate coverage thresholds met (storage 90%, types 95%, common 90%, sql 80%, server 75%)
- [ ] Diff coverage ≥80% on changed/added lines

## 4. API Shape & Error Model

- [ ] Public types implement Debug, Display, Clone where expected
- [ ] Error types use thiserror with structured fields (not flat strings)
- [ ] Public enums use `#[non_exhaustive]` for forward compatibility
- [ ] Newtypes used for IDs/offsets (not raw u64)
- [ ] `pub(crate)` preferred over `pub` for internal types
- [ ] Builder pattern for structs with many optional fields
- [ ] Trait design: not too many methods, correct associated types

## 5. Cross-Crate Contract Verification

- [ ] Storage public API changes: hgdb-sql consumers still compile and test
- [ ] WAL entry format changes: replay.rs handles new/changed format
- [ ] Catalog API changes: pgwire handlers still work
- [ ] Type mapping changes: both arrow_mapping.rs and type_mapping.rs updated
- [ ] Error type changes: all downstream From impls still compile

## 6. Project Structure

- [ ] Source files under 500 lines (tests extracted to separate files if needed)
- [ ] One module, one responsibility
- [ ] Tests in correct location (unit in `#[cfg(test)]`, integration in `tests/`)
- [ ] Workspace-level `[workspace.dependencies]` for version consistency
- [ ] No dead code (unused functions, types, imports)

## 7. Roadmap Alignment

- [ ] Work matches the current Phase (Phase 1: no hgdb-planner, no vector/search/graph)
- [ ] Phase 1 encodings only: raw, bitpacking, dictionary (no FSST/RLE/LZ4)
- [ ] Features flagged correctly for optional engines
- [ ] No premature optimization before benchmarks exist

---

## Finding Labels

Every finding must be labeled:

| Label | Meaning | Action Required |
|-------|---------|----------------|
| **blocker** | Prevents correct execution of the current phase | Must fix before proceeding |
| **design-risk** | Will cause pain later if not addressed | Should fix, may defer with justification |
| **cleanup** | Improves quality but doesn't block anything | Fix when convenient |
| **question** | Open design question, needs discussion | Discuss, no action required |

## Output Rules

- Findings first, then overall assessment
- May include open questions and design alternatives
- Distinguish present contradiction from future concern
- No line-level bug claims (those belong to correctness reviewer)
- Reference spec sections and CLAUDE.md decisions by line number
