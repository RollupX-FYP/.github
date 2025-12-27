# GoTogether - Intelligent Travel Companion for Sri Lanka

[![Backend Deploy](https://github.com/GoTogetherOrganization/GoTogether-backend/actions/workflows/deploy.yml/badge.svg)](https://github.com/GoTogetherOrganization/GoTogether-backend/actions/workflows/deploy.yml)
[![Frontend Deploy (Vercel)](https://github.com/GoTogetherOrganization/GoTogether-web/actions/workflows/deploy-vercel.yml/badge.svg)](https://github.com/GoTogetherOrganization/GoTogether-web/actions/workflows/deploy-vercel.yml)

## Overview

GoTogether is a smart, AI-driven travel management platform tailored for both local and foreign travelers in Sri Lanka. Designed with modularity, scalability, and real-time intelligence at its core, it offers seamless web and mobile interfaces, dynamic itinerary generation, transportation data integration, and social features that collectively redefine how people explore the island.

### Problem Statement

Tourists visiting Sri Lanka frequently encounter significant challenges including language barriers, inefficient route planning, and lack of accessible transportation information. These difficulties result in wasted time, increased travel costs, and suboptimal travel experiences. Similarly, Sri Lankan travelers face comparable obstacles due to the absence of optimized trip planning tools tailored to local conditions. The current travel landscape is characterized by fragmented information sources, manual planning processes, and limited access to real-time transportation data, creating a clear need for an integrated, intelligent travel solution.

### Target Users

GoTogether is designed to serve two primary user segments:

- **Foreign Travelers:** International tourists visiting Sri Lanka who require comprehensive travel guidance, cultural context, and language support to navigate the country effectively and safely.
- **Local Travelers:** Sri Lankan residents seeking efficient trip planning, optimized routes, and access to comprehensive transportation information for domestic travel within the country.

## Key Features

The application delivers six core functionalities designed to address specific travel pain points:

- **Personalized Tour Planning:** Enables users to create customized itineraries by selecting starting points and destinations, with intelligent route optimization and personalized recommendations.
- **Transport Details:** Provides comprehensive information about available transportation options between destinations, including schedules, fares, and real-time availability.
- **Travel Guidance:** Offers curated recommendations for cultural sites, entertainment venues, and hidden attractions, enhancing the discovery aspect of travel.
- **Trip Review & Travel Feed:** Creates a social platform where users can share trip plans, engage through comments and reactions, and follow other travelers for inspiration and insights.
- **Emergency Support:** Ensures traveler safety by providing quick access to essential services including hospitals and police stations during emergencies.
- **Food/Stay Suggestions:** Delivers location-based recommendations for accommodations and restaurants tailored to specific travel destinations and user preferences.

## High-Level Architecture

GoTogether adopts a multi-layered microservice architecture, optimized for cloud-native deployment and real-time responsiveness. It includes the following architectural domains:

![High-Level Architecture](../diagrams/High%20level%20architecture.png)

### Key Components

- **Frontend**
  - Web App: Built with Next.js using both SSR and CSR rendering models. Token storage uses secure cookies, with route guards protecting user-only routes.
  - Mobile App: Built with React Native and Expo, ensuring shared logic, adaptive theming, and cross-platform delivery.
- **Backend**
  - Composed of Dockerized microservices using Spring Boot and Go.
- **API Gateway**
  - Kong Gateway handles all inbound traffic (routing, rate limiting, service discovery, delegated authentication).
- **Authentication & Authorization**
  - Keycloak handles user management, token issuance, and session control.
  - Uses pre-configured realm/clients auto-imported via startup scripts.
  - HTTPS enforced via NGINX + Certbot with DuckDNS domain for backend.
- **Database**
  - Supabase PostgreSQL stores persistent data.
- **Image CDN**
  - Cloudflare Images stores and serves user-uploaded media.
- **DevOps & CI/CD**
  - GitHub Actions with custom deploy.sh script:
  - Multi-stage Docker builds optimized via .dockerignore
  - Local JAR builds to improve speed
  - Auto-deploy to Oracle Cloud VM
  - Oracle Free Tier used for backend VM hosting (replaced EC2 due to resource limits).
  - Vercel used for frontend auto-deploy.
- **Communication**
  - Internal services communicate via REST and gRPC.
- **Reverse Proxy**
  - NGINX enforces HTTPS, redirects, and fallback proxying.
  - Certbot + DuckDNS ensures SSL with free domain.

## Web Frontend Design

The web frontend is developed using Next.js, offering a modern, fast, and SEO-friendly user experience. It leverages a hybrid rendering strategy (SSR + CSR) and built-in features for performance and accessibility.

### Key Features

- **Responsive Design:** Ensures seamless interaction across mobile, tablet, and desktop devices.
- **Dark Mode Support:** Built-in toggle for light and dark themes.
- **SSR + CSR Performance:** Uses Server-Side Rendering (SSR) for fast initial loads and Client-Side Rendering (CSR) for dynamic interactions.
- **Skeleton Screens:** Displays placeholders during content fetch.
- **Image Optimization:** Utilizes Next.js Image component for automatic image resizing, lazy loading, and CDN delivery.

## Mobile Frontend Design

The mobile experience is built using React Native, enabling cross-platform development and consistent logic sharing with the web app.

### Highlights

- **Platform-Native Experience:** Delivers smooth interactions and UI responsiveness aligned with both iOS and Android guidelines.
- **Shared Logic with Web:** Core logic, models, and API services are shared with the Next.js frontend.
- **Responsive Theming:** Adaptive UI scaling, and intuitive mobile gestures.

## Backend Design

### Microservices

Deployed via containers in Docker Compose:

- **api-service (Go + Gin):**
- Interfaces with Google Maps API, geocoding, directions, and LLM query generation via Gemini.
- Implements error recovery, rate limiting, and caching middleware.
- **planning-service (Spring Boot):**
- Generates AI-personalized itineraries using user trip history and current context.
- Supports collaborative editing, trip participation logic.
- **social-media-service (Spring Boot):**
- Manages posts, comments, reactions, and user feed.
- Offers paginated REST APIs.
- **auth-service (Keycloak):**
- Handles authentication, registration, and session/token management.
- **user-service (Spring Boot):**
- Manages profile data, follows/unfollows, and user metadata.
- Secure integration with Keycloak for user identity sync.

### Storage

- **Supabase PostgreSQL:** Used for structured storage (users, trips, social metadata, relationships).
- **Cloudflare Images:** Optimized image CDN for storing and delivering profile pictures, post media, etc.

## Database Design

The application utilizes **Supabase PostgreSQL** for persistent data storage, managing users, trips, social metadata, and preferences.

![ER Diagram](../diagrams/er%20diagram.jpg)

## Security and Privacy Design

### Authentication and Authorization

- **OAuth2.0 + OpenID Connect (OIDC):** Implemented via Keycloak for secure login and token-based session management.
- **JWT Access Tokens:** APIs are protected using signed JSON Web Tokens.

### Secure Communication Channels

- **HTTPS Everywhere:** All public endpoints are secured using TLS.
- **WSS (WebSocket Secure):** Real-time communication uses encrypted WebSocket channels.
- **Reverse Proxy Security:** All traffic is routed through an NGINX-based proxy.

### Data Privacy and Protection

- **Email Verification:** Custom wait scripts ensure service boot sequence integrity (e.g., user-service waits for Keycloak).

## Key Architectural Strengths

- **Service Readiness:** All public endpoints are secured using TLS.
- **Incremental Deployments:** Devs can spin up only required services via CLI flags in deploy.sh.
- **API Resilience:** Graceful error handling, fallback logic for external APIs, offline-tolerant design.
- **Infrastructure as Codes:** Environment consistency ensured across developers using Docker, GitHub Actions, and automated Keycloak realm imports.
- **Localization Ready:** Future-proofed for Sinhala, Tamil, and English UI and LLM translation.

## Technology Stack

<p align="left">
  <a href="https://nextjs.org/" target="_blank" rel="noreferrer">
    <img src="../diagrams/nextjs-icon.png" alt="Next.js" width="40" height="40"/>
  </a>
  <a href="https://reactnative.dev/" target="_blank" rel="noreferrer">
    <img src="../diagrams/React.webp" alt="React Native" width="40" height="40"/>
  </a>
  <a href="https://go.dev/" target="_blank" rel="noreferrer">
    <img src="../diagrams/Go_Logo_Blue.svg.png" alt="Go" width="40" height="40"/>
  </a>
  <a href="https://spring.io/projects/spring-boot" target="_blank" rel="noreferrer">
    <img src="../diagrams/springboot.png" alt="Spring Boot" width="40" height="40"/>
  </a>
  <a href="https://konghq.com/kong" target="_blank" rel="noreferrer">
    <img src="../diagrams/kong-icon-icon-lg.png" alt="Kong" width="40" height="40"/>
  </a>
  <a href="https://www.postgresql.org/" target="_blank" rel="noreferrer">
    <img src="../diagrams/Postgresql_elephant.svg.png" alt="PostgreSQL" width="40" height="40"/>
  </a>
  <a href="https://www.cloudflare.com/" target="_blank" rel="noreferrer">
    <img src="../diagrams/Cloudflare_Logo.png" alt="Cloudflare" width="40" height="40"/>
  </a>
  <a href="https://vercel.com/" target="_blank" rel="noreferrer">
    <img src="../diagrams/vercel-logo.png" alt="Vercel" width="40" height="40"/>
  </a>
  <a href="https://www.nginx.com/" target="_blank" rel="noreferrer">
    <img src="../diagrams/nginx-icon-223x256-ghqr4o29.png" alt="NGINX" width="40" height="40"/>
  </a>
  <a href="https://gemini.google.com/" target="_blank" rel="noreferrer">
    <img src="../diagrams/Google_Gemini_logo.svg.png" alt="Gemini" width="40" height="40"/>
  </a>
</p>

### Frontend Technologies

- **Web Frontend:** Next.js (React)
- **Mobile App:** React Native
- **Styling:** Tailwind CSS
- **UI Features:** Dark Mode, Skeleton Views

### Backend Technologies

- **API Services:** Go, Spring Boot
- **Authentication:** Keycloak
- **User Service:** Spring Boot
- **Planning Service:** Spring Boot
- **Social Media Service:** Spring Boot
- **Notification Service:** Spring Boot (WebSocket + email alerts)

### Database

- **Relational DB:** Supabase PostgreSQL
- **Media Storage:** Cloudflare Images

### Cloud Infrastructure

- **Frontend Hosting:** Vercel (Serverless)
- **Backend Hosting:** Oracle Cloud VM + Docker Compose
- **Database Hosting:** Supabase (Serverless)
- **Image Delivery:** Cloudflare Images CDN

### DevOps and CI/CD Tools

- **CI Pipeline:** GitHub Actions
- **Orchestration & Deployment:** Docker Compose
- **Containerization:** Docker
- **Reverse Proxy (local/fallback):** Certbot + NGINX

## Deployment

- **Frontend:** Deployed on Vercel (Serverless).
- **Backend:** Microservices are containerized using Docker and orchestrated with Docker Compose on an Oracle Cloud VM.
- **Database:** Hosted on Supabase (Serverless).
- **Image CDN:** Cloudflare Images.
