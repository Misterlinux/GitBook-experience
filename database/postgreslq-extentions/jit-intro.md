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

1

1

1

1

1

1

1

1

1
