# Spec-Driven AI Bot Development Workflow

## Overview

This guide explains how to create and test Liberty Bikes AI bots using the new spec-driven development system. Instead of writing Java code directly, you describe your bot's behavior in natural language, and the system automatically generates, tests, and validates the implementation.

---

## Quick Start

### 1. Edit Your Bot Specification

Open [`liberty-bikes-ai/bot-spec.md`](liberty-bikes-ai/bot-spec.md) and describe your bot's behavior:

```markdown
## Bot Identity
- **Name:** MyAwesomeBot
- **Strategy Type:** Aggressive

## Core Behavior
### Decision Making Strategy
Look ahead 12 spaces in all directions and choose the path 
that gets closest to opponents while maintaining safety...
```

### 2. Generate Code with Bob

Ask Bob to generate your bot:
```
"Bob, read my bot-spec.md and generate the AI code"
```

### 3. Automatic Testing

Bob will automatically:
- ✅ Validate your specification
- ✅ Generate Java code
- ✅ Inject code into AILogic.java
- ✅ Compile the code
- ✅ Run comprehensive tests
- ✅ Analyze bot behavior

### 4. Review Results

Bob will provide a detailed test report:
- Compilation status
- Behavioral test results
- Performance metrics
- Recommendations

### 5. Play or Iterate

- **If tests pass:** Run the game and watch your bot play!
- **If tests fail:** Review feedback and refine your spec

---

## Detailed Workflow

### Phase 1: Specification

#### What to Include in bot-spec.md

**Required Sections:**
1. **Bot Identity** - Name, strategy type, version
2. **Core Behavior** - High-level decision-making approach
3. **Look-Ahead Configuration** - How far and how to scan
4. **Obstacle Avoidance** - What to avoid and how
5. **Risk Assessment** - Risk tolerance and safety margins

**Optional Sections:**
6. **Advanced Features** - Path scoring, opponent tracking, trap detection
7. **Expected Behaviors** - Test scenarios and success criteria
8. **Implementation Notes** - Preferences for code structure

#### Writing Effective Specifications

**Be Specific:**
❌ "Avoid obstacles"
✅ "Look 15 spaces ahead in each direction, count open spaces, and choose the direction with the most room"

**Include Numbers:**
❌ "Look ahead a bit"
✅ "Look ahead 12 spaces"

**Describe Edge Cases:**
```markdown
**When All Paths Blocked:**
Choose the direction with the most open space, even if 
below safety threshold. Never give up.
```

**Define Success Criteria:**
```markdown
**Scenario: Corner Start**
- Expected: Escape corner within 5 ticks
- Success: Avoid walls, move toward open space
```

---

### Phase 2: Code Generation

#### What Bob Does

1. **Reads bot-spec.md**
   - Parses all sections
   - Extracts key parameters
   - Builds internal representation

2. **Validates Specification**
   - Checks required sections present
   - Validates numeric ranges
   - Identifies conflicts or ambiguities
   - Reports issues if found

3. **Generates Java Code**
   - Creates `processAiMove()` implementation
   - Generates helper methods
   - Adds inline documentation
   - Optimizes for performance

4. **Injects Code**
   - Parses existing AILogic.java
   - Replaces method implementations
   - Preserves core utility methods
   - Maintains proper formatting

#### Generated Code Structure

```java
public DIRECTION processAiMove(GameTick gameTick) {
    updateBoard(gameTick);
    
    // Look-ahead distance from spec
    final int LOOK_AHEAD = 15;
    
    // Evaluate all directions
    int upScore = countOpenSpaces(DIRECTION.UP, LOOK_AHEAD);
    int downScore = countOpenSpaces(DIRECTION.DOWN, LOOK_AHEAD);
    // ... etc
    
    // Choose best direction based on spec criteria
    DIRECTION bestDirection = selectBestDirection(scores);
    
    currentDirection = bestDirection;
    return currentDirection;
}

// Helper methods generated based on spec
private int countOpenSpaces(DIRECTION direction, int lookAhead) {
    // Implementation based on your spec
}
```

---

### Phase 3: Testing & Validation

#### Automatic Test Suite

The system runs comprehensive tests to ensure your bot works correctly:

##### 1. Compilation Tests
- ✅ Code compiles without errors
- ✅ No syntax issues
- ✅ All imports resolved
- ✅ No type mismatches

