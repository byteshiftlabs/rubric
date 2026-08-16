---
pack: task-workflows
summary: Guidance for identifying and improving inefficient code paths and scaling issues.
tags: [workflow, performance, optimization]
---

# Performance

Avoid obvious inefficiency without drifting into premature optimization.

## Prompt

```
Review [CODE_OR_DESIGN] for performance issues in [PROJECT_NAME].

Focus on:
- Algorithm choice (O COMPLEXITY)
- Data structure access patterns
- Clear inefficiencies such as byte-at-a-time I/O or excessive allocation
- I/O patterns (batching, buffering)

Do NOT focus on:
- Micro-optimizations (unless profiling shows a proven bottleneck)
- Premature optimization of code that hasn't run yet
- Making code unreadable for hypothetical 5% speedups

Questions:
- Is the algorithm appropriate for the problem size?
- Does the data structure match the access pattern?
- Are there obvious inefficiencies (byte-at-a-time I/O, N+1 queries, quadratic loops)?
- Is I/O properly buffered?
- Are allocations reasonable?

If uncertain about performance impact, say so. Don't optimize without measuring.
```

## Placeholders

- `[CODE_OR_DESIGN]`: Code or architecture to review
- `[PROJECT_NAME]`: Project context

## Performance Guidelines

### 1. Choose the Right Algorithm

**Algorithmic choice matters more than micro-optimizations.**

```
Problem: Find an item in a collection

Bad: O(n) linear search through unsorted array → 1,000,000 comparisons for 1M items
Good: O(log n) binary search in sorted array → 20 comparisons for 1M items
Better: O(1) hash table lookup → 1 operation regardless of size

Don't optimize a bubble sort. Use the right algorithm.
```

**Big-O complexity matters for large inputs:**
- O(1) — Constant time (hash table lookup)
- O(log n) — Logarithmic (binary search)
- O(n) — Linear (single loop)
- O(n log n) — Linearithmic (good sorting algorithms)
- O(n²) — Quadratic (nested loops, avoid for large n)
- O(2ⁿ) — Exponential (only OK for tiny inputs)

### 2. Choose the Right Data Structure

**Data structure choice determines performance characteristics.**

Match data structures to access patterns: sequential → array, keyed lookup → hash table, sorted iteration → tree, priority → heap, FIFO/LIFO → queue/stack.

See [data-structure-design.md](data-structure-design.md) for detailed guidance on access pattern analysis and the "good taste" design principle.

### 3. Don't Do Obviously Stupid Things

**Some inefficiencies are wrong regardless of profile data:**

#### Byte-at-a-Time I/O
```c
// WRONG: Read one byte at a time
while ((c = fgetc(file)) != EOF) {
    process(c);  // System call per byte!
}

// RIGHT: Buffer reads
char buffer[4096];
size_t n = fread(buffer, 1, sizeof(buffer), file);
for (size_t i = 0; i < n; i++) {
    process(buffer[i]);
}
```

#### Allocations in Hot Loops
```c
// WRONG: Allocate inside loop
for (int i = 0; i < 1000000; i++) {
    char *buf = malloc(100);  // 1M allocations!
    process(buf);
    free(buf);
}

// RIGHT: Allocate once
char *buf = malloc(100);
for (int i = 0; i < 1000000; i++) {
    process(buf);
}
free(buf);
```

#### Unnecessary Copies
```c
// WRONG: Copy when pointer suffices
void print_string(char *str) {
    char copy[1000];
    strcpy(copy, str);  // Why copy?
    printf("%s\n", copy);
}

// RIGHT: Use pointer
void print_string(const char *str) {
    printf("%s\n", str);
}
```

#### N+1 Query Problem
```python
# WRONG: Query in loop
users = get_users()
for user in users:
    posts = get_posts(user.id)  # Query per user!

# RIGHT: Batch query
users = get_users()
user_ids = [u.id for u in users]
posts = get_posts_for_users(user_ids)  # One query
```

### 4. Buffer I/O Properly

**Batch I/O operations. Never assume buffering happens automatically.**

```c
// WRONG: Write one byte at a time
for (int i = 0; i < size; i++) {
    fputc(data[i], file);  // Thousands of system calls
}

// RIGHT: Write in chunks
fwrite(data, 1, size, file);  // One system call
```

