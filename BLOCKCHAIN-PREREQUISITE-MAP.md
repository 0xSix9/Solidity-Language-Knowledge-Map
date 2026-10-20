# Solidity ↔ Blockchain Concepts: Prerequisite Map

This guide connects the **86 core Solidity topics** in this repository to the six relevant stages of the [Blockchain Concepts Learning Roadmap](https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap). Keep the numbered Solidity topics as the **primary learning order**. Use the blockchain roadmap selectively when a topic needs that background.

> **Principle:** Do not finish all six blockchain stages before starting Solidity. Learn the Solidity topics in order, study only the relevant prerequisite concepts when needed, and deepen the underlying EVM knowledge at the milestones below.

> 📚 **Companion repository — all six blockchain concept stages:** [Blockchain Concepts Learning Roadmap](https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap). To browse the stages directly, open the [concepts directory](https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts). Each **S01–S06** link in this guide also opens its specific stage.

## How to read the map

- **None** — no blockchain-specific prerequisite is needed to learn the Solidity language feature.
- **Familiarity** — understand the basic mechanism; a full deep dive can wait.
- **Required before deep study** — before expecting to explain the feature's runtime behavior confidently, study the listed concepts. You may learn the Solidity syntax earlier.
- **Study alongside / revisit** — a useful companion topic, a later deepening point, or an application-level concept that is not a blocker right now.

“Required” refers to the depth needed to understand runtime behavior, not permission to begin reading or experimenting with the Solidity syntax. Links such as [S01] through [S06] refer to the six roadmap stages below.

## Recommended checkpoints

| Solidity topics | Learning focus | Blockchain concepts to prioritize |
|---|---|---|
| **1–34** | Language and basic data types | Mostly none. Build the syntax foundation; only keep a light awareness of persistence, data locations, and Gas. |
| **35–59** | Contracts, functions, inheritance, events, and errors | Selectively use [S01], [S02], and [S04]. Start learning ABI and external-call behavior when interfaces, events, and custom errors appear. |
| **60–63** | Storage, Memory, Calldata, and data layout | **Deep-study checkpoint:** [S01] + [S03]. Understand execution data areas, storage slots, mappings, arrays, and copying/reference behavior. |
| **64–76** | Global values, hashing, deployment, and contract calls | Prioritize [S01] + [S02]; bring in [S03] for storage context, [S04] for proxies, and [S05] for Gas and transaction-failure behavior where relevant. |
| **77–80** | Function selectors and ABI encoding | **Deep-study checkpoint:** [S01] + [S02] + the Calldata portions of [S03]. Trace encoded calls and returned/revert data. |
| **81–85** | Compiler, bytecode, arithmetic, rollback, and language semantics | Mainly Solidity/compiler semantics; use [S01] and [S05] to explain the resulting execution and transaction behavior. |
| **86** | External calls and reentrancy | **Integration checkpoint:** [S01] + [S02] + [S03] + [S04]. Add [S05] for Gas and failed-transaction context. |

## Blockchain Concepts reference

| Code | Roadmap stage | Use it for |
|---|---|---|
| **S01** | [Stage 01 — EVM Execution](https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts/01-evm-execution) | EVM execution, stack/memory/calldata, execution context, CALL-family operations, creation, and state transitions. |
| **S02** | [Stage 02 — ABI and Contract Interaction](https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts/02-abi-contract-interaction) | ABI, selectors, function dispatch, external calls, events, and error data. |
| **S03** | [Stage 03 — Storage and Data Layout](https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts/03-storage-data-layout) | Storage layout/slots, packing, mappings and arrays, data locations, and storage context. |
| **S04** | [Stage 04 — Smart Contract Architecture](https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts/04-smart-contract-architecture) | Contract composition, inheritance, interfaces/libraries, proxies, delegatecall architecture, and upgradeability. |
| **S05** | [Stage 05 — Advanced Transactions and Gas](https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts/05-advanced-transactions-gas) | Transaction lifecycle, Gas accounting, Out-of-Gas, receipts, reverts, and rollback at transaction level. |
| **S06** | [Stage 06 — Application Connectivity](https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts/06-application-connectivity) | JSON-RPC, signing/sending transactions, `eth_call`, reading chain state, and querying logs/events. |

## Topic-by-topic prerequisites