##### 2. Behavioral Tests

**Straight-Line Detection:**
- Ensures bot changes direction
- Detects if bot only goes in one direction
- **Fail Criteria:** >95% of moves in same direction

**Stuck Detection:**
- Identifies oscillating patterns
- Detects back-and-forth behavior
- **Fail Criteria:** >30% oscillations

**Boundary Awareness:**
- Verifies bot respects board edges
- Checks 3x3 size is accounted for
- **Fail Criteria:** Any boundary violations

**Obstacle Avoidance:**
- Tests bot avoids known obstacles
- Verifies collision detection
- **Fail Criteria:** Hits any obstacle

**Direction Variety:**
- Ensures bot uses multiple directions
- Checks adaptability
- **Fail Criteria:** Uses <2 directions

##### 3. Performance Tests

**Execution Time:**
- Measures decision-making speed
- **Fail Criteria:** >50ms per tick

**Memory Usage:**
- Monitors resource consumption
- **Fail Criteria:** Excessive allocations

**Stability:**
- Checks for crashes or hangs
- **Fail Criteria:** Any exceptions

##### 4. Scenario Tests

**Test Scenarios:**
1. **Corner Start** - Bot starts in corner
2. **Open Field** - Bot in center of empty space
3. **Narrow Corridor** - Limited movement options
4. **Surrounded** - Obstacles on 3 sides
5. **Chase** - Other players nearby

**Each Scenario Tests:**
- Appropriate decision-making
- Survival capability
- Strategic choices

#### Mock Game Environment

Tests run in a simulated environment:
- Custom board states
- Controlled scenarios
- No need for full game server
- Fast iteration

```
MockGameEnvironment:
  - Create test board
  - Place bot at position
  - Run 100 game ticks
  - Analyze decisions
  - Generate report
```

---

### Phase 4: Test Reports

#### Sample Test Report

```markdown
# Bot Test Report: MyAwesomeBot

**Test Date:** 2026-05-29 13:45:00
**Overall Status:** ✅ PASS

## Compilation
✅ Code compiles successfully
✅ No syntax errors
✅ All imports resolved

## Behavioral Tests
✅ Direction changes: 18 changes in 100 ticks
✅ No straight-line behavior detected
✅ No stuck patterns detected
✅ Boundary awareness confirmed
✅ Obstacle avoidance working
✅ Direction variety: 4/4 directions used

## Performance Tests
✅ Average execution time: 2.1ms
✅ Max execution time: 3.8ms (well under 50ms limit)
✅ Memory usage: Normal
✅ No exceptions or crashes

## Scenario Tests
✅ Corner Start: Escaped in 3 ticks
✅ Open Field: Made strategic choices
✅ Narrow Corridor: Navigated successfully
✅ Surrounded: Found best escape route
✅ Chase: Avoided opponent collision

## Behavior Analysis
- **Decision Quality:** 89%
- **Survival Rate:** 95%
- **Risk Management:** Appropriate
- **Path Efficiency:** Excellent

## Recommendation
✅ **Bot is ready for game execution**

All tests passed. Bot behavior meets quality standards.
You can now run the game with confidence!
```

#### Failed Test Report

```markdown
# Bot Test Report: BrokenBot

**Test Date:** 2026-05-29 13:50:00
**Overall Status:** ❌ FAIL

## Compilation
✅ Code compiles successfully

## Behavioral Tests
❌ FAIL: Straight-line behavior detected
   - Bot went RIGHT for 97/100 ticks
   - Only changed direction 3 times
   - Appears to ignore obstacles

✅ Boundary awareness confirmed
✅ No stuck patterns

## Issues Detected

### Critical Issue: Straight-Line Bot
**Problem:** Bot rarely changes direction
**Impact:** Will crash into obstacles quickly
**Suggestion:** Review decision-making logic in spec
  - Ensure all directions are evaluated
  - Check that best direction is actually selected
  - Verify obstacle detection is working

### Recommendation
❌ **Bot is NOT ready for game execution**

Please review your specification and address the issues above.
Focus on the decision-making strategy section.
```

---

### Phase 5: Execution Gate

#### Pass Criteria

Your bot can run in the game if:
- ✅ Code compiles successfully
- ✅ All behavioral tests pass
- ✅ Performance is within limits
- ✅ No critical issues detected

#### Fail Criteria

