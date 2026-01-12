# PeartoFinance Project Workflow Documentation

This document provides a comprehensive analysis of the PeartoFinance project architecture, including separate workflows for each component and combined flowcharts showing how all parts work together.

---

## 📋 Project Overview

**PeartoFinance** is a full-stack finance platform consisting of:
- **Backend**: Flask REST API with SQLAlchemy ORM
- **Frontend**: Next.js 15 consumer-facing application
- **Admin Panel**: Next.js administrative dashboard

## 🗺️ Complete Project Flowchart

![PeartoFinance Complete Project Flowchart](/home/bishal-regmi/.gemini/antigravity/brain/9fb29b3a-0622-4a59-aed9-b7fbfb49a714/pearto_project_flowchart_1768205953842.png)

---

## 🗂️ Project Structure Summary

```
PeartoFinance/
├── backend/                    # Flask REST API (Python)
│   ├── app.py                  # Main application entry
│   ├── config.py               # Database & app configuration
│   ├── models/                 # SQLAlchemy models (80+ tables)
│   ├── routes/                 # API blueprints (20+ modules)
│   │   └── admin/              # Admin-specific API routes
│   ├── handlers/               # Email, utility handlers
│   └── services/               # Business logic services
│
├── frontend/                   # Consumer Web App (Next.js)
│   └── src/
│       ├── app/                # Next.js App Router pages
│       ├── components/         # React components (120+)
│       ├── services/           # API service modules
│       ├── context/            # React contexts
│       └── types/              # TypeScript interfaces
│
└── frontend-admin/             # Admin Dashboard (Next.js)
    ├── app/                    # Admin pages
    │   └── admin/              # Protected admin routes
    ├── components/             # Admin UI components
    ├── context/                # Auth & country contexts
    └── lib/                    # Admin API client
```

---

# 🔙 BACKEND WORKFLOW

## Technology Stack
| Component | Technology |
|-----------|------------|
| Framework | Flask 3.x |
| ORM | SQLAlchemy |
| Database | MySQL (PyMySQL driver) |
| Auth | JWT + bcrypt |
| CORS | Flask-CORS |

## Architecture Diagram

```mermaid
flowchart TB
    subgraph Entry["🚀 Application Entry"]
        APP[["app.py"]]
    end
    
    subgraph Config["⚙️ Configuration"]
        CFG[config.py]
        ENV[.env file]
    end
    
    subgraph Database["🗄️ Database Layer"]
        DB[(MySQL Database)]
        ORM[SQLAlchemy ORM]
    end
    
    subgraph Models["📦 Domain Models"]
        USER_M[User Models]
        MARKET_M[Market Models]
        ARTICLE_M[Article Models]
        EDU_M[Education Models]
        PORT_M[Portfolio Models]
        MEDIA_M[Media Models]
        ADMIN_M[Admin Models]
        MISC_M[Misc Models]
    end
    
    subgraph Routes["🛣️ API Routes"]
        PUBLIC[Public Routes]
        AUTH_R[Auth Routes]
        ADMIN_R[Admin Routes]
    end
    
    subgraph Handlers["📨 Handlers"]
        EMAIL[Email Handler]
        NOTIFY[Notifications]
    end
    
    APP --> CFG
    CFG --> ENV
    APP --> ORM
    ORM --> DB
    
    APP --> PUBLIC
    APP --> AUTH_R
    APP --> ADMIN_R
    
    PUBLIC --> Models
    AUTH_R --> Models
    ADMIN_R --> Models
    
    Routes --> Handlers
```

## API Routes Structure

### Public API Endpoints (`/api/`)

| Blueprint | Prefix | Purpose |
|-----------|--------|---------|
| `auth_bp` | `/api/auth` | Login, signup, password reset, Google OAuth |
| `stocks_bp` | `/api/stocks` | Stock quotes, search, history |
| `crypto_bp` | `/api/crypto` | Cryptocurrency data |
| `news_bp` | `/api/news` | News articles, RSS feeds |
| `market_bp` | `/api/market` | Market indices, commodities, movers |
| `portfolio_bp` | `/api/portfolio` | User portfolios, watchlists |
| `articles_bp` | `/api/articles` | Blog articles, posts |
| `content_bp` | `/api/content` | Dynamic content pages |
| `account_bp` | `/api/account` | User account management |
| `user_bp` | `/api/user` | User profile, settings |
| `verification_bp` | `/api/user/verification` | Email/identity verification |
| `devices_bp` | `/api/devices` | Device management, push tokens |
| `tools_bp` | `/api/tools` | Financial calculators |
| `media_bp` | `/api/media` | TV channels, radio stations |
| `education_bp` | `/api/education` | Courses, quizzes, webinars |
| `ai_bp` | `/api/ai` | AI-powered features |

