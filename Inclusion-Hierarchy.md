In order to avoid header inclusion cycles and to facilitate refactoring, the following inclusion restrictions are used. Cycles can create build breaks that are hard to identify, seemingly unrelated to current changes. These restrictions apply to all header files (.hpp) and implementation files (.ipp). Inclusions by source files (.cpp) outside of this hierarchy is allowed, as it will not cause inclusion cycles, but is discouraged in the interest of clean factoring.

Each include directory contains a master include file. This should be used by internal library sources as opposed to individual includes. This simplifies refactoring and reduces code bloat while retaining include dependency ordering. Sources internal to a given directory may not reference their own master include, as this creates a cycle. These includes should not be "chained" other than by the use of master includes, as this complicates refactoring by hiding true dependencies. `define.hpp` is considered a master include file for `assert.hpp` and `version.hpp`, as these provide the full set of global macro definitions.

A dependency is indicated below in a manner similar to class inheritance, using `:` and `,`. Only the highest level dependency in a dependency chain is listed. In some cases there may be more than one. An inclusion cycle can be broken using a C++ forward declaration. These dependencies are indicated by square braces `[]`. If a dependency is narrow its source is braced by parenthesis `()`, and should be eliminated if possible. Current cycles are listed as `{cycle}`

```
assert      :
version     :
define      : assert, version
error       : define
exceptions  : define
constants   : define
constraints : constants
/concurrent : constraints
/unicode    : define
/log        : unicode, concurrent (asio)
/data       : unicode
/words      : /data
/radix      : /data, /words
/serial     : /radix
/stream     : /serial
/crypto     : /stream, concurrent (asio)
/math       : constraints
/chain      : /math, /crypo, [/settings],
              /config (chain_state->checkpoint) {cycle},
              /wallet (input|output->payment_address) {cycle},
              /machine (enums) {cycle}
/machine    : /chain {cycle}
/message    : /chain
/config     : /message, /exceptions
/wallet     : /message {cycle}, /config (property_tree)
settings    : /config
```
Once the asio dependency is isolated from /crypto, the /log and /concurrent directories will be moved to libbitcoin-network.

The `property_tree` class is slated to be replaced with a native implementation. This should be moved to the root directory to isolate /wallet from /config.

The /wallet-/chain dependency should be broken by isolating the `checked` type and emitting it in place of `payment_address` in /chain/chain_state, and by isolating a basic `checkpoint` tuple type from /chain/input and /chain/output.