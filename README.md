<div align="center">

# 🛍️ Full Stack E-Commerce with AI Chatbot

**A full-stack eCommerce platform with an AI shopping assistant, AI-powered review sentiment analysis and AI-generated review summaries**

![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=black)
![Vite](https://img.shields.io/badge/Vite-8-646CFF?logo=vite&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind%20CSS-4-06B6D4?logo=tailwindcss&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-4.1.2-6DB33F?logo=springboot&logoColor=white)
![Spring AI](https://img.shields.io/badge/Spring%20AI-2.0.1-6DB33F?logo=spring&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?logo=postgresql&logoColor=white)

</div>

---

## 📖 Overview

This monorepo contains both halves of an AI-enhanced eCommerce application:

| Folder                   | Description                                                          |
|--------------------------|----------------------------------------------------------------------|
| [`frontend`](./frontend) | React storefront: products, cart, reviews, chatbot UI                |
| [`backend`](./backend)   | Spring Boot + Spring AI REST API: chat, reviews, sentiment, insights |

Shoppers can browse products, chat with an **AI assistant that remembers the conversation**, read reviews labelled with **AI sentiment**, and see an AI-generated **"Customers say"** summary. The AI provider is switchable: run fully offline with **Ollama** or use **OpenAI**.

## 🏛️ Architecture

```
┌──────────────────────────┐        REST / JSON        ┌───────────────────────────┐
│  React + Vite Frontend   │ ────────────────────────► │   Spring Boot Backend     │
│  Redux · Tailwind CSS    │ ◄──────────────────────── │   Spring AI · JPA         │
│  http://localhost:5173   │                           │   http://localhost:8080   │
└──────────────────────────┘                           └─────────────┬─────────────┘
                                                                     │
                                          ┌──────────────────────────┴──────────────────┐
                                          ▼                                             ▼
                                ┌───────────────────┐                     ┌─────────────────────────┐
                                │    PostgreSQL     │                     │  LLM: Ollama or OpenAI  │
                                │ reviews + memory  │                     │  chat · sentiment ·     │
                                └───────────────────┘                     │  insights · images      │
                                                                          └─────────────────────────┘
```

## ✨ Features

### 🛒 Storefront
- Landing page with hero slider and featured products
- Product details with quantity selector and add-to-cart
- Shopping cart with subtotal, shipping fee and free-shipping threshold, persisted in `localStorage`
- Light and dark theme, fully responsive UI
- Register, login and a protected dashboard (demo authentication)

### 🤖 AI Features
- **Floating AI chatbot** with persistent conversation memory (PostgreSQL, last 20 messages)
- **Review sentiment analysis** (Positive / Neutral / Negative) with a confidence score, supporting English, Hindi and Hinglish
- **"Customers say"** AI summary grouped by aspects (Quality, Value, Size, Delivery ...) with a keyword-based fallback when the model is unavailable
- **AI image generation** for sellers (OpenAI profile)
- Graceful degradation: reviews are saved as `PENDING` if the model is down, so nothing is lost

### 🧱 Engineering
- Layered backend: Controller → Service → Repository, with DTOs and validation
- Profile-based AI provider switching (`ollama` / `openai`)
- Unit tests for controller, services and DTO logic
- CORS configured for the frontend origins

## 🧰 Tech Stack

| Layer    | Technology                                                                       |
|----------|----------------------------------------------------------------------------------|
| Frontend | React 19, Vite 8, Tailwind CSS 4, Redux Toolkit, React Router 7, React Hook Form |
| Backend  | Java 21, Spring Boot 4.1.2, Spring Data JPA, Spring AI 2.0.1                     |
| AI       | Ollama (`qwen2.5:3b`) or OpenAI                                                  |
| Database | PostgreSQL                                                                       |
| Build    | npm (frontend), Maven (backend)                                                  |

## 📂 Repository Structure

```
.
├── frontend
│   ├── src
│   │   ├── components      # ProductCard, Reviews, CustomersSay, FloatingChatbot, Navbar ...
│   │   ├── pages           # Landing, Cart, Login, Register, About, Services, Contact
│   │   ├── redux           # Cart slice and store
│   │   ├── context         # Auth and Theme providers
│   │   └── hooks
│   ├── docs                # Learning notes
│   └── package.json
├── backend
│   ├── src/main/java/in/iamandroid
│   │   ├── controller      # Chat and Review APIs
│   │   ├── service         # Chat, Sentiment, Insights, Image services
│   │   └── entity, dto, repository, config
│   └── pom.xml
└── README.md
```

## ✅ Prerequisites

- **JDK 21**
- **Node.js 22 LTS** (or 20.19+) and npm
- **PostgreSQL** on port `5432`
- **Ollama** (local model) or an **OpenAI API key**
- Git

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/naveenrajput8823/Full-Stack-E-Commerce-Project-withAIChatbot.git
cd Full-Stack-E-Commerce-Project-withAIChatbot
```

### 2. Create the database

```sql
CREATE DATABASE ecommerce;
```

Set your PostgreSQL username and password in `backend/src/main/resources/application.properties`.

### 3. Start the backend

The AI model starters live in **Maven profiles**, so pass `-Pollama` or `-Popenai`.

**Option A: Ollama (local and free)**

```bash
ollama pull qwen2.5:3b

cd backend
./mvnw spring-boot:run -Pollama
```

On Windows PowerShell: `.\mvnw.cmd spring-boot:run -Pollama`

**Option B: OpenAI**

```bash
cd backend
export OPENAI_API_KEY=your_api_key
./mvnw spring-boot:run -Popenai -Dspring-boot.run.profiles=openai
```

On Windows PowerShell:

```powershell
$env:OPENAI_API_KEY="your_api_key"
.\mvnw.cmd spring-boot:run -Popenai "-Dspring-boot.run.profiles=openai"
```

The API runs on **http://localhost:8080**

### 4. Start the frontend

```bash
cd frontend
npm install
```

Create `frontend/.env`:

```env
VITE_API_BASE_URL=http://localhost:8080
```

```bash
npm run dev
```

Open **http://localhost:5173**

> The backend allows CORS requests from `http://localhost:5173`, so keep Vite on its default port.

## 📡 API Overview

| Method | Endpoint                                        | Description                              |
|--------|-------------------------------------------------|------------------------------------------|
| `POST` | `/api/v1/openai/chat`                           | Chat with the AI assistant               |
| `GET`  | `/api/v1/openai/chat?prompt=...`                | Chat (plain text)                        |
| `POST` | `/api/v1/openai/generate-image`                 | Generate an image (OpenAI profile)       |
| `POST` | `/api/v1/reviews`                               | Add a review (sentiment auto-assigned)   |
| `GET`  | `/api/v1/reviews?productId=1`                   | Get reviews, optional `sentiment` filter |
| `GET`  | `/api/v1/reviews/sentiment/summary?productId=1` | Sentiment counts and percentages         |
| `POST` | `/api/v1/reviews/insights`                      | AI "Customers say" summary               |

Full request and response examples are in the [backend README](./backend/README.md).

## 📝 Notes

- Product data is currently loaded from the public [Fake Store API](https://fakestoreapi.com)
- Authentication is a **client-side demo** and does not use the backend yet
- Image generation works only with the `openai` profile

## 🛣️ Roadmap

- [ ] Real authentication and user accounts in the backend (JWT)
- [ ] Product catalog and order management APIs
- [ ] Checkout and order history
- [ ] Chatbot that answers product and order questions (RAG / tool calling)
- [ ] Streaming chat responses (Server-Sent Events)
- [ ] Docker and Docker Compose for one-command startup
- [ ] Integration and end-to-end tests
- [ ] Deployment (Vercel + cloud backend)

## 📚 More Documentation

- [Frontend README](./frontend/Readme.md)
- [Backend README](./backend/README.md)

Standalone repositories:
[Frontend](https://github.com/naveenrajput8823/E-CommerceWithAIChatBot) ·
[Backend](https://github.com/naveenrajput8823/ECommerceWithAIChatbotBackend)

## 👨‍💻 Author

**Naveen Rajput**

- 🐙 GitHub: [@naveenrajput8823](https://github.com/naveenrajput8823)

---

<div align="center">

⭐ If you found this project useful, consider giving it a star!

</div>
