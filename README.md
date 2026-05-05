# MISRA-C
# What is MISRA?

- **MISRA**: Motor Industry Software Reliability Association
- **Originally**: A UK initiative focused on improving software reliability in embedded automotive systems
- **Purpose**: Provide best-practice guidelines for safety-related and high-integrity software
- **Focus**: Safer use of C and C++ in critical systems
- **Critical systems**: Systems where failures can lead to injury, loss of life, or major property or operational damage

## Different behaviors in C

The C language gives programmers a lot of control, but that flexibility also creates room for dangerous or ambiguous behavior. MISRA is concerned with these areas because critical software must behave predictably.

- **Undefined behavior**: The C standard does not define what happens.
  - Examples: accessing outside array bounds, dereferencing invalid pointers, signed integer overflow.
- **Implementation-defined behavior**: The compiler is allowed to choose a behavior, but must document it.
  - Example: the size of `int`, behavior of right-shift on signed values, endianness-related details.
- **Unspecified behavior**: The behavior is valid, but the standard does not require the implementation to document which option it chooses.
  - Example: order of evaluation of some expressions.

## Problems with C in critical systems

C is widely used in embedded software, but it also has features that can easily produce fragile or unsafe code.

- **Weak type safety** can make it easy to mix signed and unsigned values or rely on implicit conversions.
- **Pointer misuse** can lead to invalid memory access, aliasing issues, and hard-to-find bugs.
- **Undefined and unspecified behavior** can make a program work one way on one compiler and differently on another.
- **Manual resource handling** increases the risk of leaks, inconsistent state, and control-flow mistakes.
- **Limited runtime checking** means many serious bugs are not caught automatically when the program executes.

## MISRA C guidelines

MISRA C guidance is organized so teams can apply it systematically in projects.

- **Directives** are broader process- or design-oriented requirements and often need manual review.
- **Rules** are more concrete and are usually easier to check with static analysis tools.
- **Categories** describe how strict a guideline is:
  - **Mandatory**: must always be followed.
  - **Required**: must normally be followed, though a documented deviation may be allowed.
  - **Advisory**: recommended best practice that should be followed where practical.
- **Enforcement** is usually done through static analysis, peer review, coding standards, and compliance evidence.

## Formal deviations

Sometimes a project cannot fully comply with a MISRA guideline. In those cases, the exception should be deliberate, justified, and traceable.

- A deviation should never be informal.
- A deviation record should include:
  - the directive or rule involved,
  - why compliance was not possible or practical,
  - the technical context,
  - the risk assessment,
  - and the mitigating controls.
- Deviations should be reviewable and traceable across the codebase and project documentation.

## MISRA C principles

MISRA C is not just a list of restrictions. It reflects a broader engineering approach for writing predictable and reviewable code.

- Avoid relying on compiler-specific behavior unless it is documented and controlled.
- Prefer simple, explicit code over compact or clever code.
- Keep control flow easy to follow.
- Make types, conversions, and interfaces explicit.
- Use static analysis and documented process controls as part of normal development.

## Language and implementation control

MISRA C expects teams to understand the language environment they are using.

- Know which C standard the compiler supports.
- Document implementation-defined behavior relevant to the project.
- Avoid compiler extensions unless they are justified and controlled.
- Do not rely on undefined or unspecified behavior.

Example:
```c
int x = 1;
int y = x++ + x++; /* Noncompliant: order and side effects are unsafe */

## Type safety and conversions
Many bugs in C come from unclear or unsafe type behavior. MISRA C places strong emphasis on type discipline.

- Avoid mixing signed and unsigned types carelessly.
- Be careful with integer promotions and usual arithmetic conversions.
- Keep casts visible and justified.
- Use fixed or project-defined types when width and range matter.
Example:

unsigned int u = 1U;
int s = -1;

if (s < u)  /* Noncompliant style: mixed signed and unsigned comparison */
{
    /* ... */
}

