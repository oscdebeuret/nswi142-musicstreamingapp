# Music Streaming System

## Features overview

| # | Feature | Core |
|---|---------|------|
| 1 | User authentication & account management | x |
| 2 | Audio streaming | x |
| 3 | Song uploading (artist application) | x |
| 4 | Catalogue search & discovery | x |
| 5 | Payment processing & premium access | x |
| 6 | Playlist management | x |
| 7 | Group listening session | x |
| 8 | Offline downloading (premium) | x |

## Core features and responsibilities

### Feature: User Authentication & Account Management

As a user, I want to create an account, log in, and manage my profile so that I can securely access the platform and personalize my information.

#### Feature breakdown

1. The user opens the login page and enters their email address or selects a third-party SSO provider.
2. The system checks whether an account is associated with this identity.
3. If an account exists, the user enters their password or completes the SSO challenge.
4. If no account exists, the system offers the user the option to create one, choosing between a listener or an artist role.
5. The user inputs personal details (name, username, email, password, optional phone/picture) and grants GDPR/privacy consent.
6. The user verifies their email address or phone number via a received code or link.
7. Once verified, the system provisions the account resources, stores data, and issues an active session.
8. The user can access, export, or update profile settings, change their password, or trigger a complete account deletion.

#### Responsibilities

##### Account registration & provisioning responsibilities
* Collect and sanitize user details (name, username, email, password, picture)
* Distinguish and provision specific roles (listener, artist, admin) during registration
* Validate input formatting for email addresses and phone numbers
* Enforce password strength rules and policy checks against compromised password databases
* Initialize default user preferences and library metadata in the primary database
* Handle account state transitions (pending, active, suspended, deleted)

##### Authentication & session management responsibilities
* Authenticate credentials against secure password hashes
* Integrate third-party SSO providers (Apple, Google)
* Issue, sign, and manage secure JWT access tokens and refresh tokens
* Manage secure, HTTP-only session cookies for web clients
* Track active user sessions across multiple client devices
* Provide cross-device token revocation and session invalidation mechanisms

##### Verification & notification responsibilities
* Generate cryptographically secure, time-limited verification tokens
* Integrate with external email and SMS gateways to transmit OTPs (One-Time Passwords)
* Validate incoming verification codes against pending account registrations
* Handle rate-limiting for verification requests to prevent abuse
* Trigger localized welcome and account state notifications

##### Data privacy & compliance responsibilities
* Hash and salt passwords using strong algorithms
* Record, version, and store user consent for personal data processing
* Provide automated data export mechanisms
* Implement soft-delete and hard-delete workflows for user accounts
* Anonymize user telemetry data upon account deletion

##### Profile management & auditing responsibilities
* Expose secure APIs for users to update personal information and avatars
* Validate and process image uploads for profile pictures
* Audit log critical account actions (password changes, failed logins, role changes)
* Detect and block suspicious login attempts (geo-velocity checks, brute-force mitigation)

---

### Feature: Audio Streaming

As a listener, I want to stream audio tracks on demand so that I can listen to music instantly without storing it locally.

#### Feature breakdown

1. The listener selects a track to play.
2. The application requests the audio stream manifest from the media server.
3. The system validates the listener's subscription tier, active session, and region limits.
4. The media server resolves the optimal CDN edge node and generates secure access URLs.
5. The application buffers the audio chunks, selecting the bitrate based on network conditions.
6. The system decodes and plays the audio while maintaining a pre-fetch buffer.
7. The system records playback telemetry for statistics, royalty calculations, and ad-insertion triggers.

#### Responsibilities

##### Playback authorization responsibilities
* Verify that the requesting user has a valid and active session token
* Retrieve and evaluate the user's current subscription tier rules (skip limits, ad-free)
* Enforce geographical licensing availability for the requested track
* Generate secure, short-lived, signed URLs for CDN asset retrieval
* Block concurrent playback across multiple devices for a single standard account

