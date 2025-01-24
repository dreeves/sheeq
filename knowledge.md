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
- fzero requires array [a,b] for bracketing points where f(a) and f(b) have opposite signs
- fzero uses decimal.js - function must accept string input and return string output
- Each variable needs its own bracketing range based on physically reasonable values:
  - gramsBrownSugarToAdd: [40, 50]
  - gramsHealthy: [200, 300]
  - healthyGramsSugarPerServing: [1, 10]
  - calories variables: [100, 200]

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
