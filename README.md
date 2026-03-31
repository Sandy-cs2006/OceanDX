# OceanDX

Source code:

```
Please build OceanDX, a multipurpose, high-performance game server for SEGA ALL.Net arcade games (including CHUNITHM, maimai DX, O.N.G.E.K.I., Card Maker, etc.). The server must handle card authentication via the AimeDB protocol, title server requests via the AllNet protocol, and game-specific API endpoints (usually JSON over HTTP).

**Technology Stack**

Language: Rust (Latest Stable) – leveraging memory safety, zero-cost abstractions, and high concurrency.
Web Framework: Axum (built on Tokio, Tower, and Hyper) for high-performance modular APIs.
Async Runtime: Tokio – the industry standard for asynchronous I/O and task scheduling.
Database: PostgreSQL or MariaDB utilizing SQLx for asynchronous, compile-time verified SQL queries.
Networking: Custom TCP server using Tokio and `tokio-util` codecs for low-level protocol handling (AimeDB).
Build System: Cargo with a Workspace-based multi-crate architecture (e.g., `ocean-core`, `ocean-games`, `ocean-net`).
Serialization: Serde with specialized support for JSON, MessagePack, and custom binary protocols.
Authentication: JWT (via `jsonwebtoken`) for web APIs and Keychip-backed secure sessions for game clients.

**Core Components to Implement**

1. AimeDB Protocol Handler (TCP Port 22345)

Implement a Tokio-based TCP server to handle card lookup and registration.
Support proprietary AimeDB encryption/decryption using custom codecs.
Handle requests: lookup, register, login, campaign, and hello.
Map 20-digit access codes (FELICA/Aime) to internal Card entities.

2. AllNet Title Server (HTTP)

Implement Axum routes to handle `/sys/servlet/PowerOn` and `/sys/servlet/DownloadOrder`.
PowerOn: Resolve game IDs (e.g., SDEZ for Maimai DX, SDHD for Chusan) to their respective API endpoints.
Implement Keychip authentication middleware to validate cabinet serial numbers.
Handle version-specific responses (V2 vs V3 formats) based on the `format_ver` parameter.

3. Game-Specific API Handlers (JSON over HTTP)

Create a modular system for game protocols using Rust traits or a plugin-based architecture.
Use macros or Axum sub-routers to define game-specific endpoints with minimal boilerplate.
Implement common game logic:
User Data: Manage profiles, options, and extended metadata.
Items: Handle collection management (icons, plates, characters) using kind-based mapping.
Playlogs: Efficiently store high-volume performance data for every song played.
Events: Provide dynamic lists for active game events and global rankings.
Support Zlib/Gzip compression and proprietary encryption as required by game versions.

4. Shared Data Architecture

Models: Implement `User` (web), `Card` (physical), and game-specific data structures using Serde.
Static Assets: Develop a robust system to load game metadata (music, events) from external JSON/YAML files.
Migrations: Use SQLx migrations to track and version the database schema.

5. OceanNet Web API (v2)

Implement a RESTful API for the web frontend (`/api/v2/*`).
Features: JWT-based user management, card linking, profile editing, and global leaderboards.
Include administrative interfaces for managing cards, keychips, and server settings.

**Key Technical Requirements**

* Internationalization: Support both JP and International (Intl/Exp/China) game versions via Game ID routing.
* Performance: Optimize database queries for "large JSON batch" requests to ensure minimal boot-up latency.
* Containerization: Provide a lightweight Dockerfile (based on Alpine or Distroless) and a `docker-compose.yml`.
* Legacy Support: Maintain compatibility with older game versions and "ghost card" emulation.

**Initial Task**

* Initialize a Cargo workspace with `core`, `games`, and `net` crates.
* Define core `User` and `Card` models with SQLx and Serde.
* Scaffold the AllNet controller using Axum and implement basic `PowerOn` logic.
* Implement the AimeDB server using `tokio::net::TcpListener` and a custom `Codec`.
```