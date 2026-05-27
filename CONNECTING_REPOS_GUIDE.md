# Connecting liberty-bikes-ai-starter and liberty-bikes-ai-spec-driven

This guide explains how to run the core Liberty Bikes services from `liberty-bikes-ai-starter` and connect your spec-driven AI bot from `liberty-bikes-ai-spec-driven` to play games.

## 🎯 Overview

**Two Repositories:**
1. **liberty-bikes-ai-starter** - Core game services (frontend, auth, game, player)
2. **liberty-bikes-ai-spec-driven** - Your spec-driven AI bot implementation

**Goal:** Run core services from repo #1, and your AI bot from repo #2, and have them communicate.

---

## 📋 Prerequisites

- Java 8 or 11 installed with `JAVA_HOME` set
- Both repositories cloned on your machine
- Docker (optional, for database/monitoring)

---

## 🚀 Step-by-Step Setup

### Step 1: Start Core Services (liberty-bikes-ai-starter)

Open a terminal in the `liberty-bikes-ai-starter` directory:

```bash
cd /Users/jessiezhou/liberty-bikes-ai-starter

# Start all core services
./gradlew start
```

**What this does:**
- Starts `frontend` on port 12000
- Starts `auth-service` on port 8082  
- Starts `game-service` on port 8080
- Starts `player-service` on port 8081

**Verify it's running:**
```bash
# Check the logs
tail -f frontend/build/wlp/usr/servers/frontend/logs/messages.log
tail -f game-service/build/wlp/usr/servers/game-service/logs/messages.log
```

**Open the game UI:**
- Browser: http://localhost:12000
- Or run: `./gradlew frontend:open`

---

### Step 2: Register Your Bot

1. Go to http://localhost:12000
2. Click **"Register Bot"**
3. Enter a bot name (e.g., "SpecDrivenBot")
4. Click **"Register Bot"** again
5. **COPY THE GENERATED BOT KEY** - You'll need this!

Example key: `a1b2c3d4-e5f6-7890-abcd-ef1234567890`

---

### Step 3: Configure Your Spec-Driven Bot

Open a **NEW terminal** and navigate to your spec-driven repo:

```bash
cd /path/to/liberty-bikes-ai-spec-driven
```

Edit the configuration file:
```bash
# Edit this file
nano src/main/resources/META-INF/microprofile-config.properties
```

Add your bot key:
```properties
player_key=a1b2c3d4-e5f6-7890-abcd-ef1234567890

# Ensure game service URL points to localhost
GAME_SERVICE_URL/mp-rest/url=http://localhost:8080
```

Save and exit (Ctrl+X, Y, Enter in nano).

---

### Step 4: Start Your Spec-Driven AI Bot

In the `liberty-bikes-ai-spec-driven` terminal:

```bash
# Start the AI bot service
./gradlew libertyRun
```

**Expected output:**
```
[AUDIT] CWWKE0001I: The server ai-service has been launched.
[AUDIT] CWWKT0016I: Web application available (default_host): http://localhost:8083/
[AUDIT] CWWKF0011I: The ai-service server is ready to run a smarter planet.
```

**Monitor the logs:**
```bash
# In another terminal
tail -f build/wlp/usr/servers/ai-service/logs/messages.log
```

**Look for:**
- "Attempting to register with game service..."
- "Found party id: XXXX"
- JSON game tick messages

---

### Step 5: Watch Your Bot Play!

1. Go to http://localhost:12000
2. Click **"Host Round"** or **"Play Now"**
3. Your bot should automatically join the game!
4. Watch it play in real-time

**You should see:**
- Your bot's name in the player list
- Your bot moving automatically on the game board
- Stats updating after each game
- Bot automatically requeuing for next game

---

## 🔄 Development Workflow

### Making Changes to Your Bot

1. **Edit AI Logic** in `liberty-bikes-ai-spec-driven`:
   ```bash
   # Edit the main AI logic file
   code src/main/java/org/libertybikes/ai/AILogic.java
   ```

2. **Save the file** - Liberty will auto-reload!

