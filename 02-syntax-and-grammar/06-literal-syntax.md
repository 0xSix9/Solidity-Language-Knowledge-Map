# Literal Syntax

> **Goal:** Understand the literal forms accepted by Solidity, how their values are interpreted, how context affects their types, and how to avoid common compile-time mistakes.

## 1. What is a literal?

A **literal** is source-code notation that directly represents a value. It is different from an identifier, which names a variable, function, contract, or other declaration.

~~~solidity
uint256 public count = 42;
bool public isActive = true;
string public label = "Vault";
bytes public payload = hex"1234abcd";
~~~

In this example, **42**, **true**, **"Vault"**, and **hex"1234abcd"** are literals. The names **count**, **isActive**, **label**, and **payload** are identifiers.

A literal's spelling alone does not always tell you its final Solidity type. The compiler considers the form of the literal, how it is used, and whether a conversion is valid. Numeric literals are especially important because Solidity can retain more precision for literal-only expressions than it does for typed integer expressions.

Official references: [Solidity Types — Literals](https://docs.solidity.org/en/latest/types.html) and [Solidity Language Grammar](https://docs.solidity.org/en/latest/grammar.html).

## 2. Literal categories

Solidity supports several important literal forms:

- Boolean literals: **true** and **false**
- Numeric literals: decimal integers, hexadecimal numbers, fractional numbers, and scientific notation
- Address literals: hexadecimal values recognized as valid checksummed addresses
- String literals: quoted text containing printable ASCII characters and supported escape sequences
- Unicode string literals: text prefixed with **unicode**
- Hexadecimal string literals: byte sequences prefixed with **hex**
- Numeric literals with denominations: for example, **1 ether**, **20 gwei**, or **1 days**

These forms are related, but they are not interchangeable. In particular, a hexadecimal number such as **0x1234** is different from a hexadecimal string such as **hex"1234"**.

## 3. Boolean literals

Solidity has two Boolean literals:

~~~solidity
true
false
~~~

They represent the two possible values of the **bool** type.

~~~solidity
bool public enabled = true;
bool public paused = false;
~~~

Boolean literals are not quoted strings. For example, **true** is a Boolean value, while **"true"** is a string literal containing four characters.

~~~solidity
bool a = true;       // Valid
string memory b = "true"; // Valid, but b is a string
// bool c = "true";  // Invalid: a string is not a bool
~~~

The operators that combine or compare Boolean values are covered in the operator and expression topics.

## 4. Decimal integer literals

A decimal integer literal is written using decimal digits.

~~~solidity
uint256 a = 0;
uint256 b = 42;
uint256 c = 1000000;
int256 d = -42;
~~~

Solidity does not use leading zeros as an octal-number notation. Avoid leading zeros in decimal integer literals; write **42**, not **042**.

Underscores may be inserted between digits to improve readability:

~~~solidity
uint256 million = 1_000_000;
uint256 oneEtherInWei = 1_000_000_000_000_000_000;
uint256 grouped = 12_345_678;
~~~

Underscores do not change the represented value. They are separators for human readability, not arithmetic operators.

The underscore must be between digits, and consecutive underscores are not allowed. Do not put an underscore at the beginning or end of a numeric literal.

~~~solidity
uint256 valid = 1_000_000;

// Invalid examples:
// uint256 a = _1000;
// uint256 b = 1000_;
// uint256 c = 1__000;
~~~

## 5. Hexadecimal number literals

A hexadecimal number literal begins with **0x** or **0X** and uses hexadecimal digits **0–9** and **a–f** (or **A–F**).

~~~solidity
uint256 a = 0x2a;        // Decimal value 42
uint256 b = 0xCAFE;
uint256 c = 0xDEAD_BEEF;
~~~

Hexadecimal numbers are useful when reading bit masks, low-level values, hashes, selectors, and raw EVM-related data.

Underscores can separate groups of hexadecimal digits when they are positioned between digits:

~~~solidity
uint256 mask = 0xFF00_FF00;
uint256 selectorLikeValue = 0xA1B2_C3D4;
~~~

A hexadecimal **number** represents a numeric value. It is not the same as a hexadecimal **string**. The distinction matters when assigning values to integer and fixed-size byte types.

~~~solidity
uint16 number = 0x1234;
bytes2 rawBytes = hex"1234";
~~~

Both examples contain the same two-byte pattern in a suitable context, but they use different literal forms and follow different conversion rules. Do not assume that every hexadecimal number can be assigned directly to a fixed-size byte array; the number's size and the target type matter.

## 6. Fractional and scientific-notation literals

Solidity supports fractional number literals and scientific notation.

~~~solidity
uint256 a = 1.3e1; // 13
uint256 b = 2.5e1; // 25
uint256 c = 1e18;  // 10^18
~~~

Examples of literal notation include:

- **0.5**
- **.5**
- **1.25**
- **2e10**
- **2e-10**
- **2.5e1**

A decimal fractional literal must have at least one digit after the decimal point. **1.3** is valid; **1.** is not the corresponding valid form. Scientific notation uses an integer exponent; the mantissa may be fractional.

### The important difference between literal arithmetic and integer arithmetic

Expressions made only from numeric literals can retain arbitrary precision during compile-time evaluation. This means division of numeric literals does not automatically truncate to an integer.

~~~solidity
// Invalid: 5 / 2 is the rational literal value 2.5,
// which cannot be implicitly converted to uint256.
uint256 invalid = 5 / 2;
~~~

The valid integer-division version converts one operand to an integer type before division:

~~~solidity
uint256 truncated = uint256(5) / 2; // Integer division produces 2
uint256 four = 0.5 * 8; // Literal expression evaluates exactly to 4
~~~

The first declaration is invalid because the literal expression **5 / 2** represents the rational value **2.5**, which is not implicitly convertible to **uint256**. In the second declaration, the explicit conversion makes the left operand an integer value before division, so integer division produces **2**.

This distinction is a frequent source of confusion for people coming from languages where integer division is assumed by default.

When a numeric literal expression is combined with a non-literal expression, normal type rules apply. For example, adding a fractional literal to a variable of integer type may fail because there is no valid common type. Review the relevant type-conversion rules rather than assuming the compiler will silently round or truncate.

## 7. Numeric literals and explicit units

Solidity supports denominations for Ether amounts and time values.

~~~solidity
uint256 amountA = 1 ether;
uint256 amountB = 20 gwei;
uint256 amountC = 1 wei;

uint256 delayA = 30 seconds;
uint256 delayB = 5 minutes;
uint256 delayC = 2 days;
~~~

These suffixes scale a numeric value; they are not strings or user-defined variable names. For example, **1 ether** is equivalent to **10** to the power of **18 wei**.

Common Ether denominations include **wei**, **gwei**, and **ether**. Common time units include **seconds**, **minutes**, **hours**, **days**, and **weeks**.

This file only introduces the literal notation for unit suffixes. Their exact scales and recommended use are covered in [Topic 64 — Ether and Time Units](../14-units-and-global-features/64-ether-and-time-units.md).

Use explicit, appropriate units in financial and time-related code. Do not use a suffix that Solidity does not support, and do not confuse a unit suffix with a type conversion.

## 8. Address literals and checksum

An **address literal** is a hexadecimal literal recognized by Solidity as a valid Ethereum address. Address literals use 20-byte values and the mixed-case checksum convention defined by [EIP-55](https://eips.ethereum.org/EIPS/eip-55).

For example, this checksummed address is used in the official documentation:

~~~solidity
address public constant TREASURY =
    0xdCad3a6d3569DF655070DEd06cb7A1b2Ccd1D3AF;
~~~

The uppercase and lowercase letters are significant to the checksum. A hexadecimal string that looks like an address but has an invalid mixed-case checksum can cause a compilation error.

Practical rules:

- Copy addresses from a trusted source and verify them before using them.
- Preserve their checksum casing.
- Do not assume every arbitrary hexadecimal number is an address.
- Do not use a sample address as a real production recipient without verifying its identity.

Address literals are a special case of hexadecimal notation. They are not ordinary strings, and they are different from a value that you explicitly convert to an address type.

Official reference: [Solidity Types — Address Literals](https://docs.solidity.org/en/latest/types.html#address-literals).

## 9. String literals

String literals are quoted text. Solidity accepts single-quoted and double-quoted forms.

~~~solidity
string memory first = "Hello, Solidity";
string memory second = 'Hello, Web3';
~~~

Adjacent quoted string literals can be concatenated at compile time:

~~~solidity
string memory message = "Hello, " "Solidity";
~~~

This is equivalent to the string **"Hello, Solidity"**.

Regular string literals are restricted to printable ASCII characters, with supported escape sequences for special characters. They do not automatically include a trailing zero byte. The literal **"foo"** represents three bytes of text, not four bytes with a trailing zero.

### Common escape sequences

~~~solidity
string memory newline = "first line\nsecond line";
string memory quote = "She said: \"hello\"";
string memory path = "folder\\file";
string memory tabSeparated = "name\tvalue";
string memory byteEscape = "\x41"; // The byte 0x41, which is ASCII "A"
string memory unicodeEscape = "\u00E9"; // Unicode code point U+00E9
~~~

Common supported escape sequences include:

| Escape | Meaning |
|---|---|
| **\n** | Newline |
| **\r** | Carriage return |
| **\t** | Tab |
| **\\** | Backslash |
| **\'** | Single quote |
| **\"** | Double quote |
| **\xNN** | Insert one byte specified by two hexadecimal digits |
| **\uNNNN** | Insert a Unicode code point specified by four hexadecimal digits |

The escape sequence changes the actual bytes represented by the literal. It is not simply a visual formatting instruction for the Solidity source file.

## 10. Unicode string literals

A regular string literal accepts printable ASCII characters. To include arbitrary valid UTF-8 text directly in the source, use the **unicode** prefix:

~~~solidity
string memory greeting = unicode"Hello 😃";
string memory persian = unicode"سلام";
~~~

Unicode string literals can contain valid UTF-8 sequences and support the same escape sequences as regular string literals.

Important distinctions:

- **unicode"سلام"** is a string literal containing UTF-8 text.
- **"Hello"** is a regular ASCII string literal.
- A literal's UTF-8 byte length is not necessarily the same as its number of visible characters.

When byte length matters—for example, when working with encoded data—do not assume one Unicode character always occupies one byte.

## 11. Hexadecimal string literals

A hexadecimal string literal starts with the prefix **hex** and then a quoted sequence of hexadecimal digits.

~~~solidity
bytes memory data = hex"1234abcd";
bytes2 twoBytes = hex"1234";
bytes4 fourBytes = hex"1234_abcd";
~~~

Hex string contents represent bytes. The text between the quotes must contain pairs of hexadecimal digits. Underscores can be used as separators between byte boundaries, but they cannot split a single byte into separate hexadecimal digits.

~~~solidity
bytes memory payload = hex"de_ad_be_ef";

// Invalid examples:
// bytes memory a = hex"123";      // Odd number of hexadecimal digits
// bytes memory b = hex"de_a";     // Separator splits a byte
~~~

Like regular string literals, consecutive hexadecimal string literals can be concatenated:

~~~solidity
bytes memory joined = hex"1234" hex"abcd";
~~~

Hexadecimal string literals are useful for describing raw bytes, binary test vectors, encoded data, and cryptographic values.

They are not regular Solidity strings and are not implicitly convertible to the **string** type. Use **bytes** when the content is byte-oriented, and use **string** when the content is text-oriented.

## 12. Literal conversion depends on the target type

Literal syntax and type conversion are related, but they are not the same topic. The same-looking value may be accepted in one target type and rejected in another.

~~~solidity
uint256 number = 42;
bytes2 exactBytes = 0x1234;
bytes2 shortText = "A";
bytes2 rawBytes = hex"1234";
string memory text = "1234";
~~~

These examples use different literal forms:

- **42** is a decimal integer literal.
- **0x1234** is a hexadecimal number literal.
- **"A"** is a string literal that can fit in a fixed-size byte array.
- **hex"1234"** explicitly represents raw hexadecimal bytes.
- **"1234"** is text, not the same thing as the binary bytes represented by **hex"1234"**.

Conversion rules still apply. For example, a string literal that is too long for a target **bytesN** type is rejected, and arbitrary values do not all convert implicitly to every elementary type.

For the complete conversion rules, see [Solidity Types](https://docs.solidity.org/en/latest/types.html#conversions-between-literals-and-elementary-types) and the related type-system topics in this repository.

## 13. Common mistakes

- **Confusing text with a Boolean:** **"false"** is text; **false** is a Boolean literal.
- **Using unsupported leading zeros:** write **42**, not **042**, for a decimal integer.
- **Assuming literal division truncates:** **5 / 2** is the rational value **2.5** when both operands are numeric literals.
- **Assuming a fraction can always initialize an integer:** conversion to an integer type must be valid; Solidity does not silently round a rational literal into an integer.
- **Confusing numeric and hexadecimal-string literals:** **0x1234** and **hex"1234"** have different literal forms and conversion behavior.
- **Splitting a byte in a hex string:** separators must occur at byte boundaries.
- **Writing non-ASCII characters in a regular string literal:** use a **unicode** string literal when the source needs non-ASCII UTF-8 characters.
- **Ignoring address checksum casing:** use valid checksummed address literals and verify every production address.
- **Assuming a string has a trailing zero:** Solidity string literals do not acquire a C-style NUL terminator automatically.
- **Assuming one Unicode character equals one byte:** UTF-8 sequences can use multiple bytes.
- **Treating unit suffixes as types:** units such as **ether** and **days** scale numeric literals but do not replace the declared type.

## 14. What to remember

1. A literal directly represents a value; an identifier names a program element.
2. Solidity has Boolean, numeric, address, regular string, Unicode string, and hexadecimal string literals.
3. Decimal and hexadecimal numeric literals can contain digit separators, but underscores must be placed correctly.
4. Numeric literal expressions retain arbitrary precision until type rules convert them into a non-literal type; consequently, **5 / 2** as a literal expression is **2.5**, not **2**.
5. Regular strings, Unicode strings, and hexadecimal strings serve different purposes: text, UTF-8 text beyond ASCII, and raw bytes.
6. Address literals need correct checksum casing. Verify addresses before deploying or transferring value.
7. Literal syntax is only one part of the language. For conversions, operators, and data types, continue to the related topics instead of merging every rule into this file.

## Official references

- [Solidity Types — Literals and Elementary-Type Conversions](https://docs.solidity.org/en/latest/types.html)
- [Solidity Language Grammar](https://docs.solidity.org/en/latest/grammar.html)
- [EIP-55: Mixed-case checksum address encoding](https://eips.ethereum.org/EIPS/eip-55)
- [Topic 64 — Ether and Time Units](../14-units-and-global-features/64-ether-and-time-units.md)
- [Topic 10 — Type Compatibility and Conversions](../03-type-system/10-type-compatibility-and-conversions.md)
