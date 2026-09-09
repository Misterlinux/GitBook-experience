# JIT intro

* 1
* 1
* 1
* 1

1

The JIT (Just-In-Time) engine is a built-in query execution feature in PostgreSQL. It's implemented as a hook&#x20;interface which triggers during query initialitation.&#x20;It requires the postgreSQL to be explicitly compiled with an external JIT librray,&#x20;using the --with-llvm configuration flag.

The JIT engine includes a series of settings in the postgresql.conf file stored in disk.&#x20;                                   The pg\_settings runtime view displays the active configuration parameters and returns&#x20;the current values for the JIT columns.

1

```sql
-- It doesnt refer to teh LLVM intallation state, just the runtime state JIT engine
-- setting describing. - The describe teh JIT configuration state
SELECT name, setting, unit FROM pg_settings WHERE name LIKE 'jit%';
```

IMMAGGINI SHOW SETTINGS.

1

The JIT parameter returns the runtime state of the jit engine while the jit\_provider setting\
identifies the assigned library provider.\
They are part of the current configuration metadata rather than hardcoded values in the\
OS envirodment.

1

```sql
// Some code
- The show commnand access teh current setting configutration value.
- WHILE set and reset MODIFY them, it confrim teh enfgine is actuive and 
- configurated for a specific librray, its, the JIT can be disabled, even on by defauilt
- ON by default and LLVMJIT by defaulty for teh jit implementation
- Executing SHOW jit displays the feature flag status for both parameters.
SHOW jit;							- jit         | on      |
SHOW jit_provider;		- jit_provider| llvmjit |
```

1

The pg\_jit\_available() function is a runtime process that returns a boolean value that represents the state of the JIT provider shared library. It implicitly indicates whether JIT compilation is enabled for query execution.

1

```sql
-- A true value means the library is loaded into the librray, ninst5alled, 
-- part of, JIT ENGINE, .so file configurated as part, can be loaded into shared meory 
SELECT pg_jit_available();		- pg_jit_available|	false |
```

1

The JIT engine is a hardcoded hook, it doesn't rely on the CREATE EXTENTION command and it acts indipendently from the extention registered functiuons.                                                                                         The pg\_stat\_statement detects the JIT engine activities as part of the query execution lifecycle and includes a series of columns for its performance statistics. The cost settings control the threshold levels used to trigger the JIT engine and its specific execution steps.

1

> jit\_above\_cost: The threshold for the initial JIT generation step, where it retrieves the LLVM IR code for the query plan's C functions. jit\_inline\_above\_cost: The threshold required to start the inlining process on the LLVM IR data. jit\_optimize\_above\_cost: The optimization step threshold, applied to the generated LLVM IR code.

```sql
-- They setting can be modified uysinfg thew SET command 
SET jit_above_cost = 0;          -- Generate IR for ALL queries
SET jit_inline_above_cost = 0;   -- Inline ALL generated IR
SET jit_optimize_above_cost = 0; -- Optimize ALL generated IR

show jit_above_cost;		- jit_above_cost|	0             |
RESET jit_above_cost;
show jit_above_cost;		- jit_above_cost|	100000        |
```

1

The other JIT columns settings handle the processes outside bthe cost threshold.

> jit\_debugging\_support: Its a boolean value (off by default) it registers the LLLVM ir coede for debug tools.grant access jit\_expressions: Its a boolean value which, when disabled, skips teh LLVM IR code doesnt produce for the operation evaluation ste0p in the query initialitatiopn, and laying back to the generic C fguncitons. jit\_tuple\_deforming: Its a boolean value which, when disabled, doesnt produce, generate, compile any LLVM IR code for the tuple deformation step in the query intiialitratrion, Its used for tables with fixed sizes column in order to remove, teh LLMV IR overhead JIT engine.

1

### The LLVM librray and its code

1

The LLVM library components are stored in a separate directory within the PostgreSQL file structure. It's an external framework that provides an assembly-like, system-independent language that represents code and can be easily converted into machine code. It differs from the default PostgreSQL library made of generic C functions; the LLVM library consists of a series of bitcode (.bc) files that store precompiled, logical representations of the underlying C functions, it adapts to all execution steps part of the query initialization.

The JIT engine triggers based on query planner estimates. It compares jit\_above\_cost with the total query tree cost, which includes disk I/O access and CPU query condition operations. The PostgreSQL assumes that any query with a cost above the JIT threshold is inefficient and requires JIT compilation. It uses distinct thresholds to determine the specific LLVM pipeline steps to apply (such as inlining or optimization).

The executor then inspects individual plan nodes for repetitive, JIT-compatible operations to optimize. The optimization strictly reduces the CPU cycles required to process data; it doesn't reduce the underlying disk I/O.

1

### Different execution between C and LLVM IR code, steps by jit engine

1

The query initialization step generates the execution code necessary to run the query plan,\
which describes two core processes.\
The data access defines the parsing process used for the query table datasets stored on disk\
as binary code. The query expression evaluation covers the logical query conditions included in\
the query request.

The LLVM IR code follows the same steps as the generic C functions. It optimizes and streamlines the process,&#x20;but it doesn't affect the sequence of I/O disk page accesses described by the plan. It compiles the&#x20;execution logic to reduce CPU overhead, but it doesn't modify or optimize the query operations defined by&#x20;the query planner.

1

The standard PostgreSQL query initialization relies on precompiled generic C functions stored in dynamic&#x20;shared libraries (.dll/.so files) and invoked via function pointers.

The data access C function includes a series of switch statements applied to the table catalog metadata;&#x20;it detect the specific columns that are part of the query condition.\
The expression evaluation C function processes the query tree node by node; it sequentially applies each&#x20;operation to every row while continually updating an execution state object.\
The generic C functions are fixed and contain precompiled functions that define the necessary processes,&#x20;but they lack tools to detect the context of the specific query condition.\
They rely on a series of checker conditions which are aplied for every table row; it creates\
CPU branching overhead when applied to large datasets.

1

The LLVM library enables the JIT engine to read the table metadata and the query execution tree once, replacing&#x20;generic C execution loops with a single query-specific execution path during query initialization.

The library generates a custom LLVM IR function designed to tuple deform the query tables.\
It extracts the column offset values from the table metadata and uses them as arguments in specific\
LLVM IR functions designed to access the compiled disk page data.\
The LLVM API reads the node tree operations as part of the expression evaluation step.\
It checks the switch statements in the generic C function and uses their corresponding LLVM IR code\
to create a custom LLVM IR function specific to the expression.\
The JIT engine applies lazy compilation; it only uses the bitcode files specified in the query condition\
and eliminates the CPU branching overhead.

1

### The INLINE, OPTIMZIE STEPS BY TEH LLVM LIBRRAY&#x20;

1

The LLVM IR library processes the raw generated IR code using the Static Single Assignment (SSA) form across&#x20;three sequential steps:

The inline step aggregates all individual LLVM IR functions into a single&#x20;continuous code block. It eliminates function-call overhead&#x20;and consolidates multiple return points into a unified executable block.                                                          &#x20;

The optimization step triggers after inlining, it applies a series of optimization passes\
to the inlined IR code. It eliminates dead code, unrolls loops, and strips safety state checks inherited from&#x20;the original C function that are no longer required in a single continuous code block.

The emission step translates the optimized LLVM IR directly into native machine code, storing\
it in RAM where it is ready for the query executor.

1

### An actual example of teh JIT columns

1

1

1

1

1
