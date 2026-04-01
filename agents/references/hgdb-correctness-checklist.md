# hgDB Correctness Review Checklist

For use by `rust-senior-engineer-reviewer`. Every check here targets a concrete bug risk — data corruption, crash, security hole, or semantic regression. No style, no future-proofing.

---

## 1. Architecture Boundary Enforcement (Bug Prevention)

These are correctness checks, not style. Violations cause real bugs.

### row_addr containment
```bash
grep -rn "row_addr\|RowAddr" crates/ --include="*.rs" | grep -v "crates/hgdb-storage/" | grep -v "crates/hgdb-common/" | grep -v "#\[cfg(test)\]" | grep -v "//.*row_addr"
```
**Why this is a bug**: row_addr changes on compaction. Any engine using row_addr instead of row_id will return wrong data after compaction runs.

### DataFusion in storage
```bash
grep -rn "datafusion\|arrow::" crates/hgdb-storage/ --include="*.rs" | grep -v "#\[cfg(test)\]" | grep -v "// "
```
**Why this is a bug**: Storage must be DataFusion-agnostic. A DataFusion import creates a coupling that breaks the Reader trait abstraction and prevents embedded use without DataFusion.

---

## 2. WAL & Durability

- [ ] Every `append_buffered()` caller either uses group commit or explicit `sync()` before treating LSN as durable
- [ ] CRC32c (Castagnoli) used — NOT IEEE CRC32. Verify the crate: `crc32c` crate = correct, `crc32fast` = wrong
- [ ] WAL recovery fails hard on corrupt segments (not warn+skip) unless `force_recover` is explicitly set
- [ ] Oversized entries rejected before write (not silently overflowing segment size)
- [ ] `data_len` capped during decode (MAX_ENTRY_PAYLOAD) to prevent OOM from corrupt data
- [ ] Every WAL entry type has a corresponding replay handler — unreplayed entry types cause silent data loss
- [ ] `sync_data()` (fdatasync) used, not `sync_all()` — correctness is same, but verify intent

---

## 3. MVCC Visibility

- [ ] Visibility rule: `created_txn <= snapshot AND deleted_txn > snapshot` — verify in every new scan path
- [ ] No `std::sync::Mutex` in production paths — poisoning causes server crash. Use `parking_lot::Mutex`
- [ ] No `expect()` or `unwrap()` in library code production paths — panics crash the server
- [ ] Compaction GC checks `oldest_active_snapshot` before deleting versions
- [ ] Update = delete old + insert new in same txn — not just overwrite

---

## 4. Serialization Round-Trips

- [ ] Every binary format (WAL entry, BMAP, BARR, DEC, segment footer) has `decode(encode(x)) == x`
- [ ] Display/parse round-trips for LogicalType (e.g., EMBEDDING with model parameter)
- [ ] Hash collisions in BMAP handled correctly (key strings verified, not just hash match)

---

## 5. Wire Protocol Safety

- [ ] No internal error details (file paths, stack traces) in pgwire responses
- [ ] User input goes through parser — no raw SQL passthrough
- [ ] Unbounded allocation from user-controlled size fields rejected
- [ ] Proper SQLSTATE codes (42P01, 42601, 22P02, etc.)

---

## 6. Concurrency

- [ ] No mutex held across `.await` (parking_lot::Mutex is not async-safe)
- [ ] AtomicOrdering sufficient for the invariant (Relaxed ok for unique IDs, need Acquire/Release for happens-before)
- [ ] Background tasks (compaction, WAL consumer) have shutdown mechanism (CancellationToken or watch channel)
- [ ] No data races on shared state — `Arc<parking_lot::RwLock<T>>` or channels

---

## Output Rules

- Every finding MUST explain what breaks: "X causes Y when Z happens"
- Every finding MUST cite file:line
- "No findings" is a valid outcome — clean code exists
- No "possible issue" language — either it's a bug or it's not
- No style, no future-proofing, no "should maybe refactor"
