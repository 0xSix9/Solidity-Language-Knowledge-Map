# Solidity Source Files

> Learn how a Solidity file is organized, what each part does, and how to read a real contract from top to bottom.

## What you will learn

By the end of this guide, you should be able to:

- Explain what a Solidity source file is.
- Recognize the purpose of the SPDX license identifier and the `pragma` directive.
- Understand imports and why projects split code across files.
- Identify contracts, interfaces, libraries, structs, enums, errors, constants, and functions.
- Read the structure of a small contract without treating every line as a mysterious keyword.

**Prerequisites:** You only need to know that Solidity is a programming language used to write smart contracts. You do not need prior Solidity experience.

---

## 1. What is a source file?

A **source file** is a text file containing the code a developer writes. Solidity files conventionally use the `.sol` extension.

For example:

```solidity
// Hello.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Hello {
    string public greeting = "Hello, blockchain!";
}
```

This file contains instructions and declarations that the Solidity compiler can read. The compiler checks the source code and, when compilation succeeds, can produce outputs such as bytecode and an ABI.

A source file is **not** the deployed smart contract itself. It is the human-readable code used to build the contract. Deployment sends compiled contract bytecode to a blockchain.

Think of it this way:

```text
Solidity source (.sol)
        |
        v
Solidity compiler
        |
        +----> Bytecode (used for deployment/execution)
        |
        +----> ABI (describes how software can interact with the contract)
```

The exact compiler outputs depend on the compiler settings.

## 2. The common shape of a Solidity file

A source file often follows this general order:

```solidity
// 1. License identifier
// SPDX-License-Identifier: MIT

// 2. Compiler-version requirement
pragma solidity ^0.8.20;

// 3. Dependencies from other files
import {SomeLibrary} from "./SomeLibrary.sol";

// 4. Optional file-level declarations
error NotAllowed();

uint256 constant MAX_ITEMS = 100;

// 5. Main program unit
contract Example {
    // State variables
    uint256 public count;

    // Functions
    function increment() external {
        count += 1;
    }
}
```

This is a learning template, not a mandatory order for every possible declaration. A file may contain multiple contracts and other top-level definitions. The import shown above only works if the referenced file and symbol exist.

Let's examine each part.

## 3. SPDX license identifier: what are others allowed to do with this code?

Example:

```solidity
// SPDX-License-Identifier: MIT
```

This is a **comment** containing a machine-readable license identifier. It tells people and tools which license the author says applies to the source code.

- `MIT` is a permissive open-source license.
- `GPL-3.0` identifies a different license with different conditions.
- `UNLICENSED` indicates that the code is not being released under a license that grants general usage rights.

The compiler does not verify that the chosen license is legally correct. The identifier is included in compiler metadata.

**Important:** Adding an SPDX line does not automatically give you permission to use someone else's code. Follow the actual license and any other applicable requirements.

## 4. The pragma directive: which compiler versions are acceptable?

Example:

```solidity
pragma solidity ^0.8.20;
```

A `pragma` is a source-file-level directive. This version pragma tells the compiler which Solidity versions are allowed to compile the file.

The caret (`^`) has a specific meaning in Solidity's versioning rules. For `^0.8.20`, compatible versions are at least `0.8.20` and below `0.9.0`.

Compare:

```solidity
pragma solidity 0.8.20;
```

This requests exactly version `0.8.20`.

```solidity
pragma solidity ^0.8.20;
```

This allows compatible releases from `0.8.20` up to, but not including, `0.9.0`.

A pragma **does not install, select, or upgrade the compiler**. Your development environment must still use a compiler version that satisfies the requirement.

Also remember: a pragma is local to its own source file. Importing a file does not make that file's pragma apply to the importing file. Each source file should declare the compiler version range it is intended to support.

## 5. Imports: how files use code from other files

As a project grows, keeping everything in one file becomes difficult. Developers separate reusable code into files and import the symbols they need.

Imagine this file exists at `./MathHelpers.sol`:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

library MathHelpers {
    function double(uint256 value) internal pure returns (uint256) {
        return value * 2;
    }
}
```

Another file can import the library:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import {MathHelpers} from "./MathHelpers.sol";

contract Counter {
    function twice(uint256 value) external pure returns (uint256) {
        return MathHelpers.double(value);
    }
}
```

Read the import line:

- `import` tells Solidity to bring in declarations from another source unit.
- `{MathHelpers}` names the symbol this file wants to use.
- `"./MathHelpers.sol"` is a relative import path. The `./` means the path starts from the current source unit's directory.

Prefer importing the specific symbols you need:

```solidity
import {MathHelpers} from "./MathHelpers.sol";
```