### Admin API Endpoints (`/api/admin/`)

| Blueprint | Purpose |
|-----------|---------|
| `dashboard_bp` | Admin dashboard stats |
| `users_bp` | User management |
| `news_bp` | News article CRUD |
| `tools_bp` | Tool configuration |
| `content_bp` | Content management |
| `team_bp` | Team member management |
| `settings_bp` | App settings |
| `audit_bp` | Audit logs |
| `marketing_bp` | Campaigns, subscribers |
| `tasks_bp` | Background tasks |
| `support_bp` | Support tickets |
| `jobs_bp` | Job listings |
| `market_bp` | Market data management |
| `media_bp` | Media asset management |
| `education_admin_bp` | Course management |

## Database Schema Overview

```mermaid
erDiagram
    User ||--o{ UserSession : has
    User ||--o{ UserDevice : has
    User ||--o{ UserPortfolio : owns
    User ||--o{ UserActivity : generates
    User ||--o{ UserAlert : receives
    
    UserPortfolio ||--o{ PortfolioHolding : contains
    UserPortfolio ||--o{ PortfolioTransaction : records
    
    User ||--o{ Watchlist : creates
    Watchlist ||--o{ WatchlistItem : contains
    
    Article ||--o{ Post : relates
    PostCategory ||--o{ Post : categorizes
    
    Course ||--o{ CourseModule : contains
    Course ||--o{ Quiz : has
    Quiz ||--o{ QuizQuestion : contains
    QuizQuestion ||--o{ QuizAnswer : has
    
    MarketData ||--o{ StockPriceHistory : tracks
    MarketData ||--o{ EarningsCalendar : schedules
    MarketData ||--o{ AnalystRecommendation : has
```

## Backend Data Flow

```mermaid
sequenceDiagram
    participant Client as Client App
    participant Flask as Flask API
    participant Decorator as Auth Decorator
    participant Route as Route Handler
    participant Model as SQLAlchemy Model
    participant DB as MySQL Database
    participant Handler as Email Handler
    
    Client->>Flask: HTTP Request
    Flask->>Decorator: Validate JWT Token
    alt Token Valid
        Decorator->>Route: Execute Handler
        Route->>Model: Query/Mutate Data
        Model->>DB: SQL Query
        DB-->>Model: Result
        Model-->>Route: Domain Objects
        opt Email Required
            Route->>Handler: Send Email
        end
        Route-->>Flask: JSON Response
    else Token Invalid
        Decorator-->>Flask: 401 Unauthorized
    end
    Flask-->>Client: HTTP Response
```

---

# 🖥️ FRONTEND WORKFLOW

## Technology Stack
| Component | Technology |
|-----------|------------|
| Framework | Next.js 15 (App Router) |
| Language | TypeScript |
| Styling | Tailwind CSS |
| Auth | Firebase + JWT |
| State | React Context |
| i18n | Custom i18n solution |

## Component Architecture

```mermaid
flowchart TB
    subgraph Layout["📐 Layout Layer"]
        ROOT[RootLayout]
        NAV[Navigation]
        FOOT[Footer]
    end
    
    subgraph Context["🌐 Context Providers"]
        AUTH[AuthContext]
        THEME[ThemeContext]
        I18N[i18n Provider]
    end
    
    subgraph Pages["📄 Page Routes"]
        HOME["/"]
        MARKETS["/markets"]
        NEWS["/news"]
        TOOLS["/tools"]
        LEARN["/learn"]
        SPORTS["/sports"]
        TV["/tv"]
        RADIO["/radio"]
        PROFILE["/profile"]
    end
    
    subgraph Services["🔌 API Services"]
        API[api.ts]
        AUTH_S[authService]
        MKT_S[marketService]
        NEWS_S[newsService]
        PORT_S[portfolioService]
        TOOL_S[toolsService]
        USER_S[userService]
    end
    
    subgraph Components["🧩 UI Components"]
        HOME_C[Home Components]
        MKT_C[Market Components]
        NEWS_C[News Components]
        TOOL_C[Tool Components]
        LEARN_C[Learn Components]
        MEDIA_C[TV/Radio Components]
    end
    
    ROOT --> Context
    Context --> Pages
    Pages --> Components
    Components --> Services
    Services --> API
```

