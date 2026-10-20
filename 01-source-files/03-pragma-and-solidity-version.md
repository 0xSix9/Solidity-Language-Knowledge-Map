# Pragma and Solidity Version

> **Goal:** Understand what `pragma solidity` does, how version constraints work, why compiler versions matter, and how to choose a sensible version range for a Solidity project.

## 1. What is `pragma solidity`?

A Solidity source file commonly begins like this:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Counter {
    uint256 public count;
}
```

The second line is called a **version pragma**:

```solidity
pragma solidity ^0.8.20;
```

It tells the Solidity compiler which compiler versions are allowed to compile the file.

Think of it like a compatibility rule on a product: the source file declares the range of compiler versions it is intended to work with. If the compiler version you actually use falls outside that range, compilation fails.

**The key point:** `pragma solidity` does not install, download, or switch the compiler to a version. It checks compatibility with the compiler that your toolchain is using.

Official reference: [Solidity documentation — Version Pragma](https://docs.solidity.org/en/latest/layout-of-source-files.html#version-pragma).

## 2. Break the line into pieces

Consider:

```solidity
pragma solidity ^0.8.20;
```

| Piece | Meaning |
| --- | --- |
| `pragma` | Introduces a compiler directive. |
| `solidity` | Specifies that this directive concerns Solidity compiler versions. |
| `^` | Allows a compatible version range according to the version-constraint rules. |
| `0.8.20` | The minimum version in this range. |
| `; ` | Ends the directive. |

The actual version range is the part many beginners misunderstand.

## 3. How Solidity version numbers work

A stable Solidity version normally has three numeric parts:

```text
0 . 8 . 20
│   │   └── Patch version
│   └────── Minor version
└────────── Major version
```

In `0.8.20`:

- **Major — `0`:** Solidity is in its 0.x version series.
- **Minor — `8`:** Identifies the minor release line.
- **Patch — `20`:** Identifies a patch release within that line.

For Solidity's versioning policy and the meaning of release versions, see [Installing the Solidity Compiler — Versioning](https://docs.solidity.org/en/latest/installing-solidity.html#versioning).

A version number is not the same thing as the EVM version, the Ethereum network, or the version of your contract. It identifies the compiler release.

## 4. What does the caret `^` mean?

This is one of the most important details in everyday Solidity code.

```solidity
pragma solidity ^0.8.20;
```

For this version, the caret means:

```text
Allowed:  >= 0.8.20 and < 0.9.0
```

Examples:

| Compiler version | Does it satisfy `^0.8.20`? | Why? |
| --- | --- | --- |
| `0.8.19` | No | It is lower than `0.8.20`. |
| `0.8.20` | Yes | It is the lower bound. |
| `0.8.21` | Yes | It is later but still in the allowed range. |
| `0.8.30` | Yes | It is still below `0.9.0`. |
| `0.9.0` | No | It reaches the excluded upper bound. |

The examples show the rule, not a guarantee that every listed compiler release exists or is recommended for a particular project.

### Why doesn't the caret allow `0.9.0`?

The caret is a compatibility constraint. With `^0.8.20`, the upper limit is the next minor boundary, `0.9.0`, which is excluded. This helps prevent the file from silently being compiled with a version outside the declared compatibility range.

**Remember:** `^0.8.20` does not mean “use exactly version 0.8.20.” It allows a range of versions.

## 5. Common version-constraint patterns

### A. Caret range

```solidity
pragma solidity ^0.8.20;
```

Allows versions from `0.8.20` up to, but not including, `0.9.0`.

This is a common pattern in Solidity source files. The compiler you actually run still depends on your development environment.

### B. Explicit range

```solidity
pragma solidity >=0.8.20 <0.9.0;
```

This spells out the lower and upper bounds directly. In this example, the practical range is equivalent to `^0.8.20`.

A wider example is:

```solidity
pragma solidity >=0.7.0 <0.9.0;
```

This declares a broader range. Use a range this wide only when the code is genuinely intended to be compatible with it. If the code uses syntax or language features that were introduced after 0.7.0, the lower compiler versions will not compile it.

### C. Exact version

```solidity
pragma solidity 0.8.20;
```

This requires compiler version `0.8.20` rather than allowing the whole `0.8.x` line.

An exact pragma can be appropriate when a project deliberately requires a specific compiler release. Many projects instead declare a compatible range in the source file and pin the actual compiler version in their build configuration for reproducible builds.

### D. Multiple constraints

```solidity
pragma solidity >=0.8.20 <0.8.26;
```

This accepts versions at least `0.8.20` but lower than `0.8.26`. The lower bound is inclusive; the upper bound written with `<` is exclusive.

You can read constraints as a question:

> “Is the compiler version I am using inside this range?”

If yes, this pragma's version check passes. The source must still satisfy all other compiler checks.

## 6. What happens when the compiler does not match?

Suppose the file contains:

```solidity
pragma solidity ^0.8.20;
```

Now consider these cases:

- You select compiler `0.8.19`: the source fails the version constraint because the compiler is too old.
- You select compiler `0.8.24`: the version constraint is satisfied, provided the rest of the source compiles.
- You select compiler `0.9.0`: the source fails the version constraint because that version is outside the declared range.

The pragma does not automatically fix the mismatch. You need to choose a compatible compiler in the tool you are using, or deliberately revise the version constraint and code after checking compatibility.

For example, in Remix you select a compiler version in the Solidity Compiler panel. In a local project, the version is usually managed by the project's build tool or compiler configuration.

## 7. Pragma versus the compiler you actually use

These are connected, but different:

| Item | What it controls |
| --- | --- |
| `pragma solidity` in a source file | Which compiler versions are permitted for that source. |
| Compiler installation/selection | Which compiler executable or release your tool actually runs. |
| Build configuration | Often pins the compiler version and settings used for a project. |
| Compiler settings | Options such as optimization that can affect compiled output. |

A compatible pragma does **not** guarantee identical bytecode across different compiler releases or settings. Compiler changes and settings can affect generated artifacts.

For reproducible builds—especially when verifying deployed contract source code—record the actual compiler version and relevant settings, such as optimizer configuration and EVM target, rather than relying on the pragma alone.

## 8. Why should developers care about the compiler version?

The Solidity compiler turns source code into artifacts such as EVM bytecode and an ABI. The compiler version matters for several reasons:

**Language compatibility:** Newer releases can support syntax or features that older releases do not recognize.

**Bug fixes and improvements:** Compiler releases can fix bugs and improve implementation or optimization.

**Reproducibility:** If a project is built with a different compiler release or different settings, the resulting bytecode may differ.

**Security review:** Auditors and developers need to know which compiler and settings produced the code, because compiler bugs and version-specific behavior can matter.

This is why “my contract compiled” is not the end of the process. For a real project, use a deliberate compiler version, tests, and an appropriate review process.

See the official [Solidity compiler documentation](https://docs.solidity.org/en/latest/using-the-compiler.html).

## 9. Does `pragma solidity` change contract behavior directly?

No. The pragma is source-level compiler guidance; it is not an on-chain setting such as an access-control check.

It does not:

- Choose which Ethereum network the contract is deployed to.
- Set the EVM version by itself.
- Set the contract's address.
- Restrict who can call a function.
- Automatically upgrade a deployed contract.
- Download or install a compiler.

However, the compiler version and its settings can affect the compiled output. That is different from the pragma directly running as part of the contract. The pragma is checked during compilation; it is not an instruction that executes each time a user calls the deployed contract.

## 10. A source-file example

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/// @title SimpleCounter
/// @notice A tiny contract used to study a source-file version pragma.
contract SimpleCounter {
    uint256 public count;

    function increment() external {
        count += 1;
    }
}
```

