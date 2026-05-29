# Spec-Driven AI Bot System - Implementation Plan

## Executive Summary

This document provides a concrete implementation plan for building the spec-driven AI bot development system for Liberty Bikes. The system transforms natural language specifications into tested, validated Java code.

---

## System Components Overview

### 1. User-Facing Components
- ✅ **bot-spec.md** - Template for bot behavior specifications
- ✅ **SPEC_DRIVEN_WORKFLOW.md** - User guide and documentation
- ⏳ **Bob Mode Integration** - AI assistant for code generation

### 2. Core Processing Components
- ⏳ **Spec Parser** - Reads and validates bot-spec.md
- ⏳ **Code Generator** - Converts specs to Java implementation
- ⏳ **Code Injector** - Merges generated code into AILogic.java

### 3. Testing & Validation Components
- ⏳ **Test Suite Framework** - Comprehensive testing infrastructure
- ⏳ **Behavioral Validators** - Detects problematic bot patterns
- ⏳ **Mock Game Environment** - Simulates game scenarios
- ⏳ **Test Reporter** - Generates detailed test reports

### 4. Supporting Components
- ⏳ **AILogic Template** - Clean Java template without markers
- ⏳ **Execution Gate** - Blocks execution if tests fail

**Legend:** ✅ Complete | ⏳ To Be Implemented

---

## Implementation Phases

### Phase 1: Foundation (Week 1)
**Goal:** Set up basic infrastructure and templates

#### Tasks:
1. ✅ Create bot-spec.md template
2. ✅ Write architecture documentation
3. ✅ Write user workflow guide
4. ⏳ Create clean AILogic.java template
5. ⏳ Set up test directory structure

#### Deliverables:
- bot-spec.md with comprehensive template
- Documentation for users and developers
- Clean AILogic.java without generation markers
- Test directory structure in place

---

### Phase 2: Parsing & Generation (Week 2)
**Goal:** Build spec parsing and code generation

#### 2.1 Spec Parser Implementation

**File:** `liberty-bikes-ai/src/main/java/org/libertybikes/ai/parser/SpecParser.java`

**Responsibilities:**
- Read bot-spec.md file
- Parse markdown sections
- Extract structured data
- Validate completeness

**Key Methods:**
```java
public class SpecParser {
    public BotSpecification parse(String specFilePath) throws SpecParseException;
    private void validateRequiredSections(Map<String, String> sections);
    private int extractLookAheadDistance(String content);
    private RiskLevel extractRiskTolerance(String content);
    private List<TestScenario> extractTestScenarios(String content);
}
```

**Data Model:**
```java
public class BotSpecification {
    private String botName;
    private StrategyType strategyType;
    private int lookAheadDistance;
    private RiskLevel riskTolerance;
    private int safetyMargin;
    private Map<String, Double> evaluationWeights;
    private List<TestScenario> testScenarios;
    // ... getters/setters
}
```

#### 2.2 Code Generator Implementation

**File:** `liberty-bikes-ai/src/main/java/org/libertybikes/ai/generator/CodeGenerator.java`

**Responsibilities:**
- Convert BotSpecification to Java code
- Generate processAiMove() method
- Create helper methods
- Add documentation

**Key Methods:**
```java
public class CodeGenerator {
    public String generateCode(BotSpecification spec);
    private String generateProcessAiMove(BotSpecification spec);
    private String generateHelperMethods(BotSpecification spec);
    private String generateDocumentation(BotSpecification spec);
}
```

**Code Templates:**
```java
public class CodeTemplate {
    public static final String PROCESS_AI_MOVE_TEMPLATE = """
        public DIRECTION processAiMove(GameTick gameTick) {
            updateBoard(gameTick);
            
            final int LOOK_AHEAD = %d;
            
            // Evaluate all directions
            %s
            
            // Choose best direction
            %s
            
            currentDirection = bestDirection;
            return currentDirection;
        }
        """;
    
    public static final String COUNT_OPEN_SPACES_TEMPLATE = """
        private int countOpenSpaces(DIRECTION direction, int lookAhead) {
            %s
        }
        """;
}
```

#### 2.3 Code Injector Implementation

**File:** `liberty-bikes-ai/src/main/java/org/libertybikes/ai/injector/CodeInjector.java`

**Responsibilities:**
- Parse existing AILogic.java
- Identify methods to replace
- Inject generated code
- Preserve core utilities
- Maintain formatting

**Key Methods:**
```java
public class CodeInjector {
    public void injectCode(String generatedCode, String targetFile);
    private CompilationUnit parseJavaFile(String filePath);
    private void replaceMethod(CompilationUnit cu, String methodName, String newBody);
    private void addOrUpdateHelperMethods(CompilationUnit cu, List<MethodDeclaration> methods);
    private void writeJavaFile(CompilationUnit cu, String filePath);
}
```

