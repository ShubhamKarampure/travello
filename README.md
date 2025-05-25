# Travello: AI-Powered Smart Travel & Safety Assistant

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Travello is an intelligent mobile application designed to revolutionize your travel experience in Mumbai. It combines a smart, voice-controlled travel assistant for multi-modal route planning with an automated rapid response system for enhanced safety on the go.

## 🚀 Core Features

Travello is built around three primary features to provide a seamless and secure travel experience:

1.  **🗣️ Smart Travelling Assistant:**
    * **Voice-Controlled Trip Planning:** Simply tell Travello your source, destination, and any desired stopovers (e.g., "I want to go from Andheri to Thane, stopping at a coffee shop near Bandra").
    * **AI-Powered Itinerary Generation:** Leverages advanced AI (LangGraph with Gemini/Groq LLMs) to understand natural language queries and plan optimized routes.
    * **Multi-Modal Routing:** Provides routes for both private vehicles (cars) and public transport (bus, train, metro), integrating with OpenTripPlanner for accurate public transit schedules.
    * **Customizable Stopovers:** Incorporates user-specified requirements like refueling, coffee breaks, or ATM visits into the journey plan.
    * **Interactive Map Display:** Visualizes the generated routes and points of interest.

2.  **🎫 Accurate Multimodal Transport Information:**
    * **Comprehensive Coverage:** Utilizes scraped and integrated transport schedules for buses, trains, and metro lines across Mumbai, fed into an OpenTripPlanner (OTP) instance for precise routing.
    * **Real-time Options:** Delivers up-to-date transport options based on current schedules.
    * **Digital Ticketing:** Generates QR codes for relevant public transport legs of your journey, viewable directly within the app.
    * **Detailed Leg Information:** Shows mode of transport, route names/numbers, origin, destination, duration, distance, and validity for each ticket.

3.  **🛡️ Rapid Response System (Safety Feature):**
    * **Automatic Crash Detection:** Employs the device's accelerometer to detect potential vehicular accidents automatically.
    * **Manual SOS Alert:** Allows users to manually trigger an emergency alert if they feel unsafe or witness an incident.
    * **Real-time Location Sharing:** Transmits the user's current location to the backend upon alert activation.
    * **Emergency Audio Recording & Transcription:** Captures ambient audio after an alert is triggered (with user consent countdown for automatic detection) and transcribes it to text using the Groq Whisper API for context.
    * **AI-Driven Situation Analysis:** A Python backend analyzes the transcript using Langchain and Gemini to understand the emergency's nature and identify required services (Police, Hospital, Fire Brigade).
    * **Automated Authority Notification:** Alerts the closest relevant emergency services by querying a PostGIS-enabled database for nearby units.
    * **In-App Emergency Guidance:** Displays a summary of the situation, recommended actions, and a map showing the user's location and nearby notified services with routes.

## 🛠️ Tech Stack

### Frontend (React Native)
* **Framework:** React Native with Expo
* **Navigation:** Expo Router
* **Mapping:** `react-native-maps` (Google Maps Provider)
* **Sensors & Hardware Interaction:**
    * `expo-sensors` (Accelerometer for crash detection)
    * `expo-av` (Audio recording for voice commands and emergency reports)
    * `expo-location` (User location tracking)
* **UI Components:** Custom components, `react-native-qrcode-svg`
* **State Management:** React Hooks (`useState`, `useEffect`, `useRef`, `useCallback`)

### Backend
* **Smart Travel Assistant (Route Planning):**
    * **Language:** Python
    * **AI/NLP Orchestration:** LangGraph
    * **LLMs:** Gemini (Google) / Groq (Llama 3.1 or similar) for intent parsing and route structuring
    * **Geocoding & POI:** Ola Maps API (Autocomplete, Nearby Search, Geocode)
    * **Public Transit Routing:** OpenTripPlanner (OTP) instance
    * **Hosting (Assumed):** Flask/FastAPI or similar for the LangGraph service endpoint.

* **Rapid Response System (Emergency Handling):**
    * **Language/Framework:** Python, Flask
    * **Audio Transcription:** Groq API (Whisper model)
    * **AI-Powered Transcript Analysis:** Langchain with Google Generative AI (Gemini)
    * **Database:** PostgreSQL with PostGIS extension (for spatial queries of emergency services)
    * **File Storage (Temporary for Uploads):** Local filesystem (`uploads` folder)

### APIs & Services
* **Mapping & Geocoding:** Ola Maps API
* **Large Language Models:** Google Gemini, Groq API
* **Audio Transcription:** Groq API (Whisper)
* **Public Transport Data:** Self-hosted OpenTripPlanner (OTP) instance fed with Mumbai transport data.

## 🏗️ System Architecture Overview

1.  **Frontend (React Native App):**
    * Manages user interaction for all features.
    * Handles sensor data (accelerometer, location).
    * Captures audio for voice commands and emergency reports.
    * Communicates with backend services via REST APIs.
    * Renders maps, routes, tickets, and emergency information.

