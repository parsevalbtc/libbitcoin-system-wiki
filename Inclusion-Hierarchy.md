In order to avoid header inclusion cycles and to facilitate refactoring, the following inclusion restrictions are used. Cycles can create build breaks that are hard to identify, seemingly unrelated to current changes. These restrictions apply to all header files (.hpp) and implementation files (.ipp). Inclusions by source files (.cpp) outside of this hierarchy is allowed, as it will not cause inclusion cycles, but is discouraged in the interest of clean factoring.

Each include directory contains a master include file. This should be used by internal library sources as opposed to individual includes. This simplifies refactoring and reduces code bloat while retaining include dependency ordering. Sources internal to a given directory may not reference their own master include, as this creates a cycle. These includes should not be "chained" other than by the use of master includes, as this complicates refactoring by hiding true dependencies. `define.hpp` is considered a master include file for `assert.hpp` and `version.hpp`, as these provide the full set of global macro definitions.

A dependency is indicated below in a manner similar to class inheritance, using `:` and `,`. Only the highest level dependency in a dependency chain is listed. In some cases there may be more than one. A dependency cycle can be broken using a C++ forward declaration so as to avoid a cyclical header inclusion. These dependencies are indicated by square braces `[]`. If a dependency is narrow its source is braced by parenthesis `()`, and should be eliminated if possible. Current cycles are listed as `{cycle}`



```
assert         :
version        :
define         : assert, version
error          : define
exceptions     : define
constants      : define
constraints    : constants
/concurrency   : constraints
/unicode       : define
/log           : unicode, concurrency (asio)
/data          : unicode
/words         : /data
/radix         : /data, /words
/serialization : /radix
/stream        : /serialization
/crypto        : /stream, concurrency (asio)
/math          : constraints
/chain         : /math, /crypo, [/settings], [/config], /wallet {cycle}, /machine {cycle}
/machine       : /chain {cycle}
/message       : /chain
/config        : /message, /exceptions
/wallet        : /message, /config (property_tree)
settings       : /config
```