##### Audio delivery & infrastructure responsibilities
* Retrieve audio files from distributed object storage or Content Delivery Networks (CDNs)
* Deliver audio via Adaptive Bitrate Streaming (ABR) protocols (HLS/DASH)
* Provide multiple codec options (AAC (Advanced Audio Coding), Ogg Opus, FLAC (Free Lossless Audio Codec) for Hi-Fi)
* Dynamically adjust served bitrates based on real-time client bandwidth constraints
* Manage edge caching policies to minimize origin server load and reduce latency

##### Client playback & state responsibilities
* Buffer incoming audio chunks to guarantee uninterrupted playback
* Decode audio stream formats on client hardware/OS
* Provide seamless playback controls (play, pause, seek, skip, crossfade)
* Recover gracefully from network state transitions (Wi-Fi to Cellular)
* Manage the local playback queue and auto-play transitions (gapless playback)

##### Advertisement & monetization responsibilities (Free Tier)
* Track the number of tracks played or time elapsed to trigger ad breaks
* Resolve personalized audio and display advertisements from the ad exchange
* Stitch audio ads into the playback queue seamlessly
* Block playback controls (skip/seek) during advertisement playback

##### Playback telemetry & royalty logging responsibilities
* Record precise playback events (user ID, track ID, context, timestamp, duration)
* Detect when audio playback reaches the monetization threshold (30 seconds)
* Forward verified streaming logs to the royalty calculation data pipeline
* Detect and filter out fraudulent streams, bot activity, and artificial inflation
* Synchronize play counts with the public catalogue database

---

### Feature: Song Uploading (Artist Portal)

As an artist, I want to upload audio files and track metadata so that my music becomes available for public streaming.

#### Feature breakdown

1. The artist selects an audio file and cover art in the artist portal.
2. The artist inputs detailed metadata (title, genre, contributors, ISRC codes).
3. The system validates the file format, size limits, and checks for corruption.
4. The system queues the upload for asynchronous media processing.
5. The system transcodes the audio into optimized streaming formats and normalizes loudness.
6. The system stores the processed assets in distributed storage and updates the catalogue.
7. The system notifies the artist of the successful publication and triggers release radars.

#### Responsibilities

##### Upload intake & validation responsibilities
* Verify the user holds verified artist or label privileges
* Accept multipart uploads for lossless audio files (WAV, FLAC, ALAC)
* Support resumable uploads for large files over unstable connections
* Validate audio file integrity, bit-depth, and sample rate requirements
* Validate cover art image dimensions, aspect ratios, and color profiles
* Detect malicious payloads or corrupted file headers

##### Metadata & rights responsibilities
* Collect standard track metadata (title, genre, release date, explicit lyrics flag)
* Collect industry-standard identifiers (ISRC for tracks, UPC for albums)
* Manage contributor credits (producers, writers, featured artists)
* Validate copyright ownership assertions and handle digital fingerprinting matches
* Link the track to the correct artist profile and album grouping

##### Media processing & transcoding responsibilities
* Queue and distribute transcoding jobs across a worker pool
* Transcode raw audio files into target streaming formats and bitrates 
* Perform LUFS loudness analysis and apply volume normalization metadata
* Generate multi-resolution thumbnail variants and WebP versions of cover art
* Extract and store audio waveforms for visual scrubbing

##### Storage & cataloguing responsibilities
* Store transcoded audio chunks and artwork in geo-redundant distributed storage
* Assign unique internal resource identifiers (URIs) to the generated media assets
* Create and link the track, album, and artist records in the relational catalogue database
* Notify the global search index about the newly published entities
* Schedule timed releases ( publish automatically at midnight on a specific date)

##### Notification & audit responsibilities
* Dispatch real-time webhooks or WebSocket events updating upload progress
* Notify the artist via email/app about a successful publication or processing failure
* Log all metadata changes and file uploads for compliance and rollback
* Trigger notifications to users who follow the artist (Release Radar)

---

### Feature: Catalogue Search & Discovery

As a listener, I want to search and browse tracks, artists, albums, and playlists so that I can quickly find and explore music I want to listen to.

#### Feature breakdown

1. The listener inputs a search query (keyword, artist name, track title, album).
2. The client application debounces the input and fetches autocomplete suggestions.
3. The system parses the query, applies typo tolerance, and queries the search index.
4. The system ranks results by relevance and popularity, grouping them into distinct views.
5. The system retrieves rich metadata (artwork, durations) for the top hits.
6. The system returns structured results to the client.
7. The listener selects an entity, and the system logs the interaction to improve future ranking.