Game execution is blocked if:
- ❌ Compilation errors
- ❌ Straight-line behavior
- ❌ Stuck patterns
- ❌ Boundary violations
- ❌ Performance issues
- ❌ Critical bugs detected

#### Override (Advanced)

If you understand the risks, you can override the gate:
```
"Bob, I understand the risks, allow execution anyway"
```

**Warning:** Overriding may result in poor game performance or crashes.

---

## Iteration & Refinement

### Common Issues & Solutions

#### Issue: Bot Goes Straight

**Symptoms:**
- Rarely changes direction
- Crashes into obstacles
- Test report shows >90% same direction

**Solutions:**
1. Check decision-making logic in spec
2. Ensure all directions are evaluated
3. Verify scoring algorithm is correct
4. Add more weight to obstacle avoidance

**Example Fix:**
```markdown
## Core Behavior
### Decision Making Strategy
❌ Before: "Go forward unless blocked"
✅ After: "Evaluate all 4 directions each tick, 
          score each based on open space, 
          choose highest score"
```

#### Issue: Bot Gets Stuck

**Symptoms:**
- Oscillates between two directions
- Back-and-forth movement
- Test report shows high oscillation rate

**Solutions:**
1. Add direction continuity bonus
2. Increase look-ahead distance
3. Implement path memory
4. Add randomness for tie-breaking

**Example Fix:**
```markdown
## Look-Ahead Configuration
### Evaluation Criteria
Add: "Tertiary Factor (10% weight): Direction continuity
      - Slight preference for maintaining current direction"
```

#### Issue: Bot Too Cautious

**Symptoms:**
- Survives but doesn't win
- Avoids all risks
- Misses opportunities

**Solutions:**
1. Reduce safety margins
2. Increase risk tolerance
3. Add aggressive behaviors
4. Reduce look-ahead distance

**Example Fix:**
```markdown
## Risk Assessment
### Risk Tolerance
❌ Before: Level: Low, Safety Margin: 15 spaces
✅ After: Level: Medium, Safety Margin: 8 spaces
```

#### Issue: Bot Too Aggressive

**Symptoms:**
- Dies quickly
- Takes unnecessary risks
- Crashes often

**Solutions:**
1. Increase safety margins
2. Reduce risk tolerance
3. Add defensive checks
4. Increase look-ahead distance

---

## Best Practices

### Specification Writing

1. **Start Simple**
   - Begin with basic behavior
   - Test and iterate
   - Add complexity gradually

2. **Be Explicit**
   - Don't assume Bob knows what you mean
   - Provide specific numbers
   - Describe edge cases

3. **Test Scenarios**
   - Include expected behaviors
   - Define success criteria
   - Cover edge cases

4. **Iterate Based on Feedback**
   - Read test reports carefully
   - Address specific issues
   - Make incremental changes

### Testing Strategy

1. **Run Tests Frequently**
   - Test after each spec change
   - Don't wait until "done"
   - Catch issues early

2. **Read Reports Thoroughly**
   - Understand what failed
   - Follow suggestions
   - Look for patterns

3. **Use Scenarios**
   - Think about game situations
   - Describe expected behavior
   - Verify in tests

### Development Cycle

```
1. Write/Update Spec
   ↓
2. Generate Code
   ↓
3. Run Tests
   ↓
4. Review Report
   ↓
5. Pass? → Play Game
   ↓
6. Fail? → Refine Spec → Back to Step 2
```

---

## Advanced Features

### Custom Test Scenarios

Add your own test scenarios to bot-spec.md:

```markdown
## Expected Behaviors

### Test Scenarios

**Scenario: Aggressive Chase**
- **Setup:** Opponent directly ahead, 10 spaces away
- **Expected:** Maintain course, try to trap opponent
- **Success Criteria:** Close distance, force opponent to turn
```

### Performance Tuning

Optimize for speed:

```markdown
## Performance Constraints

### Optimization Priorities
1. Early termination when obstacle found
2. Cache board state calculations
3. Minimize object creation
4. Use efficient data structures
```

### Strategy Variants

Create multiple specs for different strategies:
- `bot-spec-defensive.md`
- `bot-spec-aggressive.md`
- `bot-spec-balanced.md`

Switch between them as needed.

---

## Troubleshooting

### Bob Can't Parse Spec

**Problem:** Bob reports spec validation errors

