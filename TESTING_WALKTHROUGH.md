# Liberty Bikes AI - Complete Testing Walkthrough

This guide will walk you through testing the entire Liberty Bikes project, including both the core services and your AI bot.

## Prerequisites Checklist

Before starting, ensure you have:
- ✅ Java 8 or 11 installed (`java -version` works)
- ✅ `JAVA_HOME` environment variable set
- ✅ Git installed
- ✅ Docker installed (optional, for database and monitoring)
- ✅ A code editor (VS Code, IntelliJ, Eclipse)

## Project Structure

You should have two repositories:
1. **liberty-bikes-ai-starter** - Your current working directory (the AI bot project)
2. **liberty-bikes-ai-spec-driven** - Your spec-driven development version

## Testing Phase 1: Core Services (Liberty Bikes Main Game)

### Step 1.1: Start Core Services

From the root of `liberty-bikes-ai-starter`:

```bash
# Start all core services (frontend, auth, game, player)
./gradlew start
```

**Expected Output:**
- Gradle will download dependencies (first time only)
- 4 Liberty servers will start (frontend, auth-service, game-service, player-service)
- This may take 2-5 minutes on first run

**Verify Success:**
```bash
# Check if servers are running
./gradlew tasks | grep "Server"
```

### Step 1.2: Open the Game UI

```bash
# This should automatically open http://localhost:12000 in your browser
./gradlew frontend:open
```

Or manually open: http://localhost:12000

### Step 1.3: Play Your First Manual Game

1. Click **"Play as Guest"**
2. Enter a username (e.g., "TestPlayer")
3. Click **"Sign in as Guest"**
4. Click **"Play Now"**
5. Use arrow keys to control your bike
6. Try to survive without crashing!

**What to verify:**
- ✅ Game board renders correctly
- ✅ Your bike moves with arrow keys
- ✅ Game ends when you crash
- ✅ Stats update after game ends
- ✅ You can requeue for another game

### Step 1.4: Test Hosting a Game

1. Open a new browser tab to http://localhost:12000
2. Click **"Host Round"**
3. You'll see a countdown timer
4. Open another tab and join as a guest
5. Both players should appear in the hosted game

### Step 1.5: Check Server Logs

```bash
# View logs for each service
cat frontend/build/wlp/usr/servers/frontend/logs/messages.log
cat auth-service/build/wlp/usr/servers/auth-service/logs/messages.log
cat game-service/build/wlp/usr/servers/game-service/logs/messages.log
cat player-service/build/wlp/usr/servers/player-service/logs/messages.log
```

**Look for:**
- No ERROR messages
- Services started successfully
- Port numbers: frontend:12000, auth:8082, game:8080, player:8081

## Testing Phase 2: AI Bot Service

### Step 2.1: Navigate to AI Service

```bash
cd liberty-bikes-ai
```

### Step 2.2: Register Your Bot

1. Go to http://localhost:12000
2. Click **"Register Bot"**
3. Enter a bot name (e.g., "MyAIBot")
4. Click **"Register Bot"** again
5. **IMPORTANT:** Copy the generated Bot ID (long string)

### Step 2.3: Configure Bot ID

Edit `src/main/resources/META-INF/microprofile-config.properties`:

```properties
player_key=YOUR_BOT_ID_HERE
```

Replace `YOUR_BOT_ID_HERE` with the ID you copied.

### Step 2.4: Start AI Service

```bash
./gradlew start
```

**Expected Output:**
- AI service starts on port 8083
- Server ready message appears

### Step 2.5: Monitor AI Service Logs

```bash
# Open and watch the log file
tail -f build/wlp/usr/servers/ai-service/logs/messages.log
```

**What to look for:**
- "Attempting to register with game service..."
- "Found party id: XXXX" (4-character code)
- JSON messages coming through (game ticks)

### Step 2.6: Test AI Bot in Game

1. Go to http://localhost:12000
2. Click **"Host Round"**
3. Your AI bot should automatically join!
4. Watch it play (it will go straight and crash initially)

**Verify:**
- ✅ Bot appears in the game
- ✅ Bot moves automatically
- ✅ Bot requeues after game ends
- ✅ Bot stats update

## Testing Phase 3: Hot Code Updates

### Step 3.1: Test Auto-Reload

With AI service still running:

1. Open `liberty-bikes-ai/src/main/java/org/libertybikes/ai/service/StartupProcedure.java`
2. Add a print statement: `System.out.println("Testing hot reload!");`
3. Save the file
4. Check logs - you should see:
   - Application stopped
   - Application restarted
   - Your new print statement

**This proves:** Code changes don't require server restart!

## Testing Phase 4: Docker Environment (Optional)

### Step 4.1: Start with Docker Compose

From `liberty-bikes-ai-starter` root:

```bash
# Build and start all services in containers
./gradlew dockerStart
```