#### Responsibilities

##### Query processing & routing responsibilities
* Receive search queries via a dedicated, low-latency API gateway
* Normalize input queries (strip diacritics, lowercase, remove special characters)
* Apply fuzzy string matching, stemming, and typo tolerance algorithms
* Detect search intent (matching "best rock songs" to curated playlists)
* Route search requests to the appropriate distributed search nodes (Elasticsearch)

##### Indexing & ranking responsibilities
* Maintain a highly available inverted index of the entire active music catalogue
* Ingest real-time catalogue updates (new releases, deleted tracks, metadata edits)
* Calculate and update entity popularity scores based on recent streaming velocity
* Rank search results based on textual relevance, global popularity, and personalization weight
* Exclude private playlists, unlisted tracks, and region-blocked content from public results

##### Result aggregation & formatting responsibilities
* Group results into distinct categories (Top Result, Tracks, Artists, Albums, Playlists)
* Fetch supplementary metadata and CDN image URLs from the primary database or cache
* Hydrate tracklist data for album and playlist results
* Format search payloads to minimize response payload size (GraphQL or sparse REST)
* Support cursor-based pagination for deep browsing of search categories

##### Personalization & recommendation responsibilities
* Boost search ranking based on the user's historical listening preferences and library
* Suggest query auto-completions while the user is typing
* Generate dynamic "More by this artist" or "Fans also like" relations for detail pages
* Filter explicit content dynamically based on the user's parental control settings

##### Search telemetry & analytics responsibilities
* Log raw search queries for trend analysis and vocabulary improvement
* Track click-through rates (CTR) on specific search results (query-to-click mapping)
* Identify "zero-result" queries to inform content acquisition or fix synonym mappings
* Monitor search API latency and index performance under load

---

### Feature: Payment Processing & Premium Access

As a listener, I want to subscribe to Premium so that I can unlock additional features on the platform.

#### Feature breakdown

1. The user views subscription tiers and selects a plan (Individual, Family, Student).
2. The system redirects the user to a secure payment gateway or handles native in-app purchases.
3. The payment gateway processes the transaction securely and issues an asynchronous webhook.
4. The system validates the webhook payload and updates the user's subscription state in the database.
5. The system provisions the Premium entitlements and clears client-side capability caches.
6. The system generates a receipt and schedules the next recurring billing cycle.
7. The application unlocks ad-free listening, offline downloads, and higher audio quality.

#### Responsibilities

##### Subscription & plan management responsibilities
* Define and manage product catalogues, pricing tiers, and currency localization
* Support complex subscription models (free trials, family plans, student discounts)
* Manage the complete subscription lifecycle (active, past_due, canceled, trialing)
* Process scheduled recurring renewals via cron jobs or queue workers
* Manage grace periods and dunning flows for failed recurring payments

##### Payment gateway integration responsibilities
* Integrate securely with external PSPs (Payment Service Providers like Apple/Google IAP)
* Render secure, hosted payment fields (PCI-DSS compliant) to collect card details
* Validate and process asynchronous payment confirmation webhooks securely (signature verification)
* Handle 3D Secure (SCA) authentication flows for European users
* Execute refunds, prorations, and subscription upgrades/downgrades

##### Entitlement & capability responsibilities
* Update user database records to reflect active Premium status instantly upon payment
* Inject authorization flags into the user's session token (JWT claims)
* Unlock Premium capabilities (offline mode, 320kbps audio, unlimited skips) dynamically
* Revoke Premium capabilities strictly when a subscription expires or is chargebacked
* Manage seat allocations for Family plan managers and invitees

##### Security, compliance, & auditing responsibilities
* Ensure zero internal storage of raw Primary Account Numbers (PAN) to maintain PCI compliance
* Audit log all financial state transitions, webhook receipts, and administrative overrides
* Generate, store, and email legal invoices and tax receipts (VAT calculation)
* Implement idempotency keys to prevent duplicate charges during network failures
* Reconcile internal subscription states with the external payment gateway's source of truth

---