Read the top of this file:

1. The SPDX comment states the declared license identifier.
2. The version pragma says the file can be compiled with a Solidity version from `0.8.20` up to, but not including, `0.9.0`.
3. The NatSpec comments document the contract.
4. The contract declaration and function define the actual program logic.

The SPDX identifier and version pragma are both written as comments/directives near the top, but they answer **different questions**:

- SPDX: “Under what license is this source file declared?”
- Version pragma: “Which Solidity compiler versions are allowed to compile this file?”

## 11. Practical guidance for a new project

A sensible workflow is:

1. **Choose the toolchain intentionally.** Decide which Solidity compiler release your project will use.
2. **Use a matching source pragma.** Set a compatibility range that fits the language features you use and your project's compatibility goals.
3. **Pin the real compiler version in the build setup when reproducibility matters.** The pragma alone does not pin it.
4. **Record relevant build settings.** Depending on the toolchain, this may include optimizer settings, EVM target, and other compiler options.
5. **Compile and test with the intended settings.** Do not treat a wider pragma range as proof the code has been tested against every permitted compiler.
6. **Review upgrade or version changes.** Read release notes and changelogs for releases that may affect compilation or behavior.

There is no single pragma range that is ideal for every project. Follow the toolchain and compatibility requirements of the codebase you are using rather than changing the version line blindly.

