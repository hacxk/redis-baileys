# Redis-Baileys

<p align="center">
  <img src="https://img.shields.io/github/license/hacxk/redis-baileys" alt="License">
  <img src="https://img.shields.io/npm/v/redis-baileys" alt="npm version">
  <img src="https://img.shields.io/github/stars/hacxk/redis-baileys" alt="GitHub stars">
  <img src="https://img.shields.io/github/issues/hacxk/redis-baileys" alt="GitHub issues">
</p>

<p align="center">
  Save your Baileys session in Redis DB using this powerful and efficient package!
</p>

## 🌟 Features

- 🚀 Fast and efficient storage of Baileys sessions
- 🔒 Secure data management with Redis
- 🔄 Easy integration with existing Baileys projects
- 📦 TypeScript support
- 🛠 Simple API for session management

## 📦 Installation

```bash
npm install redis-baileys
```

or

```bash
yarn add redis-baileys
```

## 🚀 Usage

### TypeScript

```typescript
import { useRedisAuthState } from 'redis-baileys';
import { Boom } from '@hapi/boom';
import makeWASocket, { DisconnectReason } from '@whiskeysockets/baileys';

async function connectToWhatsApp() {
    const redisConfig = {
        password: 'your_redis_password',
        host: 'your_redis_host',
        port: 6379,
    };

    const { state, saveCreds } = await useRedisAuthState(redisConfig, 'your_session_id');

    const sock = makeWASocket({
        auth: state,
        printQRInTerminal: true,
    });

    sock.ev.on('connection.update', (update) => {
        const { connection, lastDisconnect } = update;
        if (connection === 'close') {
            const shouldReconnect = (lastDisconnect?.error as Boom)?.output?.statusCode !== DisconnectReason.loggedOut;
            console.log('connection closed due to ', lastDisconnect?.error, ', reconnecting ', shouldReconnect);
            if (shouldReconnect) {
                connectToWhatsApp();
            }
        } else if (connection === 'open') {
            console.log('opened connection');
        }
    });

    sock.ev.on('creds.update', saveCreds);
}

connectToWhatsApp();
```

### JavaScript

```javascript
const { useRedisAuthState } = require('redis-baileys');
const { Boom } = require('@hapi/boom');
const makeWASocket = require('@whiskeysockets/baileys').default;

async function connectToWhatsApp() {
    const redisConfig = {
        password: 'your_redis_password',
        host: 'your_redis_host',
        port: 6379,
    };

    const { state, saveCreds } = await useRedisAuthState(redisConfig, 'your_session_id');

    const sock = makeWASocket({
        auth: state,
        printQRInTerminal: true,
    });

    sock.ev.on('connection.update', (update) => {
        const { connection, lastDisconnect } = update;
        if (connection === 'close') {
            const shouldReconnect = (lastDisconnect?.error instanceof Boom) && lastDisconnect.error.output.statusCode !== DisconnectReason.loggedOut;
            console.log('connection closed due to ', lastDisconnect?.error, ', reconnecting ', shouldReconnect);
            if (shouldReconnect) {
                connectToWhatsApp();
            }
        } else if (connection === 'open') {
            console.log('opened connection');
        }
    });

    sock.ev.on('creds.update', saveCreds);
}

connectToWhatsApp();
```

## 🛠 API

### `useRedisAuthState(redisConfig, sessionId)`

Creates and manages the authentication state using Redis.

- `redisConfig`: An object containing Redis connection details (password, host, port).
- `sessionId`: A unique identifier for the session.

Returns an object with:
- `state`: The current authentication state.
- `saveCreds`: A function to save credentials.
- `deleteSession`: A function to delete the current session.

## 📚 Documentation

For more detailed documentation, please visit our [Wiki](https://github.com/hacxk/redis-baileys/wiki).

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page](https://github.com/hacxk/redis-baileys/issues).

## 🙏 Acknowledgements

- [Baileys](https://github.com/whiskeysockets/baileys) - The awesome WhatsApp Web API library
- [Redis](https://redis.io/) - The open-source, in-memory data store used by millions of developers

---

<p align="center">
  Made with ❤️ by <a href="https://github.com/hacxk">HacxK</a>
</p>