### Feature: Playlist Management

As a user, I want to create, modify, delete, and organize tracks into playlists, and control their visibility, so that I can manage my music collection and choose whether to share it with others.

#### Feature breakdown

1. The user triggers the "Create playlist" action and provides metadata (name, description, image).
2. The system provisions the playlist record and assigns the user as the owner.
3. The user interacts with the application to add, remove, or reorder tracks.
4. The system validates the operations, updates the track indices, and persists changes.
5. The user toggles the playlist visibility (private, unlisted, public, collaborative).
6. The system synchronizes the playlist state to the global search index (if public) and to all user devices.
7. The user can delete the playlist, triggering a cleanup of the relational data.

#### Responsibilities

##### Metadata & lifecycle responsibilities
* Create playlist entities with validated metadata (name length, description sanitation)
* Bind cryptographic ownership of the playlist to the creator's user ID
* Process, resize, and store custom user-uploaded playlist cover images
* Enforce system limits (maximum tracks per playlist, maximum playlists per user)
* Soft-delete playlist entities to allow for accidental deletion recovery (trash bin)

##### Content mutation & ordering responsibilities
* Handle bulk and single track additions to a playlist
* Handle track removals and deduplication (preventing/warning about duplicate entries)
* Manage complex ordering logic (linked lists or fractional indexing) for drag-and-drop reordering
* Validate that added tracks exist, are active, and are available in the user's region
* Support collaborative mutations where multiple authorized users can edit the tracklist

##### Synchronization & state management responsibilities
* Persist playlist track relations and metadata in a high-throughput relational database
* Push playlist state changes via WebSockets/SSE to synchronize the user's active devices
* Expose paginated APIs for clients to retrieve large playlists (10,000 tracks) efficiently
* Resolve mutation conflicts if a user edits a playlist simultaneously on two devices

##### Access control & visibility responsibilities
* Enforce strict Authorization rules (only owners/collaborators can mutate)
* Manage visibility states: Public (searchable), Unlisted (link-only), Private (owner-only)
* Generate secure sharing links for unlisted playlists
* Notify the distributed search engine immediately when a playlist becomes public
* Scrub the search engine index when a public playlist is made private or deleted

##### Engagement & relation responsibilities
* Allow users to "Like" or "Follow" other users' public playlists
* Track follower counts and update playlist popularity metrics
* Generate personalized playlist recommendations based on added tracks (auto-suggest)
* Manage offline sync state flags for playlists downloaded by the user

---

### Feature: Group Listening Session

As a listener, I want to create a listening room with other users so that we can experience the same audio playback simultaneously, regardless of our physical locations.

#### Feature breakdown

1. The host initiates a session; the system creates a virtual room and returns a deep link.
2. Guests click the link, authenticate, and establish WebSocket connections to the room server.
3. The system manages the room roster, enforcing capacity limits and broadcasting join/leave events.
4. The host (or permitted guests) modifies the playback queue or changes playback state (play/pause/seek).
5. The server calculates absolute timestamps and broadcasts the playback command to all clients.
6. Client applications adjust playback speed slightly or skip to synchronize with the global clock, compensating for latency.
7. The system handles gracefully if the host disconnects (reassigning host or closing the room).

#### Responsibilities

##### Session & topology management responsibilities
* Generate short-lived, secure unique identifiers for listening rooms
* Manage the routing of users to specific stateful real-time servers 
* Authenticate joining participants and enforce Premium-only restrictions if applicable
* Maintain the real-time presence registry (who is currently online in the room)
* Execute host-migration algorithms or teardown procedures when the session creator leaves

##### Synchronization & communication responsibilities
* Establish, upgrade, and maintain bidirectional WebSocket or WebRTC connections
* Implement a highly accurate global clock synchronization protocol 
* Calculate network latency offset (ping time) for each individual connected client
* Broadcast synchronized playback vectors (Track ID, State, Position, Absolute Timestamp)
* Throttle and debounce rapid playback commands to prevent broadcast storms