The list below follows the Solidity map's numeric order from **1 through 86**. The order of the six blockchain stages does not override this sequence.

<a id="topics-1-34-language-foundations"></a>

### Topics 1–34 — Language foundations

| # | Solidity topic | Before deep study | Study alongside / revisit |
|---:|---|---|---|
| 1 | Solidity source files | **None** — Learn the source-file structure as a Solidity language topic. | Return to compiler settings only when the project setup requires them. |
| 2 | SPDX license identifier | **None** — This is source-file metadata, not a blockchain execution concept. | Review repository licensing independently from Solidity runtime behavior. |
| 3 | Pragma and Solidity version | **None** — Learn version constraints and compiler selection first. | Compiler version matters later when comparing language semantics and bytecode. |
| 4 | Import directives | **None** — Imports are a source-code organization feature. | Interfaces and libraries become more important at [#40](#topics-35-59-contract-structure-and-functions) and [#52](#topics-35-59-contract-structure-and-functions). |
| 5 | Identifiers and keywords | **None** — No blockchain-specific prerequisite. | Learn the language vocabulary from examples. |
| 6 | Literal syntax | **None** — No blockchain-specific prerequisite. | Ether-denominated numeric literals connect to [#64](#topics-60-80-data-locations-and-contract-interaction). |
| 7 | Declaration syntax | **None** — Learn declarations directly from Solidity examples. | Revisit when studying state variables, functions, structs, and enums. |
| 8 | Expression, block, and statement syntax | **None** — Learn Solidity syntax directly. | Side effects and call behavior matter later at [#73](#topics-60-80-data-locations-and-contract-interaction) and [#85](#topics-81-86-compiler-semantics-and-security). |
| 9 | Value types and reference types | **Familiarity** — No blockchain prerequisite is needed to learn the type distinction. | Use [S03](#blockchain-concepts-reference) later to understand where reference-type data lives. |
| 10 | Type compatibility and conversions | **None** — Language type rules are sufficient. | Revisit types when encoding values through ABI at [#78](#topics-60-80-data-locations-and-contract-interaction). |
| 11 | Type checking | **None** — Focus on compiler type rules. | Compiler diagnostics return at [#81](#topics-81-86-compiler-semantics-and-security). |
| 12 | State variables | **Familiarity** — Know the basic idea that contract state persists between successful transactions; see [S03](#blockchain-concepts-reference). | A deep storage study is not required yet; prepare [S03: Storage Layout and Storage Context](#blockchain-concepts-reference) before [#60–63](#topics-60-80-data-locations-and-contract-interaction). |
| 13 | Local variables | **Familiarity** — A basic idea of EVM execution and temporary data in [S01](#blockchain-concepts-reference) helps, but is not a blocker. | Study Stack, Memory, and Calldata deeply at [#60](#topics-60-80-data-locations-and-contract-interaction). |
| 14 | Function parameters and return variables | **None** — Learn parameter and return syntax first. | ABI encoding and external-call data become relevant at [#71](#topics-60-80-data-locations-and-contract-interaction) and [#77–80](#topics-60-80-data-locations-and-contract-interaction). |
| 15 | Initialization and default values | **Familiarity** — Basic state-variable persistence from [S03](#blockchain-concepts-reference) is useful, not mandatory. | Revisit storage implications at [#60–63](#topics-60-80-data-locations-and-contract-interaction). |
| 16 | State-variable visibility | **None** — Learn Solidity visibility rules directly. | Do not confuse Solidity visibility with confidentiality: public chain state is not made secret by `private`. |
| 17 | Constant and immutable variables | **None** — Learn the language rules first. | Contract creation and initialization in [S01](#blockchain-concepts-reference) can help when you inspect deployment behavior. |
| 18 | Bool type | **None** — No blockchain-specific prerequisite. | Learn conditions and boolean expressions through small examples. |
| 19 | Uint and int types | **None** — Learn integer widths and signedness as Solidity language rules. | Checked versus unchecked arithmetic is covered at [#83](#topics-81-86-compiler-semantics-and-security). |
| 20 | Address and address payable | **Familiarity** — Know that addresses identify accounts/contracts; basic [S01](#blockchain-concepts-reference) execution context and [S02](#blockchain-concepts-reference) payable-call ideas help. | Revisit account code/balance at [#68](#topics-60-80-data-locations-and-contract-interaction) and external calls at [#71–74](#topics-60-80-data-locations-and-contract-interaction). |
| 21 | Bytes and string types | **None** — Learn the types directly. | Dynamic-data encoding and locations matter later in [S02](#blockchain-concepts-reference), [S03](#blockchain-concepts-reference), and [#60–61](#topics-60-80-data-locations-and-contract-interaction). |
| 22 | Fixed and dynamic arrays | **Familiarity** — No deep blockchain study required for basic syntax. | Use [S03](#blockchain-concepts-reference) for data locations and dynamic-array storage when you reach [#60–63](#topics-60-80-data-locations-and-contract-interaction). |
| 23 | Mapping type | **Familiarity** — Understand that mappings associate keys with values; no deep storage study yet. | Learn the mapping storage formula and layout through [S03](#blockchain-concepts-reference) before [#63](#topics-60-80-data-locations-and-contract-interaction). |
| 24 | Arithmetic operators | **None** — Learn operator behavior directly in Solidity. | Overflow behavior and `unchecked` are revisited at [#83](#topics-81-86-compiler-semantics-and-security). |
| 25 | Comparison and equality operators | **None** — No blockchain-specific prerequisite. | Test boundary conditions with Solidity examples. |
| 26 | Logical operators | **None** — No blockchain-specific prerequisite. | Short-circuit evaluation returns at [#85](#topics-81-86-compiler-semantics-and-security). |
| 27 | Bitwise and shift operators | **None** — Learn the language operators directly. | [S01: EVM Opcodes](#blockchain-concepts-reference) is optional background when you want to connect source operations to lower-level execution. |
| 28 | Assignment, increment, and decrement | **None** — No blockchain-specific prerequisite. | When an assignment changes a state variable, relate it to storage at [#60–63](#topics-60-80-data-locations-and-contract-interaction). |
| 29 | Conditional operator | **None** — No blockchain-specific prerequisite. | Review evaluation and short-circuit semantics at [#85](#topics-81-86-compiler-semantics-and-security). |
| 30 | Delete operator | **Familiarity** — Basic state persistence in [S03](#blockchain-concepts-reference) helps explain what is reset. | Deepen the storage-layout connection at [#60–63](#topics-60-80-data-locations-and-contract-interaction), especially for arrays and mappings. |
| 31 | If and else statements | **None** — Learn branching syntax directly. | Use conditions to validate inputs and enforce contract rules in later exercises. |
| 32 | For, while, and do-while loops | **Familiarity** — Know at a high level that EVM execution consumes Gas; [S05](#blockchain-concepts-reference) is useful background. | Detailed Gas accounting is not needed yet; revisit it when measuring loops or analyzing Out-of-Gas behavior. |
| 33 | Break and continue statements | **None** — No blockchain-specific prerequisite. | Test loop exit conditions and edge cases. |
| 34 | Return and function exit | **Familiarity** — [S01: Return Data and Revert Data](#blockchain-concepts-reference) helps for external calls, but not for learning basic syntax. | Connect function returns to ABI at [#78–80](#topics-60-80-data-locations-and-contract-interaction) and rollback behavior at [#84](#topics-81-86-compiler-semantics-and-security). |

<a id="topics-35-59-contract-structure-and-functions"></a>

### Topics 35–59 — Contract structure and functions

| # | Solidity topic | Before deep study | Study alongside / revisit |
|---:|---|---|---|
| 35 | Contract definition and body | **None** — Learn the Solidity contract skeleton first. | Contract composition in [S04](#blockchain-concepts-reference) becomes useful when organizing multiple contracts. |
| 36 | Constructor | **Familiarity** — Before studying deployment behavior deeply, review [S01: CREATE and Contract Creation/Initialization](#blockchain-concepts-reference) and [S02: Constructors](#blockchain-concepts-reference). | You can learn the constructor syntax before completing these concepts; focus on what executes at deployment. |
| 37 | Receive and fallback functions | **Required for deep understanding** — Review [S02: Receive, Fallback, and Function Dispatch](#blockchain-concepts-reference), plus [S01: Calldata and Execution Context](#blockchain-concepts-reference). | Revisit the dispatch path at [#76](#topics-60-80-data-locations-and-contract-interaction). |
| 38 | Structs | **Familiarity** — Basic data-location concepts in [S03](#blockchain-concepts-reference) help but are not required for defining a struct. | Study struct storage, copying, and reference behavior at [#60–63](#topics-60-80-data-locations-and-contract-interaction). |
| 39 | Enums | **None** — No blockchain-specific prerequisite. | Later combine enums with state variables, validation, and state-machine examples. |
| 40 | Interfaces | **Familiarity** — Understand the role of [S02: ABI and External Function Calls](#blockchain-concepts-reference) and [S04: Interfaces and Libraries](#blockchain-concepts-reference). | A full ABI deep dive can wait until [#77–80](#topics-60-80-data-locations-and-contract-interaction). |
| 41 | Abstract contracts | **Familiarity** — Review [S04: Contract Composition and Inheritance](#blockchain-concepts-reference). | Proxy architecture is not a prerequisite here; learn the language's abstract-contract rules first. |
| 42 | Function declaration and signature | **None** — Learn function syntax and signature vocabulary first. | For selectors and external dispatch, study [S02: Function Selectors](#blockchain-concepts-reference) by [#77](#topics-60-80-data-locations-and-contract-interaction). |
| 43 | Function calls and return values | **Familiarity** — Internal calls need no blockchain prerequisite; external calls require [S01: CALL and Execution Context](#blockchain-concepts-reference) and [S02: External Function Calls](#blockchain-concepts-reference). | Return data and failures become important at [#57–59](#topics-35-59-contract-structure-and-functions) and [#80](#topics-60-80-data-locations-and-contract-interaction). |
| 44 | Function visibility | **None** — Learn `public`, `external`, `internal`, and `private` as language rules. | Review [S02: Visibility](#blockchain-concepts-reference) when comparing Solidity access rules with external contract calls. |
| 45 | View, pure, and payable | **Familiarity** — Review [S02: Mutability and Payable Functions](#blockchain-concepts-reference); [S01: STATICCALL](#blockchain-concepts-reference) helps explain read-only external execution. | Learn the compiler's mutability rules separately from transaction/RPC behavior. |
| 46 | Function modifiers | **None** — No blockchain-specific prerequisite. | Access control is a practical use case; do not treat a modifier alone as proof that a contract is secure. |
| 47 | Function overloading | **Familiarity** — [S02: Function Selectors and ABI](#blockchain-concepts-reference) help explain how overloads are distinguished externally. | The full selector calculation can wait until [#77](#topics-60-80-data-locations-and-contract-interaction). |
| 48 | Virtual and override | **Familiarity** — Review [S04: Inheritance](#blockchain-concepts-reference). | Focus on inheritance and override rules before exploring complex library or proxy architectures. |
| 49 | Base, derived, and multiple inheritance | **Required for deep understanding** — Study [S04: Inheritance and Contract Composition](#blockchain-concepts-reference). | Use small inheritance examples before adding libraries, interfaces, or proxy patterns. |
| 50 | Constructor execution order | **Required for deep understanding** — Combine [S04: Inheritance](#blockchain-concepts-reference), [S02: Constructors](#blockchain-concepts-reference), and [S01: Contract Creation and Initialization](#blockchain-concepts-reference). | Trace a multiple-inheritance example and record the actual constructor order. |
| 51 | Super calls | **Familiarity** — [S04: Inheritance](#blockchain-concepts-reference) should be understood first. | Review method resolution and override behavior with a small example. |
| 52 | Libraries and library functions | **Familiarity** — Review [S04: Interfaces and Libraries](#blockchain-concepts-reference). [S01: DELEGATECALL](#blockchain-concepts-reference) is needed when analyzing external/deployed library-call behavior. | Do not assume every library function is an external `delegatecall`; distinguish inlined internal library functions from deployed-library calls. |
| 53 | Using for and extension methods | **None** — The syntax can be learned without blockchain prerequisites. | [S04: Interfaces and Libraries](#blockchain-concepts-reference) is useful background for how extension functions organize reusable code. |
| 54 | Events and emit | **Familiarity** — Learn [S02: Events and Event Logs](#blockchain-concepts-reference). | [S06: Log and Event Filtering](#blockchain-concepts-reference) is needed when a client must query emitted logs; it is not required to write `emit`. |
| 55 | Indexed event parameters and log topics | **Required for deep understanding** — Study [S02: Topics and Indexed Parameters](#blockchain-concepts-reference). | Add [S06: Log and Event Filtering](#blockchain-concepts-reference) when you build log queries or event-driven applications. |
| 56 | Require input validation | **None for syntax** — You can learn `require` directly; [S01: State Transition and Revert Data](#blockchain-concepts-reference) helps explain its runtime effect. | Review transaction failure and receipt behavior in [S05](#blockchain-concepts-reference) when testing a reverted transaction. |
| 57 | Revert and custom errors | **Required for deep understanding** — Study [S01: Return/Revert Data](#blockchain-concepts-reference) and [S02: Custom Errors and Error Selectors](#blockchain-concepts-reference). | [S05: Transaction Reverts](#blockchain-concepts-reference) explains how a failed execution is surfaced at transaction level. |
| 58 | Assert and panic errors | **Familiarity** — Review [S01: Return/Revert Data and State Transition](#blockchain-concepts-reference) and [S02: Error concepts](#blockchain-concepts-reference). | Use [S05: Transaction Reverts and Receipts](#blockchain-concepts-reference) to connect an error to the transaction result. |
| 59 | Try-catch | **Required for deep understanding** — Review [S01: CALL, Call Stack, and Return/Revert Data](#blockchain-concepts-reference), plus [S02: External Function Calls and Error Data](#blockchain-concepts-reference). | [S05: Transaction Reverts](#blockchain-concepts-reference) is a useful companion; distinguish a caught external-call failure from an overall transaction failure. |

<a id="topics-60-80-data-locations-and-contract-interaction"></a>

### Topics 60–80 — Data locations and contract interaction

| # | Solidity topic | Before deep study | Study alongside / revisit |
|---:|---|---|---|
| 60 | Storage, memory, and calldata | **Required before deep study** — Study [S01: Stack, Memory, and Calldata](#blockchain-concepts-reference) and [S03: Storage Layout, Memory Layout, Calldata Layout, and Storage Context](#blockchain-concepts-reference). | This is the main checkpoint for upgrading from basic familiarity to a working mental model of data locations. |
| 61 | Copying and referencing arrays and structs | **Required for deep understanding** — Be comfortable with [#60](#topics-60-80-data-locations-and-contract-interaction), then review [S03: Storage Context and Memory/Calldata Layout](#blockchain-concepts-reference). | Test assignment behavior separately for value types, memory references, and storage references. |
| 62 | Storage slots and packing | **Required before deep study** — Study [S03: Storage Layout, Storage Slots, and Storage Packing](#blockchain-concepts-reference). | Use compiler storage-layout output or a simple example to verify slot offsets and packing. |
| 63 | Mapping and dynamic-array storage layout | **Required before deep study** — Study [S03: Storage Slots, Mapping/Dynamic-Array Layout, and Storage Layout](#blockchain-concepts-reference); basic hashing helps. | Treat the storage formula as an implementation-level concept; verify the relevant compiler/version behavior. |
| 64 | Ether and time units | **None for syntax** — Learn Solidity's unit suffixes directly. | [S01: Execution Context](#blockchain-concepts-reference) helps with block-time concepts; [S02: Payable Functions](#blockchain-concepts-reference) helps when sending Ether. |
| 65 | Msg global variables | **Required for deep understanding** — Review [S01: Execution Context and CALL](#blockchain-concepts-reference) and [S02: ABI and External Function Calls](#blockchain-concepts-reference). | Include `msg.sender`, `msg.value`, and `msg.data`; connect `msg.sender` to [#69 and #75](#topics-60-80-data-locations-and-contract-interaction). |
| 66 | Block global information | **Familiarity** — Study [S01: Execution Context](#blockchain-concepts-reference). | If using `block.basefee`, add relevant [S05: Gas](#blockchain-concepts-reference) background; avoid treating block data as a trustworthy source of randomness. |
| 67 | Keccak256 and hashing | **None for using the builtin** — You can use `keccak256` before studying advanced cryptography. | [S02: Function Selectors](#blockchain-concepts-reference) and [S03: Mapping Storage](#blockchain-concepts-reference) show important Ethereum use cases for hashing. |
| 68 | Address, balance, and code | **Familiarity** — Review [S01: Execution Context, CALL, and CREATE](#blockchain-concepts-reference). | [S06: Reading Blockchain State](#blockchain-concepts-reference) is a companion when reading balances/code from an external application via RPC. |
| 69 | Tx.origin vs msg.sender | **Required for deep/security understanding** — Study [S01: Execution Context, CALL, and DELEGATECALL](#blockchain-concepts-reference). | Review [S04: Proxy/Delegatecall Architecture](#blockchain-concepts-reference) for context changes; understand why `tx.origin` is generally unsuitable for authorization. |
| 70 | Contract creation with new | **Required for deep understanding** — Study [S01: CREATE and Contract Creation/Initialization](#blockchain-concepts-reference) and [S02: Constructors](#blockchain-concepts-reference). | [S04: Deterministic Deployment and CREATE2](#blockchain-concepts-reference) is a later comparison, not a prerequisite for `new`. |
| 71 | External interface calls | **Required for deep understanding** — Study [S02: ABI and External Function Calls](#blockchain-concepts-reference) plus [S01: CALL, Execution Context, and Return Data](#blockchain-concepts-reference). | [S04: Internal and External Calls](#blockchain-concepts-reference) helps compare contract composition patterns. |
| 72 | Call options, value, and gas | **Required for deep understanding** — Review [S01: CALL and Execution Context](#blockchain-concepts-reference), [S02: Payable/Receive/Fallback](#blockchain-concepts-reference), and [S05: Gas Accounting and Out-of-Gas](#blockchain-concepts-reference). | Test low-level call success flags and returned data; don't equate a successful low-level call with successful business logic. |
| 73 | Call, delegatecall, and staticcall | **Required before deep study** — Study [S01: CALL, DELEGATECALL, STATICCALL, Execution Context, Call Stack, and Return Data](#blockchain-concepts-reference), [S03: Storage Context](#blockchain-concepts-reference), and [S04: Proxy Architecture](#blockchain-concepts-reference). | Compare `msg.sender`, `msg.value`, and storage behavior for each call type using controlled examples. |
| 74 | Ether transfer with call and result checking | **Required for deep/security understanding** — Review [S01: CALL and Return/Revert Data](#blockchain-concepts-reference), [S02: Payable/Receive/Fallback](#blockchain-concepts-reference), and [S05: Gas and Transaction Reverts](#blockchain-concepts-reference). | Connect this topic to the checks-effects-interactions pattern and [#86](#topics-81-86-compiler-semantics-and-security). |
| 75 | Call context and msg.sender | **Required before deep study** — Study [S01: Execution Context, CALL, DELEGATECALL, and Call Stack](#blockchain-concepts-reference), plus [S03: Storage Context](#blockchain-concepts-reference). | [S04: Delegatecall-Based Proxy Architecture](#blockchain-concepts-reference) is important for understanding context in proxies. |
| 76 | Receive and fallback dispatch | **Required for deep understanding** — Review [S02: Function Dispatch, ABI, Receive, and Fallback](#blockchain-concepts-reference) and [S01: Calldata and Execution Context](#blockchain-concepts-reference). | Connect selector matching and empty-calldata behavior with [#37](#topics-35-59-contract-structure-and-functions). |
| 77 | Function signatures and selectors | **Required for deep understanding** — Study [S02: ABI, Function Selectors, and Function Dispatch](#blockchain-concepts-reference). | Connect selector hashing to [#67](#topics-60-80-data-locations-and-contract-interaction); practise identifying a selector in calldata. |
| 78 | ABI encode and decode | **Required before deep study** — Study [S02: ABI Encoding/Decoding](#blockchain-concepts-reference), [S01: Memory/Calldata/Return Data](#blockchain-concepts-reference), and [S03: Calldata Layout](#blockchain-concepts-reference). | Trace a function call from typed values to encoded bytes and back to return data. |
| 79 | ABI encodePacked and encoding ambiguity | **Familiarity** — Know standard ABI encoding from [S02](#blockchain-concepts-reference) and basic hashing from [#67](#topics-60-80-data-locations-and-contract-interaction). | Focus on ambiguous concatenation of dynamic values and safe domain separation; do not assume packed encoding is always collision-safe. |
| 80 | Calldata and returndata structure | **Required before deep study** — Study [S01: Calldata and Return/Revert Data](#blockchain-concepts-reference), [S02: ABI Encoding/Decoding](#blockchain-concepts-reference), and [S03: Calldata Layout](#blockchain-concepts-reference). | Inspect selector, encoded arguments, return data, and revert data in real or locally generated call traces. |

<a id="topics-81-86-compiler-semantics-and-security"></a>

### Topics 81–86 — Compiler, semantics, and security

| # | Solidity topic | Before deep study | Study alongside / revisit |
|---:|---|---|---|
| 81 | Compiler warnings and errors | **None** — Compiler diagnostics can be learned without blockchain prerequisites. | Distinguish compile-time errors from runtime reverts and deployment failures. |
| 82 | Creation and runtime bytecode | **Required for deep understanding** — Study [S01: EVM Opcodes and Contract Creation/Initialization](#blockchain-concepts-reference), plus [S02: Function Dispatch and Selectors](#blockchain-concepts-reference). | Compare creation bytecode with the deployed runtime bytecode; compiler version and settings affect output. |
| 83 | Checked and unchecked arithmetic | **None for language semantics** — Understand [#19](#topics-1-34-language-foundations) integer types and [#24](#topics-1-34-language-foundations) arithmetic first. | [S01: EVM execution](#blockchain-concepts-reference) is optional lower-level context; test both success and revert behavior for the selected compiler version. |
| 84 | Revert and transaction state rollback | **Required for deep understanding** — Study [S01: State Transition and Return/Revert Data](#blockchain-concepts-reference) and [S05: Transaction Lifecycle, Reverts, and Receipts](#blockchain-concepts-reference). | Distinguish a reverted top-level transaction from an external-call failure that is caught and handled. |
| 85 | Expression evaluation order and short circuit | **None for language semantics** — Learn the Solidity evaluation rules directly. | If an expression makes external calls or performs side effects, review [S01: CALL and Execution Context](#blockchain-concepts-reference) and test the exact expression behavior. |
| 86 | External calls, control transfer, and reentrancy | **Required before deep/security study** — Study [S01: CALL, Execution Context, Call Stack, and State Transition](#blockchain-concepts-reference), [S02: External Calls and Receive/Fallback](#blockchain-concepts-reference), [S03: Storage Context](#blockchain-concepts-reference), and [S04: Internal/External Calls and Proxy Context](#blockchain-concepts-reference). | [S05: Gas and Transaction Reverts](#blockchain-concepts-reference) is supporting context. Practise tracing control transfer and state changes; understand Checks-Effects-Interactions and reentrancy guards. |

## How to use this while learning

1. Start with Solidity topic **#1** and continue numerically.
2. Read the topic file and write/test a small example.
3. Check its row in this guide. If it says **None**, do not pause for blockchain theory.
4. If it says **Familiarity**, learn the named idea at a high level and continue.
5. If it says **Required before deep study**, open only the named concepts from the linked blockchain stage(s), then return to the Solidity topic.
6. Test both successful and failing behavior for topics that touch state, external calls, Gas, or revert handling.
7. Treat **#60–63**, **#77–80**, and especially **#86** as checkpoints for connecting Solidity source to EVM behavior.

## Important boundary: language skill vs. application connectivity

You do **not** need to complete all of [S06 — Application Connectivity](https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts/06-application-connectivity) before learning Solidity or writing a basic contract. S06 becomes a priority when you start using an external application to call contracts, read state, submit signed transactions, or query event logs.

Likewise, advanced Rollup, consensus, MEV, and cryptography topics from later stages of the blockchain roadmap are not general prerequisites for these 86 core Solidity topics. Study them when your projects or next specialization require them.

---

*This is a practical learning dependency guide, not an official Solidity specification. Exact runtime behavior can depend on the Solidity compiler version, EVM revision, and target chain. Confirm version-sensitive details against the relevant documentation when studying a topic.*

[S01]: https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts/01-evm-execution "Stage 01 — EVM Execution"
[S02]: https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts/02-abi-contract-interaction "Stage 02 — ABI and Contract Interaction"
[S03]: https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts/03-storage-data-layout "Stage 03 — Storage and Data Layout"
[S04]: https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts/04-smart-contract-architecture "Stage 04 — Smart Contract Architecture"
[S05]: https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts/05-advanced-transactions-gas "Stage 05 — Advanced Transactions and Gas"
[S06]: https://github.com/0xSix9/Blockchain-Concepts-Learning-Roadmap/tree/main/concepts/06-application-connectivity "Stage 06 — Application Connectivity"
