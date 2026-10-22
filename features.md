# Music Streaming Platform : Feature Definitions

This document presents the first eight features in detail. Additional features are also listed at the end.

## 1. User Authentication & Account Management

**User story**

As a user, I want to create an account, log in, and manage my profile so that I can securely access the platform and personalize my information.

**Breakdown**

1. The user opens the login page and enters their email address.
2. The system checks whether an account is associated with this email address.
3. If an account exists, the user enters their password. If it is correct, they are logged in. Otherwise, they can try again or reset their password.
4. If no account exists, the system offers the user the option to create one. If they decline, they are redirected to the home page.
5. The user chooses between creating a listener account or an artist account, then enters their name, username, email address, and password. A phone number and profile picture are optional.
6. The system asks the user for consent to store their personal data.
7. The user chooses to verify their email address or phone number and receives a verification code or link.
8. Once verification is successful, the system saves the account information and confirms account creation.
9. The user can access and update their profile settings, change their password, or reset it if they forget it.

**Responsibilities**

- **Business:** Manage listener and artist account types, ensure registration information is complete, and handle account creation and profile updates.
- **Technical:** Manage registration and login, verify email addresses or phone numbers, store account information, and handle password resets and user sessions.
- **Quality:** Protect user data, store passwords securely, prevent unauthorized access, and ensure a reliable registration and login process.

## 2. Audio Streaming

**User story**

As a listener, I want to stream audio tracks on demand so that I can listen to music instantly without storing it locally.

**Breakdown**

1. The user selects a track to play.
2. The application requests the audio stream from the media server.
3. The system checks the user's access level and applies the relevant listening rules.
4. The server retrieves the audio file and transmits it in data chunks.
5. The application buffers and plays the audio.
6. The system records the playback event for listening statistics and artist royalty calculations.

**Responsibilities**

- **Business:** Track play counts to support accurate artist royalty calculations.
- **Technical:** Deliver audio data, manage buffering, and retrieve files from storage.
- **Quality:** Ensure low-latency delivery and maintain reliable playback during periods of high traffic.

## 3. Song Uploading (Artist Application)

**User story**

As an artist, I want to upload audio files and track metadata so that my music becomes available for public streaming.

**Breakdown**

1. The artist selects an audio file and cover art in the artist portal.
2. The artist enters metadata such as the track title, genre, and album.
3. The system validates the file format and size.
4. The system converts the audio into formats suitable for streaming.
5. The system stores the files and updates the music catalogue.
6. The system confirms that the release has been published successfully.

**Responsibilities**

- **Business:** Check that required metadata is provided and link the track to the correct artist profile.
- **Technical:** Convert audio files, store media files, and update the catalogue.
- **Quality:** Preserve audio integrity and ensure newly published music becomes reliably available.

## 4. Catalogue Search & Discovery

**User story**

As a listener, I want to search and browse tracks, artists, albums, and playlists so that I can quickly find and explore music I want to listen to.

**Breakdown**

1. The listener enters a search query, such as an artist name, track title, album title, or playlist name.
2. The system searches the music catalogue and retrieves relevant results.
3. The system displays results grouped into categories such as Top Result, Tracks, Artists, Albums, and Playlists.
4. The listener selects a result to open its detail page or selects a track directly for playback.
5. The system retrieves the item's metadata, artwork, tracklist, and available actions.
6. The system displays the detail page, allowing the listener to start playback, add tracks to the queue, or continue browsing.

**Responsibilities**

- **Business:** Ensure results are relevant and catalogue information is consistent.
- **Technical:** Process search queries, retrieve matching items, group results by category, and display detailed information.
- **Quality:** Ensure accurate results, responsive searches, consistent artwork and metadata, and reliable catalogue access.

## 5. Premium Access & Payment Processing

**User story**

As a listener, I want to subscribe to Premium so that I can unlock additional features on the platform.

**Breakdown**

1. The user views the Premium offer and chooses to subscribe.
2. The system redirects the user to a secure payment service.
3. The payment service processes the transaction and returns its result.
4. If the payment is successful, the system updates the user's account to Premium.
5. The application unlocks the features included in Premium and confirms activation to the user.
6. If the payment fails, the system informs the user and does not activate Premium.

**Responsibilities**

- **Business:** Define which features are included in Premium and the subscription price.
- **Technical:** Integrate the payment service and update the user's access level after successful payment.
- **Quality:** Protect payment information, handle failed or interrupted transactions, and ensure Premium access is granted only after successful payment.

## 6. Playlist Management

**User story**

As a user, I want to create, modify, delete, and organize tracks into playlists, and control their visibility, so that I can manage my music collection and choose whether to share it with others.

**Breakdown**

1. The user opens their library and selects the option to create a playlist.
2. The system asks the user to enter a playlist name and choose whether it should be public or private.
3. The system creates the playlist and links it to the user's account.
4. The user can add tracks, remove tracks, or change their order.
5. The system saves the playlist changes and track order.
6. If the playlist is public, the system makes it available in catalogue search.
7. When the user deletes a playlist, the system removes it and its associated track list.

**Responsibilities**