**Solutions:**
- Check all required sections are present
- Verify markdown formatting
- Ensure numeric values are valid
- Look for typos in section headers

### Code Won't Compile

**Problem:** Generated code has compilation errors

**Solutions:**
- Check spec for conflicting requirements
- Verify numeric ranges are reasonable
- Report issue to Bob for regeneration
- Check AILogic.java wasn't manually edited

### Tests Keep Failing

**Problem:** Same tests fail repeatedly

**Solutions:**
- Read failure details carefully
- Focus on one issue at a time
- Simplify spec to isolate problem
- Ask Bob for specific help

### Bot Behaves Unexpectedly

**Problem:** Bot works but doesn't match expectations

**Solutions:**
- Review generated code
- Check if spec was interpreted correctly
- Add more specific test scenarios
- Refine decision-making description

---

## FAQ

**Q: Can I edit AILogic.java directly?**
A: Not recommended. Changes will be overwritten when Bob regenerates code. Edit bot-spec.md instead.

**Q: How do I see the generated code?**
A: Open `liberty-bikes-ai/src/main/java/org/libertybikes/ai/AILogic.java`

**Q: Can I skip tests?**
A: Not recommended. Tests prevent buggy bots from running. Use override only if you understand the risks.

**Q: How long does testing take?**
A: Usually 5-10 seconds for full test suite.

**Q: Can I add custom helper methods?**
A: Yes! Describe them in the "Implementation Notes" section of your spec.

**Q: What if my strategy is too complex for the spec format?**
A: Break it down into smaller, clearer steps. If still too complex, consider simplifying your strategy.

**Q: Can I version control my specs?**
A: Yes! bot-spec.md is designed to be git-friendly.

---

## Examples

### Example 1: Simple Defensive Bot

```markdown
## Bot Identity
- **Name:** SafeBot
- **Strategy Type:** Defensive

## Core Behavior
### Decision Making Strategy
Look 20 spaces ahead in all directions. Choose the 
direction with the most open space. Never take risks.

## Look-Ahead Configuration
- **Look-Ahead Distance:** 20 spaces
- **Directions to Evaluate:** All four

## Risk Assessment
- **Level:** Low
- **Minimum Safe Distance:** 15 spaces
```

### Example 2: Aggressive Bot

```markdown
## Bot Identity
- **Name:** HunterBot
- **Strategy Type:** Aggressive

## Core Behavior
### Decision Making Strategy
Look 10 spaces ahead. Prioritize paths that get closer 
to opponents while maintaining minimum safety of 5 spaces.

## Look-Ahead Configuration
- **Look-Ahead Distance:** 10 spaces

## Risk Assessment
- **Level:** High
- **Minimum Safe Distance:** 5 spaces
```

### Example 3: Balanced Bot

```markdown
## Bot Identity
- **Name:** BalancedBot
- **Strategy Type:** Balanced

## Core Behavior
### Decision Making Strategy
Look 15 spaces ahead. Balance between open space (60%), 
obstacle avoidance (30%), and direction continuity (10%).

## Look-Ahead Configuration
- **Look-Ahead Distance:** 15 spaces

### Evaluation Criteria
- Primary (60%): Open space count
- Secondary (30%): Obstacle proximity
- Tertiary (10%): Direction continuity

## Risk Assessment
- **Level:** Medium
- **Minimum Safe Distance:** 8 spaces
```

---

## Getting Help

### Ask Bob

Bob can help with:
- Writing specifications
- Understanding test failures
- Refining strategies
- Debugging issues

Example questions:
- "Bob, why did my bot fail the straight-line test?"
- "Bob, how can I make my bot more aggressive?"
- "Bob, explain the path scoring algorithm"

### Resources

- [Architecture Document](SPEC_DRIVEN_ARCHITECTURE.md) - Technical details
- [Bot Spec Template](liberty-bikes-ai/bot-spec.md) - Your spec file
- [Liberty Bikes README](liberty-bikes-ai/README.md) - Game basics

---

## Summary

The spec-driven workflow makes AI bot development:
- **Easier:** Write natural language instead of code
- **Safer:** Comprehensive testing prevents bugs
- **Faster:** Iterate quickly with immediate feedback
- **Better:** Focus on strategy, not implementation

**Remember:**
1. Write clear, specific specifications
2. Test frequently
3. Read reports carefully
4. Iterate based on feedback
5. Have fun!

Happy bot building! 🤖🚴