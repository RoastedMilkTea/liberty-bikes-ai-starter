# Liberty Bikes AI - Specification Examples

This document provides examples of bot behavior specifications that you can use with the AI Bot Builder. These examples range from simple to complex and demonstrate different strategies and approaches.

## 📋 Table of Contents

1. [Basic Specifications](#basic-specifications)
2. [Intermediate Specifications](#intermediate-specifications)
3. [Advanced Specifications](#advanced-specifications)
4. [Refinement Examples](#refinement-examples)
5. [Strategy Patterns](#strategy-patterns)

---

## Basic Specifications

### Example 1: Simple Path Finder
```
"Create a bot that looks in three directions (forward, left, right) 
and always chooses the direction with the most open space."
```

**Expected Behavior**: The bot will scan ahead in each possible direction and move toward the area with the longest clear path.

---

### Example 2: Wall Avoider
```
"Make a bot that avoids walls by turning when it gets within 5 spaces 
of an obstacle. It should prefer turning right over turning left."
```

**Expected Behavior**: The bot maintains a safe distance from obstacles and has a directional preference when choosing turns.

---

### Example 3: Cautious Survivor
```
"Create a defensive bot that prioritizes survival. It should:
- Look at least 15 spaces ahead
- Turn early when space gets tight
- Avoid narrow passages
- Prefer open areas"
```

**Expected Behavior**: A conservative bot that plays it safe and avoids risky situations.

---

## Intermediate Specifications

### Example 4: Balanced Strategy
```
"Build a bot with a balanced approach:
- Look 12 spaces ahead in each direction
- Weight available space at 60%
- Consider distance to nearest obstacle at 30%
- Add 10% randomness for unpredictability
- Turn when forward space drops below 8 spaces"
```

**Expected Behavior**: A well-rounded bot that considers multiple factors in its decision-making.

---

### Example 5: Adaptive Bot
```
"Create a bot that adapts its strategy based on available space:
- In open areas (>20 spaces): Move aggressively, prefer straight paths
- In medium areas (10-20 spaces): Be cautious, evaluate all options
- In tight areas (<10 spaces): Emergency mode, take the safest turn immediately"
```

**Expected Behavior**: The bot changes its behavior dynamically based on the current game situation.

---

### Example 6: Opponent-Aware Bot
```
"Make a bot that considers other players:
- Track the positions of all opponents
- Avoid moving toward areas where opponents are heading
- Maintain at least 10 spaces distance from other players when possible
- If trapped, choose the direction away from the nearest opponent"
```

**Expected Behavior**: The bot actively avoids other players and tries to stay out of their paths.

---

## Advanced Specifications

### Example 7: Aggressive Trapper
```
"Create an aggressive bot that tries to trap opponents:
- Look 15 spaces ahead in each direction
- Identify opponent positions and their likely paths
- Try to position itself to cut off opponent escape routes
- Weight trapping opportunities at 40%
- Weight own survival at 60%
- Be willing to take calculated risks (go into spaces with 8+ free slots)
- If an opponent is within 15 spaces, prioritize blocking their path"
```

**Expected Behavior**: An offensive bot that actively tries to eliminate opponents while maintaining reasonable safety.

---

### Example 8: Territory Controller
```
"Build a bot that controls territory:
- Divide the board into quadrants
- Try to dominate one quadrant by creating walls
- Avoid crossing into opponent-controlled areas
- Look 20 spaces ahead
- Create spiral patterns when in open space
- Switch to defensive mode if space drops below 15 spaces"
```

**Expected Behavior**: A strategic bot that tries to claim and defend a portion of the board.

---

### Example 9: Predictive Bot
```
"Create a bot with predictive capabilities:
- Analyze opponent movement patterns (last 5 moves)
- Predict where opponents will be in 3 seconds
- Avoid predicted collision points
- Look 18 spaces ahead
- Weight predictions at 25%
- Weight current state at 75%
- Recalculate predictions every 10 game ticks"
```

**Expected Behavior**: A sophisticated bot that tries to anticipate future game states.

---

### Example 10: Multi-Strategy Bot
```
"Build a bot that switches between strategies:

EARLY GAME (first 30 seconds):
- Aggressive expansion
- Claim territory quickly
- Look 10 spaces ahead
- Take more risks

MID GAME (30-60 seconds):
- Balanced approach
- Look 15 spaces ahead
- Avoid other players
- Maintain safe distance from walls

LATE GAME (after 60 seconds):
- Ultra defensive
- Look 20 spaces ahead
- Prioritize survival above all
- Avoid any risky moves"
```

**Expected Behavior**: A dynamic bot that changes its entire strategy based on game progression.

---

## Refinement Examples

These examples show how to refine an existing bot based on observed behavior.

### Refinement 1: Adjusting Aggression
**Original Spec**: "Create a bot that looks 10 spaces ahead and chooses the path with most space."

**Observed Issue**: "The bot is too cautious and often gets trapped in corners."

**Refinement**:
```
"The bot is too cautious. Make it more aggressive by:
- Reducing look-ahead from 10 to 7 spaces
- Being willing to go into tighter spaces (minimum 5 spaces instead of 8)
- Adding a preference for directions that lead toward the center of the board
- Turning earlier to avoid getting cornered"
```

---

### Refinement 2: Fixing Collision Issues
**Original Spec**: "Make an aggressive bot that tries to trap opponents."

**Observed Issue**: "The bot crashes too often trying to trap opponents."

**Refinement**:
```
"The bot is too aggressive and crashes frequently. Adjust it to:
- Increase minimum safe distance from 5 to 8 spaces
- Only attempt trapping when the bot has at least 12 spaces of escape room
- Add a safety check before aggressive moves
- Prioritize survival over trapping when space is limited"
```

---

### Refinement 3: Improving Decision Speed
**Original Spec**: "Create a bot that analyzes all possible paths and chooses the optimal one."

**Observed Issue**: "The bot is too slow and sometimes misses turns."

**Refinement**:
```
"The bot's decision-making is too complex. Simplify it by:
- Only looking at 3 directions instead of all possibilities
- Reducing look-ahead from 20 to 12 spaces
- Using simpler calculations (count free spaces instead of complex scoring)
- Caching board analysis results for 2 ticks"
```

---

### Refinement 4: Handling Edge Cases
**Original Spec**: "Build a bot that avoids walls and other players."

**Observed Issue**: "The bot sometimes crashes at board boundaries."

**Refinement**:
```
"The bot has boundary issues. Fix it by:
- Adding explicit boundary checks (ensure 0 <= x,y < 120)
- Treating board edges as obstacles
- Adding a 3-space buffer from board boundaries
- Implementing a fallback direction if all paths are blocked"
```

---

## Strategy Patterns

### Pattern 1: Defensive (Survival-Focused)
```
Key Characteristics:
- Long look-ahead distance (15-20 spaces)
- High safety margins (10+ spaces minimum)
- Avoids other players
- Prefers open areas
- Early turning
```

**Use When**: You want maximum survival time, playing against aggressive opponents.

---

### Pattern 2: Aggressive (Elimination-Focused)
```
Key Characteristics:
- Medium look-ahead (10-15 spaces)
- Lower safety margins (5-8 spaces)
- Actively pursues opponents
- Takes calculated risks
- Attempts to trap others
```

**Use When**: You want to eliminate opponents, playing in competitive matches.

---

### Pattern 3: Balanced (Adaptive)
```
Key Characteristics:
- Medium look-ahead (12-15 spaces)
- Moderate safety margins (8-10 spaces)
- Considers multiple factors
- Adapts to game state
- Switches between offensive and defensive
```

**Use When**: You want a well-rounded bot that performs consistently.

---

### Pattern 4: Territorial (Space Control)
```
Key Characteristics:
- Long look-ahead (15-20 spaces)
- Creates patterns (spirals, grids)
- Claims and defends areas
- Avoids opponent territories
- Strategic positioning
```

**Use When**: You want to control board space, playing longer matches.

---

### Pattern 5: Opportunistic (Reactive)
```
Key Characteristics:
- Short to medium look-ahead (8-12 spaces)
- Quick decision-making
- Exploits opponent mistakes
- Adapts rapidly
- Takes advantage of openings
```

**Use When**: You want a flexible bot that capitalizes on opportunities.

---

## Tips for Writing Specifications

### ✅ DO:
- Be specific about numbers (distances, percentages, thresholds)
- Describe the desired behavior clearly
- Include edge case handling
- Specify priorities when multiple factors conflict
- Use concrete examples

### ❌ DON'T:
- Be vague ("make it better")
- Use contradictory requirements
- Specify implementation details (let Bob decide)
- Forget about performance constraints
- Ignore boundary conditions

### 📝 Template Structure:
```
"Create a [strategy type] bot that:
- [Primary behavior/goal]
- [Look-ahead distance]
- [Decision factors and weights]
- [Safety thresholds]
- [Special conditions or adaptations]
- [Edge case handling]"
```

---

## Testing Your Specifications

After Bob generates code from your specification:

1. **Watch Multiple Games**: Observe at least 3-5 games to see consistent behavior
2. **Note Patterns**: Look for repeated mistakes or successful strategies
3. **Identify Issues**: Crashes, getting trapped, poor decisions
4. **Provide Specific Feedback**: Reference exact situations you observed
5. **Iterate**: Refine the specification based on observations

---

## Example Iteration Cycle

**Iteration 1**: "Create a bot that looks ahead and chooses the path with most space."
- **Result**: Works but too cautious

**Iteration 2**: "Make it more aggressive by reducing look-ahead to 8 spaces."
- **Result**: Better but crashes in corners

**Iteration 3**: "Add corner detection and turn earlier when approaching corners."
- **Result**: Survives longer but misses opportunities

**Iteration 4**: "Balance aggression and safety: 10 space look-ahead, turn at 6 spaces."
- **Result**: Good balance, competitive performance

---

## Advanced Topics

### Combining Multiple Strategies
```
"Create a hybrid bot that:
- Uses defensive strategy when health is low or space is limited
- Switches to aggressive when in open areas with opponents nearby
- Uses territorial strategy in the late game
- Adapts the transition thresholds based on game state"
```

### Machine Learning Integration (Future)
```
"Build a bot that:
- Starts with a basic strategy
- Tracks success/failure of decisions
- Adjusts weights based on outcomes
- Learns opponent patterns over multiple games"
```

---

**Remember**: The best specifications are clear, specific, and testable. Start simple and iterate based on observed behavior!