# Identifiers and Keywords

> **Goal:** Learn how Solidity names are formed, which words have special meaning to the compiler, how identifiers are used in declarations, and how to choose names that remain clear and compatible.

## 1. What is an identifier?

An **identifier** is a name used to refer to a program element. You create identifiers when you name things such as:

- Contracts, interfaces, and libraries
- State variables and local variables
- Functions and modifiers
- Parameters and return values
- Events and custom errors
- Structs, enums, and user-defined value types
- Imported symbols and aliases

In this example, **Vault**, **owner**, **deposit**, **amount**, **Deposited**, and **InsufficientBalance** are identifiers:

~~~solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Vault {
    address public owner;

    event Deposited(address indexed account, uint256 amount);
    error InsufficientBalance(uint256 requested, uint256 available);

    function deposit(uint256 amount) external {
        emit Deposited(msg.sender, amount);
    }
}
~~~

Identifiers are labels in source code. They are not themselves values or types: for example, **owner** is a variable name, while **address** is a built-in type.

Official reference: [Solidity Language Grammar — identifier](https://docs.solidity.org/en/latest/grammar.html#identifier).

## 2. The basic rules for valid identifiers

Solidity identifiers follow these core rules:

1. The first character must be a letter, underscore (**_**), or dollar sign (**$**).
2. After the first character, letters, digits, underscores, and dollar signs may be used.
3. An identifier cannot start with a digit.
4. Spaces and punctuation such as hyphens and periods cannot be part of an ordinary identifier.
5. Solidity identifiers are case-sensitive.
6. Identifiers are restricted to the ASCII character set. Unicode text can still be stored in a **string**; that does not mean Unicode letters can be used as Solidity variable or function names.

| Name | Valid? | Explanation |
|---|---|---|
| **balance** | Yes | Starts with a letter. |
| **_owner** | Yes | Starts with an underscore. |
| **$internalId** | Yes | A dollar sign is permitted by the identifier grammar. |
| **token2** | Yes | Digits are allowed after the first character. |
| **TokenRegistry** | Yes | Capital letters are allowed. |
| **2tokens** | No | An identifier cannot start with a digit. |
| **token-name** | No | A hyphen is not part of an identifier. |
| **user.balance** | No, as one identifier | A period expresses member access in an expression; it is not part of a simple name. |
| **token name** | No | Spaces are not allowed. |
| **café** | No, as an identifier | Solidity identifiers are ASCII-only. |

**Important:** An identifier's spelling must be valid, and the name must also be legal in its syntactic context. A spelling that passes the character rules can still fail compilation if it is a reserved keyword or conflicts with a declaration.

## 3. Identifiers are case-sensitive

These are different identifiers:

~~~solidity
uint256 public totalSupply;
uint256 public totalsupply;
uint256 public TotalSupply;
~~~

The compiler treats them as three distinct names. This is legal syntax, but using names that differ only by capitalization is usually poor style because readers can easily confuse them.

Case sensitivity also matters when referencing a declared name: **totalSupply** and **totalsupply** are not interchangeable.

## 4. What is a keyword?

A **keyword** is a word that has a special syntactic or semantic role in Solidity. Keywords help the compiler recognize the structure of a program.

For example:

~~~solidity
contract Counter {
    uint256 public count;

    function increment() external {
        count += 1;
    }
}
~~~

Here:

- **contract** introduces a contract declaration.
- **uint256** names a built-in integer type.
- **public** specifies visibility for the state variable and causes the compiler to generate a getter.
- **function** introduces a function declaration.
- **external** specifies function visibility.

You cannot freely use reserved keywords as your own variable, contract, or function names in contexts where the compiler reserves them. For example, naming a variable **contract** is invalid:

~~~solidity
// Invalid Solidity:
uint256 contract = 1;
~~~

Some words are treated specially only in particular grammar contexts, and language rules can evolve. Do not assume that every word that appears in Solidity syntax behaves identically in every position.

## 5. Common keyword groups

The following groups are a learning aid, not an exhaustive or permanently fixed keyword list. Consult the documentation for the compiler version used by your project.

### A. Declarations and type definitions

Examples include:

- **contract**, **interface**, **library**
- **function**, **modifier**, **constructor**
- **event**, **error**
- **struct**, **enum**, **type**
- **is**, **using**

These words help define program structures and relationships between them.

### B. Visibility and inheritance

Examples include:

- **public**, **external**, **internal**, **private**
- **virtual**, **override**, **abstract**

These describe who can access a declaration or how inheritance and overriding work. Visibility and inheritance semantics are covered in their own topics; here, the important point is that these words have defined language meanings.

### C. Function mutability and data locations

Examples include:

- **pure**, **view**, **payable**
- **memory**, **storage**, **calldata**
- **transient** in supported compiler versions

These keywords affect function restrictions or where reference-type data is handled. For example, **memory** and **calldata** are not arbitrary names when used as data-location specifiers.

### D. Control flow and execution

Examples include:

- **if**, **else**
- **for**, **while**, **do**
- **break**, **continue**, **return**
- **try**, **catch**
- **emit**, **revert**, **delete**, **new**
- **unchecked**

These words define control flow, event emission, error handling, object creation, or specific execution behavior.

### E. Built-in types and literal words

Examples include:

- **bool**, **address**, **string**, **bytes**
- **int**, **uint** and their sized variants
- **true**, **false**

The exact type grammar and supported features depend on the language and compiler version. Some familiar names, such as **uint256**, are built-in type names; **true** and **false** are Boolean literals, not identifiers you declare yourself.

## 6. Keywords, built-ins, and ordinary identifiers are not the same thing

These categories are related but different:

| Category | Examples | Meaning |
|---|---|---|
| Reserved keyword | **contract**, **function**, **return** | Has a language-defined role in the grammar. |
| Built-in type | **uint256**, **address**, **bool** | Names a type supplied by Solidity. |
| Built-in global object or namespace | **msg**, **block**, **tx**, **abi** | Provides information or functionality associated with execution or encoding. |
| Built-in function | **require**, **assert**, **keccak256** | Refers to functionality provided by the language environment. |
| User-defined identifier | **owner**, **withdraw**, **Treasury** | A name chosen by the programmer. |

This distinction matters because not every built-in name is necessarily a reserved keyword. However, declaring a local identifier with the same name as a built-in can hide or shadow that built-in within a scope and make code confusing. Avoid doing so even where the compiler permits it.

For example, do not use names such as **msg**, **block**, or **require** for your own variables. A later expression like **msg.sender** or **require(condition)** should be immediately recognizable as the built-in operation.

## 7. A version-sensitive warning: future keywords

The keyword set can change between Solidity compiler releases. Code that compiles with one release may conflict with a newly reserved word in a later breaking release.

The official Solidity documentation currently warns developers to avoid the names **layout** and **at**, even though they are not yet reserved keywords in the documented language version, because they are intended to become reserved in a future breaking release. Treat this as a compiler-version-sensitive warning and check the documentation for the compiler you actually use.

Practical rules:

- Compile with the version configured for your project.
- Check compiler release notes when upgrading Solidity.
- Avoid names that the current documentation explicitly marks as future keywords.
- If a word seems to behave differently from a normal identifier, check the grammar and reserved-word documentation instead of guessing.

References: [Solidity Contracts — Custom Storage Layout warning](https://docs.solidity.org/en/latest/contracts.html#custom-storage-layout) and [Solidity v0.5.0 Breaking Changes — new reserved keywords](https://docs.solidity.org/en/latest/050-breaking-changes.html#syntax).

## 8. Naming conventions for readable Solidity

The compiler's identifier rules describe what is allowed. The Solidity Style Guide recommends conventions that make code easier to scan and review.

| What you are naming | Common style | Example |
|---|---|---|
| Contract, interface, or library | **CapWords** | **TokenVault**, **ITokenVault** |
| Struct or enum | **CapWords** | **UserInfo**, **TransferStatus** |
| Function | **mixedCase** | **deposit**, **withdrawTo** |
| State or local variable | **mixedCase** | **totalSupply**, **recipient** |
| Modifier | **mixedCase** | **onlyOwner**, **whenNotPaused** |
| Constant | **UPPER_CASE_WITH_UNDERSCORES** | **MAX_SUPPLY**, **FEE_BPS** |
| Event or custom error | **CapWords** | **Deposited**, **Unauthorized** |

These are style conventions, not all compiler-enforced requirements. Existing projects may use slightly different conventions; consistency within a codebase matters.

Official reference: [Solidity Style Guide](https://docs.solidity.org/en/latest/style-guide.html).

### Names to avoid

The style guide warns against using a single lowercase **l**, uppercase **O**, or uppercase **I** as variable names because they can resemble the digits **1** and **0**.

Prefer descriptive names such as **userCount**, **withdrawalAmount**, or **isAuthorized** over vague names such as **x**, **data1**, or **temp** in code that other people will maintain.

## 9. Naming can affect a contract's external interface

Identifier choice is not only a readability issue.

- Renaming a **public or external function** changes its function signature and normally changes the function selector used by ABI-based callers. Integrations may need to be updated.
- Renaming a **public state variable** can change the automatically generated getter's name and therefore affect callers.
- Renaming an **event** changes its event signature and topic used by event consumers.
- Renaming a **custom error** changes its error signature and selector used when decoding revert data.
- Renaming a private/local variable usually does not change the contract's externally callable ABI. Do not confuse a source-level name with a storage slot: storage layout is determined by declaration and layout rules, not simply by the spelling of a variable.

Therefore, public-facing identifiers should be chosen deliberately and treated as part of the contract's API.

## 10. Short example: identifiers in a contract

~~~solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract TokenRegistry {
    struct TokenInfo {
        address issuer;
        uint64 issuedAt;
    }

    enum Status {
        Active,
        Paused
    }

    error Unauthorized(address caller);
    event TokenRegistered(address indexed token, address indexed issuer);

    uint256 public constant MAX_TOKENS = 100;
    address private immutable admin;
    mapping(address => TokenInfo) private tokenInfo;
    Status public status = Status.Active;

    modifier onlyAdmin() {
        if (msg.sender != admin) {
            revert Unauthorized(msg.sender);
        }
        _;
    }

    constructor() {
        admin = msg.sender;
    }

    function register(address token) external onlyAdmin {
        tokenInfo[token] = TokenInfo(msg.sender, uint64(block.timestamp));
        emit TokenRegistered(token, msg.sender);
    }
}
~~~

Examples of identifiers in the contract:

- **TokenRegistry** — contract name
- **TokenInfo** — struct name
- **issuer** and **issuedAt** — struct-member names
- **Status**, **Active**, and **Paused** — enum and member names
- **Unauthorized** — custom error name
- **TokenRegistered** — event name
- **MAX_TOKENS**, **admin**, **tokenInfo**, and **status** — state-variable names
- **onlyAdmin** — modifier name
- **register** and **token** — function and parameter names

Words such as **contract**, **struct**, **enum**, **error**, **event**, **modifier**, **if**, **revert**, **external**, **private**, **immutable**, **mapping**, **address**, **uint256**, and **return** have language-defined roles or are built-in type names. By contrast, names such as **TokenRegistry** and **tokenInfo** are chosen by the programmer.

## 11. Common mistakes

- **Starting a name with a digit:** **1stToken** is invalid; **firstToken** is valid.
- **Using a reserved keyword as a name:** do not name a variable **function** or a contract **contract**.
- **Assuming case does not matter:** **owner** and **Owner** are distinct identifiers.
- **Using spaces, hyphens, or periods in a name:** use **totalSupply** or **total_supply** rather than **total supply**, **total-supply**, or **total.supply**.
- **Confusing built-ins with keywords:** **msg.sender** is a member expression involving a built-in global object; the complete text is not one identifier.
- **Shadowing useful names:** redeclaring a name like **msg** or **require** can obscure a built-in and reduce readability.
- **Ignoring compiler versions:** a name may be rejected after the compiler's reserved-word set changes.
- **Breaking an external API by renaming:** changing a public function or event name can break tools and integrations that depend on the old signature.

## 12. What to remember

1. An identifier is a programmer-chosen name for a declaration or symbol.
2. Solidity identifiers must follow the lexical rules: a letter, underscore, or dollar sign first, then permitted letters, digits, underscores, or dollar signs; identifiers are ASCII-only and case-sensitive.
3. Keywords and built-in names have language-defined roles and should not be chosen casually as user-defined names.
4. The complete reserved-word set depends on the compiler version. Consult official documentation and release notes.
5. Follow consistent naming conventions, avoid shadowing built-ins, and remember that public-facing names are part of a contract's external interface.

## Official references

- [Solidity Language Grammar](https://docs.solidity.org/en/latest/grammar.html)
- [Solidity Types — reference for built-in type names](https://docs.solidity.org/en/latest/types.html)
- [Solidity Style Guide](https://docs.solidity.org/en/latest/style-guide.html)
- [Solidity Contracts — custom storage layout and future-keyword warning](https://docs.solidity.org/en/latest/contracts.html#custom-storage-layout)
- [Solidity v0.5.0 Breaking Changes](https://docs.solidity.org/en/latest/050-breaking-changes.html)
