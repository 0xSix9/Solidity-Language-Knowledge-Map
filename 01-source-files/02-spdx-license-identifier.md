# SPDX License Identifier

> **Goal:** Understand what `SPDX-License-Identifier` means in a Solidity file, why developers add it, how to choose a valid identifier, and what it does **not** do.

## 1. The idea in plain English

Imagine opening a source file written by someone else. Before reusing, changing, or publishing that code, you need to know the terms under which it may be used.

A **software license** describes permissions, conditions, and restrictions for using and distributing code. The **SPDX License Identifier** is a short, standardized label that identifies a license without pasting the entire license text into every source file.

In Solidity, it usually looks like this:

```solidity
// SPDX-License-Identifier: MIT
```

Read it as:

> “This source file is marked as being under the MIT license.”

The label is useful to both people and software tools. Instead of interpreting a different paragraph of license text in every file, tools can recognize a consistent identifier and look up the corresponding license.

**Important:** The line records a license declaration; it does not magically grant you rights to code you do not own, and it is not a security setting for the smart contract.

Official references: [Solidity — Layout of a Source File](https://docs.solidity.org/en/latest/layout-of-source-files.html#spdx-license-identifier) · [SPDX License List](https://spdx.org/licenses/)

## 2. Where does it go?

Place the identifier near the top of each Solidity source file, usually as the first line:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Counter {
    uint256 public count;
}
```

Line by line:

1. `// SPDX-License-Identifier: MIT` — a comment identifying the declared license.
2. `pragma solidity ^0.8.20;` — tells the compiler which compiler-version range the file is compatible with.
3. `contract Counter` — declares the contract itself.

The SPDX line is a **comment**, not an executable Solidity statement. It does not change the contract's functions, storage, permissions, or runtime behavior.

Solidity can recognize the comment when it appears elsewhere at file level, but putting it at the top makes the file easier to read and is the recommended convention.

### Why put it in every source file?

A project may contain many files:

```text
contracts/
├── Token.sol
├── Treasury.sol
├── interfaces/
│   └── ITreasury.sol
└── libraries/
    └── MathHelper.sol
```

Each file is a separate source file. Do not assume that adding an SPDX line to `Token.sol` automatically labels `Treasury.sol`, an imported interface, or a library. Add an accurate identifier to each file, taking account of who owns that file and which terms actually apply to it.

## 3. What does the Solidity compiler do with it?

The Solidity compiler uses the provided SPDX string as part of the source/compilation metadata associated with compiled output. This helps make the declared license information available to tools and people examining the build.

There are two things to distinguish:

- **What the line does:** records a machine-readable license declaration in source/compilation metadata.
- **What the line does not do:** enforce licensing terms, prevent copying, verify that you own the code, or make a contract safer.

The compiler does not determine whether you are legally entitled to choose that license. It also does not replace the need to understand the actual license terms. If you publish a project, include the full license text in a project-level `LICENSE` file when appropriate.

When an SPDX identifier is missing, Solidity compilers commonly issue a warning asking for a license identifier. A missing identifier is not, by itself, a runtime vulnerability or a reason the contract's functions behave differently.

## 4. Common identifiers you will see

The identifier should come from the official [SPDX License List](https://spdx.org/licenses/). Here are a few commonly encountered examples:

### MIT

```solidity
// SPDX-License-Identifier: MIT
```

MIT is a **permissive** license. In broad terms, it permits reuse, modification, and distribution, subject to conditions that include preserving the copyright and license notice. Read the full text before choosing it.

### Apache-2.0

```solidity
// SPDX-License-Identifier: Apache-2.0
```

Apache License 2.0 is also a permissive open-source license. It includes terms that differ from MIT, including express patent-license provisions and conditions for redistribution. Do not treat the two as interchangeable.

### GPL-3.0-only

```solidity
// SPDX-License-Identifier: GPL-3.0-only
```

This identifies the GNU General Public License version 3.0, **only**. GPL licenses have copyleft requirements, so distributing a derivative or combined work can raise obligations that differ from permissive licenses. The correct obligations depend on the situation and the license text.

### GPL-3.0-or-later

```solidity
// SPDX-License-Identifier: GPL-3.0-or-later
```

This identifies GPL version 3.0 or a later version. The suffix matters: `-only` and `-or-later` do not mean the same thing.

For GNU family licenses, use the explicit `-only` or `-or-later` form when selecting the license version. Check the official SPDX entry rather than guessing an identifier from a license's everyday name.

### UNLICENSED

```solidity
// SPDX-License-Identifier: UNLICENSED
```

Solidity recognizes `UNLICENSED` as a special value for code that is not being offered under an open-source license. It is **not** an SPDX-listed license and should not be confused with `UNLICENSE`, which is associated with granting broad rights to everyone.

If you want to keep code proprietary or do not intend to grant an open-source license, do not casually replace `UNLICENSED` with `MIT` or another license. Choose wording that reflects your actual intent and seek appropriate legal guidance for important projects.

## 5. Can a file refer to more than one license?

Yes. SPDX supports **license expressions** for cases where more than one license applies or a recipient is offered a choice. The terms are meaningful; do not combine identifiers just because they look useful.

### OR — a choice between licenses

```solidity
// SPDX-License-Identifier: MIT OR Apache-2.0
```

This expresses that the code is offered under **either** MIT **or** Apache-2.0, giving the recipient a choice of those licenses.

### AND — both licenses apply

```solidity
// SPDX-License-Identifier: MIT AND Apache-2.0
```

This expresses that **both** licenses apply. It does not mean the user can simply choose whichever one is easier.

### WITH — a license exception

SPDX uses `WITH` to pair a license with a recognized exception. For example, an exception may modify how a particular license condition applies. The exception name must be valid for the selected license; do not invent one.

These operators have legal meaning. Only use a compound expression when it accurately reflects the terms under which the file is licensed. For unfamiliar combinations, check the official [SPDX license-expression guidance](https://spdx.dev/learn/handling-license-info/) and the license texts.

## 6. SPDX identifier versus a repository LICENSE file

They solve related but different documentation needs:

- **SPDX identifier:** a compact label, usually placed in each source file.
- **`LICENSE` file:** commonly contains the full text of the project's chosen license.
- **Copyright notice:** identifies relevant copyright ownership or claims; an SPDX identifier does not replace such a notice.

For example, a repository might contain:

```text
my-project/
├── LICENSE
├── README.md
└── contracts/
    ├── Vault.sol
    └── interfaces/
        └── IVault.sol
```

A `LICENSE` file at the repository root is useful, but you must still make sure the source-file identifiers are accurate. Third-party files may have their own licenses or copyright notices. Do not relabel copied or imported code without checking its provenance and applicable terms.

## 7. A complete Solidity example

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/// @title SimpleCounter
/// @notice Demonstrates file-level license and compiler-version comments.
contract SimpleCounter {
    uint256 public count;

    function increment() external {
        count += 1;
    }
}
```

What matters for this lesson:

- The first line declares a license identifier for this source file.
- The `pragma` specifies a compatible Solidity compiler-version range; it is separate from the license.
- The NatSpec comments describe the contract for readers and documentation tools.
- The contract code implements the counter; the SPDX comment does not alter that logic.

The example uses MIT only to demonstrate the syntax. For a real project, choose the identifier based on the license you have the right and intention to use.

## 8. Common mistakes

**Mistake 1: Thinking the SPDX line makes code open source by itself.**  
It is a declaration, not a substitute for the actual license terms or the right to license the code.

**Mistake 2: Copying `MIT` from another project without making a decision.**  
Choose a license deliberately. If code comes from another source, check its license and preserve required notices.

**Mistake 3: Using a made-up or approximate identifier.**  
Use the exact identifier from the official SPDX list. Similar-looking names may represent different licenses or versions.

**Mistake 4: Confusing `UNLICENSED` and `UNLICENSE`.**  
Solidity documentation distinguishes the special `UNLICENSED` value from `UNLICENSE`. They are not synonyms.

**Mistake 5: Assuming the license line controls on-chain access.**  
It does not behave like `onlyOwner`, a visibility keyword, or an access-control modifier. It has no direct effect on who can call a deployed contract.

**Mistake 6: Assuming one file's identifier automatically covers every imported file.**  
Each source file may need its own accurate declaration.

## 9. A practical workflow

When creating or reviewing a Solidity source file:

1. **Check ownership and origin.** Did you write the file, adapt someone else's code, or copy a third-party implementation?
2. **Choose the terms intentionally.** Decide whether you are offering permissive reuse, a copyleft license, another license, or no open-source license.
3. **Look up the exact identifier.** Use the [official SPDX License List](https://spdx.org/licenses/).
4. **Add the comment near the top.** Keep the exact spelling and punctuation.
5. **Document the project license.** Add the full license text or the appropriate project-level notice when needed.
6. **Review imported and vendored code separately.** Keep third-party copyright and license information intact.

For a serious commercial project, protocol, or contract library, do not treat a short identifier as legal advice. License compatibility can be complex; get qualified advice when the consequences matter.

## 10. Check your understanding

Try answering these before reading the solutions.

**Question 1:** Is `// SPDX-License-Identifier: MIT` executable Solidity code?

**Question 2:** Does an SPDX identifier restrict who can call a deployed contract?

**Question 3:** If `Token.sol` has an SPDX line, is every imported file automatically licensed under the same terms?

**Question 4:** What is the difference between `GPL-3.0-only` and `GPL-3.0-or-later`?

**Question 5:** Where should you check the exact spelling of a license identifier?

### Answers

1. No. It is a comment.
2. No. It is license metadata, not an on-chain access-control rule.
3. No. Each file's origin and applicable terms must be considered independently.
4. The first specifies GPL version 3.0 only; the second permits GPL version 3.0 or a later version.
5. In the official [SPDX License List](https://spdx.org/licenses/).

## 11. Key takeaways

- `SPDX-License-Identifier` is a standardized, machine-readable license label in a source-file comment.
- In Solidity, it is usually placed at the top of every source file.
- The compiler can include the supplied identifier in compilation metadata, but it does not verify your legal right to use that license.
- The SPDX line is not executable code and does not change smart-contract runtime behavior.
- Choose identifiers deliberately, preserve third-party notices, and consult the full license text.

## Official references

- [Solidity documentation — SPDX License Identifier](https://docs.solidity.org/en/latest/layout-of-source-files.html#spdx-license-identifier)
- [SPDX License List](https://spdx.org/licenses/)
- [SPDX — Handling License Information and License Expressions](https://spdx.dev/learn/handling-license-info/)
