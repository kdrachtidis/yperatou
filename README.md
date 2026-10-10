# Yperatou - Multiplayer Card Game Android App

A turn-based card game Android application where two players compare card stats to win opponent's cards. Play offline against AI or online with friends in real-time multiplayer sessions.

## 🎮 Game Overview

**Core Mechanic**: Players alternate playing cards. For each card played, a stat is chosen (Attack, Defense, Speed, Magic, etc.). The player with the higher stat value wins and takes the opponent's card.

**Game Modes**:
- **Offline Single Player**: Play against AI opponent
- **Online Multiplayer**: Create a room, share room ID with a friend, and play in real-time

## 📋 Features

- ✅ Offline gameplay with AI opponent
- ✅ Online multiplayer with WebSocket real-time sync
- ✅ Room-based matchmaking (create & join via ID)
- ✅ Card collection management
- ✅ Player profiles and leaderboards
- ✅ Automatic reconnection handling
- ✅ Match history and statistics
- ✅ Card ownership transfer after matches

## 🏗️ Architecture

```
yperatou/
├── android-app/                 # Android Jetpack Compose frontend
│   ├── presentation/
│   │   ├── screens/            # UI screens (GameScreen, RoomScreen, etc.)
│   │   ├── components/         # Reusable UI components
│   │   └── theme/              # App theming
│   ├── domain/
│   │   ├── models/             # Data classes (Card, GameMatch, etc.)
│   │   ├── repository/         # Repository interfaces
│   │   └── usecase/            # Business logic use cases
│   ├── data/
│   │   ├── local/              # Room Database DAOs
│   │   ├── remote/             # Retrofit API & WebSocket clients
│   │   └── repository/         # Repository implementations
│   ├── di/                     # Hilt dependency injection
│   └── AndroidManifest.xml
├── backend/                     # Node.js/Express or Spring Boot
│   ├── src/
│   │   ├── api/                # REST API routes
│   │   ├── websocket/          # WebSocket event handlers
│   │   ├── services/           # GameService, RoomService, etc.
│   │   ├── models/             # Database models/schemas
│   │   ├── middleware/         # Auth, validation, error handling
│   │   └── utils/              # Helper functions
│   ├── tests/                  # Jest unit & integration tests
│   ├── migrations/             # Database migrations
│   └── package.json
├── database/
│   ├── schema/                 # PostgreSQL schemas
│   ├── migrations/             # SQL migration files
│   └── seeds/                  # Test data
├── docker-compose.yml          # Local development environment
├── SKILLS.md                   # Detailed technical documentation
└── README.md                   # This file
```

## 🛠️ Tech Stack

### Frontend (Android)
- **UI Framework**: Jetpack Compose
- **Networking**: Retrofit + OkHttp for REST, Socket.IO for WebSocket
- **Local Storage**: Room Database
- **State Management**: ViewModel + StateFlow
- **Dependency Injection**: Hilt
- **Testing**: JUnit, Mockito, Espresso, Compose Testing

### Backend
- **Framework**: Node.js (Express) or Spring Boot
- **Real-time**: Socket.IO (WebSocket)
- **Database**: PostgreSQL (persistent data)
- **Caching**: Redis (session management)
- **Testing**: Jest, Supertest

### DevOps
- **Containerization**: Docker
- **Orchestration**: Docker Compose (local) / Kubernetes (production)
- **CI/CD**: GitHub Actions (optional)

## 🚀 Quick Start

### Prerequisites
- Android Studio (latest)
- Docker & Docker Compose
- Node.js 18+ or Java 11+
- PostgreSQL 14+

