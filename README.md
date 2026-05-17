# Net Cat 🐱

A lightweight, terminal-based TCP chat server written in Go. Supports multiple concurrent users, real-time broadcasting, persistent message history, and graceful shutdown.

## Features

- **Multi-user chat** — up to 10 simultaneous connections
- **Real-time broadcasting** — messages are instantly relayed to all connected users
- **Message history** — new users receive the full chat history upon joining
- **Username validation** — enforces alphanumeric names between 3–13 characters
- **Timestamped messages** — every message is prefixed with `[YYYY-MM-DD HH:MM:SS][username]:`
- **Colored join/leave notices** — green for joins, red for departures
- **Persistent logging** — all server events are written to `logs/server.log`
- **Graceful shutdown** — `Ctrl+C` notifies all clients and closes connections cleanly

## Requirements

- Go 1.22.3 or higher

## Getting Started

### Build

```bash
go build -o TCPChat .
```

### Run

```bash
# Default port (8989)
./TCPChat

# Custom port
./TCPChat 2525
```

### Connect (as a client)

Use any TCP client such as `nc` (netcat):

```bash
nc <server-ip> <port>
```

Example:

```bash
nc localhost 8989
```

You will be greeted with a welcome banner and prompted to enter your username.

## Usage

```
[USAGE]: ./TCPChat $port
```

| Argument | Description              | Default |
|----------|--------------------------|---------|
| `$port`  | Port to listen on        | `8989`  |

## Username Rules

- Minimum **3 characters**, maximum **13 characters**
- Only **letters, digits, underscores (`_`), and hyphens (`-`)** are allowed
- Must be **unique** — duplicate names are rejected

## Message Rules

- Only **printable ASCII characters** (codes 32–126) are accepted
- Empty messages are silently ignored

## Project Structure

```
.
├── main.go
├── go.mod
├── internal/
│   ├── chat/
│   │   ├── server.go      # Core server logic (connections, broadcast, lifecycle)
│   │   └── types.go       # Server, Users, and BroadcastDetails types
│   ├── helpers/
│   │   └── helper.go      # Argument parsing, name/message validation, prefix formatting
│   └── logger/
│       └── logger.go      # File-based logger setup
└── logs/
    └── server.log         # Auto-created at runtime
```

## How It Works

1. The server starts a TCP listener on the specified port.
2. Each new connection is handled in its own goroutine.
3. On connect, the user receives a welcome banner and message history, then is prompted for a username.
4. Validated messages are sent to a `Broadcast` channel, which fans them out to all other connected users.
5. When a user disconnects or the server shuts down, their connection is cleaned up and others are notified.

## Logging

Server events (connections, joins, leaves, errors, shutdown) are appended to `logs/server.log`, created automatically on first run.

## Stopping the Server

Press `Ctrl+C`. The server will notify all connected clients and close gracefully.