**Rule of thumb:** Buffer size should be at least 4KB (page size). Larger for bulk operations (64KB to 1MB).

### 5. Minimize System Calls

**System calls are expensive. Batch operations when possible.**

```
Expensive operations:
- File I/O (open, read, write, close)
- Network I/O (send, recv)
- Memory allocation (malloc, free)
- Process creation (fork, exec)

Strategy:
- Batch file reads/writes
- Reuse allocations
- Use buffer pools
- Amortize setup costs
```

### 6. Cache-Friendly Code

**Modern CPUs care about cache locality. Simple guidelines:**

```c
// BAD: Cache-unfriendly (jumps around memory)
struct Node {
    int data;
    struct Node *next;  // Pointer chasing is slow
};

// GOOD: Cache-friendly (sequential access)
int data[1000];  // All data contiguous in memory
```

**Guidelines:**
- Access memory sequentially when possible (arrays > linked lists)
- Keep hot data together in structs
- Avoid pointer chasing in tight loops
- Use struct-of-arrays vs array-of-structs based on access pattern

### 7. Measure Before Optimizing

**Don't guess. Profile.**

```
Before optimizing:
1. Profile to find actual bottleneck
2. Measure current performance
3. Optimize
4. Measure again to verify improvement

If you haven't profiled, you're optimizing the wrong thing.
```

**Tools:**
- **Linux:** `perf`, `gprof`, `valgrind --tool=callgrind`
- **Python:** `cProfile`, `line_profiler`
- **Node.js:** Built-in profiler, Chrome DevTools
- **Any language:** Insert timers around suspected slow code

## When to Optimize

### Always Avoid These (Wrong from the Start)
- ✅ Byte-at-a-time I/O
- ✅ Wrong algorithm (bubble sort for 10K items)
- ✅ Wrong data structure (linked list for random access)
- ✅ Allocations in tight loops
- ✅ N+1 query problem

### Optimize After Measuring
- ⏸️ Micro-optimizations (loop unrolling, etc.)
- ⏸️ Memory layout changes
- ⏸️ Assembly or intrinsics
- ⏸️ Multithreading (complexity cost!)

### Never Optimize
- ❌ Code that runs once at startup
- ❌ Code that's already fast enough
- ❌ Code without profiling data
- ❌ Sacrificing correctness for speed

## Variations

### Algorithm Review
```
Review algorithm choice for [PROBLEM].

Current approach: [ALGORITHM_DESCRIPTION]
Input size: [SIZE_ESTIMATE]

Questions:
- What is the time complexity? O(?)
- Is this appropriate for the input size?
- Are there better algorithms for this problem?
- What are the tradeoffs?

If the complexity is acceptable for the input size, no change needed.
```

### Data Structure Review
```
Review data structure choice for [PURPOSE].

Current: [CURRENT_STRUCTURE]
Access patterns:
- [OPERATION_1]: frequency [HIGH/MEDIUM/LOW]
- [OPERATION_2]: frequency [HIGH/MEDIUM/LOW]

Is the structure appropriate for these access patterns?
If not, suggest alternatives with tradeoff analysis.
```

### I/O Pattern Review
```
Review I/O patterns in [CODE].

Check for:
- Are reads/writes buffered?
- Is buffer size appropriate (>4KB)?
- Are operations batched when possible?
- Are unnecessary copies avoided?

Suggest improvements if found.
```

### Profile-Guided Optimization
```
Profiling results show [FUNCTION] is the bottleneck (X% of runtime).

Function: [FUNCTION_CODE]

Optimize this specific bottleneck. Options:
- Better algorithm?
- Better data structure?
- Reduce allocations?
- Cache results?
- Parallelize (if beneficial)?

Show before/after and explain the improvement.
```

## Examples

### Example 1: String Concatenation

```python
# WRONG: O(n²) — creates new string each iteration
result = ""
for s in strings:  # 10,000 strings
    result += s  # Copies entire result each time!

# RIGHT: O(n) — builds once
result = "".join(strings)
```