**Dependencies:**
- JavaParser library for AST manipulation
- Preserves imports, fields, and utility methods
- Maintains code formatting

---

### Phase 3: Testing Infrastructure (Week 3)
**Goal:** Build comprehensive testing framework

#### 3.1 Test Suite Framework

**File:** `liberty-bikes-ai/src/test/java/org/libertybikes/ai/test/TestRunner.java`

**Structure:**
```java
public class TestRunner {
    private CompilationTest compilationTest;
    private BehaviorTest behaviorTest;
    private PerformanceTest performanceTest;
    private ScenarioTest scenarioTest;
    
    public TestResult runAllTests(AILogic logic) {
        TestResult result = new TestResult();
        
        // Run tests in order
        result.addResult("Compilation", compilationTest.run());
        if (!result.hasFailures()) {
            result.addResult("Behavior", behaviorTest.run(logic));
            result.addResult("Performance", performanceTest.run(logic));
            result.addResult("Scenarios", scenarioTest.run(logic));
        }
        
        return result;
    }
}
```

#### 3.2 Compilation Test

**File:** `liberty-bikes-ai/src/test/java/org/libertybikes/ai/test/CompilationTest.java`

**Tests:**
```java
public class CompilationTest {
    public TestResult run() {
        TestResult result = new TestResult("Compilation");
        
        // Test 1: Code compiles
        result.add(testCompilation());
        
        // Test 2: No syntax errors
        result.add(testSyntax());
        
        // Test 3: Imports resolved
        result.add(testImports());
        
        return result;
    }
    
    private TestCase testCompilation() {
        try {
            // Trigger Gradle build
            Process p = Runtime.getRuntime().exec("./gradlew war");
            int exitCode = p.waitFor();
            return new TestCase("Code Compilation", exitCode == 0);
        } catch (Exception e) {
            return new TestCase("Code Compilation", false, e.getMessage());
        }
    }
}
```

#### 3.3 Behavior Test

**File:** `liberty-bikes-ai/src/test/java/org/libertybikes/ai/test/BehaviorTest.java`

**Tests:**
```java
public class BehaviorTest {
    public TestResult run(AILogic logic) {
        TestResult result = new TestResult("Behavior");
        
        // Create mock environment
        MockGameEnvironment env = new MockGameEnvironment();
        env.addBot(logic);
        
        // Run simulation
        env.runTicks(100);
        List<DIRECTION> moves = env.getMovementHistory();
        
        // Test 1: Not straight-line
        result.add(testStraightLine(moves));
        
        // Test 2: Not stuck
        result.add(testStuckPattern(moves));
        
        // Test 3: Direction variety
        result.add(testDirectionVariety(moves));
        
        // Test 4: Boundary awareness
        result.add(testBoundaryAwareness(env));
        
        // Test 5: Obstacle avoidance
        result.add(testObstacleAvoidance(env));
        
        return result;
    }
    
    private TestCase testStraightLine(List<DIRECTION> moves) {
        if (moves.size() < 20) {
            return new TestCase("Straight-Line Detection", true, "Insufficient data");
        }
        
        Map<DIRECTION, Integer> counts = new HashMap<>();
        for (DIRECTION d : moves) {
            counts.merge(d, 1, Integer::sum);
        }
        
        int maxCount = counts.values().stream().max(Integer::compare).orElse(0);
        double percentage = maxCount / (double) moves.size();
        
        boolean passed = percentage < 0.95;
        String message = passed ? 
            String.format("Direction variety confirmed (max: %.1f%%)", percentage * 100) :
            String.format("FAIL: Bot goes straight %.1f%% of the time", percentage * 100);
            
        return new TestCase("Straight-Line Detection", passed, message);
    }
    
    private TestCase testStuckPattern(List<DIRECTION> moves) {
        int oscillations = 0;
        for (int i = 2; i < moves.size(); i++) {
            if (isOpposite(moves.get(i), moves.get(i-2))) {
                oscillations++;
            }
        }
        
        double oscillationRate = oscillations / (double) moves.size();
        boolean passed = oscillationRate < 0.3;
        
        String message = passed ?
            String.format("No stuck pattern (oscillation: %.1f%%)", oscillationRate * 100) :
            String.format("FAIL: High oscillation rate: %.1f%%", oscillationRate * 100);
            
        return new TestCase("Stuck Pattern Detection", passed, message);
    }
    
    private boolean isOpposite(DIRECTION d1, DIRECTION d2) {
        return (d1 == DIRECTION.UP && d2 == DIRECTION.DOWN) ||
               (d1 == DIRECTION.DOWN && d2 == DIRECTION.UP) ||
               (d1 == DIRECTION.LEFT && d2 == DIRECTION.RIGHT) ||
               (d1 == DIRECTION.RIGHT && d2 == DIRECTION.LEFT);
    }
}
```