##### Shared state & playback responsibilities
* Maintain the server-side source of truth for the shared playback queue
* Process queue mutations (add next, remove, reorder) from authorized participants
* Instruct client devices to pre-fetch upcoming audio chunks to prevent buffering desync
* Forbid standard clients from relying on local time; enforce server-dictated playhead positions
* Resolve regional licensing conflicts if a track is unavailable for a specific international guest

##### Client execution & UX responsibilities
* Dynamically adjust playback rate (pitch-corrected audio stretching) to catch up to the sync point
* Mute or crossfade gracefully during hard seeks dictated by the host
* Display visual indicators of other users' presence and listening status
* Provide localized error handling if synchronization fails due to extreme packet loss

##### Infrastructure & scalability responsibilities
* Scale real-time WebSocket nodes horizontally using a PubSub backplane (Redis Pub/Sub)
* Handle abrupt WebSocket disconnections (mobile network drops) with fast reconnection windows
* Isolate group listening traffic to prevent it from impacting standard REST API performance

---

### Feature: Offline Downloading (Premium)

As a Premium subscriber, I want to download songs to my device so that I can listen to music without an active network connection.

#### Feature breakdown

1. The subscriber marks an album, track, or playlist for offline sync.
2. The application requests download manifests and DRM licenses from the server.
3. The system verifies the user's Premium entitlement and offline device limits.
4. The application pulls encrypted audio chunks, metadata, and artwork in the background.
5. The local OS stores the files in a secure, sandboxed directory and updates the local SQLite index.
6. When offline, the application routes playback requests to the local storage instead of the network.
7. The application periodically "calls home" to renew DRM licenses and report offline listening stats.

#### Responsibilities

##### Licensing & DRM responsibilities
* Validate active Premium account subscription before issuing download manifests
* Check individual track distribution rights (some labels prohibit offline caching)
* Generate and issue device-bound, time-limited decryption keys (DRM)
* Enforce maximum device limits (max 5 devices per account for offline sync)
* Enforce maximum track limits (max 10,000 downloaded tracks per device)

##### Payload & network transfer responsibilities
* Package audio streams, essential JSON metadata, and compressed artwork for batch transfer
* Interface with mobile OS background download managers (iOS Background Tasks, Android WorkManager)
* Support automatic pause/resume of downloads based on network type (Wi-Fi only settings)
* Verify cryptographic checksums (MD5/SHA) of downloaded chunks to prevent file corruption
* Manage download queues and prioritize explicit user requests over background syncs

##### Local storage & state responsibilities
* Encrypt and store media files within secure, non-accessible application sandboxes
* Maintain a robust local relational database (SQLite/Room/CoreData) to index offline content
* Map network URIs to local file paths dynamically during offline mode
* Monitor device storage limits and pause downloads if disk space drops below safe thresholds
* Provide UI mechanics to clear all downloads or purge specific entities

##### Offline playback rules responsibilities
* Intercept playback routing to serve from local disk instead of initiating network requests
* Buffer and log playback telemetry locally while the device is offline
* Batch and flush offline playback telemetry to the server upon network reconnection
* Seamlessly fall back to streaming if a downloaded file is found to be corrupted

##### Synchronization & revocation responsibilities
* Periodically sync playlist additions/removals with the server and queue delta downloads
* Mandate a "call home" heartbeat (every 30 days) to keep offline licenses valid
* Automatically invalidate local DRM keys if the user's Premium subscription lapses
* Purge local media files automatically upon explicit user logout

---

## Auxiliary features

### Feature: Notifications
As a user, I want to receive notifications about new releases from my favorite artists so that I can discover their latest music.

### Feature: Music Recommendations (AI-Powered)
As a listener, I want to receive personalized song recommendations based on my listening history and preferences so that I can discover new music.

### Feature: Artist Management & Promotion
As an artist, I want to manage my music releases, track their performance, and promote my songs so that I can reach a wider audience and grow my listener base.

### Feature: Music Player & Playback Queue
As a listener, I want to control media playback and manage my upcoming queue so that I have full control over my continuous listening experience.

### Feature: User Library & Artist Following
As a listener, I want to save tracks, albums, and playlists to my library and follow artists so that I can easily access my favorite content.

### Feature: Settings & Privacy
As a user, I want to configure playback settings and privacy preferences so that I can tailor the platform behavior to my needs.
