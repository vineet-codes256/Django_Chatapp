# Django_Chatapp 💬

A real-time chat application built with Django Channels and Redis to demonstrate WebSocket-based communication in Django. This educational project showcases how to implement live, bidirectional messaging using modern Python web technologies.

🔗 **Live Demo**: https://vineet-rawat-chat-with-friends.herokuapp.com/

## 🎯 Project Purpose

This is an **educational project** designed to demonstrate:
- Real-time communication with Django Channels
- WebSocket implementation in Python
- Async/await patterns in Django
- Redis as a message broker
- Group-based message broadcasting

## ✨ Features

- 💬 **Real-time Messaging** - Instant message delivery using WebSockets
- 🏠 **Multiple Chat Rooms** - Create and join different chat rooms
- 📝 **Message Persistence** - Chat history saved to database
- 👥 **Multi-user Support** - Multiple users can chat simultaneously
- 🔄 **Message Broadcasting** - Messages broadcast to all users in a room
- 📱 **Responsive Design** - Clean, functional interface

## ����️ Technology Stack

- **Backend**: Django 4.0.3, Django Channels
- **Database**: SQLite (development)
- **Cache/Message Broker**: Redis
- **Frontend**: HTML, CSS, JavaScript (Vanilla)
- **Deployment**: Heroku + Whitenoise
- **WebSocket**: AsyncWebsocketConsumer

## 🏗️ Architecture

```
Browser → WebSocket → Django Channels → Redis → All Connected Clients
               ↓
            SQLite (Message Storage)
```

### Key Components

1. **ASGI Application** (`chatapp/asgi.py`) - Handles both HTTP and WebSocket protocols
2. **WebSocket Consumer** (`chat/consumers.py`) - Manages WebSocket connections and message flow
3. **Channel Layers** - Redis-based message broker for real-time broadcasting
4. **Message Model** - Stores chat history in SQLite database

## 📋 Prerequisites

- Python 3.x
- Redis server
- pip (Python package manager)

## 🚀 Installation & Setup

### 1. Clone the repository
```bash
git clone https://github.com/vineet-codes256/Django_Chatapp.git
cd Django_Chatapp
```

### 2. Create virtual environment
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Start Redis server
```bash
# On macOS/Linux
redis-server

# On Windows (if installed via WSL)
sudo service redis-server start

# Or using Docker
docker run -p 6379:6379 -d redis
```

### 5. Run migrations
```bash
python manage.py migrate
```

### 6. Start the development server
```bash
python manage.py runserver
```

### 7. Access the application
Open your browser and navigate to `http://127.0.0.1:8000/`

## 📖 How It Works

### Message Flow

1. User enters a room name on the homepage
2. WebSocket connection established to `ws://<host>/ws/<room_name>/`
3. User joins the room group via Channel Layer
4. Past 25 messages loaded from database
5. New messages sent via WebSocket → saved to DB → broadcast to room group
6. All connected clients in the room receive the message instantly

### WebSocket Consumer Lifecycle

```python
connect()      # User joins room, added to group
receive()      # Process incoming messages
chat_message() # Handle broadcasts from group
disconnect()   # User leaves, removed from group
```

## 📁 Project Structure

```
Django_Chatapp/
├── chat/                      # Main chat application
│   ├── consumers.py          # WebSocket consumer logic
│   ├── routing.py            # WebSocket URL routing
│   ├── views.py              # HTTP views
│   ├── models.py             # Message model
│   ├── urls.py               # URL patterns
│   └── templates/            # HTML templates
│
├── chatapp/                   # Django project settings
│   ├── settings.py           # Configuration
│   ├── asgi.py               # ASGI config for Channels
│   └── urls.py               # Root URLs
│
├── manage.py                 # Django management script
├── requirements.txt          # Python dependencies
├── Procfile                  # Heroku deployment config
└── README.md
```

## 🎓 Key Learning Concepts

### 1. Django Channels Setup
```python
# asgi.py - Protocol routing
application = ProtocolTypeRouter({
    "http": get_asgi_application(),
    "websocket": AuthMiddlewareStack(
        URLRouter(chat.routing.websocket_urlpatterns)
    )
})
```

### 2. Async WebSocket Consumer
```python
# Mixing sync and async code
async def receive(self, text_data):
    # Async WebSocket handling
    await self.save_message(username, room, message)

@sync_to_async
def save_message(self, username, room, message):
    # Sync Django ORM operation
    Message.objects.create(...)
```

### 3. Channel Layers with Redis
```python
CHANNEL_LAYERS = {
    'default': {
        'BACKEND': 'channels_redis.core.RedisChannelLayer',
        'CONFIG': {
            'hosts': [('127.0.0.1', 6379)],
        }
    }
}
```

### 4. Group-based Broadcasting
```python
# Add user to room group
await self.channel_layer.group_add(self.room_group_name, self.channel_name)

# Broadcast to all users in room
await self.channel_layer.group_send(
    self.room_group_name,
    {'type': 'chat_message', 'message': message, 'username': username}
)
```

## 🔧 Configuration

### Redis Configuration
Default: `localhost:6379`

To change Redis host, edit `chatapp/settings.py`:
```python
CHANNEL_LAYERS = {
    'default': {
        'BACKEND': 'channels_redis.core.RedisChannelLayer',
        'CONFIG': {
            'hosts': [('your-redis-host', 6379)],
        }
    }
}
```

### Database
Default: SQLite (`db.sqlite3`)

For production, consider PostgreSQL:
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'your_db_name',
        # ... other settings
    }
}
```

## 🚀 Deployment

The app is configured for Heroku deployment:

1. **Install Heroku CLI**
2. **Create Heroku app**
   ```bash
   heroku create your-app-name
   ```

3. **Add Redis addon**
   ```bash
   heroku addons:create heroku-redis:hobby-dev
   ```

4. **Deploy**
   ```bash
   git push heroku main
   ```

5. **Run migrations**
   ```bash
   heroku run python manage.py migrate
   ```

## 📚 Topics Covered

This project demonstrates:
- ✅ Django Channels fundamentals
- ✅ WebSocket protocol implementation
- ✅ Async/await in Python
- ✅ Redis pub/sub pattern
- ✅ Channel layers and groups
- ✅ Real-time broadcasting
- ✅ Message persistence
- ✅ ASGI vs WSGI

## 🎯 Potential Enhancements

### Beginner Level
- Add Django authentication system
- Implement input validation
- Add message timestamps to UI
- Create room list page

### Intermediate Level
- Private one-on-one messaging
- User presence indicators (online/offline)
- Typing indicators
- Message edit/delete functionality
- PostgreSQL database

### Advanced Level
- File/image sharing
- Message encryption
- Rate limiting
- Read receipts
- Kubernetes deployment
- Mobile app integration

## ⚠️ Educational Note

This project prioritizes **learning and clarity** over production-ready features. Some simplifications made for educational purposes:

- No authentication system (focus on Channels, not auth)
- Simple username entry (demonstrates core concepts)
- SQLite database (easy setup)
- Hardcoded configurations (production apps should use environment variables)

## 🤝 Contributing

This is an educational project, but suggestions and improvements are welcome! Feel free to:
- Open issues for bugs or questions
- Submit pull requests for enhancements
- Fork and experiment with your own features

## 📝 License

This project is open source and available for educational purposes.

## 📧 Contact

**Vineet Rawat**
- GitHub: [@vineet-codes256](https://github.com/vineet-codes256)

## 🙏 Acknowledgments

- Django Channels documentation
- Django community
- Redis community

---

⭐ If this project helped you learn Django Channels, consider giving it a star!

**Happy Learning! 🚀**