A broad import such as `import "./MathHelpers.sol";` can introduce every global symbol from that file into the current scope, which can make names harder to track. Explicit imports are usually easier to read.

You may also see namespace imports:

```solidity
import * as Helpers from "./MathHelpers.sol";
```

With a namespace import, imported symbols are accessed through the namespace, such as `Helpers.MathHelpers` where applicable to the imported declarations.

**Common mistake:** Import paths are resolved by the compiler toolchain and its source-unit rules. They are not simply guaranteed to behave like arbitrary operating-system file paths in every environment. Use consistent paths and check your framework's remappings and configuration when an import cannot be found.

## 6. What can exist at the top level of a file?

The **top level** means declarations outside the body of a contract, interface, or library. Solidity source units can contain items such as:

- `pragma` directives
- `import` directives
- Contract definitions
- Interface definitions
- Library definitions
- Structs and enums
- Custom errors
- Constants
- Certain free-standing function and variable declarations supported by the Solidity version and language rules

For example:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

error ZeroValueNotAllowed();

uint256 constant DEFAULT_LIMIT = 10;

struct UserRecord {
    address account;
    uint256 score;
}

contract Registry {
    // Contract members go here.
}
```

The error, constant, and struct above are declared outside the contract. This is different from declaring a struct or state variable inside a contract. The location of a declaration affects how it is organized and referenced.

Do not memorize every possible top-level declaration at once. First learn to distinguish file-level declarations from members declared inside a contract.

## 7. The contract: where related state and behavior are grouped

A contract is a named unit that groups related data and behavior.

```solidity
contract Counter {
    uint256 public count;

    function increment() external {
        count += 1;
    }
}
```

Break it down:

- `contract Counter` begins a contract definition named `Counter`.
- The braces `{` and `}` mark the contract body.
- `uint256 public count;` declares a state variable.
- `function increment() external` declares a function that can be called from outside the contract.
- `count += 1;` increases the stored count by one.

The `public` visibility on a state variable causes Solidity to generate a getter function. It does **not** mean every detail of the contract is private or secret when deployed to a public blockchain.

The `external` keyword means this function is intended to be called from outside the contract. Visibility and function behavior are explained in more detail in their dedicated language topics.

## 8. Other declaration types you will encounter

These are not all required in every file. They are tools for organizing a program.

### Interface

An interface describes functions that a contract exposes, without providing their ordinary implementation bodies.

```solidity
interface IERC20Like {
    function transfer(address to, uint256 amount) external returns (bool);
}
```

Use an interface when code needs to interact with another contract through a known function shape.

### Library

A library groups reusable code.

```solidity
library NumberTools {
    function double(uint256 value) internal pure returns (uint256) {
        return value * 2;
    }
}
```

Libraries are commonly used for reusable operations and helpers. Their functions have rules that differ from ordinary contract functions, so learn the details in the dedicated library topic.

### Struct

A struct groups multiple fields into one custom data type.

```solidity
struct Profile {
    address wallet;
    uint256 reputation;
}
```

Instead of handling a wallet and reputation as unrelated values, a program can represent them together as a `Profile`.

### Enum

An enum defines a finite set of named options.

```solidity
enum Status {
    Pending,
    Active,
    Closed
}
```

This is useful when a value should represent one choice from a known set.

### Custom error

A custom error names a failure condition and can include data.

```solidity
error InsufficientBalance(uint256 available, uint256 required);
```

A contract can later revert with this error when a requested operation cannot be completed. Custom errors are generally more efficient than using long revert strings, though actual costs depend on context.

### Constant

A constant declares a value that must be known at compile time and cannot be reassigned.

```solidity
uint256 constant MAX_USERS = 1_000;
```

A constant is different from a normal state variable. State variables, constants, and immutable variables have distinct rules; study those differences in the variables topic.

### Function

A function defines an operation or calculation.

```solidity
function add(uint256 a, uint256 b) external pure returns (uint256) {
    return a + b;
}
```

Here, `a` and `b` are inputs, and `returns (uint256)` describes the returned value. The `pure` modifier indicates that the function does not read or modify blockchain state.

## 9. Read a complete example from top to bottom

The following small contract brings the pieces together:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/// @title Simple Counter
/// @notice Stores a number and allows it to be increased.
contract SimpleCounter {
    uint256 public count;

    event CountChanged(uint256 newCount);

    error IncrementTooLarge(uint256 requested);

    function increment(uint256 amount) external {
        if (amount > 10) {
            revert IncrementTooLarge(amount);
        }

        count += amount;
        emit CountChanged(count);
    }

    function reset() external {
        count = 0;
        emit CountChanged(count);
    }
}
```

Follow the structure:

1. **SPDX comment:** identifies the declared license.
2. **Pragma:** specifies the acceptable Solidity compiler version range.
3. **NatSpec comments:** the `///` comments document the contract for people and tools.
4. **Contract declaration:** starts the `SimpleCounter` unit.
5. **State variable:** `count` stores the counter value in contract state.
6. **Event declaration:** `CountChanged` describes a log the contract emits when the count changes.
7. **Custom error:** `IncrementTooLarge` describes a specific rejected operation.
8. **Function:** `increment` checks the requested amount, updates state, and emits an event.
9. **Function:** `reset` sets the count to zero and emits the new value.

This example is for learning structure, not a complete production design. For example, `reset` is callable by anyone; whether that is appropriate depends on the intended behavior.

## 10. Comments and NatSpec

Comments explain code to readers. They do not execute as program instructions.

Single-line comment:

```solidity
// This is a single-line comment.
```

Multi-line comment:

```solidity
/*
This comment spans
multiple lines.
*/
```

NatSpec comments use `///` or `/** ... */` and are commonly placed above public-facing contracts, functions, and parameters.

```solidity
/// @notice Returns the sum of two numbers.
/// @param a The first number.
/// @param b The second number.
/// @return result The sum of a and b.
function add(uint256 a, uint256 b) external pure returns (uint256 result) {
    result = a + b;
}
```

Useful NatSpec tags include `@notice`, `@dev`, `@param`, and `@return`. Clear documentation helps users, developers, and tooling understand intended behavior. It does not replace tests or security review.

## 11. Frequent beginner misunderstandings

- **“The source file is the deployed contract.”** Not quite. The source is compiled; bytecode is what is deployed for an ordinary contract deployment.
- **“The pragma chooses the compiler for me.”** No. It specifies a version requirement; the build environment chooses and runs the compiler.
- **“An import deploys another contract.”** No. An import makes declarations available during compilation. Deployment is a separate operation.
- **“Everything in a Solidity file must be inside a contract.”** No. Solidity supports several kinds of file-level declarations.
- **“A public variable makes the stored value secret.”** No. Public blockchain state should not be treated as confidential.
- **“Comments change program behavior.”** Ordinary comments and NatSpec document code; they are not executable statements.
- **“Every project must use exactly the same file order.”** No. There are common conventions, but the exact organization depends on the codebase.

## 12. Practice: can you explain this file?

Read this code before opening the answers.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TipJar {
    uint256 public totalTips;

    event TipReceived(address indexed sender, uint256 amount);

    function tip() external payable {
        totalTips += msg.value;
        emit TipReceived(msg.sender, msg.value);
    }
}
```

Try to answer:

1. What does the SPDX line describe?
2. What does the pragma restrict?
3. What is the contract's name?
4. Which line declares stored state?
5. What does `payable` allow this function to do?
6. What is `msg.value`?
7. Why does the function emit an event?

<details>
<summary>Show the answers</summary>

1. It declares the source's intended license identifier.
2. It allows compatible Solidity compiler versions in the specified range.
3. `TipJar`.
4. `uint256 public totalTips;`.
5. It allows the function to receive Ether with the call.
6. The amount of Ether, in wei, sent with the current call.
7. It records a log that off-chain applications and users can monitor. An event is not the same as contract storage.

</details>

**Safety note:** This example is educational only. A real tip jar may need additional requirements, withdrawal logic, access controls, and tests depending on its purpose.

## 13. What to learn next

After understanding source-file structure, study these topics in this order:

1. **Contracts and contract members** — how a contract is defined and organized.
2. **State variables and data locations** — where values live and how they are accessed.
3. **Functions, parameters, return values, and visibility** — how behavior is expressed.
4. **Data types, structs, and enums** — how information is represented.
5. **Events and custom errors** — how contracts report outcomes and failures.
6. **Imports, interfaces, and libraries in real projects** — how multi-file projects are organized.

You do not need to master every detail in this file before writing code. Your goal is to recognize the structure, explain why each part exists, and know which topic to study when you encounter an unfamiliar declaration.

## Official references

- [Layout of a Solidity Source File](https://docs.solidity.org/en/latest/layout-of-source-files.html)
- [Solidity language grammar](https://docs.solidity.org/en/latest/grammar.html)
- [Import Path Resolution](https://docs.solidity.org/en/latest/path-resolution.html)
- [NatSpec documentation](https://docs.solidity.org/en/latest/natspec-format.html)

---

**Learning checkpoint:** You understand this topic when you can open an unfamiliar `.sol` file and identify its license comment, compiler pragma, imports, top-level declarations, contract definitions, state variables, and functions—and explain the role of each in plain language.