## Page Routes Structure

| Route | Description | Components |
|-------|-------------|------------|
| `/` | Homepage with market overview | DashboardWidgets, MarketOverview |
| `/markets` | Full market data view | StockTable, CryptoList, Indices |
| `/news` | News feed and articles | NewsCard, ArticleList |
| `/news/[slug]` | Individual article | ArticleContent, RelatedNews |
| `/tools` | Financial calculators grid | ToolsGrid, ToolCard |
| `/tools/[slug]` | Individual calculator | Calculator components (60+) |
| `/learn` | Educational content | CourseList, QuizComponent |
| `/sports` | Sports events | EventCard, EventModal |
| `/tv` | Live TV streams | TVPlayer, ChannelList |
| `/radio` | Radio stations | RadioPlayer, StationList |
| `/profile` | User dashboard | ProfileStats, AccountSettings |
| `/login` | Authentication | LoginForm |
| `/signup` | Registration | SignupForm |

## Frontend Services Flow

```mermaid
flowchart LR
    subgraph UI["User Interface"]
        PAGE[Page Component]
        COMP[UI Component]
    end
    
    subgraph Services["Service Layer"]
        SVC[Domain Service]
        API[api.ts Core]
    end
    
    subgraph Auth["Authentication"]
        TOKEN[JWT Token]
        STORAGE[LocalStorage]
        HEADERS[Request Headers]
    end
    
    subgraph External["External"]
        BACKEND[Flask API]
    end
    
    PAGE --> COMP
    COMP --> SVC
    SVC --> API
    API --> STORAGE
    STORAGE --> TOKEN
    TOKEN --> HEADERS
    HEADERS --> API
    API --> BACKEND
    BACKEND --> API
    API --> SVC
    SVC --> COMP
```

## Authentication Flow (Frontend)

```mermaid
sequenceDiagram
    participant User
    participant UI as Login Page
    participant Firebase
    participant AuthService
    participant Backend
    participant Storage as LocalStorage
    
    alt Email/Password Login
        User->>UI: Enter credentials
        UI->>AuthService: login(email, password)
        AuthService->>Backend: POST /api/auth/login
        Backend-->>AuthService: JWT Token + User
        AuthService->>Storage: Save token & user
        Storage-->>UI: Redirect to home
    else Google OAuth
        User->>UI: Click Google Login
        UI->>Firebase: signInWithPopup()
        Firebase-->>UI: Firebase User
        UI->>AuthService: googleSignIn(firebaseData)
        AuthService->>Backend: POST /api/auth/google-signin
        Backend-->>AuthService: JWT Token + User
        AuthService->>Storage: Save token & user
        Storage-->>UI: Redirect to home
    end
```

---

# 🔧 ADMIN PANEL WORKFLOW

## Technology Stack
| Component | Technology |
|-----------|------------|
| Framework | Next.js 15 (App Router) |
| Language | TypeScript |
| Auth | Secret-based (X-Admin-Secret) |
| State | React Context |
| API Client | Custom adminApi |

## Admin Panel Architecture

```mermaid
flowchart TB
    subgraph Auth["🔐 Authentication"]
        LOGIN[Login Page]
        AUTH_CTX[AdminAuthContext]
        SECRET[Admin Secret]
    end
    
    subgraph Layout["📐 Admin Layout"]
        SIDEBAR[Sidebar Navigation]
        HEADER[Admin Header]
        CONTENT[Content Area]
    end
    
    subgraph Pages["📄 Admin Pages"]
        DASH[Dashboard]
        USERS[Users Management]
        NEWS_P[News Management]
        TOOLS_P[Tools Config]
        MARKET_P[Market Data]
        CONTENT_P[Content Pages]
        SETTINGS[Settings]
        AUDIT[Audit Logs]
    end
    
    subgraph API["🔌 Admin API"]
        ADMIN_API[adminApi.ts]
        COUNTRY_CTX[AdminCountryContext]
    end
    
    LOGIN --> AUTH_CTX
    AUTH_CTX --> SECRET
    AUTH_CTX --> Layout
    Layout --> Pages
    Pages --> ADMIN_API
    ADMIN_API --> COUNTRY_CTX
```

