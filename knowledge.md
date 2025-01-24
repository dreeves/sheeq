# Equation Calculator Project

## Purpose
Create a web-based calculator that allows users to input and evaluate mathematical equations.

## Current Implementation
- Simple textarea input for equations
- Basic JavaScript evaluation
- Client-side only computation
- Uses fzero for root finding with careful bracketing to avoid division by zero

## Numerical Solver Tips
- Keep equations as simple as possible to improve numerical stability
- When converting equality to root finding (a == b → (a) - (b)), use parentheses around both sides
- For robust root finding:
  1. Bracket positive root by expanding interval (1,2,4,8...) until sign change
  2. If no positive root, try negative side
  3. Apply Brent's method once bracket is found - always call toString() on the result
- Use small positive initial guess (e.g. 1e-6) to bias towards positive solutions

## Variable Ranges
When solving equations with multiple variables that can be inferred:
- Each variable type needs its own appropriate bracketing range
- Ranges should reflect physically meaningful values
- Avoid ranges that could cause division by zero
- Test ranges with known working values first

## TODO
- Replace eval() with secure equation parser
- Add support for more mathematical functions
- Add input validation
- Add error handling for malformed equations
- Consider adding history of calculations

## Security Notes
- Current implementation uses eval() which is unsafe for production
- Need to implement proper sanitization and parsing
- When using eval() to create functions, must bind them with .bind(null) to ensure proper scope