**Why:** String concatenation in a loop is quadratic. For 10K strings, wrong approach does 50 million operations. Right approach does 10K.

### Example 2: Unnecessary Computation

```c
// WRONG: Recomputes in loop
for (int i = 0; i < width * height; i++) {
    process(i, width * height);  // Multiplies every iteration!
}

// RIGHT: Compute once
int size = width * height;
for (int i = 0; i < size; i++) {
    process(i, size);
}
```

**Why:** The multiplication doesn't change. Move it outside the loop. Compiler might optimize this, but why rely on it?

### Example 3: Cache Locality

```c
// BAD: Column-major access (cache-unfriendly)
for (int col = 0; col < cols; col++) {
    for (int row = 0; row < rows; row++) {
        process(matrix[row][col]);  // Jumps around memory
    }
}

// GOOD: Row-major access (cache-friendly)
for (int row = 0; row < rows; row++) {
    for (int col = 0; col < cols; col++) {
        process(matrix[row][col]);  // Sequential in memory
    }
}
```

**Why:** Memory is accessed in rows. Accessing columns means jumping across cache lines. For a 1000×1000 matrix, this can be 10× slower.

## Red Flags

**These patterns usually indicate a real problem:**

```
🚩 Loop with file I/O inside
🚩 malloc/new inside tight loop
🚩 Nested loops with O(n²) complexity on large input
🚩 Database query inside loop
🚩 Recursive function without memoization on repeated subproblems
🚩 String concatenation in loop (in languages where strings are immutable)
🚩 Copying large structures instead of using pointers/references
🚩 Reading/writing one byte/character at a time
```

## Common Misconceptions

### "Always use the fastest data structure"
**Wrong.** Use the **appropriate** structure. A simple array is often faster than a fancy tree for small datasets. Complexity matters more for large data.

### "Premature optimization is the root of all evil"
**True, but often misunderstood.** It does not mean "ignore efficiency." It means do not micro-optimize before you know where the bottleneck is. You should still choose the right algorithm and avoid obvious waste.

### "The compiler will optimize it"
**Sometimes.** Don't write obviously bad code expecting the compiler to fix it. Also, the compiler can't fix wrong algorithm choice.

### "Multithreading makes everything faster"
**Wrong.** Multithreading adds complexity and synchronization overhead. Profile first. Many problems don't benefit from parallelization.

### "Low-level languages are always faster"
**Not necessarily.** Python with NumPy can beat naive C code because NumPy uses optimized C libraries. Algorithm choice matters more than language (until it doesn't—then profile).

## Tips

- Think about complexity. O(n²) may be fine for small inputs and unacceptable for large ones.
- Profile before optimizing. Bottlenecks are often not where people first expect them.
- Optimize the hot path instead of spreading effort evenly.
- Start simple. A simple array is often better than a complex structure for small datasets.
- Batch I/O operations. Do not read or write one byte at a time.
- Reuse allocations when possible.
- Measure the improvement. If the change does not help, remove it.
- Readability still matters. Do not trade clarity for a tiny speedup without evidence.

## Trade-offs

Performance often conflicts with other goals:

**Performance vs Readability:**
- Fast code is sometimes ugly code
- Optimize readability first, performance second
- Exception: hot path in performance-critical code

**Performance vs Maintainability:**
- Clever optimizations are hard to maintain
- Document why optimization is needed
- Consider if 2× speedup is worth 10× maintenance cost

**Performance vs Portability:**
- Platform-specific optimizations may not be portable
- SIMD, assembly, calling conventions vary
- Decide if portability or performance matters more

## When Performance Really Matters

Some domains where performance is critical:
- Real-time systems (hard deadlines)
- High-frequency trading (microseconds matter)
- Game engines (60 FPS requirement)
- Video encoding (hours of CPU time per video)
- Large-scale services (small improvement × billions of requests = big savings)

If you are not in one of these domains, optimize for clarity and correctness first, and performance second.

## Related Guides

- [data-structure-design.md](data-structure-design.md) — choosing structures for performance
- [architecture.md](../setup/architecture.md) — system-level performance considerations
- [code-review.md](code-review.md) — reviewing for performance issues
- [debugging.md](debugging.md) — diagnosing performance problems