## Admin Page Routes

| Route | Purpose | Features |
|-------|---------|----------|
| `/admin` | Dashboard | Stats, activity feed |
| `/admin/users` | User management | CRUD, verification |
| `/admin/news` | News articles | Create, edit, publish |
| `/admin/tools` | Tool configuration | Enable/disable, settings |
| `/admin/market` | Market data | Indices, stocks, crypto |
| `/admin/content` | Content pages | Dynamic page management |
| `/admin/settings` | App settings | Global configuration |
| `/admin/team` | Team members | Staff management |
| `/admin/jobs` | Job listings | Career page management |
| `/admin/audit` | Audit logs | Activity tracking |
| `/admin/subscribers` | Email subscribers | Newsletter management |
| `/admin/messages` | Support messages | Customer support |
| `/admin/tv` | TV Channels | Live stream management |
| `/admin/radio` | Radio Stations | Audio stream management |
| `/admin/tasks` | Background tasks | Job scheduling |

## Admin Authentication Flow

```mermaid
sequenceDiagram
    participant Admin as Admin User
    participant UI as Login Page
    participant Context as AdminAuthContext
    participant API as adminApi
    participant Backend as Flask Backend
    participant Storage as LocalStorage
    
    Admin->>UI: Enter admin secret
    UI->>Context: login(secret)
    Context->>API: setSecret(secret)
    API->>Storage: Store secret
    API->>Backend: GET /api/admin/stats
    Note over API,Backend: X-Admin-Secret header
    alt Valid Secret
        Backend-->>API: 200 OK + Stats
        API-->>Context: Success
        Context-->>UI: Authenticated
        UI->>UI: Redirect to dashboard
    else Invalid Secret
        Backend-->>API: 401 Unauthorized
        API-->>Context: Failure
        Context->>Storage: Clear secret
        Context-->>UI: Show error
    end
```

---

# 🔄 COMBINED SYSTEM WORKFLOW

## Complete System Architecture

```mermaid
flowchart TB
    subgraph Users["👥 Users"]
        CONSUMER[Consumer User]
        ADMIN_USER[Admin User]
    end
    
    subgraph Frontend["🖥️ Consumer Frontend"]
        direction TB
        NEXT_FE[Next.js App]
        FE_SERVICES[Services Layer]
        FIREBASE[Firebase Auth]
    end
    
    subgraph Admin["🔧 Admin Panel"]
        direction TB
        NEXT_ADMIN[Next.js Admin]
        ADMIN_API[Admin API Client]
    end
    
    subgraph Backend["⚡ Flask Backend"]
        direction TB
        FLASK[Flask Application]
        PUBLIC_API[Public API Routes]
        ADMIN_ROUTES[Admin API Routes]
        MODELS[SQLAlchemy Models]
        HANDLERS[Email Handlers]
    end
    
    subgraph Database["🗄️ Database"]
        MYSQL[(MySQL)]
    end
    
    subgraph External["🌐 External Services"]
        MAIL[Email Service]
        YFINANCE[yfinance API]
    end
    
    CONSUMER --> NEXT_FE
    NEXT_FE --> FE_SERVICES
    FE_SERVICES --> FIREBASE
    FE_SERVICES -->|JWT Auth| PUBLIC_API
    
    ADMIN_USER --> NEXT_ADMIN
    NEXT_ADMIN --> ADMIN_API
    ADMIN_API -->|Secret Auth| ADMIN_ROUTES
    
    PUBLIC_API --> MODELS
    ADMIN_ROUTES --> MODELS
    MODELS --> MYSQL
    
    FLASK --> HANDLERS
    HANDLERS --> MAIL
    PUBLIC_API --> YFINANCE
```

## Complete Request Flow Diagram

