# Complete Workflow: Core Services + Spec-Driven Bot Development

## 🎯 Current Status
✅ Core services running in `liberty-bikes-ai-starter`
✅ Frontend accessible at http://localhost:12000
✅ Backend services ready (auth, game, player)

## 📍 Next Steps: Set Up Spec-Driven Development

---

## Step 1: Navigate to Your Spec-Driven Repo

```bash
cd /path/to/liberty-bikes-ai-spec-driven
```

This is where Bob will generate code based on your specifications.

---

## Step 2: Register Your Bot

1. Open http://localhost:12000 in your browser
2. Click **"Register Bot"**
3. Enter a bot name (e.g., "SpecDrivenBot")
4. Click **"Register Bot"** again
5. **COPY THE BOT KEY** (long string like: `a1b2c3d4-e5f6-7890-abcd-ef1234567890`)

---

## Step 3: Configure Your Bot

Edit the configuration file:

```bash
nano src/main/resources/META-INF/microprofile-config.properties
```

Add your bot key:
```properties
player_key=YOUR_BOT_KEY_HERE
GAME_SERVICE_URL/mp-rest/url=http://localhost:8080
```

Save and exit (Ctrl+X, Y, Enter)

---

## Step 4: Start Your Bot Service

```bash
./gradlew libertyRun
```

**Keep this running!** Open a new terminal for the next steps.

**Verify it's working:**
- Check logs: `tail -f build/wlp/usr/servers/ai-service/logs/messages.log`
- Should see: "Found party id: XXXX"
- Should see: JSON game tick messages

---

## Step 5: Work with Bob (Spec-Driven Development)

Now you're ready to use Bob to generate AI logic!

### Open Bob in Your Spec-Driven Repo

In a **NEW terminal** (keep bot service running):

```bash
cd /path/to/liberty-bikes-ai-spec-driven

# Make sure you're in the right directory
pwd
# Should show: /path/to/liberty-bikes-ai-spec-driven
```

### Tell Bob What You Want

Now you can describe your bot's behavior to Bob. For example:

**Example 1: Simple Bot**
```
Bob, create a bot that looks 10 spaces ahead in all directions 
(forward, left, right) and chooses the direction with the most 
open space. Update the AILogic.java file.
```

**Example 2: Smarter Bot**
```
Bob, improve the bot to:
- Look 15 spaces ahead
- Avoid walls and obstacles
- Turn early when space gets tight (less than 8 spaces)
- Prefer going straight when possible
Update the AILogic.java file.
```

**Example 3: Advanced Bot**
```
Bob, create an advanced bot that:
- Scans 20 spaces ahead in each direction
- Calculates a safety score for each direction
- Weights: 60% open space, 30% distance from obstacles, 10% randomness
- Implements emergency turning when space < 5
- Avoids other players' trails
Update the AILogic.java file.
```

### Bob Will:
1. Generate the code for `AILogic.java`
2. Use the `apply_diff` or `write_to_file` tool to update the file
3. Liberty will **automatically hot-reload** the changes (no restart needed!)
4. Your bot will use the new logic in the next game

---

## Step 6: Test Your Bot

1. Go to http://localhost:12000
2. Click **"Host Round"** or **"Play Now"**
3. Your bot should join automatically
4. Watch it play with the new logic!

---

## 🔄 Iterative Development Cycle

```
1. Describe behavior to Bob
   ↓
2. Bob generates/updates AILogic.java
   ↓
3. Liberty auto-reloads (watch logs)
   ↓
4. Test in a game
   ↓
5. Observe bot behavior
   ↓
6. Refine specification → Back to step 1
```

---

## 📁 File Structure

```
Your Machine:
├── liberty-bikes-ai-starter/          # Core services (RUNNING)
│   ├── frontend/                      # Port 12000 ✅
│   ├── auth-service/                  # Port 8082 ✅
│   ├── game-service/                  # Port 8080 ✅
│   └── player-service/                # Port 8081 ✅
│
└── liberty-bikes-ai-spec-driven/      # Your bot (WORKING HERE)
    ├── src/main/java/org/libertybikes/ai/
    │   ├── AILogic.java               # ⭐ Bob edits this
    │   ├── model/                     # Game models
    │   └── service/                   # WebSocket service
    └── build/wlp/usr/servers/ai-service/
        └── logs/messages.log          # Watch this
```

---

## 💬 Example Bob Conversation