2.  **Backend Services:**
    * **Smart Travel Assistant Service (Python/LangGraph):**
        * Receives transcribed text or typed queries from the app.
        * Uses LangGraph to orchestrate LLM calls (Gemini/Groq) for understanding user intent, identifying locations, and desired stopovers.
        * Interacts with Ola Maps API for geocoding and finding POIs.
        * Queries the OpenTripPlanner instance for public transit routes or calculates car routes (details of car route calculation might involve another API or logic).
        * Returns structured route data to the app.
    * **Rapid Response Service (Python/Flask):**
        * **`/transcribe` endpoint:** Receives audio recordings from the app (for both voice commands and emergency reports), sends them to Groq API for transcription, and returns the text.
        * **`/process_transcript` endpoint:** Receives emergency transcripts and location data. Uses Langchain with Gemini to analyze the transcript. Queries the PostGIS database to find nearby emergency services. Returns analysis and service details to the app.

3.  **Databases:**
    * **PostgreSQL with PostGIS:** Stores locations of emergency services (police stations, hospitals, fire brigades) for efficient geospatial querying.
    * *(Implicit)* **OpenTripPlanner Data:** OTP uses its own graph representation built from GTFS feeds or similar (scraped Mumbai data in this case).

## ⚙️ Setup & Installation

(Provide general instructions here. Specifics would depend on your exact setup environment, API key management, etc.)

1.  **Prerequisites:**
    * Node.js and npm/yarn
    * Expo CLI
    * Python 3.8+
    * Access to a PostgreSQL server with PostGIS extension enabled.
    * An OpenTripPlanner server instance configured with Mumbai transport data.
    * API keys for Google (Gemini), Groq, and Ola Maps.

2.  **Frontend (React Native - Travello App):**
    ```bash
    # Clone the repository
    git clone <your-repo-url>
    cd <frontend-folder-name>

    # Install dependencies
    npm install
    # or
    yarn install

    # Set up environment variables (e.g., in a .env file)
    # EXPO_PUBLIC_BACKEND_IP=<your_flask_backend_ip_for_safety_feature>
    # EXPO_PUBLIC_MACHINE_IP=<your_langgraph_service_ip>
    # (Add any other necessary Expo environment variables)

    # Run the app
    npx expo start
    ```
    *Follow instructions in the terminal to open on a simulator or physical device via Expo Go.*

3.  **Backend Services:**
    * **Rapid Response (Flask - `app.py`):**
        ```bash
        cd <safety_backend_folder_name> # e.g., where app.py is
        pip install -r requirements.txt # (You'll need to create a requirements.txt)

        # Set up environment variables (e.g., in a .env file or system variables)
        # GOOGLE_API_KEY=your_google_api_key
        # GROQ_API_KEY=your_groq_api_key
        # DB_NAME=your_db_name
        # DB_USER=your_db_user
        # DB_PASSWORD=your_db_password
        # DB_HOST=your_db_host
        # DB_PORT=your_db_port

        # Run the Flask app
        python app.py
        ```
        *Ensure your PostgreSQL database is running and accessible, and the necessary tables (police, hospital, firebrigade) with geodata are created.*

    * **Smart Travel Assistant (LangGraph - `graph.py`):**
        ```bash
        cd <assistant_backend_folder_name> # e.g., where graph.py is
        pip install -r requirements.txt # (You'll need to create a requirements.txt)

        # Set up environment variables (e.g., in a .env file or system variables)
        # GROQ_API_KEY=your_groq_api_key
        # GEMINI_API_KEY=your_google_api_key
        # OLA_API_KEY=your_ola_maps_api_key
        # HERE_API_KEY=your_here_api_key (if used, mentioned in graph.py but not directly in flow)
        # MACHINE_IP=<ip_of_otp_server_if_different_or_localhost>
        # LANGSMITH_API_KEY=your_langsmith_key (optional, for tracing)

        # Run the LangGraph service (e.g., wrap graph.py in a FastAPI/Flask endpoint)
        # Example: python assistant_server.py (you'd need to create this server file)
        ```
        *Ensure your OpenTripPlanner server is running and accessible.*

## 🚀 Usage

1.  **Home Screen:**
    * Tap the "Where to?" search bar or the microphone icon to initiate the Smart Travel Assistant.
    * If using the microphone, speak your travel query clearly (e.g., "Go from SPIT College Andheri to Gateway of India, and find a McDonald's on the way"). The app will transcribe your speech and populate the input field.
    * Confirm your query to receive route options.

2.  **Travel Planning:**
    * View suggested routes on the map.
    * Select your preferred mode of transport if multiple options are available.
    * For public transit routes, navigate to the "Tickets" tab to view your digital tickets with QR codes.

3.  **Rapid Response (Safety):**
    * **Automatic Detection:** The app continuously monitors for potential crashes if the safety monitoring is active. If a crash is detected, a countdown begins before an alert is automatically sent. You can cancel the alert during this countdown.
    * **Manual Alert:** On the "Safety" (Account) tab, you can manually input an emergency message or record a voice message to send an alert.
    * **Post-Alert:** If an alert is dispatched, the app will display information received from the backend, including a summary, suggested actions, and details of notified emergency services.

## 💡 Future Scope

* Integration with real-time traffic data for route optimization.
* Live location sharing with trusted contacts.
* Offline maps and transport schedules.
* Gamification for eco-friendly travel choices.
* Community reporting for road hazards or incidents.
* Smart parking assistance.

## 🤝 Contributing

Contributions are welcome! If you'd like to contribute, please follow these steps:
1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

Please ensure your code adheres to the existing style and all tests pass.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.

---
