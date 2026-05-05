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

```

Example:
```
unsigned int u = 1U;
int s = -1;

if (s < u)  /* Noncompliant style: mixed signed and unsigned comparison */
{
    /* ... */
}
```

## Expressions and side effects
Complex expressions are harder to review and more likely to hide subtle bugs. MISRA C prefers expressions that do one thing clearly.

- Avoid multiple side effects in one expression.
- Do not depend on evaluation order unless the language guarantees it.
- Use intermediate variables when they improve readability and safety.
- Keep arithmetic and logical intent explicit.

```
Example:

a[i] = i++; /* Noncompliant: modifies and uses i in the same expression */
```

## Control Flow
Control flow in critical code should be explicit, complete, and easy to reason about.

- Ensure every decision path is intentional.
- Use well-formed if, switch, and loop constructs.
- Make loop termination conditions clear.
- Avoid fragile or confusing branching logic.

```
Example:

if (speed > 100)
{
    alarm = 1;
}
else if (speed < 20)
{
    alarm = 0;
}
else
{
    /* Explicit final branch */
}
```

## Pointers and arrays
Pointers are one of the biggest strengths of C, but also one of its biggest risks. MISRA C therefore treats pointer usage very carefully.

- Use pointers conservatively.
- Avoid unsafe pointer conversions.
- Keep array bounds visible and controlled.
- Prevent invalid access, aliasing problems, and overlap issues.

```
Example:

int arr[10];
int *p = arr;

p[10] = 5; /* Noncompliant: out-of-bounds access */
```

## Functions and interfaces
Functions should have clear responsibilities and consistent interfaces.

- Keep interfaces type-safe and explicit.
- Make sure declarations and definitions match.
- Avoid unused parameters unless they are clearly intentional.
- Use return values meaningfully.

```
Example:

void log_message(int level, const char *msg)
{
    (void)level; /* Suspicious if level is never used */
    /* msg handling */
}
```

## Preprocessor usage
The preprocessor is powerful, but it can hide logic and make code harder to analyze. MISRA C expects it to be used carefully.

- Keep macros simple and controlled.
- Avoid macros that behave like hidden functions or expressions.
- Use include guards consistently.
- Keep conditional compilation understandable and justified.

```
Example:

#define SQUARE(x) x * x /* Noncompliant style */
Safer pattern:

#define SQUARE(x) ((x) * (x))
```

## Dynamic memory
Dynamic memory is often discouraged in critical embedded systems because it can introduce fragmentation, failure paths, and non-deterministic timing.

- Avoid malloc, calloc, realloc, and free in critical application code unless there is strong justification.
- Prefer static allocation, fixed-size buffers, or controlled memory pools.
- Do not let allocation failure become an uncontrolled runtime hazard.

```
Example:

int *buffer = (int *)malloc(10U * sizeof(int)); /* Often noncompliant in critical systems */
free(buffer);
```

## Standard library usage
Some C library functions are risky because they hide bounds, rely on external state, or make failures hard to handle safely.

- Be cautious with string handling functions.
- Avoid APIs that do not make buffer size explicit.
- Prefer safer, clearer, and more reviewable alternatives.
- Ensure library use is compatible with project safety goals.

```
Example:

char dest[8];
strcpy(dest, "example text"); /* Noncompliant: possible overflow */
```

## Readability and maintainability
MISRA C is not only about preventing runtime faults. It also aims to make code easier to inspect and maintain over time.

- Keep comments accurate and useful.
- Avoid dead code and unused objects.
- Use consistent naming and declarations.
- Make the programmer’s intent obvious during review.

```
Example:

int result = compute();
(void)result; /* Check whether ignoring the result is intentional */
```

## Compliance in practice
MISRA C compliance is not just a tool report. It is part of the overall software development process.

- Define a project compliance plan.
- Configure analysis tools carefully.
- Review violations systematically.
- Track deviations and tool limitations.
- Keep evidence for compliance claims.

## Conclusion
MISRA C helps teams use the C language in a more disciplined and predictable way. The goal is not to make code unnecessarily restrictive, but to reduce the chance of hidden bugs, unsafe behavior, and portability problems in critical systems.

A practical MISRA C workflow combines:
- a defined coding standard,
- static analysis,
- peer review,
- formal deviations,
- and consistent engineering discipline.