**You:**
```
Bob, I need you to create a bot for Liberty Bikes. The bot should:
- Look 12 spaces ahead in three directions (forward, left, right)
- Count the number of free spaces in each direction
- Choose the direction with the most free spaces
- If all directions are blocked, choose a random direction

Update the processAiMove method in AILogic.java.
```

**Bob:**
```
I'll update the AILogic.java file with your specification.
<apply_diff>
<path>src/main/java/org/libertybikes/ai/AILogic.java</path>
<diff>
[Bob generates the code changes]
</diff>
</apply_diff>
```

**You check logs:**
```
[AUDIT] CWWKZ0003I: The application liberty-bikes-ai updated in 0.152 seconds.
```

**You test:**
- Host a game at http://localhost:12000
- Watch your bot play with the new logic!

**You refine:**
```
Bob, the bot is too cautious. Make it look only 8 spaces ahead 
and be willing to go into tighter spaces (minimum 5 free spaces 
instead of 8).
```

**Bob updates the code again → Auto-reload → Test again!**

---

## 🎮 Testing Scenarios

### Scenario 1: Basic Movement
```
Bob, create a simple bot that just avoids walls by turning 
when it gets within 5 spaces of an obstacle.
```
Test: Watch it navigate around the board

### Scenario 2: Smart Pathfinding
```
Bob, improve the bot to calculate the longest clear path 
in each direction and always choose the longest one.
```
Test: See if it survives longer

### Scenario 3: Opponent Awareness
```
Bob, make the bot avoid other players by checking their 
positions and staying at least 10 spaces away.
```
Test: Play with multiple bots

---

## 🐛 Troubleshooting

### Bot Not Updating After Bob's Changes

**Check:**
```bash
# Watch the logs
tail -f build/wlp/usr/servers/ai-service/logs/messages.log

# Should see:
# [AUDIT] CWWKZ0009I: The application liberty-bikes-ai has stopped successfully.
# [AUDIT] CWWKZ0003I: The application liberty-bikes-ai updated in 0.XXX seconds.
```

**If not updating:**
```bash
# Restart the bot service
./gradlew libertyStop
./gradlew libertyRun
```

### Bot Crashes During Game

**Check logs for errors:**
```bash
tail -50 build/wlp/usr/servers/ai-service/logs/messages.log
```

**Tell Bob:**
```
Bob, the bot is crashing with a NullPointerException. 
Add null checks and boundary validation (ensure x,y are 
between 0 and 119).
```

### Bot Not Joining Games

**Verify:**
```bash
# Check bot is registered
curl http://localhost:8081/player

# Check game service is accessible
curl http://localhost:8080/party/describe

# Check bot logs
tail -f build/wlp/usr/servers/ai-service/logs/messages.log
```

---

## 📊 What's Running Where

| Component | Location | Port | Status |
|-----------|----------|------|--------|
| Frontend | liberty-bikes-ai-starter | 12000 | ✅ Running |
| Auth | liberty-bikes-ai-starter | 8082 | ✅ Running |
| Game | liberty-bikes-ai-starter | 8080 | ✅ Running |
| Player | liberty-bikes-ai-starter | 8081 | ✅ Running |
| **Your Bot** | **liberty-bikes-ai-spec-driven** | **8083** | **🔄 Working Here** |

---

## 🎯 Key Points

1. **Core services run in liberty-bikes-ai-starter** (leave them running)
2. **Your bot runs in liberty-bikes-ai-spec-driven** (this is where you work with Bob)
3. **Bob edits AILogic.java** based on your specifications
4. **Liberty auto-reloads** - no manual restarts needed
5. **Test immediately** after each change
6. **Iterate quickly** - describe, generate, test, refine

---

## ✅ Ready to Start!

You should now have:
- ✅ Core services running (liberty-bikes-ai-starter)
- ✅ Bot service running (liberty-bikes-ai-spec-driven)
- ✅ Bot registered with a key
- ✅ Ready to work with Bob

**Next command:**
```
Bob, create a bot that [describe your desired behavior]...
```

---

## 📚 Helpful Resources

- **Spec Examples:** See `SPEC_EXAMPLES.md` for behavior examples
- **Game Mechanics:** Board is 120x120, bot is 3x3, game ticks every 50ms
- **Available Data:** Each tick provides obstacles, moving obstacles, and player positions
- **Direction Enum:** UP, DOWN, LEFT, RIGHT

---

**Happy Bot Building with Bob! 🤖🚴‍♂️**