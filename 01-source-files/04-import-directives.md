# Import Directives

> **Goal:** Learn how Solidity source files reuse declarations from other files, how to write import statements, how import paths are resolved, and how to avoid common import errors.

## 1. Why do we need imports?

As a Solidity project grows, keeping every interface, library, contract, and data type in one giant file becomes difficult to read and maintain.

Instead, developers split code into focused files and import the declarations they need.

Imagine a project arranged like this:

```text
contracts/
├── PriceReader.sol
├── interfaces/
│   └── IPriceFeed.sol
└── libraries/
    └── MathHelper.sol
```

- `IPriceFeed.sol` defines an interface.
- `MathHelper.sol` defines reusable helper code.
- `PriceReader.sol` can import declarations from those files and use them.

An import tells the compiler where to find declarations referenced by the current source file.

**Important:** Importing a file does not automatically deploy a contract from it, and it does not mean one contract inherits from another. Imports make declarations available to the source file; inheritance and external contract calls are separate concepts.

Official reference: [Solidity documentation — Importing Other Source Files](https://docs.solidity.org/en/latest/layout-of-source-files.html#importing-other-source-files).

## 2. The simplest import

Suppose `IPriceFeed.sol` contains this interface:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

interface IPriceFeed {
    function latestAnswer() external view returns (int256);
}
```

Another file can import it:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "./interfaces/IPriceFeed.sol";

contract PriceReader {
    // IPriceFeed is now available in this source file.
}
```

The important line is:

```solidity
import "./interfaces/IPriceFeed.sol";
```

It tells Solidity to load the source unit identified by that import path, so declarations from that source can be referenced here.

This import form brings the imported source file's global symbols into the current global scope. It works, but for modern code it is usually clearer to import only the declarations you need.

## 3. Import the exact symbol you need

A common, readable form is a **named import**:

```solidity
import {IPriceFeed} from "./interfaces/IPriceFeed.sol";
```

The braces identify the declaration being imported. This tells readers exactly which name the file depends on.

For example:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import {IPriceFeed} from "./interfaces/IPriceFeed.sol";

contract PriceReader {
    IPriceFeed public priceFeed;

    constructor(address feedAddress) {
        priceFeed = IPriceFeed(feedAddress);
    }

    function readPrice() external view returns (int256) {
        return priceFeed.latestAnswer();
    }
}
```

### What is happening in this example?

1. `import {IPriceFeed} ...` makes the interface declaration available.
2. `IPriceFeed public priceFeed;` declares a variable whose type is that interface.
3. `IPriceFeed(feedAddress)` treats the supplied address as an address through which the interface can be used.
4. `priceFeed.latestAnswer()` makes an external call using the function signature declared in the interface.

**The interface is not the price feed implementation.** Importing it does not prove that the address contains a compatible contract, and the constructor does not deploy a price feed. A call can fail if the address or implementation is unsuitable.

This example demonstrates imports and interface types. It does not include production-grade validation or oracle safety checks.

## 4. The main import forms

Solidity supports several import forms. Learn the first two particularly well.

### A. Import a file's global symbols

```solidity
import "./interfaces/IPriceFeed.sol";
```

This makes global declarations from the imported source available in the current global scope.

**Trade-off:** It may introduce names you did not expect. If a file later adds more top-level declarations, those names can also enter importing files' scopes. For that reason, the official documentation advises against relying on this form in ordinary code when explicit imports are possible.

### B. Import named declarations

```solidity
import {IPriceFeed} from "./interfaces/IPriceFeed.sol";
```

This is often the clearest option when you know exactly which contract, interface, library, struct, or other global declaration you need.

You can import more than one name:

```solidity
import {IPriceFeed, PriceData} from "./interfaces/IPriceFeed.sol";
```

Use this only when both `IPriceFeed` and `PriceData` are actually declared in that source file and are available to import.

### C. Import a namespace

```solidity
import * as PriceFeedModule from "./interfaces/IPriceFeed.sol";
```

This imports the source's global symbols under the namespace `PriceFeedModule`. You refer to a symbol through that namespace:

```solidity
PriceFeedModule.IPriceFeed
```

Namespace imports can help keep names grouped and reduce ambiguity.

### D. Import a file under a namespace alias

```solidity
import "./interfaces/IPriceFeed.sol" as PriceFeedModule;
```

This is another supported namespace form. It is equivalent in meaning to importing all the file's global symbols under a namespace, as in the previous example.

### Quick comparison

| Form | How you refer to declarations | Practical note |
| --- | --- | --- |
| `import "path";` | Names are brought into the current global scope | Convenient, but may pollute the namespace. |
| `import {Name} from "path";` | Use `Name` | Explicit and generally easy to review. |
| `import * as Alias from "path";` | Use `Alias.Name` | Groups names under a namespace. |
| `import "path" as Alias;` | Use `Alias.Name` | Alternative namespace syntax. |

Solidity does **not** use the JavaScript concept of a default export/import. Do not expect syntax such as `import Name from "./File.sol";` to work as a Solidity default import.

## 5. Renaming imported symbols with `as`

Sometimes a source file exports a declaration with a name that clashes with another name in your current file. A named import can give that imported declaration a local alias.

```solidity
import {IPriceFeed as IExternalPriceFeed}
    from "./interfaces/IPriceFeed.sol";
```

Now this file refers to the imported interface as `IExternalPriceFeed`:

```solidity
IExternalPriceFeed public priceFeed;
```

The alias changes the name used in the importing file. It does **not** rename the declaration in its original source file or change the deployed contract's name.

You can also rename one symbol while keeping another unchanged:

```solidity
import {IPriceFeed as IExternalPriceFeed, PriceData}
    from "./interfaces/IPriceFeed.sol";
```

Here, `IPriceFeed` is referred to as `IExternalPriceFeed` in the current source file, while `PriceData` retains its name.

Use aliases to solve real naming conflicts or improve clarity—not to hide confusing dependencies.

## 6. Understanding import paths: `./`, `../`, and package paths

The text inside quotes is called the **import path**. Solidity's path-resolution rules use source-unit names; an import path should not be thought of simply as an arbitrary operating-system path.

Consider this project:

```text
project/
└── contracts/
    ├── PriceReader.sol
    ├── interfaces/
    │   └── IPriceFeed.sol
    └── shared/
        └── PriceData.sol
```

Assume the importing file is `contracts/PriceReader.sol`.

### Relative path: `./`

```solidity
import {IPriceFeed} from "./interfaces/IPriceFeed.sol";
```

`./` means the current directory of the importing source unit. From `contracts/PriceReader.sol`, the path leads to `contracts/interfaces/IPriceFeed.sol`.

### Parent directory: `../`

Suppose the importing file were `contracts/interfaces/IPriceFeed.sol` and it needed a source file one directory above `interfaces`:

```solidity
import {PriceData} from "../shared/PriceData.sol";
```

This path moves to the parent directory and then into `shared`. The exact destination must match your project's folder layout.

### Direct import path

```solidity
import {IPriceFeed} from "contracts/interfaces/IPriceFeed.sol";
```

A path that does **not** start with `./` or `../` is a direct import path in Solidity's path-resolution model. It is not automatically interpreted relative to the importing file. How it is mapped to a source file depends on the compiler inputs and build configuration.

That distinction matters: `import "interfaces/IPriceFeed.sol";` is not equivalent in path-resolution terms to `import "./interfaces/IPriceFeed.sol";`.

### Package-style imports

You may see imports such as:

```solidity
import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
```

The `@openzeppelin/...` form is a common package-style path. Solidity does not magically install this package. Your environment must make those sources available and resolve the path—often through a package manager, compiler base/include paths, or import remappings, depending on the toolchain.

If a package-style import fails, check that the dependency is installed and that your compiler/build configuration knows where to find it.

For the full rules, see [Solidity documentation — Import Path Resolution](https://docs.solidity.org/en/latest/path-resolution.html).

## 7. Imports in Remix versus a local project

The import statement is part of Solidity syntax, but finding the source file also depends on how your compilation environment supplies source files.

**In Remix:** Make sure the imported file exists in the workspace, or use an import source Remix can resolve. Confirm the path and capitalization.

**In a local toolchain:** A build tool such as Foundry or Hardhat manages project sources and dependencies according to its configuration. Package paths may rely on installed dependencies or remappings.

**With the command-line compiler (`solc`):** Base paths, include paths, and remappings can influence how import paths are resolved.

The same-looking import can therefore work in one setup and fail in another when the source file or path configuration differs. A pragma version constraint does not solve a missing import; the compiler must also be able to locate the imported source.

## 8. Imports do not mean inheritance or deployment

These concepts are related but not interchangeable.

### Importing

```solidity
import {IPriceFeed} from "./interfaces/IPriceFeed.sol";
```

Makes the `IPriceFeed` declaration available in this source file.

### Inheriting

```solidity
contract MyToken is SomeBaseContract {
    // ...
}
```

The `is` keyword declares inheritance. The base contract must be known to the compiler, commonly by importing its source first, but the inheritance relationship is expressed by `is`, not by `import`.

### Calling another deployed contract

```solidity
IPriceFeed(feedAddress).latestAnswer();
```

This uses an interface type to call a function at an address. The imported interface describes the function signature, while a deployed implementation at that address must provide compatible behavior.

### Deploying

Deploying is a separate action performed through your development/deployment workflow. Importing a source file does not by itself deploy every contract declared in that file.

## 9. What can be imported?

Imports can make top-level declarations from other source files available. These may include:

- Contracts and abstract contracts
- Interfaces
- Libraries
- Structs and enums declared at file level
- Custom errors, events, and other supported global declarations
- Other top-level declarations supported by the Solidity version in use

You cannot place a Solidity import directive inside a contract body as if it were a function statement. Import directives belong at source-file level.

An import also does not automatically make every internal detail in the imported file a public function of your contract. Scope, visibility, inheritance, and whether a function can be called externally remain separate language concepts.

## 10. A complete two-file example

Let's connect the concepts with a minimal example.

### File 1 — `contracts/interfaces/IPriceFeed.sol`

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

interface IPriceFeed {
    function latestAnswer() external view returns (int256);
}
```

This file declares the interface and a function signature. It does not contain the implementation of that function.

### File 2 — `contracts/PriceReader.sol`

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import {IPriceFeed} from "./interfaces/IPriceFeed.sol";

contract PriceReader {
    IPriceFeed public immutable priceFeed;

    constructor(address feedAddress) {
        priceFeed = IPriceFeed(feedAddress);
    }

    function readPrice() external view returns (int256) {
        return priceFeed.latestAnswer();
    }
}
```

### Follow the steps

1. The compiler starts with `PriceReader.sol`.
2. It encounters the named import and resolves `./interfaces/IPriceFeed.sol`.
3. It reads the interface declaration, making `IPriceFeed` available to this source file.
4. The compiler can now check that the code uses the interface type and function signature correctly.
5. When `readPrice()` is called on a deployed `PriceReader`, it attempts an external call to the configured address.

The compiler can check the declared signature, but it cannot infer from the interface alone that the address is trustworthy or that the implementation returns a meaningful price. Validating oracle addresses, responses, staleness, and economic assumptions belongs to the contract's design and security review.

## 11. Common import errors

**Error 1: Wrong relative path**

```solidity
import "./IPriceFeed.sol";
```

This works only if the target source resolves at that location relative to the importing source unit. If the interface is in an `interfaces/` subfolder, the path likely needs to include that folder.

**Fix:** Check the importing file's directory and the target file's actual location.

**Error 2: Forgetting the path prefix**

```solidity
import "IPriceFeed.sol";
```

This is a direct import path, not the same as `./IPriceFeed.sol`. It can fail when your build configuration does not resolve the direct path.

**Fix:** Use the intended relative path or configure direct-import resolution deliberately.

**Error 3: Importing a name that does not exist**

```solidity
import {IPricFeed} from "./interfaces/IPriceFeed.sol";
```

Here, `IPricFeed` is misspelled. A named import must match an available declaration, unless you intentionally use `as` to provide a local alias.

**Fix:** Check spelling and capitalization in both the imported name and the file.

**Error 4: Dependency not installed or configured**

```solidity
import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
```

The syntax can be valid while the compiler still cannot find the source.

**Fix:** Confirm the dependency is installed and your toolchain resolves the package path.

**Error 5: Importing too broadly**

```solidity
import "./shared/Utils.sol";
```

This can bring many global names into scope and make it harder to see what the file depends on.

**Fix:** Prefer explicit named imports when practical.

**Error 6: Assuming import equals inheritance**

Importing `Base.sol` does not make your contract a child of the base contract.

**Fix:** Use an inheritance declaration such as `contract Child is Base` when inheritance is actually intended.

**Error 7: Treating an interface import as address validation**

The compiler can use the interface type to encode the expected call. It does not prove that the address contains a valid or trustworthy implementation.

**Fix:** Validate and test addresses and returned data according to the protocol's requirements.

## 12. Good import habits

- Keep source files focused: separate interfaces, libraries, and contracts when doing so improves organization.
- Prefer named imports for dependencies you use directly.
- Use aliases when they resolve naming conflicts or improve clarity.
- Keep paths accurate and consistent with the project's folder structure.
- Avoid unnecessary complex relative paths; clear project-root or package-based imports can be easier to maintain when supported by the toolchain.
- Verify that dependencies are installed and import paths are configured.
- Review third-party code and its license; importing a package does not automatically tell you what license applies to each file.
- Compile and test with the project's intended compiler and build settings.

## 13. Check your understanding

**Question 1:** What does an `import` directive make possible?

**Question 2:** What is the practical difference between `import {IPriceFeed} from "./interfaces/IPriceFeed.sol";` and `import "./interfaces/IPriceFeed.sol";`?

**Question 3:** Why is `./interfaces/IPriceFeed.sol` different from `interfaces/IPriceFeed.sol`?

**Question 4:** Does importing a contract automatically deploy it?

**Question 5:** Does importing an interface prove that an address implements its functions correctly?

**Question 6:** What does `as` do in `import {IPriceFeed as IExternalPriceFeed} from "...";`?

**Question 7:** Why can a package-style import fail even when the import syntax looks correct?

### Answers

1. It makes declarations from another source file available for use in the current source file.
2. The named form explicitly imports the stated symbol. The plain file import brings the imported file's global symbols into the current global scope.
3. Only paths beginning with `./` or `../` are relative imports in Solidity's path-resolution model. The other form is a direct import path and resolves differently.
4. No. Importing is a source-compilation concern, not a deployment action.
5. No. The interface specifies expected function signatures; runtime behavior and trustworthiness must be evaluated separately.
6. It gives the imported declaration a different local name in the importing source file.
7. The dependency might be missing, or the compiler/build tool might not be configured to resolve that path.

## Key takeaways

- Imports help organize a Solidity project across multiple source files.
- Named imports make dependencies explicit and are usually a good default.
- Namespace imports and aliases help group or rename symbols.
- Relative imports start with `./` or `../`; other import paths are resolved according to the compiler's source-unit and build configuration rules.
- An import is not inheritance, a contract call, address validation, or deployment.
- Import syntax and path resolution are separate concerns: the syntax may be valid while the target file is unavailable.

## Official references

- [Solidity documentation — Importing Other Source Files](https://docs.solidity.org/en/latest/layout-of-source-files.html#importing-other-source-files)
- [Solidity documentation — Import Path Resolution](https://docs.solidity.org/en/latest/path-resolution.html)
- [Solidity grammar — Import Directive](https://docs.solidity.org/en/latest/grammar.html#import-directive)
