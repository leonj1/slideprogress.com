# Stopping Criteria

## Core Heuristic

**STOP decomposing when the task can be written as a unit test with an assertion of a primitive.**

### Primitive Assertions (Leaf Node Indicators)
A task is atomic (cannot be decomposed further) when its acceptance can be verified by asserting:

| Primitive Type | Example Assertion |
|----------------|-------------------|
| **JSON payload** | `assertEquals(expectedJson, response.body)` |
| **String** | `assertEquals("expected-value", result)` |
| **Integer/Number** | `assertEquals(42, calculateTotal())` |
| **Boolean** | `assertTrue(isValid)` |
| **Array/List** | `assertEquals(["a", "b"], getItems())` |

### Decision Flow

```
Can I write: assert(primitive, functionUnderTest()) ?
    │
    ├── YES → STOP. This is a leaf node. Proceed to implementation.
    │
    └── NO → CONTINUE decomposing. Break into smaller sub-tasks.
```

### Examples

**Leaf Node (STOP)**:
```gherkin
Scenario: Calculate order total
  Given an order with items costing $10, $20, $30
  When I calculate the total
  Then the total should be $60
```
→ Can assert: `assertEquals(60, order.calculateTotal())` ✓

**Not a Leaf (CONTINUE decomposing)**:
```gherkin
Scenario: Complete checkout process
  Given a user with items in cart
  When they complete checkout
  Then order is placed and confirmation email sent
```
→ Cannot assert with single primitive. Multiple side effects. Decompose.

### Anti-Patterns (Keep Decomposing)

| Signal | Why It's Not Atomic |
|--------|---------------------|
| "...and also..." | Multiple outcomes |
| "...then X happens, then Y..." | Sequential side effects |
| "...the system should..." | Vague, not measurable |
| Assertion requires mocking 3+ dependencies | Too coupled |
| Assertion requires database state check | Integration, not unit |

## Usage

This file is the single source of truth for decomposition stopping criteria.
Referenced by:
- `architect.md` - for task breakdown decisions
- `scope-manager.md` - for complexity evaluation