```mermaid
sequenceDiagram
    participant User as End User
    participant Frontend as Next.js Frontend
    participant Firebase
    participant API as Flask API
    participant Auth as Auth Middleware
    participant DB as MySQL Database
    participant Email as Email Service
    
    Note over User,Email: User Registration Flow
    User->>Frontend: Sign up with email
    Frontend->>API: POST /api/auth/signup
    API->>Auth: Hash password
    Auth->>DB: Create user record
    DB-->>Auth: User created
    Auth->>Email: Send welcome email
    Email-->>User: Welcome email received
    API-->>Frontend: Success response
    Frontend-->>User: Redirect to login
    
    Note over User,Email: User Login Flow
    User->>Frontend: Login with credentials
    Frontend->>API: POST /api/auth/login
    API->>DB: Verify credentials
    DB-->>API: User data
    API->>Email: Send login notification
    API-->>Frontend: JWT token + user
    Frontend->>Frontend: Store in localStorage
    
    Note over User,Email: Data Access Flow
    User->>Frontend: View market data
    Frontend->>API: GET /api/market/overview
    Note over Frontend,API: Authorization: Bearer {token}
    API->>Auth: Validate JWT
    Auth->>DB: Query market data
    DB-->>Auth: Market data
    Auth-->>API: Formatted response
    API-->>Frontend: JSON response
    Frontend-->>User: Display data
```

## Data Flow Between Components

```mermaid
flowchart LR
    subgraph Consumer["Consumer App"]
        C_UI[UI Components]
        C_SVC[Services]
        C_AUTH[Auth Context]
    end
    
    subgraph Backend["Flask API"]
        B_PUBLIC[Public Endpoints]
        B_ADMIN[Admin Endpoints]
        B_AUTH[Auth Decorator]
        B_MODELS[Models]
    end
    
    subgraph Admin["Admin Panel"]
        A_UI[Admin UI]
        A_API[Admin API]
        A_CTX[Auth Context]
    end
    
    subgraph DB["Database"]
        MYSQL[(MySQL)]
    end
    
    C_UI --> C_SVC
    C_SVC -->|JWT Token| B_PUBLIC
    C_AUTH --> C_SVC
    
    A_UI --> A_API
    A_API -->|Admin Secret| B_ADMIN
    A_CTX --> A_API
    
    B_PUBLIC --> B_AUTH
    B_ADMIN --> B_AUTH
    B_AUTH --> B_MODELS
    B_MODELS --> MYSQL
```

---

## 📊 Key Features Mapping

| Feature | Frontend | Backend | Admin |
|---------|----------|---------|-------|
| User Auth | ✅ Login/Signup/OAuth | ✅ JWT Generation | ✅ Secret-based |
| Market Data | ✅ Display | ✅ CRUD + External APIs | ✅ Management |
| News | ✅ Read | ✅ CRUD | ✅ Create/Edit |
| Tools | ✅ 60+ Calculators | ✅ Config Storage | ✅ Enable/Disable |
| Portfolio | ✅ View/Manage | ✅ CRUD | ❌ |
| Education | ✅ Courses/Quizzes | ✅ CRUD | ✅ Management |
| Media (TV/Radio) | ✅ Playback | ✅ Stream URLs | ✅ Management |
| Sports | ✅ Event Display | ✅ CRUD | ✅ Management |

---

## 🚀 Deployment Architecture

```mermaid
flowchart TB
    subgraph Internet["🌐 Internet"]
        USERS[Users]
    end
    
    subgraph CDN["CDN / Load Balancer"]
        LB[Load Balancer]
    end
    
    subgraph Frontend_Deploy["Frontend Deployment"]
        VERCEL[Vercel / Next.js Host]
    end
    
    subgraph Backend_Deploy["Backend Deployment"]
        FLASK_SRV[Flask Server :5000]
        GUNICORN[Gunicorn Workers]
    end
    
    subgraph Database_Deploy["Database"]
        MYSQL_SRV[(MySQL Server :3306)]
    end
    
    USERS --> LB
    LB --> VERCEL
    LB --> FLASK_SRV
    VERCEL -->|API Calls| FLASK_SRV
    FLASK_SRV --> GUNICORN
    GUNICORN --> MYSQL_SRV
```

---

## 📝 Summary

### Backend
- **20+ API blueprints** serving both public and admin endpoints
- **80+ database models** covering users, market data, content, and more
- **JWT-based authentication** for public APIs
- **Secret-based authentication** for admin APIs

### Frontend
- **12+ page routes** for consumer features
- **120+ reusable components** with modular organization
- **10 service modules** abstracting API calls
- **Firebase integration** for OAuth authentication

### Admin Panel
- **15 admin sections** for comprehensive management
- **Country-based filtering** via context
- **Real-time stats** on dashboard
- **Audit logging** for all admin actions

---

> **Generated**: January 12, 2026  
> **Project**: PeartoFinance