3. **Watch the logs** to see the reload:
   ```
   [AUDIT] CWWKZ0009I: The application liberty-bikes-ai has stopped successfully.
   [AUDIT] CWWKZ0003I: The application liberty-bikes-ai updated in 0.152 seconds.
   ```

4. **Test immediately** - Join a new game to see your changes

### Using Bob for Spec-Driven Development

Tell Bob what you want your bot to do:

```
"Create a bot that looks 15 spaces ahead in all directions 
and chooses the path with the most open space. It should 
turn early when space gets tight (less than 8 spaces)."
```

Bob will generate the code in `AILogic.java`, and Liberty will auto-reload it!

---

## 🧪 Testing Scenarios

### Test 1: Basic Connectivity
```bash
# Terminal 1: Core services running
cd liberty-bikes-ai-starter
./gradlew start

# Terminal 2: Your bot running  
cd liberty-bikes-ai-spec-driven
./gradlew libertyRun

# Browser: http://localhost:12000
# Host a game and verify bot joins
```

### Test 2: Multiple Bots
```bash
# You can run the sample bot from liberty-bikes-ai-starter too!
# Terminal 3:
cd liberty-bikes-ai-starter/liberty-bikes-ai
./gradlew libertyRun

# Now you have 2 bots competing!
```

### Test 3: Hot Reload
```bash
# With bot running, edit AILogic.java
# Add: System.out.println("Testing hot reload!");
# Save and check logs - should see message without restart
```

---

## 📊 Port Reference

| Service | Port | URL |
|---------|------|-----|
| Frontend | 12000 | http://localhost:12000 |
| Auth Service | 8082 | http://localhost:8082 |
| Game Service | 8080 | http://localhost:8080 |
| Player Service | 8081 | http://localhost:8081 |
| AI Bot (spec-driven) | 8083 | http://localhost:8083 |
| AI Bot (sample) | 8084 | http://localhost:8084 |

---

## 🐛 Troubleshooting

### Bot Not Connecting

**Problem:** Bot starts but doesn't join games

**Solutions:**
1. Verify bot key is correct in `microprofile-config.properties`
2. Check core services are running: `curl http://localhost:8080/party/describe`
3. Check bot logs for connection errors
4. Ensure no firewall blocking localhost connections

### Port Conflicts

**Problem:** "Port already in use" error

**Solutions:**
```bash
# Find what's using the port
lsof -i :8083

# Kill the process
kill -9 <PID>

# Or change the port in server.xml
```

### Bot Crashes During Game

**Problem:** Bot joins but crashes immediately

**Solutions:**
1. Check for null pointer exceptions in logs
2. Verify boundary checks (0 <= x,y < 120)
3. Ensure `processAiMove()` returns a valid DIRECTION
4. Add defensive null checks

### Hot Reload Not Working

**Problem:** Code changes don't take effect

**Solutions:**
```bash
# Restart the bot service
./gradlew libertyStop
./gradlew libertyRun

# Or force a rebuild
./gradlew clean libertyRun
```

### Core Services Won't Start

**Problem:** `./gradlew start` fails

**Solutions:**
```bash
# Check Java version
java -version

# Clean and rebuild
./gradlew clean build

# Check for port conflicts
lsof -i :8080
lsof -i :8081
lsof -i :8082
lsof -i :12000
```

---

## 🎮 Game Testing Checklist

Use this to verify everything works:

- [ ] Core services start successfully
- [ ] Frontend loads at http://localhost:12000
- [ ] Can register a bot and get a key
- [ ] Bot service starts with correct key
- [ ] Bot appears in game when hosting
- [ ] Bot moves automatically
- [ ] Bot survives at least a few seconds
- [ ] Bot requeues after game ends
- [ ] Stats update correctly
- [ ] Hot reload works for code changes
- [ ] No errors in any service logs

---

## 📁 Directory Structure