#### 3.4 Mock Game Environment

**File:** `liberty-bikes-ai/src/test/java/org/libertybikes/ai/test/MockGameEnvironment.java`

**Implementation:**
```java
public class MockGameEnvironment {
    private GAME_SLOT[][] board;
    private AILogic bot;
    private int botX, botY;
    private List<DIRECTION> movementHistory;
    private List<GameTick> tickHistory;
    
    public MockGameEnvironment() {
        board = new GAME_SLOT[120][120];
        movementHistory = new ArrayList<>();
        tickHistory = new ArrayList<>();
        initializeBoard();
    }
    
    public void setBoard(TestBoard testBoard) {
        this.board = testBoard.getBoard();
    }
    
    public void addBot(AILogic logic) {
        this.bot = logic;
        this.botX = 60; // Center
        this.botY = 60;
    }
    
    public void runTicks(int numTicks) {
        for (int i = 0; i < numTicks; i++) {
            GameTick tick = createGameTick();
            DIRECTION decision = bot.processAiMove(tick);
            
            movementHistory.add(decision);
            tickHistory.add(tick);
            
            // Update bot position
            updateBotPosition(decision);
            
            // Check for collisions
            if (checkCollision()) {
                break; // Bot died
            }
        }
    }
    
    public BehaviorAnalysis analyze() {
        return new BehaviorAnalysis(movementHistory, tickHistory);
    }
    
    private GameTick createGameTick() {
        // Create GameTick from current board state
        GameTick tick = new GameTick();
        tick.players = createPlayerList();
        tick.obstacles = createObstacleList();
        tick.movingObstacles = createMovingObstacleList();
        return tick;
    }
}
```

#### 3.5 Test Boards

**File:** `liberty-bikes-ai/src/test/java/org/libertybikes/ai/test/TestBoards.java`

**Predefined Scenarios:**
```java
public class TestBoards {
    public static TestBoard EMPTY_BOARD = new TestBoard("Empty", (board) -> {
        // All open spaces
    });
    
    public static TestBoard CORNER_START = new TestBoard("Corner", (board) -> {
        // Bot in corner with walls on two sides
        for (int i = 0; i < 10; i++) {
            board[i][0] = GAME_SLOT.FIXED_OBSTACLE;
            board[0][i] = GAME_SLOT.FIXED_OBSTACLE;
        }
    });
    
    public static TestBoard NARROW_CORRIDOR = new TestBoard("Corridor", (board) -> {
        // Create narrow corridor
        for (int y = 0; y < 120; y++) {
            if (y < 50 || y > 70) {
                for (int x = 40; x < 80; x++) {
                    board[x][y] = GAME_SLOT.FIXED_OBSTACLE;
                }
            }
        }
    });
    
    public static TestBoard SURROUNDED = new TestBoard("Surrounded", (board) -> {
        // Obstacles on 3 sides
        for (int i = 55; i < 65; i++) {
            board[i][55] = GAME_SLOT.FIXED_OBSTACLE; // Top
            board[55][i] = GAME_SLOT.FIXED_OBSTACLE; // Left
            board[65][i] = GAME_SLOT.FIXED_OBSTACLE; // Right
        }
    });
}
```

---

### Phase 4: Integration & Polish (Week 4)
**Goal:** Integrate all components and refine

#### 4.1 Bob Mode Integration

**Update:** `bob-mode/ai-bot-builder.json`

**New Commands:**
```json
{
  "commands": [
    {
      "trigger": "generate bot from spec",
      "action": "read_and_generate",
      "steps": [
        "Read bot-spec.md",
        "Validate specification",
        "Generate Java code",
        "Inject into AILogic.java",
        "Run test suite",
        "Report results"
      ]
    },
    {
      "trigger": "test bot",
      "action": "run_tests_only",
      "steps": [
        "Compile current code",
        "Run test suite",
        "Generate report"
      ]
    },
    {
      "trigger": "explain test failure",
      "action": "analyze_failure",
      "steps": [
        "Read test report",
        "Identify root cause",
        "Suggest fixes",
        "Offer to update spec"
      ]
    }
  ]
}
```

#### 4.2 Execution Gate

**File:** `liberty-bikes-ai/src/main/java/org/libertybikes/ai/gate/ExecutionGate.java`

**Implementation:**
```java
public class ExecutionGate {
    public static boolean allowExecution() {
        TestRunner runner = new TestRunner();
        AILogic logic = new AILogic("test-bot");
        
        TestResult result = runner.runAllTests(logic);
        
        if (result.allPassed()) {
            System.out.println("✅ All tests passed. Bot ready for execution.");
            return true;
        } else {
            System.out.println("❌ Tests failed. Execution blocked.");
            System.out.println(result.getDetailedReport());
            return false;
        }
    }
}
```

