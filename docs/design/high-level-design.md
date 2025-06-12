# High-Level Design Document: Simple WebRTC React App

This document outlines the high-level design of a simple 1-to-1 video call application built using React, WebRTC, and Socket.IO.

## 1. System Overview

The application consists of two main components: a client-side React application and a server-side component (implied, not explicitly shown in the provided code).  The client handles user interface, media capture and rendering, and WebRTC communication. The server facilitates signaling between peers using Socket.IO.

**Diagram:**

```mermaid
graph LR
    A[React Client (User 1)] --> B(Socket.IO Server);
    C[React Client (User 2)] --> B;
    B --> A;
    B --> C;
    subgraph WebRTC
        A -.-> D[WebRTC PeerConnection];
        C -.-> D;
    end
```

## 2. Client-Side Architecture (React App)

The React application uses React Router for navigation between the home screen and the call screen.

### 2.1. Home Screen (`HomeScreen.js`)

* **Functionality:** Allows users to enter their username and the room ID they wish to join.
* **Components:** Input fields for username and room ID, a submit button (which is actually a `<Link>` component in React Router).
* **Data Flow:**  Collects username and room ID from input fields and passes them as URL parameters to the `/call/:username/:room` route.

### 2.2. Call Screen (`CallScreen.js`)

* **Functionality:** Establishes a WebRTC connection between two peers within a specified room.  Handles media capture, peer connection establishment, ICE candidate exchange, and data transfer via Socket.IO.
* **Components:** Two `<video>` elements for local and remote video streams.
* **Data Flow:**
    * Uses `useParams` hook to retrieve username and room ID from the URL.
    * Connects to the Socket.IO server.
    * Gets user media (audio and video).
    * Creates a WebRTC PeerConnection.
    * Sends and receives signaling messages (offer, answer, ICE candidates) via Socket.IO.
    * Adds remote tracks to the remote video element.
* **Key Logic:**
    * **`startConnection()`:** Initiates media capture and Socket.IO connection.
    * **`createPeerConnection()`:** Creates a WebRTC peer connection with ICE servers for NAT traversal.
    * **`sendData()`:** Sends data (signaling messages) to the server via Socket.IO.
    * **`onIceCandidate()`:** Handles ICE candidate generation and transmission.
    * **`onTrack()`:** Handles receiving and rendering remote tracks.
    * **`sendOffer()`/`sendAnswer()`:** Creates and sends SDP offers and answers.
    * **`signalingDataHandler()`:** Processes incoming signaling messages.


## 3. Server-Side Architecture (Implied)

The server-side component (not provided in the code) is crucial for facilitating communication between peers.  It uses Socket.IO for real-time bidirectional communication.

### 3.1. Functionality

* **Room Management:**  Manages rooms and tracks users within each room.
* **Signaling:** Relays signaling messages (offer, answer, ICE candidates) between peers in the same room.
* **Connection Management:** Handles connection establishment and disconnection of clients.

### 3.2. API

The server exposes a Socket.IO endpoint.  The client interacts with the server through the following events:

* **`join` (client -> server):**  A client joins a room.  The server informs other clients in the room.
* **`data` (client <-> server):**  Clients exchange signaling messages (offer, answer, ICE candidates).
* **`ready` (server -> client):** Server signals that a peer is ready for connection.


## 4. Database (None)

This application doesn't appear to use a database.  Room and user information is likely managed in memory on the server.

## 5. System Integration

The application integrates three key technologies:

* **React:** For building the user interface.
* **WebRTC:** For peer-to-peer communication.
* **Socket.IO:** For real-time signaling between peers.

## 6. Recommendations

* **Error Handling:** The code lacks robust error handling.  Adding error handling for media capture failures, peer connection errors, and Socket.IO connection issues is crucial.
* **Scalability:** The current server-side design (implied) is not scalable for many concurrent users.  Consider using a more robust solution like a message queue or a distributed signaling server for larger-scale deployments.
* **Security:**  The application needs security enhancements.  Consider using HTTPS for secure communication and implementing measures to prevent unauthorized access to rooms.
* **Server-Side Code:** The provided code only shows the client-side.  A detailed design and implementation of the server-side component is needed for a complete system.
* **Testing:**  Add comprehensive unit and integration tests to ensure the reliability and stability of the application.


This HLD provides a high-level overview.  A more detailed LLD would include specific data structures, algorithms, and implementation details for both the client and server components.