### Local Development Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/kdrachtidis/yperatou.git
   cd yperatou
   ```

2. **Start backend services**
   ```bash
   docker-compose up -d
   ```
   This starts:
   - PostgreSQL database
   - Redis cache
   - Backend API server (port 3000)

3. **Open Android app in Android Studio**
   ```bash
   cd android-app
   # Open in Android Studio
   ```

4. **Configure API endpoint**
   - In `BuildConfig.kt` or `local.properties`:
   ```properties
   API_BASE_URL=http://10.0.2.2:3000/api
   WEBSOCKET_URL=ws://10.0.2.2:3000
   ```

5. **Run the app**
   - Select emulator and press Run

## 📱 Game Flow

### Offline Mode
```
1. Player selects "Play Offline"
2. Player builds deck (5 cards)
3. AI opponent generates deck
4. Game starts
5. Players alternate playing cards each round
6. Stat comparison determines winner
7. Winning player takes opponent's card
8. Continue until one player has no cards
9. Show results and store in local database
```

### Online Mode
```
1. Player A: "Create Room" → Receives room ID (e.g., "ABC123")
2. Player A: Shares room ID to Player B via messaging app
3. Player B: "Join Room" → Enters room ID
4. Backend validates both players present
5. WebSocket connection established for both
6. Match starts with real-time move sync
7. Each move: Play card → Choose stat → Opponent sees move
8. Round resolves instantly
9. Winner takes loser's card
10. After match: Cards transferred, stats updated
```

## 🧪 Testing

See [SKILLS.md](./SKILLS.md) for comprehensive testing strategies and examples.

### Run Tests

**Android Unit Tests**
```bash
cd android-app
./gradlew test
```

**Android UI Tests**
```bash
cd android-app
./gradlew connectedAndroidTest
```

**Backend Unit Tests**
```bash
cd backend
npm test
```

**Backend Integration Tests**
```bash
cd backend
npm run test:integration
```

## 📚 Core Data Models

### User
```kotlin
data class User(
    val id: String,
    val username: String,
    val email: String,
    val elo: Int = 1000,
    val cardsOwned: List<String>,
    val matchesPlayed: Int = 0,
    val wins: Int = 0
)
```

### Card
```kotlin
data class Card(
    val id: String,
    val name: String,
    val stats: Map<String, Int>, // e.g., {"attack": 8, "defense": 5, "speed": 6}
    val rarity: String,           // Common, Rare, Epic, Legendary
    val imageUrl: String
)
```

### GameRoom
```kotlin
data class GameRoom(
    val id: String,
    val hostId: String,
    val guestId: String?,
    val status: RoomStatus,     // WAITING, ACTIVE, COMPLETED
    val createdAt: Long,
    val expiresAt: Long
)
```

### GameMatch
```kotlin
data class GameMatch(
    val id: String,
    val roomId: String,
    val player1Id: String,
    val player2Id: String,
    val currentRound: Int,
    val player1Cards: List<String>,
    val player2Cards: List<String>,
    val player1Score: Int,
    val player2Score: Int,
    val gameState: GameState  // WAITING_FOR_MOVE, MOVE_MADE, ROUND_COMPLETE, GAME_OVER
)
```

## 🔌 API Endpoints

### Authentication
```
POST   /api/auth/register        # Register new user
POST   /api/auth/login           # Login user
POST   /api/auth/refresh         # Refresh JWT token
```

### Cards
```
GET    /api/cards                # Get user's cards
GET    /api/cards/:id            # Get card details
GET    /api/cards/search         # Search cards by name/stats
```

### Rooms
```
POST   /api/rooms                # Create new game room
GET    /api/rooms/:id            # Get room details
POST   /api/rooms/:id/join       # Join existing room
GET    /api/rooms/:id/status     # Check room status
```

### Matches
```
POST   /api/matches/:id/move     # Submit card play move
GET    /api/matches/:id/state    # Get current game state
GET    /api/matches/:id/history  # Get move history
POST   /api/matches/:id/forfeit  # Forfeit match
GET    /api/matches/:id/result   # Get final result
```

### User
```
GET    /api/user/profile         # Get user profile
PUT    /api/user/profile         # Update user profile
GET    /api/user/stats           # Get user statistics
GET    /api/leaderboard          # Get top players (paginated)
```

## 🔌 WebSocket Events

### Client → Server
```javascript
// Join/Leave
socket.emit('room:join', { roomId, playerId })
socket.emit('room:leave', { roomId, playerId })

// Gameplay
socket.emit('game:play-card', { matchId, cardId, stat })
socket.emit('game:forfeit', { matchId, playerId })
socket.emit('game:resume', { matchId, playerId })
```

### Server → Clients
```javascript
// Room Events
socket.on('room:player-joined', { playerId, username, playerCount })
socket.on('room:match-started', { matchId, opponent })
socket.on('room:match-complete', { winnerId, loser, result })

// Game Events
socket.on('game:move-received', { playerId, cardId, stat })
socket.on('game:round-result', { winner, loser, player1Score, player2Score })
socket.on('game:opponent-disconnected', {})
socket.on('game:opponent-reconnected', {})
socket.on('game:match-ended', { winnerId, loser, cardsTransferred })
socket.on('game:timeout', { playerId })  // 30s inactivity
```

## 🔐 Security

- **Authentication**: JWT tokens with refresh mechanism
- **Validation**: All moves validated server-side before applying
- **Rate Limiting**: API endpoints protected against abuse
- **Card Ownership**: Server verifies player owns card before allowing play
- **Encryption**: HTTPS for API, WSS for WebSocket (production)
- **Input Sanitization**: All user inputs validated and sanitized

## 🐛 Troubleshooting

### Backend not connecting
```bash
# Check if services are running
docker-compose ps

# View logs
docker-compose logs backend
docker-compose logs postgres
```

### Android emulator can't reach backend
```
# Use 10.0.2.2 instead of localhost for emulator
API_BASE_URL=http://10.0.2.2:3000/api
```

### WebSocket connection drops
- Automatic reconnection with exponential backoff enabled
- Grace period of 30 seconds for rejoining match
- Check network connectivity and firewall rules

## 📖 Documentation

- **[SKILLS.md](./SKILLS.md)**: Comprehensive technical documentation
  - Detailed architecture explanation
  - Testing strategies with code examples
  - Database schema and migrations
  - Deployment guide

## 🤝 Contributing

1. Create a feature branch: `git checkout -b feature/my-feature`
2. Commit changes: `git commit -am 'Add my feature'`
3. Push to branch: `git push origin feature/my-feature`
4. Open a Pull Request

## 📝 Project Structure Details

Each component has its own README:
- `android-app/README.md` - Android-specific setup and build instructions
- `backend/README.md` - Backend API documentation and deployment
- `database/README.md` - Database schema, migrations, and setup

## 🎯 Roadmap

- [ ] Phase 1: Core offline gameplay
- [ ] Phase 2: Online multiplayer (WebSocket)
- [ ] Phase 3: Leaderboard & rankings
- [ ] Phase 4: Card trading system
- [ ] Phase 5: Tournament mode
- [ ] Phase 6: Mobile payments (in-app purchases)
- [ ] Phase 7: Cross-platform web version

## 📄 License

This project is licensed under the MIT License - see LICENSE file for details.

## 👤 Author

Created by [@kdrachtidis](https://github.com/kdrachtidis)

## 💬 Questions or Issues?

Open an issue on GitHub: [Issues](https://github.com/kdrachtidis/yperatou/issues)

---

**Happy gaming! 🎴**
