Summary
Build a lightweight, modern C++ wrapper around the SQLite C API. The goal is a clean, RAII-safe interface that eliminates boilerplate, prevents resource leaks, and is easy to drop into any future C++ project as a single header or two-file library.

Motivation
The raw SQLite C API requires manual statement preparation, explicit sqlite3_finalize calls, error code checking on every operation, and careful lifetime management. This is error-prone and verbose. A well-designed C++ wrapper handles all of that transparently, letting the caller focus on queries and data rather than API mechanics.

Goals

Zero dependencies beyond SQLite itself
RAII ownership of all SQLite handles — no manual close or finalize calls
Type-safe parameter binding and result extraction
Iterable result sets usable in range-for loops
Clear, useful error messages on failure
Single header or two-file design for easy integration


Public API Design (target interface)
cpp// opening a database
auto db = Database::open("data.db");

// simple query with bound parameters
auto rows = db.query("SELECT * FROM users WHERE age > ?", 30);

for (auto& row : rows) {
    auto name = row.get<std::string>("name");
    auto age  = row.get<int>("age");
}

// execute without results
db.execute("INSERT INTO users (name, age) VALUES (?, ?)", "Alice", 31);

// transactions
db.transaction([&]() {
    db.execute("DELETE FROM users WHERE age < ?", 18);
    db.execute("UPDATE stats SET count = count - 1");
});

Implementation Phases
Phase 1 — Core handle management
RAII wrappers for sqlite3* and sqlite3_stmt*. Ensure correct lifetime, move semantics, no copies.
Phase 2 — Statement & parameter binding
Prepare statements, bind typed parameters (int, double, std::string, std::string_view, nullptr for NULL).
Phase 3 — Result iteration
Row type with typed get<T>(column_name) and get<T>(column_index). Result set iterable with range-for.
Phase 4 — Transactions
transaction(callable) — begins, commits on success, rolls back on exception.
Phase 5 — Error handling
Custom exception type carrying the SQLite error code and message. No silent failures.

Key C++ Concepts Practiced

RAII and smart pointer design
Move semantics and deleted copy constructors
Template parameter binding
std::optional for nullable columns
Range-for compatible iterators
Exception safety guarantees


Out of Scope

ORM / schema generation
Async or multi-threaded access patterns
Full query builder DSL
Migration runner (separate future task)


Success Criteria

Wraps the full basic SQLite workflow with no manual resource management
All SQLite errors surface as typed C++ exceptions
Usable by dropping two files into a project
Passes a basic test suite covering open, query, bind, iterate, transaction, and error cases
