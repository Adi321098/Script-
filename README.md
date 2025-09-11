

# Connect Pi - A Modern Social Platform

Connect Pi is a feature-rich social media application built with a modern tech stack. It allows users to create posts, follow others, interact with content, and much more. The project leverages AI for content moderation and personalized suggestions and integrates with the Pi Network for premium features.

## Tech Stack

- **Framework**: [Next.js](https://nextjs.org/) (with App Router)
- **Language**: [TypeScript](https://www.typescriptlang.org/)
- **Styling**: [Tailwind CSS](https://tailwindcss.com/)
- **UI Components**: [ShadCN UI](https://ui.shadcn.com/)
- **Database**: [Firebase Firestore](https://firebase.google.com/docs/firestore) & [MongoDB](https://www.mongodb.com/)
- **Real-time**: [Socket.IO](https://socket.io/)
- **Authentication**: Firebase Authentication & Pi Network SDK
- **Generative AI**: [Google's Gemini on Vertex AI](https://firebase.google.com/docs/vertex-ai) via Genkit

## Project Vision & Roadmap

This project is more than just a social media clone; it's an exploration into the future of community-driven platforms integrated with decentralized technology.

Our plan is broken down into several key phases:

### Phase 1: Core Social Experience (Complete)
- **User Profiles & Authentication:** Secure user accounts with customizable profiles.
- **Content Creation:** Users can create, edit, and delete posts.
- **Social Graph:** Follow and unfollow other users to build a personalized feed.
- **Engagement:** Like, dislike, and comment on posts to interact with the community.
- **Real-time Chat:** Secure, one-on-one messaging with message request handling.
- **Mobile App:** A fully functional mobile application for iOS and Android.

### Phase 2: Enhanced Engagement & AI Integration (Complete)
- **Rich Media:** Support for embedded media previews from popular services like YouTube and Imgur.
- **Real-time Notifications:** Instant in-app alerts and native push notifications for mobile.
- **Advanced AI Moderation:** A full-scale moderation dashboard using Genkit to automatically flag and review reported content.
- **AI-Powered Suggestions:** Intelligent recommendations for users to follow.

### Phase 3: Deep Pi Network Integration (Next Steps)
- **Premium Features via Pi:** Unlock exclusive features by making payments with Pi.
- **Creator Tipping:** Allow users to directly tip their favorite content creators with Pi.

### Phase 4: Towards Decentralization (Future Vision)
- **Community Governance:** Introduce a system for users to vote on platform features.
- **Decentralized Storage:** Investigate using decentralized storage solutions like IPFS for user-generated content.

## Setup and Installation

Follow these steps to get your development environment set up.

### 1. Clone the Repository
```bash
git clone <YOUR_REPOSITORY_URL>
cd <PROJECT_DIRECTORY>
npm install
```

### 2. Configure Firebase
This project requires a Firebase project for the main database and user authentication.
- Go to the [Firebase Console](https://console.firebase.google.com/) and create a new project.
- Inside your project, create a **Web App** (`</>`).
- Copy the `firebaseConfig` object and replace the placeholder in `src/lib/firebase.ts`.
- In **Project Settings > Service Accounts**, generate a new private key. This will download a JSON file.
- **Base64 Encode the Service Account JSON:** You must convert the entire content of this JSON file into a single-line Base64 string. You can use an online tool or the following command:
  ```bash
  # For macOS/Linux
  cat /path/to/your/serviceAccountKey.json | base64
  ```
- Enable **Firestore Database** and **Authentication** (with Email/Password provider) in the Firebase console.

### 3. Configure MongoDB
The real-time chat and notifications run on a MongoDB backend.
- Create a free cluster on [MongoDB Atlas](https://www.mongodb.com/cloud/atlas).
- Get the connection string for your database. It will look like `mongodb+srv://<user>:<password>@cluster.mongodb.net/`.
- Replace the database name in the connection string with a name for your database (e.g., `piconnect`).

### 4. Set Up Environment Variables
Create `.env` files in the following directories and add the required variables:
- `admin-dashboard/backend/.env`
- `server/.env`
- `apps/web/.env`

You will need the following variables:
- `FIREBASE_SERVICE_ACCOUNT_BASE64`: The Base64 encoded string from step 2.
- `MONGO_URI`: Your MongoDB connection string from step 3.
- `JWT_SECRET`: A long, random, secret string for signing admin tokens (e.g., generate one with `openssl rand -hex 32`).
- `PI_API_KEY`: Your API key from the Pi Network Developer Portal.
- `NEXT_PUBLIC_CHAT_SERVER_URL`: The public URL of your deployed chat server (e.g., `https://your-chat-server.onrender.com`). For local development, this will be `http://localhost:3001`.

### 5. Run the Application
You can run each service in a separate terminal:
- **Web App:** `npm run dev` (Runs on `http://localhost:9002`)
- **Chat Server:** `npm run chat:dev` (Runs on `http://localhost:3001`)
- **Admin Backend:** From the `admin-dashboard/backend` directory, run `npm run dev` (Runs on `http://localhost:4001`)
- **Admin Frontend:** From the `admin-dashboard/frontend` directory, run `npm run dev` (Runs on `http://localhost:9003`)

---

## Deployment Guide

Follow these steps to deploy all parts of the Connect Pi application to production.

### 1. Backend Services (Chat & Admin)

Both the Chat Server (`server/`) and the Admin Backend (`admin-dashboard/backend/`) are standard Node.js applications. A platform like [Render](https://render.com/) is an excellent choice for hosting them.

**Steps for each backend service on Render:**
1.  Create a new "Web Service".
2.  Connect your Git repository.
3.  Set the **Root Directory** to either `server` or `admin-dashboard/backend`.
4.  Set the **Build Command** to `npm install`.
5.  Set the **Start Command** to `npm start` for the chat server (`server/index.js`) and `npm run dev` for the admin backend (as it's set up to watch with `tsx`).
6.  Under the **Environment** tab, add all the necessary environment variables (`MONGO_URI`, `JWT_SECRET`, `FIREBASE_SERVICE_ACCOUNT_BASE64`, `PI_API_KEY`).
7.  Deploy! Take note of the public URL Render provides for each service (e.g., `https://your-chat-server.onrender.com`).

### 2. Web & Admin Frontend

The Web App (`apps/web`) and Admin Frontend (`admin-dashboard/frontend`) are Next.js applications. The best platform for hosting them is [Vercel](https://vercel.com/).

**Steps for each frontend on Vercel:**
1.  Create a new project and connect your Git repository.
2.  Set the **Framework Preset** to "Next.js".
3.  Set the **Root Directory** to either `apps/web` or `admin-dashboard/frontend`.
4.  Expand the **Environment Variables** section and add the public URLs of your backend services:
    *   For the Web App, add `NEXT_PUBLIC_CHAT_SERVER_URL` pointing to your deployed Chat Server URL.
    *   For the Admin Frontend, add `NEXT_PUBLIC_API_URL` pointing to your deployed Admin Backend URL.
5.  Deploy.

**Important:** After deploying, you must update the `corsOptions` in `server/index.ts` and `admin-dashboard/backend/index.ts` to allow requests from your newly deployed frontend URLs, then redeploy the backends.

### 3. Building the Mobile App (.apk)

The mobile app is built using **Expo Application Services (EAS)**. This is a cloud service from Expo that builds your app into a native binary (`.apk` for Android, `.ipa` for iOS).

**Prerequisites:**
- An account at [expo.dev](https://expo.dev).
- The EAS CLI installed globally: `npm install -g eas-cli`.

**Steps:**
1.  **Log in to your Expo account:**
    ```bash
    eas login
    ```
2.  **Configure the project:**
    EAS uses a file called `eas.json` to configure the build. Run the following command in the root of your project:
    ```bash
    eas build:configure
    ```
    Select "Android" when prompted. This will create an `eas.json` file for you.

3.  **Start the Build:**
    Navigate to the mobile app's directory and start the build process for Android:
    ```bash
    cd apps/mobile
    eas build -p android
    ```
4.  **Monitor and Download:**
    EAS will queue your build and run it on their servers. You can monitor the progress from the link it provides. Once complete, it will give you a secure link to download your `.apk` file.

You can now install this `.apk` on any Android device or submit it to the Google Play Store.

```