- **Business:** Define any playlist limits that depend on the user's account type.
- **Technical:** Store playlists and their tracks, preserve track order, and update the interface when changes are made.
- **Quality:** Ensure playlist changes are saved correctly and remain consistent across the user's devices.

## 7. Group Listening Session

**User story**

As a listener, I want to create a listening room with other users so that we can experience the same audio playback simultaneously, regardless of our physical locations.

**Breakdown**

1. The host creates a listening room and the system generates a unique invitation link.
2. Guest users open the link and join the room.
3. The system establishes a persistent, bidirectional connection (for example, a WebSocket) between the server and the clients in the room.
4. The host selects a track and triggers playback commands such as play, pause, or seek.
5. The system receives the command, calculates the synchronization timestamp, and broadcasts the playback state to all connected clients.
6. Client applications adjust their audio playback to keep the music synchronized, accounting for network latency.
7. If a user disconnects, the system removes them from the active session without interrupting playback for the others.

**Responsibilities**

- **Business:** Manage room access, define host and guest permissions for queue management, and check that participants have the required access to the selected track.
- **Technical:** Maintain concurrent WebSocket connections, distribute playback-state messages, and synchronize playback across clients.
- **Quality:** Keep playback synchronized, maintain service availability, and handle temporary network disconnections without disrupting the room.

## 8. Downloading

**User story**

As a Premium subscriber, I want to download songs to my device so that I can listen to music without an active network connection.

**Breakdown**

1. The subscriber selects a track, album, or playlist and starts the download.
2. The system checks the user's Premium status and verifies that the selected content is available for offline listening.
3. The system prepares the audio files for local storage.
4. The system downloads the audio files, metadata, and artwork to the user's device.
5. The system verifies the download and marks the content as available offline.
6. The subscriber can access and play the downloaded content without an internet connection.
7. The system applies storage limits, subscription rules, and relevant licensing restrictions to downloaded content.

**Responsibilities**

- **Business:** Restrict offline listening to eligible Premium users and respect content licensing rules.
- **Technical:** Handle download requests, store files locally, maintain offline metadata, verify file integrity, and synchronize download status with the user's library.
- **Quality:** Ensure reliable downloads, preserve audio integrity, provide a smooth offline playback experience, and manage local storage efficiently.

---

# Additional Features

The following features are also part of the project scope. They are included in full here, even though only the first eight are intended for the initial presentation.

## 9. Notifications

**User story**

As a user, I want to receive notifications about new releases from my favorite artists so that I can discover their latest music.

**Breakdown**

1. An artist publishes a new song or album through the artist application.
2. The system identifies users who follow the artist.
3. The system creates a notification containing the release information, including cover art, song or album title, artist name, and a link.
4. The system sends the notification to users who are currently using the application.
5. Depending on their notification preferences, the system sends push notifications or emails to users who are not currently using the application.
6. The user clicks on the notification and is redirected to the new release page.

**Responsibilities**

- **Business:** Identify which users should receive notifications and respect their notification preferences.
- **Technical:** Create and send notifications, store notification history, and integrate with push notification and email services.
- **Quality:** Ensure notifications are delivered reliably, avoid sending duplicates, and handle large numbers of notifications when popular artists release new music.

## 10. Music Recommendations (AI-Powered)

**User story**

As a listener, I want to receive personalized song recommendations based on my listening history and preferences so that I can discover new music.

**Breakdown**

1. The system collects information about the listener's activity, such as played songs, liked tracks, and favorite artists.
2. The system analyzes this information to identify the listener's musical preferences.
3. The system generates personalized song recommendations based on these preferences.
4. The system displays the recommendations on the home page or in a dedicated discovery section.
5. The listener can play, like, or skip recommended songs.
6. The system uses the listener's new activity to improve future recommendations.

**Responsibilities**

- **Business:** Define how recommendations are presented and ensure they help listeners discover relevant music.
- **Technical:** Collect listening activity, generate personalized recommendations, and update suggestions based on user behavior.
- **Quality:** Ensure recommendations are relevant, diverse, and regularly updated, while protecting user listening data.

## 11. Artist Management & Promotion

**User story**

As an artist, I want to manage my music releases, track their performance, and promote my songs so that I can reach a wider audience and grow my listener base.

**Breakdown**

1. The artist logs into the dedicated artist application.
2. The artist uploads and manages their songs and associated information.
3. The system makes published songs available on the streaming platform.
4. The artist accesses statistics about their music, including streams, listeners, and audience demographics.
5. The artist selects a song they want to promote and chooses an available promotion option.
6. The artist pays for the promotion, which can increase the song's visibility on selected playlists.
7. The system activates the promotion and displays its status and performance to the artist.

**Responsibilities**

- **Business:** Define artist account features, available statistics, promotion options, and pricing.
- **Technical:** Manage music releases, display performance statistics, handle promotion payments, and manage promoted songs on playlists.
- **Quality:** Ensure statistics are accurate, payments are processed securely, and promotions are properly displayed and tracked.

## 12. Music Player & Playback Queue
## 13. User Library & Artist Following
## 15. Settings & Privacy