```
Your Machine:
├── liberty-bikes-ai-starter/          # Core services repo
│   ├── frontend/                      # Game UI
│   ├── auth-service/                  # Authentication
│   ├── game-service/                  # Game engine
│   ├── player-service/                # Player management
│   ├── liberty-bikes-ai/              # Sample AI bot
│   ├── gradlew                        # Gradle wrapper
│   └── docker-compose.yml             # Docker config
│
└── liberty-bikes-ai-spec-driven/      # Your AI bot repo
    ├── src/main/java/org/libertybikes/ai/
    │   ├── AILogic.java               # ⭐ Your bot logic
    │   ├── model/                     # Game models
    │   └── service/                   # WebSocket service
    ├── src/main/resources/
    │   └── META-INF/
    │       └── microprofile-config.properties  # Bot config
    └── gradlew                        # Gradle wrapper
```

---

## 🔧 Advanced Configuration

### Running on Different Machines

If you want to run core services on one machine and bot on another:

**On Core Services Machine:**
```bash
# Find your IP address
ifconfig | grep "inet "
# Example: 192.168.1.100
```

**On Bot Machine:**
Edit `microprofile-config.properties`:
```properties
GAME_SERVICE_URL/mp-rest/url=http://192.168.1.100:8080
```

### Using Docker for Core Services

```bash
cd liberty-bikes-ai-starter

# Start with Docker
docker-compose up

# Your bot still runs locally
cd ../liberty-bikes-ai-spec-driven
./gradlew libertyRun
```

### Running Multiple Spec-Driven Bots

```bash
# Clone your repo multiple times or use different branches
git clone <your-repo> bot1
git clone <your-repo> bot2

# Register different bot keys for each
# Edit each one's microprofile-config.properties
# Change ports in server.xml to avoid conflicts

# Start each bot
cd bot1 && ./gradlew libertyRun
cd bot2 && ./gradlew libertyRun
```

---

## 📝 Quick Reference Commands

```bash
# === CORE SERVICES (liberty-bikes-ai-starter) ===
./gradlew start              # Start all services
./gradlew stop               # Stop all services
./gradlew frontend:open      # Open game UI
./gradlew dockerStart        # Start with Docker
./gradlew dockerStop         # Stop Docker services

# === YOUR BOT (liberty-bikes-ai-spec-driven) ===
./gradlew libertyRun         # Start bot (foreground)
./gradlew libertyStart       # Start bot (background)
./gradlew libertyStop        # Stop bot
./gradlew clean build        # Clean rebuild
./gradlew war                # Build WAR file

# === MONITORING ===
tail -f <service>/build/wlp/usr/servers/*/logs/messages.log
curl http://localhost:8080/party/describe
curl http://localhost:8081/player/<player-id>
```

---

## 🎯 Success Criteria

Your setup is working correctly when:

1. ✅ Core services start without errors
2. ✅ Game UI loads at http://localhost:12000
3. ✅ You can register a bot and get a key
4. ✅ Your spec-driven bot starts and connects
5. ✅ Bot automatically joins games
6. ✅ Bot moves and plays (even if poorly)
7. ✅ Bot requeues after games end
8. ✅ Code changes hot-reload automatically
9. ✅ No errors in any service logs
10. ✅ You can iterate on bot logic with Bob

---

## 🤝 Working with Bob

Once everything is connected, you can work with Bob to improve your bot:

**Example conversation:**
```
You: "My bot keeps crashing into walls. Can you make it look 
further ahead and turn earlier?"

Bob: [Generates improved AILogic.java]

You: [Save file, watch auto-reload, test in game]

You: "Better! But now it's too cautious. Make it more aggressive 
when there's lots of space."

Bob: [Refines the logic]
```

---

## 📚 Additional Resources

- [Main README](README.md) - Liberty Bikes overview
- [AI Starter README](README-AI-STARTER.md) - Spec-driven development guide
- [Spec Examples](SPEC_EXAMPLES.md) - Bot behavior examples
- [Testing Walkthrough](TESTING_WALKTHROUGH.md) - Detailed testing guide

---

**You're all set! 🎉**

Now you can develop your AI bot using spec-driven development while the core services run separately. Happy bot building! 🤖🏍️