C++ headers may not form a circular dependency. The graph of header dependencies therefore forms a directed acyclic graph (DAG). Cycles can create build breaks that are hard to identify, seemingly unrelated to current changes.

In order to avoid header inclusion cycles and to facilitate refactoring, the following inclusion restrictions are used. These restrictions apply to all header files (.hpp) and implementation files (.ipp). Inclusions by source files (.cpp) outside of this DAG is allowed, as it will not cause inclusion cycles, but is discouraged in the interest of clean factoring.

Each root include directory contains a master include file. This should be used by internal library sources external to the directory, as opposed to individual includes. This simplifies refactoring and reduces code bloat while enforcing directory dependency ordering. Sources internal to a given directory may not reference their own master include, as this creates a cycle. All other includes (those internal to a root directory) should be explicit (with cascading not assumed).

A dependency is indicated below in a manner similar to class inheritance, using `:` and `,`. Only the highest level dependency in a dependency chain is listed. In some cases there may be more than one. An inclusion cycle can be broken using a C++ forward declaration where appropriate, indicated by square braces `[]`.

```
mutex         :
optional      :
assert        :
version       :
define        : assert, version
error         : define
exceptions    : define
constants     : define
constraints   : constants
/unicode      : exceptions
/math         : constraints, exceptions
/data         : /math, /unicode
/words        : /data
/radix        : /words
/serial       : /radix
/stream       : /serial, error
/crypto       : /stream
/chain        : /crypo, mutex, optional, [/settings]
settings      : /chain
/machine      : /chain
/message      : /chain
/config       : /message
/wallet       : /message
```