## 12. Common mistakes

**Mistake 1: Thinking `^0.8.20` means exactly `0.8.20`.**  
It allows a compatible range beginning at that version and ending before `0.9.0`.

**Mistake 2: Thinking the pragma installs the compiler.**  
It only declares a version constraint. Your compiler tool or build configuration chooses the compiler that actually runs.

**Mistake 3: Setting a range that is too broad.**  
If the code uses a feature not available in the earliest allowed version, that version will not compile it.

**Mistake 4: Assuming every compiler allowed by the pragma has been tested.**  
A version constraint is not a test matrix. Test the versions and configurations your project intends to support.

**Mistake 5: Assuming compilation with any allowed version produces identical bytecode.**  
Compiler releases and settings may change the compiled result. Pin and document build settings when reproducibility matters.

**Mistake 6: Confusing the compiler version with the EVM target.**  
The compiler version identifies the Solidity compiler release. The EVM target is a separate compiler setting.

## 13. Check your understanding

Answer these without looking at the explanations first.

**Question 1:** What does `pragma solidity ^0.8.20;` permit?

**Question 2:** Would compiler version `0.8.19` satisfy that pragma? Why?

**Question 3:** Would compiler version `0.9.0` satisfy it? Why?

**Question 4:** Does writing `pragma solidity ^0.8.20;` cause your computer to download Solidity 0.8.20?

**Question 5:** What is the difference between a source pragma and the actual compiler version pinned in the build configuration?

**Question 6:** Does the pragma select an Ethereum network or execute as part of the contract on-chain?

### Answers

1. It permits compiler versions `>=0.8.20` and `<0.9.0`.
2. No. `0.8.19` is below the minimum `0.8.20`.
3. No. `0.9.0` is the excluded upper boundary.
4. No. You must install or select a compiler through your development toolchain.
5. The pragma expresses which compiler versions are compatible with the source; build configuration can determine the exact compiler actually used.
6. No. It is checked during compilation. The EVM target and network/deployment choices are separate concerns.

## Key takeaways

- `pragma solidity` declares the Solidity compiler versions a source file accepts.
- `^0.8.20` means `>=0.8.20` and `<0.9.0`, not one exact version.
- The pragma does not install or select the compiler for you.
- The selected compiler release and build settings can affect generated output.
- A broad range does not prove the code works with every allowed version; test the configurations you intend to support.
- For reproducible builds and source verification, record the actual compiler version and relevant settings.

## Official references

- [Solidity documentation — Layout of a Source File / Version Pragma](https://docs.solidity.org/en/latest/layout-of-source-files.html#version-pragma)
- [Solidity documentation — Installing the Compiler / Versioning](https://docs.solidity.org/en/latest/installing-solidity.html#versioning)
- [Solidity documentation — Using the Compiler](https://docs.solidity.org/en/latest/using-the-compiler.html)
