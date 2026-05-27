# Liberty Bikes AI - Starter Project

Welcome to the Liberty Bikes AI Starter Project! This is a spec-driven development framework where you can describe bot behavior in natural language, and an AI agent (Bob) generates the corresponding implementation.

## 🚀 Quick Start

### Prerequisites

- Java 8 or higher
- Docker and Docker Compose (for running the full Liberty Bikes game)
- Bob AI assistant (for spec-driven development)

### Step 1: Start the Liberty Bikes Core Services

The AI bot needs the core Liberty Bikes services to be running. Open a terminal and start the services:

```bash
cd /path/to/liberty-bikes-ai-starter
docker-compose up
```

Wait until you see messages indicating all services are running. The game UI will be available at http://localhost:12000

### Step 2: Register Your Bot

1. Open http://localhost:12000 in your browser
2. Click on "Register Bot" or navigate to the bot registration page
3. Copy the generated player key
4. Paste the key into `liberty-bikes-ai/src/main/resources/META-INF/microprofile-config.properties`:
   ```properties
   player.key=YOUR_PLAYER_KEY_HERE
   ```

### Step 3: Start Your AI Bot

In a new terminal, start the AI bot service:

```bash
cd liberty-bikes-ai
./gradlew libertyRun
```

Your bot should now connect to the game server and be ready to play!

### Step 4: Use Bob to Generate Bot Logic

Now comes the fun part - describing how you want your bot to behave!

1. Open Bob in AI Bot Builder mode:
   ```bash
   bob --mode ai-bot-builder
   ```

2. Describe your bot's behavior in natural language:
   ```
   "Create a bot that looks ahead in all directions and chooses 
   the path with the most open space. It should look at least 
   10 spaces ahead and turn early if space gets tight."
   ```

3. Bob will generate the code and inject it into `AILogic.java`

4. The Liberty server will automatically hot-reload the changes

5. Test your bot by hosting or joining a game at http://localhost:12000

### Step 5: Iterate and Refine

Watch your bot play and provide feedback to Bob:

```
"The bot is too cautious. Make it look 20 spaces ahead instead 
of 10, and be more willing to take risks in tight spaces."
```

Bob will refine the implementation based on your feedback. Repeat until you're satisfied!

## 📁 Project Structure

```
liberty-bikes-ai-starter/
├── auth-service/              # Authentication service
├── frontend/                  # Game UI
├── game-service/             # Core game logic
├── player-service/           # Player management
├── liberty-bikes-ai/         # 🤖 YOUR AI BOT (main focus)
│   ├── src/main/java/org/libertybikes/ai/
│   │   ├── AILogic.java      # ⭐ Main AI logic (spec-driven)
│   │   ├── model/            # Game data models
│   │   ├── restclient/       # REST API client
│   │   └── service/          # WebSocket and services
│   ├── build.gradle          # Build configuration
│   └── src/main/resources/   # Configuration files
├── docker-compose.yml        # Docker services
└── README-AI-STARTER.md      # This file
```

## 🎮 How It Works

### The AILogic Template

The `AILogic.java` file contains:

1. **Core Fields** (preserved):
   - `gameBoard`: 120x120 2D array representing the game state
   - `currentX`, `currentY`: Your bot's current position
   - `currentDirection`: Your bot's current direction
   - `myPlayerId`: Your unique player identifier

2. **Generated Code Section** (between markers):
   ```java
   // ===== GENERATED CODE SECTION START =====
   public DIRECTION processAiMove(GameTick gameTick) {
       // Your AI logic goes here
   }
   // ===== GENERATED CODE SECTION END =====
   ```
   This is where Bob generates code based on your specifications.

3. **Core Utility Methods** (preserved):
   - `updateBoard()`: Updates the internal game state
   - `fill()`: Fills obstacles on the game board
   - `printBoard()`: Prints the board for debugging

### Game Mechanics

- **Board Size**: 120x120 grid
- **Bot Size**: 3x3 squares
- **Game Tick**: Every 50ms, your `processAiMove()` method is called
- **Objective**: Survive the longest by avoiding collisions
- **Obstacles**: Walls, other players, player trails, moving obstacles

### Available Information

Each game tick, you receive a `GameTick` object containing:
- `obstacles`: List of fixed obstacles (walls)
- `movingObstacles`: List of moving obstacles
- `players`: List of all players (including yourself)

## 📝 Specification Examples

See [SPEC_EXAMPLES.md](SPEC_EXAMPLES.md) for detailed examples of bot specifications.

### Basic Example
```
"Create a bot that looks in three directions (forward, left, right) 
and always chooses the direction with the most open space."
```

### Advanced Example
```
"Make an aggressive bot that:
- Looks 15 spaces ahead in each direction
- Weights open space heavily (70%)
- Considers opponent proximity (20%)
- Adds randomness for unpredictability (10%)
- Turns early when space gets tight (< 10 spaces)
- Tries to position itself to cut off opponents"
```

### Refinement Example
```
"The bot is too cautious. Make it take more risks by:
- Reducing the look-ahead distance from 15 to 10 spaces
- Increasing the aggression factor
- Being willing to go into tighter spaces if it can trap an opponent"
```

## 🔧 Manual Development (Optional)

If you prefer to write code manually instead of using Bob:

1. Edit `liberty-bikes-ai/src/main/java/org/libertybikes/ai/AILogic.java`
2. Implement your logic in the `processAiMove()` method
3. The Liberty server will automatically hot-reload your changes
4. Test in the game at http://localhost:12000

**Note**: If you manually edit the generated code section, it will be overwritten the next time you use Bob.

## 🐛 Troubleshooting

### Bot Not Connecting
- Ensure all services are running (`docker-compose up`)
- Check that you've registered the bot and added the player key
- Verify the AI service is running (`./gradlew libertyRun`)

### Compilation Errors
- Check the Liberty server logs for error messages
- Ensure generated code is syntactically correct
- Try regenerating with Bob using clearer specifications

### Bot Crashes During Game
- Check for null pointer exceptions in the logs
- Ensure boundary checks are in place (0 <= x,y < 120)
- Verify the bot handles edge cases properly

### Hot Reload Not Working
- Restart the Liberty server: `./gradlew libertyStop libertyRun`
- Check that the file was saved correctly
- Verify no compilation errors in the logs

## 📚 Additional Resources

- [Liberty Bikes GitHub](https://github.com/OpenLiberty/liberty-bikes)
- [Spec Examples](SPEC_EXAMPLES.md)
- [Implementation Summary](liberty-bikes-ai-implementation-summary.md)

## 🎯 Tips for Success

1. **Start Simple**: Begin with a basic strategy and iterate
2. **Test Frequently**: Watch your bot play after each change
3. **Be Specific**: Clear specifications lead to better code generation
4. **Iterate**: Don't expect perfection on the first try
5. **Learn from Failures**: Analyze why your bot crashed or got trapped
6. **Experiment**: Try different strategies and see what works best

## 🤝 Contributing

This is a starter project for learning and experimentation. Feel free to:
- Modify the template structure
- Add new helper methods
- Experiment with different AI strategies
- Share your successful bot specifications

## 📄 License

This project follows the same license as the original Liberty Bikes project.

---

**Happy Bot Building! 🤖🏍️**

For questions or issues, refer to the main Liberty Bikes documentation or the spec-driven development plan.