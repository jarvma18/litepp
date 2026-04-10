Build a thin, modern C++ wrapper around the SQLite C API. Zero dependencies,
RAII-safe, type-generic, single header. Eliminates boilerplate and manual
resource management from any project that needs local persistent storage.

### Goals

- Zero dependencies beyond SQLite itself
- RAII ownership of all SQLite handles — no manual `close` or `finalize` calls
- Type-safe parameter binding and result extraction
- Iterable result sets usable in range-for loops
- Clear, useful error messages on failure
- Single header or two-file design for easy integration

### Implementation Phases

**Phase 1 — Core handle management**  
RAII wrappers for `sqlite3*` and `sqlite3_stmt*`. Correct lifetime,
move semantics, no copies.

**Phase 2 — Statement & parameter binding**  
Prepare statements, bind typed parameters (`int`, `double`,
`std::string`, `std::string_view`, `nullptr` for NULL).

**Phase 3 — Result iteration**  
Row type with typed `get<T>(column_name)` and `get<T>(column_index)`.
Result set iterable with range-for.

**Phase 4 — Transactions**  
`transaction(callable)` — begins, commits on success, rolls back
on exception.

**Phase 5 — Error handling**  
Custom exception type carrying the SQLite error code and message.
No silent failures.