**This starts:**
- All 4 core services
- PostgreSQL database
- Prometheus (metrics)
- Grafana (dashboards)

### Step 4.2: Access Services

- Game UI: http://localhost:12000
- Prometheus: http://localhost:9090
- Grafana: http://localhost:3000 (admin/admin)

### Step 4.3: Stop Docker Services

```bash
./gradlew dockerStop
```

## Testing Phase 5: Database Integration (Optional)

### Step 5.1: Start PostgreSQL

```bash
# From liberty-bikes-ai-starter root
./startDB.sh
```

Or manually with Docker:

```bash
docker run -d \
  --name liberty-bikes-db \
  -p 5432:5432 \
  -e POSTGRES_DB=playerdb \
  -e POSTGRES_USER=lb_user \
  -e POSTGRES_PASSWORD=lb_password \
  postgres:11-alpine
```

### Step 5.2: Verify Database Connection

Check player-service logs for database connection messages.

## Testing Phase 6: Monitoring (Optional)

### Step 6.1: Start Monitoring Services

```bash
./startMonitoring.sh
```

### Step 6.2: Access Grafana Dashboard

1. Open http://localhost:3000
2. Login: admin/admin
3. View Liberty Bikes dashboard
4. Play some games and watch metrics update

**Metrics to observe:**
- Number of players in queue
- Number of active games
- Total logins
- System CPU/memory usage

## Common Issues and Solutions

### Issue: Port Already in Use

```bash
# Find process using port 8080
lsof -i :8080
# Kill the process
kill -9 <PID>
```

### Issue: Gradle Build Fails

```bash
# Clean and rebuild
./gradlew clean build
```

### Issue: AI Bot Not Joining Games

1. Verify bot ID is correct in `microprofile-config.properties`
2. Check AI service logs for errors
3. Ensure core services are running
4. Try re-registering the bot

### Issue: Hot Reload Not Working

1. Ensure your IDE has auto-build enabled
2. Or manually rebuild: `./gradlew war`
3. Check file permissions

## Shutdown Procedures

### Stop All Services

```bash
# From liberty-bikes-ai-starter root
./gradlew stop

# From liberty-bikes-ai directory
cd liberty-bikes-ai
./gradlew stop
```

### Stop Docker Services

```bash
./gradlew dockerStop
```

### Stop Database

```bash
docker stop liberty-bikes-db
docker rm liberty-bikes-db
```

## Testing Checklist

Use this checklist to verify everything works:

- [ ] Core services start successfully
- [ ] Can play manual game with keyboard
- [ ] Can host a game
- [ ] Multiple players can join same game
- [ ] AI bot registers successfully
- [ ] AI bot joins games automatically
- [ ] AI bot requeues after games
- [ ] Hot code reload works
- [ ] Stats persist between games
- [ ] No errors in any service logs
- [ ] Docker deployment works (optional)
- [ ] Database integration works (optional)
- [ ] Monitoring dashboards work (optional)

## Next Steps: Improving Your AI

Once everything is working, improve your AI logic in:
`liberty-bikes-ai/src/main/java/org/libertybikes/ai/AILogic.java`

**Ideas:**
1. Avoid obstacles by looking ahead
2. Calculate safe paths
3. Predict other players' movements
4. Implement pathfinding algorithms (A*, Dijkstra)
5. Add defensive/offensive strategies

## Spec-Driven Development Testing

For your `liberty-bikes-ai-spec-driven` repository:

1. Follow the same steps above
2. Additionally verify:
   - OpenAPI specs are generated correctly
   - API documentation is accessible
   - Contract tests pass
   - Spec-first development workflow works

## Performance Testing

### Load Testing

```bash
# Run multiple AI bots simultaneously
for i in {1..4}; do
  # Register 4 different bots and start them
  # Each needs unique player_key
done
```

### Stress Testing

1. Host a game
2. Have 4 players join
3. Monitor CPU/memory usage
4. Check for memory leaks over multiple games

## Troubleshooting Commands

```bash
# Check Java version
java -version

# Check JAVA_HOME
echo $JAVA_HOME

# List running Java processes
jps -l

# Check port usage
netstat -an | grep LISTEN

# View all Gradle tasks
./gradlew tasks

# Clean everything
./gradlew clean

# Rebuild without cache
./gradlew clean build --no-build-cache
```

## Resources

- Main README: `README.md`
- AI Lab Guide: `liberty-bikes-ai/README.md`
- Docker Compose: `docker-compose.yml`
- Server Configs: `*/src/main/liberty/config/server.xml`

## Success Criteria

Your setup is fully working when:
1. ✅ You can play manual games
2. ✅ Your AI bot joins and plays automatically
3. ✅ Code changes hot-reload without restart
4. ✅ Stats persist across games
5. ✅ No errors in any logs
6. ✅ All services communicate correctly

---

**Happy Testing! 🚴‍♂️🤖**