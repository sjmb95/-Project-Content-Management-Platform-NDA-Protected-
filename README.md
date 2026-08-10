# Project: Content Management Platform (NDA Protected)

> *⚠️ Due to a confidentiality agreement, I cannot share source code or screenshots. This README serves as a public overview of my work.*

---

## 📱 Overview

I built the mobile frontend for an internal content management platform used by a Nigerian media company. The app handles song submissions, complaint tracking, automated user rewards, and real-time data synchronization across multiple user roles.

The platform serves **200+ daily active users** and processes submissions from content creators who earn rewards for approved content.

---

## 🎯 My Role

I was the **Lead React Native Developer** on a team of 4. I was responsible for:

- Architecting the entire mobile application from the ground up
- Designing the Firebase Realtime Database structure
- Integrating third-party APIs (Reloadly for rewards)
- Building reusable component libraries
- Implementing light/dark theme support
- Managing navigation across 15+ screens

---

## 🛠️ Tech Stack

| Technology & Why I Chose It |

 **React Native + Expo** | Cross-platform development with faster iteration via hot reloading 
 **Firebase Realtime DB** | Real-time sync across devices without building a WebSocket server 
 **Firebase Authentication** | Secure, ready-to-use user management 
 **Axios** | Better error handling and interceptors for API retry logic 
 **React Navigation (Expo Router)** | File-based routing simplified navigation across 15+ screens 
 **React Native Reanimated** | Smooth 60fps animations for better UX 
 **Reloadly API** | Mobile top-up service supporting Nigerian networks (MTN, Airtel, Glo, 9mobile) 

---

## 🏗️ Architecture

+-----------------------------------------------------------+
|                        Mobile App                         |
|                        (React Native)                     |
+-----------------------------------------------------------+
|                        Screens (15+)                      |
|                        Components                         |
|                        Navigation (Expo-Router)           |
+-----------------------------------------------------------+
|                            HTTP Layer                     |
|                           (Axios)                         |
+-----------------------------------------------------------+
| External & Backend Services                                |
|                                                           |
|   +----------------+     +----------------+     +----------------+  |
|   | Firebase Realtime | | Reloadly API   | | Custom PHP API |  |
|   | Database            | | (Top-ups)      | | (Song Data,    |  |
|   | - Auth              | | - OAuth2       | | Status, etc.)  |  |
|   | - Data Storage      | | - Airtime dist.| +----------------+  |
|   | - Real-time Sync    | +----------------+                     |
|   +----------------+                                               |
+-----------------------------------------------------------+


---

## 📱 Key Features I Built

### 1. Multi-Category Complaint System

I designed a complaint management system with three categories:

- **Songs** — Complaints about specific song submissions
- **Others** — General complaints or feedback  
- **Users** — User-related issues or reports

Each category has its own table view with:
- Real-time search/filtering
- Alternating row colors for better readability
- Touch to view full details

**How it works:**
I used Firebase's `onValue()` listeners to sync data instantly across all active sessions. When an admin updates a complaint, every user sees the change without refreshing the screen.

### 2. Song Submission & Draft Workflow

I built a complete submission flow where users can:

- **Save drafts** — Users can start a submission, save it, and come back later
- **Resume drafts** — Drafts are stored per user in Firebase under their UID
- **Submit for review** — Submissions go to admin for approval
- **Track status** — Users see "pending", "published", or "rejected" in real-time

**Technical challenge solved:**
I used `useFocusEffect` to refresh the drafts table every time the user navigates back to it, ensuring they always see the latest data without manual refresh.

### 3. Automated Reward Distribution

This was the most complex feature I built. When a user's submission is approved:

1. The app fetches their phone number from Firebase
2. Detects their network operator (MTN/Airtel/Glo/9mobile)
3. Maps the operator to Reloadly's operator IDs (e.g., MTN = "344")
4. Calls Reloadly's API to send ₦500 airtime

**Challenges I solved:**
- **API Authentication:** I implemented OAuth2 client credentials flow to get bearer tokens
- **Retry Logic:** Network failures happen — I built automatic retry mechanisms
- **Preventing Double-Crediting:** Once a transaction ID is returned, it's stored permanently in the database

### 4. Custom Theming System

I built a light/dark mode system from scratch instead of using a library because:
- It gave me full control over colors
- It reduced bundle size (no third-party theme library)
- It was simpler to maintain

The system uses React Native's `useColorScheme()` hook with custom hooks to manage colors throughout the app.

### 5. Real-Time Sync Architecture

The app uses Firebase listeners extensively:

```javascript
const reference = ref(db, 'Users/Posters/' + auth.currentUser?.uid + '/drafts');
onValue(reference, (snapshot) => {
  // Automatically updates UI when data changes
});