**Integration Point:**
```java
// In StartupProcedure.java
@PostConstruct
public void joinFirstRound() {
    if (!ExecutionGate.allowExecution()) {
        System.out.println("Fix issues and restart server.");
        return;
    }
    
    regBean.joinRound();
}
```

#### 4.3 Test Report Generator

**File:** `liberty-bikes-ai/src/test/java/org/libertybikes/ai/test/TestReportGenerator.java`

**Generates:**
- Markdown report file
- Console summary
- HTML report (optional)
- JSON results (for automation)

---

## Implementation Priorities

### Must Have (MVP)
1. ✅ bot-spec.md template
2. ⏳ Spec parser (basic)
3. ⏳ Code generator (basic)
4. ⏳ Code injector
5. ⏳ Compilation test
6. ⏳ Basic behavioral tests
7. ⏳ Test report generation
8. ⏳ Execution gate

### Should Have (V1.0)
9. ⏳ Mock game environment
10. ⏳ Comprehensive behavioral tests
11. ⏳ Scenario tests
12. ⏳ Performance tests
13. ⏳ Bob mode integration
14. ✅ User documentation

### Nice to Have (V2.0)
15. ⏳ Spec validation UI
16. ⏳ Visual test reports
17. ⏳ Historical test tracking
18. ⏳ Performance profiling
19. ⏳ A/B testing framework
20. ⏳ Bot strategy library

---

## Technical Dependencies

### Required Libraries
```gradle
dependencies {
    // Java parsing
    implementation 'com.github.javaparser:javaparser-core:3.24.0'
    
    // Markdown parsing
    implementation 'com.vladsch.flexmark:flexmark-all:0.64.0'
    
    // Testing
    testImplementation 'junit:junit:4.13.2'
    testImplementation 'org.mockito:mockito-core:4.6.1'
    
    // JSON for test reports
    implementation 'com.google.code.gson:gson:2.9.0'
}
```

### Build Configuration
```gradle
// Add to build.gradle
test {
    useJUnit()
    testLogging {
        events "passed", "skipped", "failed"
        exceptionFormat "full"
    }
}

task runBotTests(type: Test) {
    include '**/test/**'
    outputs.upToDateWhen { false }
}
```

---

## Success Metrics

### Phase 1 Success Criteria
- ✅ bot-spec.md template created and documented
- ✅ Architecture documented
- ✅ User workflow documented
- ⏳ Clean AILogic.java template ready

### Phase 2 Success Criteria
- ⏳ Spec parser can read and validate bot-spec.md
- ⏳ Code generator produces compilable Java code
- ⏳ Code injector successfully updates AILogic.java
- ⏳ Generated code matches spec intent

### Phase 3 Success Criteria
- ⏳ Test suite detects straight-line bots
- ⏳ Test suite detects stuck bots
- ⏳ Mock environment simulates game accurately
- ⏳ Test reports are clear and actionable

### Phase 4 Success Criteria
- ⏳ Bob can generate bots from specs
- ⏳ Execution gate blocks bad bots
- ⏳ End-to-end workflow works smoothly
- ⏳ Users can iterate quickly

---

## Risk Mitigation

### Risk 1: Spec Ambiguity
**Mitigation:** 
- Provide clear template with examples
- Implement validation with helpful error messages
- Allow Bob to ask clarifying questions

### Risk 2: Code Generation Complexity
**Mitigation:**
- Start with simple templates
- Iterate based on real specs
- Allow manual code review

### Risk 3: Test False Positives/Negatives
**Mitigation:**
- Tune thresholds based on testing
- Provide override mechanism
- Allow custom test scenarios

### Risk 4: Performance Issues
**Mitigation:**
- Profile test execution
- Optimize hot paths
- Cache results where possible

---

## Next Steps

### Immediate (This Week)
1. Create clean AILogic.java template
2. Set up test directory structure
3. Implement basic spec parser
4. Create simple code generator

### Short Term (Next 2 Weeks)
5. Implement code injector
6. Build test suite framework
7. Create mock game environment
8. Implement behavioral tests

### Medium Term (Next Month)
9. Integrate with Bob mode
10. Add execution gate
11. Polish user experience
12. Gather user feedback

---

## Conclusion

This implementation plan provides a clear roadmap for building the spec-driven AI bot system. The phased approach allows for iterative development and early validation of core concepts.

**Key Success Factors:**
- Clear, comprehensive specifications
- Robust testing infrastructure
- Smooth user experience
- Helpful error messages and feedback

**Expected Outcome:**
A system that makes AI bot development accessible, safe, and fun for users of all skill levels.

---

**Status:** Planning Complete ✅  
**Next Phase:** Implementation  
**Target Completion:** 4 weeks from start