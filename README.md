# Tài liệu kỹ thuật: Ứng dụng học tiếng Anh với Spaced Repetition

**Ngày cập nhật: 2025-08-25**

## Mục lục
1. [Tổng quan](#tổng-quan)
2. [Mô tả chức năng và yêu cầu chức năng](#mô-tả-chức-năng-và-yêu-cầu-chức-năng)
3. [Luồng dữ liệu](#luồng-dữ-liệu)
4. [Kỹ thuật sử dụng](#kỹ-thuật-sử-dụng)
5. [Công nghệ sử dụng](#công-nghệ-sử-dụng)
6. [Mô tả chi tiết các thành phần](#mô-tả-chi-tiết-các-thành-phần)
   - [API Backend](#api-backend)
   - [Mobile App](#mobile-app)
   - [Chrome Extension](#chrome-extension)
7. [Yêu cầu phi chức năng](#yêu-cầu-phi-chức-năng)
8. [Kiến trúc hệ thống](#kiến-trúc-hệ-thống)
9. [Đặc tả API](#đặc-tả-api)
10. [Cấu trúc dự án](#cấu-trúc-dự-án)
    - [Cấu trúc thư mục](#cấu-trúc-thư-mục)
    - [Mô tả các thành phần chính](#mô-tả-các-thành-phần-chính)
    - [Quy ước đặt tên](#quy-ước-đặt-tên)
11. [Các biểu đồ](#các-biểu-đồ)
12. [Các Use Case](#các-use-case)

## Tổng quan

Ứng dụng học tiếng Anh với Spaced Repetition là một hệ thống toàn diện giúp người dùng học và ghi nhớ từ vựng tiếng Anh hiệu quả thông qua phương pháp lặp lại ngắt quãng (Spaced Repetition). Hệ thống bao gồm ứng dụng di động, API backend và tiện ích mở rộng Chrome để cung cấp trải nghiệm học tập liền mạch trên nhiều nền tảng.

## Mô tả chức năng và yêu cầu chức năng

### Quản lý người dùng
- **FR-01**: Đăng ký tài khoản sử dụng email/mật khẩu hoặc tài khoản mạng xã hội
- **FR-02**: Đăng nhập/đăng xuất khỏi hệ thống
- **FR-03**: Khôi phục mật khẩu qua email
- **FR-04**: Quản lý thông tin cá nhân và cài đặt học tập
- **FR-05**: Đồng bộ hóa dữ liệu học tập giữa các thiết bị

### Quản lý từ vựng
- **FR-06**: Tạo và quản lý bộ thẻ từ vựng (flashcard decks)
- **FR-07**: Thêm từ mới vào hệ thống (thủ công hoặc tự động từ extension)
- **FR-08**: Tổ chức từ vựng theo chủ đề hoặc cấp độ
- **FR-09**: Nhập/xuất từ vựng từ các định dạng phổ biến (CSV, Excel)
- **FR-10**: Chia sẻ và sao chép bộ thẻ từ vựng từ người dùng khác

### Học tập với Spaced Repetition
- **FR-11**: Tạo các phiên học tập dựa trên thuật toán Spaced Repetition
- **FR-12**: Hiển thị thẻ từ vựng theo lịch trình tối ưu
- **FR-13**: Đánh giá khả năng nhớ từ và điều chỉnh khoảng thời gian ôn tập
- **FR-14**: Theo dõi tiến độ học tập và số liệu thống kê
- **FR-15**: Nhắc nhở lịch học hàng ngày

### Chrome Extension
- **FR-16**: Chọn và lưu từ vựng từ các trang web
- **FR-17**: Dịch nhanh nội dung được chọn
- **FR-18**: Thêm từ vào bộ thẻ từ vựng hiện có
- **FR-19**: Xem nghĩa và ví dụ ngay lập tức
- **FR-20**: Đồng bộ hóa dữ liệu với tài khoản người dùng

### Tính năng bổ sung
- **FR-21**: Phát âm từ vựng
- **FR-22**: Ví dụ và hình ảnh minh họa
- **FR-23**: Trò chơi học từ vựng
- **FR-24**: Bảng xếp hạng và thành tích
- **FR-25**: Các bài kiểm tra đánh giá năng lực

## Luồng dữ liệu

### Luồng dữ liệu chính
1. **Thu thập từ vựng**
   - Người dùng nhập thủ công hoặc sử dụng Chrome Extension để thu thập từ vựng từ các trang web
   - Dữ liệu được gửi đến API Backend để lưu trữ trong cơ sở dữ liệu
   - API lấy thông tin bổ sung (phát âm, nghĩa, ví dụ) từ các dịch vụ bên thứ ba nếu cần

2. **Tạo lập lịch học tập**
   - Thuật toán Spaced Repetition xử lý dữ liệu từ vựng và lịch sử học tập
   - Tạo ra lịch ôn tập tối ưu cho từng từ vựng
   - Dữ liệu lịch học được lưu trữ trong cơ sở dữ liệu

3. **Quá trình học tập**
   - Ứng dụng di động lấy dữ liệu học tập từ API
   - Người dùng tương tác với thẻ học (xem, trả lời, đánh giá mức độ nhớ)
   - Kết quả học tập được gửi lại API để cập nhật thuật toán
   - Thuật toán điều chỉnh lịch ôn tập dựa trên phản hồi

4. **Phân tích và báo cáo**
   - Dữ liệu học tập được tổng hợp và phân tích
   - Tạo báo cáo tiến độ, thống kê học tập
   - Hiển thị cho người dùng qua giao diện ứng dụng

### Diagram mô tả luồng dữ liệu
```
[Chrome Extension] ---(Thu thập từ)---> [API Backend] <---(Đồng bộ)---> [Mobile App]
         |                                   |                              |
         |                                   v                              |
         +-----------> [Dictionary API] <----+----> [Thuật toán SRS] <-----+
                                             |                              |
                                             v                              v
                                     [Cơ sở dữ liệu] <--------> [Phân tích & Báo cáo]
```

## Kỹ thuật sử dụng

1. **Spaced Repetition Algorithm**
   - Thuật toán SM-2 (SuperMemo 2) được tùy chỉnh
   - Kỹ thuật xác định khoảng thời gian lặp lại dựa trên đường cong quên (forgetting curve)
   - Công thức tính toán khoảng thời gian: I(n+1) = I(n) * EF
   - Hệ số dễ nhớ (Ease Factor - EF) được điều chỉnh dựa trên phản hồi người dùng

2. **Natural Language Processing (NLP)**
   - Phân tích văn bản để trích xuất từ quan trọng
   - Xác định cấp độ từ vựng (A1, A2, B1, B2, C1, C2)
   - Gợi ý từ liên quan và collocation

3. **Progressive Web App (PWA)**
   - Ứng dụng web tiến bộ cho trải nghiệm đa nền tảng
   - Service workers cho khả năng hoạt động offline

4. **Đồng bộ hóa đa thiết bị**
   - Đồng bộ hóa theo thời gian thực với WebSocket
   - Đồng bộ hóa trạng thái offline với cơ chế queue và conflict resolution

5. **Gamification**
   - Hệ thống điểm thưởng và cấp độ
   - Thành tích và bảng xếp hạng
   - Streak và nhắc nhở học tập

## Công nghệ sử dụng

### Frontend
- **React Native** - Framework cho ứng dụng di động đa nền tảng
- **Redux** - Quản lý trạng thái
- **TypeScript** - Ngôn ngữ lập trình với kiểu dữ liệu tĩnh
- **React Navigation** - Điều hướng trong ứng dụng
- **Styled Components** - CSS-in-JS styling
- **Jest & React Testing Library** - Kiểm thử
- **i18next** - Đa ngôn ngữ

### Chrome Extension
- **JavaScript/TypeScript** - Ngôn ngữ lập trình
- **Chrome Extension API** - Tương tác với trình duyệt
- **Manifest V3** - Phiên bản mới nhất của Chrome Extension
- **Webpack** - Bundle module

### Backend
- **Node.js** - Môi trường runtime
- **Express.js** - Framework web
- **MongoDB** - Cơ sở dữ liệu NoSQL
- **Mongoose** - ODM (Object Data Modeling)
- **Redis** - Bộ nhớ đệm và queue
- **GraphQL** - API query language
- **Apollo Server** - GraphQL server
- **JWT** - Xác thực và phân quyền
- **Socket.IO** - Giao tiếp thời gian thực

### DevOps & Infrastructure
- **Docker** - Containerization
- **Kubernetes** - Orchestration
- **AWS/GCP** - Cloud hosting
- **CI/CD** - GitHub Actions/Jenkins
- **Prometheus & Grafana** - Monitoring
- **ELK Stack** - Logging
- **Terraform** - Infrastructure as Code

### Công cụ phát triển
- **Git & GitHub** - Version control
- **ESLint & Prettier** - Linting và formatting
- **Storybook** - UI component development
- **Figma** - UI/UX design
- **Postman** - API testing

## Mô tả chi tiết các thành phần

### API Backend

Backend của hệ thống cung cấp API RESTful và GraphQL để hỗ trợ các chức năng của ứng dụng di động và extension Chrome.

#### Chức năng chính:
- Xác thực và quản lý người dùng
- Quản lý dữ liệu từ vựng và thẻ học
- Thực thi thuật toán Spaced Repetition
- Đồng bộ hóa dữ liệu giữa các thiết bị
- Tích hợp với API từ điển bên thứ ba
- Phân tích dữ liệu học tập và tạo báo cáo
- Quản lý thông báo và nhắc nhở

#### Kiến trúc:
- Microservice architecture
- API Gateway
- Authentication Service
- Vocabulary Service
- Spaced Repetition Engine
- Statistics Service
- Notification Service
- Integration Service (third-party APIs)

### Mobile App

Ứng dụng di động là giao diện chính cho người dùng học từ vựng sử dụng phương pháp Spaced Repetition.

#### Chức năng chính:
- Đăng ký, đăng nhập và quản lý tài khoản
- Quản lý bộ thẻ từ vựng
- Phiên học tập với Spaced Repetition
- Theo dõi tiến độ và thống kê học tập
- Tùy chỉnh cài đặt học tập
- Chế độ học offline
- Thông báo nhắc nhở

#### Thành phần UI:
- Màn hình đăng nhập/đăng ký
- Trang chính (Dashboard)
- Màn hình quản lý bộ thẻ
- Giao diện học tập (Flashcard)
- Báo cáo và thống kê
- Màn hình cài đặt
- Thư viện từ vựng

### Chrome Extension

Tiện ích mở rộng Chrome giúp người dùng thu thập từ vựng mới khi duyệt web và đồng bộ hóa với tài khoản học tập.

#### Chức năng chính:
- Chọn và dịch từ/cụm từ trên trang web
- Thêm từ vựng vào bộ thẻ học
- Xem nhanh định nghĩa và ví dụ
- Phát âm từ vựng
- Đồng bộ hóa với tài khoản người dùng
- Hoạt động cả trong chế độ offline

#### Thành phần:
- Background script
- Content script
- Popup UI
- Options page
- Storage management

## Yêu cầu phi chức năng

### Hiệu suất
- **NFR-01**: Thời gian phản hồi API không quá 300ms cho 95% các request
- **NFR-02**: Ứng dụng phải tải và sẵn sàng sử dụng trong vòng 2 giây
- **NFR-03**: Thuật toán SRS phải xử lý tối thiểu 10,000 thẻ/giây trên server
- **NFR-04**: Chrome Extension không được làm giảm hiệu suất duyệt web quá 5%

### Khả năng mở rộng
- **NFR-05**: Hệ thống phải hỗ trợ tối thiểu 1 triệu người dùng đồng thời
- **NFR-06**: Kiến trúc phải cho phép thêm ngôn ngữ mới dễ dàng
- **NFR-07**: Cơ sở dữ liệu phải xử lý được ít nhất 100TB dữ liệu

### Bảo mật
- **NFR-08**: Tuân thủ GDPR và các quy định về bảo vệ dữ liệu
- **NFR-09**: Mã hóa dữ liệu nhạy cảm sử dụng AES-256
- **NFR-10**: Xác thực hai lớp cho tài khoản người dùng
- **NFR-11**: Kiểm tra bảo mật (security audit) hàng quý

### Độ tin cậy
- **NFR-12**: Uptime tối thiểu 99.9% (downtime dưới 8.76 giờ/năm)
- **NFR-13**: Backup dữ liệu hàng ngày với khả năng phục hồi nhanh
- **NFR-14**: Khả năng hoạt động offline cho các tính năng cơ bản
- **NFR-15**: Recovery Point Objective (RPO) không quá 1 giờ

### Khả năng sử dụng
- **NFR-16**: Tuân thủ WCAG 2.1 AA accessibility standards
- **NFR-17**: Hỗ trợ đa ngôn ngữ giao diện (ít nhất 10 ngôn ngữ)
- **NFR-18**: Thời gian học để sử dụng thành thạo ứng dụng không quá 15 phút
- **NFR-19**: Thiết kế phản hồi (responsive) cho tất cả kích thước màn hình

## Kiến trúc hệ thống

### Kiến trúc tổng thể
Hệ thống được xây dựng theo mô hình microservices với các thành phần sau:

```
[Client Layer]
    |
    ├── Mobile App (iOS/Android)
    ├── Web App
    └── Chrome Extension
    |
    v
[API Gateway] --- [Authentication & Authorization]
    |
    ├── [User Service] --- [User Database]
    ├── [Vocabulary Service] --- [Vocabulary Database]
    ├── [SRS Engine] --- [Learning Data Store]
    ├── [Statistics Service] --- [Analytics Database]
    ├── [Notification Service]
    └── [Integration Service] --- [Third-party APIs]
           |
           └── [Dictionary APIs, Translation Services, etc.]
```

### Tầng dữ liệu
- **User Database**: MongoDB - Lưu trữ thông tin người dùng, cài đặt
- **Vocabulary Database**: MongoDB - Lưu trữ từ vựng, bộ thẻ
- **Learning Data Store**: MongoDB & Redis - Dữ liệu học tập và lịch SRS
- **Analytics Database**: PostgreSQL - Dữ liệu phân tích và báo cáo
- **Cache Layer**: Redis - Bộ nhớ đệm để tăng hiệu suất
- **File Storage**: AWS S3 - Lưu trữ âm thanh, hình ảnh

### Tầng dịch vụ
- **API Gateway**: Điểm vào duy nhất cho tất cả các request
- **Authentication Service**: Xử lý đăng nhập/đăng ký và bảo mật
- **User Service**: Quản lý thông tin người dùng
- **Vocabulary Service**: Quản lý từ vựng và bộ thẻ
- **SRS Engine**: Thuật toán Spaced Repetition
- **Statistics Service**: Phân tích dữ liệu học tập
- **Notification Service**: Gửi thông báo và nhắc nhở
- **Integration Service**: Kết nối với dịch vụ bên thứ ba

### Luồng dữ liệu
1. Người dùng tương tác với ứng dụng di động hoặc extension
2. Request được gửi đến API Gateway
3. API Gateway định tuyến request đến microservice thích hợp
4. Microservice xử lý logic nghiệp vụ và tương tác với cơ sở dữ liệu
5. Kết quả được trả về qua API Gateway
6. Client hiển thị kết quả cho người dùng

## Đặc tả API

### RESTful API

#### Authentication APIs
```
POST /api/v1/auth/register
POST /api/v1/auth/login
POST /api/v1/auth/refresh-token
POST /api/v1/auth/forgot-password
POST /api/v1/auth/reset-password
GET /api/v1/auth/me
```

#### User APIs
```
GET /api/v1/users/:id
PUT /api/v1/users/:id
PATCH /api/v1/users/:id/settings
GET /api/v1/users/:id/statistics
```

#### Vocabulary APIs
```
GET /api/v1/vocabulary
POST /api/v1/vocabulary
GET /api/v1/vocabulary/:id
PUT /api/v1/vocabulary/:id
DELETE /api/v1/vocabulary/:id
```

#### Deck APIs
```
GET /api/v1/decks
POST /api/v1/decks
GET /api/v1/decks/:id
PUT /api/v1/decks/:id
DELETE /api/v1/decks/:id
GET /api/v1/decks/:id/cards
POST /api/v1/decks/:id/cards
```

#### Flashcard APIs
```
GET /api/v1/cards
POST /api/v1/cards
GET /api/v1/cards/:id
PUT /api/v1/cards/:id
DELETE /api/v1/cards/:id
POST /api/v1/cards/:id/review
```

#### Study Session APIs
```
GET /api/v1/study-sessions
POST /api/v1/study-sessions
GET /api/v1/study-sessions/today
GET /api/v1/study-sessions/:id
PUT /api/v1/study-sessions/:id
```

#### Statistics APIs
```
GET /api/v1/statistics/overview
GET /api/v1/statistics/daily
GET /api/v1/statistics/progress
GET /api/v1/statistics/decks/:id
```

### GraphQL API

```graphql
type User {
  id: ID!
  email: String!
  name: String
  settings: UserSettings
  statistics: UserStatistics
  createdAt: DateTime!
  updatedAt: DateTime!
}

type Vocabulary {
  id: ID!
  word: String!
  translation: String!
  definition: String
  examples: [String]
  pronunciation: String
  partOfSpeech: String
  level: String
  tags: [String]
  createdAt: DateTime!
  updatedAt: DateTime!
}

type Deck {
  id: ID!
  name: String!
  description: String
  owner: User!
  isPublic: Boolean!
  cardCount: Int!
  cards: [Card]
  createdAt: DateTime!
  updatedAt: DateTime!
}

type Card {
  id: ID!
  vocabulary: Vocabulary!
  deck: Deck!
  dueDate: DateTime
  interval: Int
  easeFactor: Float
  repetitions: Int
  reviewHistory: [Review]
  createdAt: DateTime!
  updatedAt: DateTime!
}

type Review {
  id: ID!
  card: Card!
  quality: Int!
  timestamp: DateTime!
}

type StudySession {
  id: ID!
  user: User!
  cards: [Card]!
  completedCards: Int!
  startedAt: DateTime!
  completedAt: DateTime
}

type Query {
  me: User
  vocabulary(id: ID!): Vocabulary
  vocabularyList(search: String, limit: Int, offset: Int): [Vocabulary]
  deck(id: ID!): Deck
  decks(ownerId: ID, isPublic: Boolean, limit: Int, offset: Int): [Deck]
  card(id: ID!): Card
  dailyCards: [Card]
  studySession(id: ID!): StudySession
  statistics(period: String): Statistics
}

type Mutation {
  register(email: String!, password: String!, name: String): AuthPayload
  login(email: String!, password: String!): AuthPayload
  createVocabulary(input: VocabularyInput!): Vocabulary
  updateVocabulary(id: ID!, input: VocabularyInput!): Vocabulary
  deleteVocabulary(id: ID!): Boolean
  createDeck(input: DeckInput!): Deck
  updateDeck(id: ID!, input: DeckInput!): Deck
  deleteDeck(id: ID!): Boolean
  addCardToDeck(deckId: ID!, vocabularyId: ID!): Card
  reviewCard(id: ID!, quality: Int!): Card
  createStudySession(deckId: ID!): StudySession
  completeStudySession(id: ID!): StudySession
}
```

## Cấu trúc dự án

### Cấu trúc thư mục

#### Backend
```
/backend
├── /src
│   ├── /api            # API endpoints
│   │   ├── /v1         # API version 1
│   │   └── /graphql    # GraphQL schema và resolvers
│   ├── /config         # Cấu hình ứng dụng
│   ├── /constants      # Hằng số
│   ├── /controllers    # Controller functions
│   ├── /middlewares    # Express middlewares
│   ├── /models         # MongoDB models
│   ├── /services       # Business logic
│   │   ├── /auth       # Authentication logic
│   │   ├── /users      # User management
│   │   ├── /vocabulary # Vocabulary management
│   │   ├── /srs        # Spaced Repetition Algorithm
│   │   └── /stats      # Statistics and analytics
│   ├── /utils          # Utility functions
│   ├── /validators     # Input validation
│   └── index.js        # Entry point
├── /tests              # Unit and integration tests
├── /docs               # API documentation
├── package.json
└── README.md
```

#### Mobile App
```
/mobile
├── /src
│   ├── /assets         # Images, fonts, etc.
│   ├── /components     # Reusable UI components
│   │   ├── /common     # Common UI elements
│   │   ├── /cards      # Flashcard components
│   │   └── /study      # Study session components
│   ├── /config         # App configuration
│   ├── /constants      # Constants and enums
│   ├── /hooks          # Custom React hooks
│   ├── /navigation     # Navigation setup
│   ├── /screens        # App screens
│   │   ├── /auth       # Authentication screens
│   │   ├── /decks      # Deck management screens
│   │   ├── /study      # Study session screens
│   │   ├── /profile    # User profile screens
│   │   └── /stats      # Statistics screens
│   ├── /services       # API services
│   ├── /store          # Redux store
│   │   ├── /actions    # Redux actions
│   │   ├── /reducers   # Redux reducers
│   │   └── /sagas      # Redux sagas
│   ├── /types          # TypeScript type definitions
│   └── /utils          # Utility functions
├── /e2e                # E2E tests
├── /tests              # Unit tests
├── App.tsx             # App entry point
└── package.json
```

#### Chrome Extension
```
/extension
├── /src
│   ├── /assets         # Images, icons, etc.
│   ├── /background     # Background script
│   ├── /content        # Content scripts
│   ├── /popup          # Popup UI
│   │   ├── /components # UI components
│   │   ├── /hooks      # React hooks
│   │   └── /services   # API services
│   ├── /options        # Options page
│   ├── /services       # Shared services
│   ├── /storage        # Extension storage
│   └── /utils          # Utility functions
├── /dist               # Build output
├── manifest.json       # Extension manifest
├── webpack.config.js   # Webpack config
└── package.json
```

### Mô tả các thành phần chính

#### Backend Components

1. **API Controllers**: Xử lý HTTP requests và responses
2. **Services**: Chứa business logic cho các chức năng
3. **Models**: Định nghĩa schema và tương tác với database
4. **Middlewares**: Xử lý authentication, error handling, logging
5. **SRS Engine**: Thuật toán Spaced Repetition
6. **Utils**: Helper functions và shared utilities

#### Mobile App Components

1. **Screens**: UI screens của ứng dụng
2. **Components**: Reusable UI components
3. **Navigation**: Quản lý điều hướng trong ứng dụng
4. **Services**: Tương tác với API backend
5. **Store**: Quản lý state với Redux
6. **Hooks**: Custom React hooks
7. **Utils**: Helper functions và shared utilities

#### Chrome Extension Components

1. **Background Script**: Xử lý các tác vụ nền
2. **Content Script**: Tương tác với trang web
3. **Popup UI**: Giao diện popup của extension
4. **Options Page**: Trang cài đặt extension
5. **Storage Service**: Quản lý lưu trữ local
6. **API Service**: Tương tác với backend

### Quy ước đặt tên

#### Chung
- **Tên file**: Sử dụng kebab-case (`user-service.js`)
- **Hằng số**: Sử dụng UPPER_SNAKE_CASE (`MAX_RETRY_COUNT`)
- **Biến**: Sử dụng camelCase (`userName`)
- **Kiểu dữ liệu/Interface**: PascalCase (`UserInterface`)

#### Backend
- **Classes**: Sử dụng PascalCase (`UserService`)
- **Functions**: Sử dụng camelCase (`getUserById`)
- **API Routes**: Sử dụng kebab-case (`/api/user-profile`)
- **Database collections**: Sử dụng PascalCase (`Users`, `Vocabularies`)

#### Frontend (Mobile & Extension)
- **Components**: Sử dụng PascalCase (`FlashcardDeck.tsx`)
- **Hooks**: Bắt đầu với "use" (`useAuth.ts`)
- **Context**: Kết thúc với "Context" (`UserContext.tsx`)
- **Redux Actions**: Sử dụng UPPER_SNAKE_CASE (`USER_LOGIN_REQUEST`)
- **Reducers**: Sử dụng camelCase (`userReducer.ts`)
- **Styles**: Kết thúc với "Styles" (`cardStyles.ts`)

## Các biểu đồ

### System Architecture Diagram
```
┌───────────────┐      ┌───────────────┐     ┌───────────────┐
│  Mobile App   │      │    Web App    │     │Chrome Extension│
└───────┬───────┘      └───────┬───────┘     └───────┬───────┘
        │                      │                     │
        └──────────────┬───────┴─────────────────────┘
                       │
                       ▼
             ┌─────────────────┐
             │   API Gateway   │
             └────────┬────────┘
                      │
        ┌─────────────┴──────────────┐
        │                            │
┌───────▼──────┐            ┌────────▼─────┐
│ Auth Service │            │ User Service │
└───────┬──────┘            └────────┬─────┘
        │                            │
        │       ┌───────────────┐    │
        ├──────►│ SRS Engine    │◄───┤
        │       └───────┬───────┘    │
        │               │            │
┌───────▼────┐    ┌─────▼─────┐   ┌─▼────────────┐
│Vocabulary  │    │ Study     │   │ Statistics   │
│Service     │    │ Service   │   │ Service      │
└───────┬────┘    └─────┬─────┘   └──────┬───────┘
        │               │                │
        │         ┌─────▼─────────┐      │
        └────────►│ MongoDB       │◄─────┘
                  │ Redis         │
                  └───────────────┘
```

### Database Schema Diagram
```
┌─────────────┐       ┌───────────────┐       ┌───────────────┐
│   Users     │       │     Decks     │       │ Vocabularies  │
├─────────────┤       ├───────────────┤       ├───────────────┤
│ _id         │       │ _id           │       │ _id           │
│ email       │       │ name          │       │ word          │
│ password    │       │ description   │       │ translation   │
│ name        │       │ ownerId       ├───────┤ definition    │
│ settings    │       │ isPublic      │       │ examples      │
│ createdAt   │       │ createdAt     │       │ pronunciation │
│ updatedAt   │       │ updatedAt     │       │ partOfSpeech  │
└──────┬──────┘       └───────┬───────┘       │ level         │
       │                      │               │ tags          │
       │                      │               │ createdAt     │
       │                      │               │ updatedAt     │
       │                      │               └───────┬───────┘
       │                      │                       │
       │               ┌──────▼──────┐                │
       └───────────────►    Cards    ◄────────────────┘
                       ├─────────────┤
                       │ _id         │
                       │ deckId      │
                       │ vocabularyId│
                       │ dueDate     │
                       │ interval    │
                       │ easeFactor  │
                       │ repetitions │
                       │ createdAt   │
                       │ updatedAt   │
                       └──────┬──────┘
                              │
                              │
                       ┌──────▼──────┐
                       │   Reviews   │
                       ├─────────────┤
                       │ _id         │
                       │ cardId      │
                       │ userId      │
                       │ quality     │
                       │ timestamp   │
                       └─────────────┘
```

### Class Diagram
```
┌───────────────┐     ┌───────────────┐     ┌───────────────┐
│     User      │     │     Deck      │     │  Vocabulary   │
├───────────────┤     ├───────────────┤     ├───────────────┤
│ -id: string   │     │ -id: string   │     │ -id: string   │
│ -email: string│     │ -name: string │     │ -word: string │
│ -password: str│     │ -desc: string │     │ -trans: string│
│ -name: string │     │ -owner: User  │     │ -def: string  │
│ -settings: obj│     │ -isPublic: boo│     │ -examples: str│
├───────────────┤     ├───────────────┤     ├───────────────┤
│ +getDecks()   │     │ +getCards()   │     │ +pronounce()  │
│ +getStats()   │     │ +addCard()    │     │ +getExamples()│
└───────┬───────┘     └───────┬───────┘     └───────────────┘
        │ 1                   │ 1                    ▲
        │                     │                      │
        │ owns               │ contains             │ references
        ▼ *                   ▼ *                    │
┌───────────────┐     ┌───────────────┐             │
│ StudySession  │     │     Card      │─────────────┘
├───────────────┤     ├───────────────┤ 1
│ -id: string   │     │ -id: string   │
│ -user: User   │     │ -deck: Deck   │
│ -cards: Card[]│     │ -vocab: Vocab │
│ -startedAt: dt│     │ -dueDate: dt  │
│ -completedAt: │     │ -interval: num│
├───────────────┤     ├───────────────┤
│ +start()      │     │ +review(q)    │
│ +complete()   │     │ +resetProgress│
└───────────────┘     └───────┬───────┘
                              │ 1
                              │
                              │ has
                              ▼ *
                      ┌───────────────┐
                      │    Review     │
                      ├───────────────┤
                      │ -id: string   │
                      │ -card: Card   │
                      │ -quality: num │
                      │ -timestamp: dt│
                      ├───────────────┤
                      │ +getInterval()│
                      └───────────────┘
```

### Sequence Diagram - Flashcard Review Process
```
┌──────┐          ┌─────┐          ┌───────┐          ┌──────┐          ┌──────┐
│ User │          │ App │          │ API   │          │ SRS  │          │  DB  │
└──┬───┘          └──┬──┘          └───┬───┘          └──┬───┘          └──┬───┘
   │                 │                 │                 │                 │
   │ Open Study      │                 │                 │                 │
   │ Session         │                 │                 │                 │
   │────────────────>│                 │                 │                 │
   │                 │ Request cards   │                 │                 │
   │                 │ for today       │                 │                 │
   │                 │────────────────>│                 │                 │
   │                 │                 │ Get due cards   │                 │
   │                 │                 │────────────────>│                 │
   │                 │                 │                 │ Query cards     │
   │                 │                 │                 │────────────────>│
   │                 │                 │                 │                 │
   │                 │                 │                 │ Return cards    │
   │                 │                 │                 │<────────────────│
   │                 │                 │ Return cards    │                 │
   │                 │                 │<────────────────│                 │
   │                 │ Display cards   │                 │                 │
   │                 │<────────────────│                 │                 │
   │ View card       │                 │                 │                 │
   │<────────────────│                 │                 │                 │
   │                 │                 │                 │                 │
   │ Rate recall     │                 │                 │                 │
   │ quality (1-5)   │                 │                 │                 │
   │────────────────>│                 │                 │                 │
   │                 │ Submit review   │                 │                 │
   │                 │────────────────>│                 │                 │
   │                 │                 │ Calculate next  │                 │
   │                 │                 │ interval        │                 │
   │                 │                 │────────────────>│                 │
   │                 │                 │                 │ Update card     │
   │                 │                 │                 │ parameters      │
   │                 │                 │                 │────────────────>│
   │                 │                 │                 │                 │
   │                 │                 │                 │ Confirm update  │
   │                 │                 │                 │<────────────────│
   │                 │                 │ Return updated  │                 │
   │                 │                 │ card            │                 │
   │                 │                 │<────────────────│                 │
   │                 │ Show next card  │                 │                 │
   │                 │<────────────────│                 │                 │
   │                 │                 │                 │                 │
   │ Continue until  │                 │                 │                 │
   │ session complete│                 │                 │                 │
   │<───────────────>│                 │                 │                 │
   │                 │                 │                 │                 │
```

## Các Use Case

### Use Case 1: Đăng ký và Thiết lập tài khoản
**Tác nhân chính**: Người dùng mới  
**Tiền điều kiện**: Không có  
**Luồng chính**:
1. Người dùng truy cập vào ứng dụng
2. Chọn "Đăng ký"
3. Nhập thông tin (email, mật khẩu, tên)
4. Hệ thống gửi email xác nhận
5. Người dùng xác nhận email
6. Hoàn thành hồ sơ cá nhân và thiết lập học tập
7. Hệ thống tạo tài khoản và chuyển đến màn hình chính

**Luồng thay thế**:
- Đăng ký bằng tài khoản mạng xã hội
- Xử lý email đã tồn tại

### Use Case 2: Thu thập từ vựng từ trang web
**Tác nhân chính**: Người dùng đã đăng nhập  
**Tiền điều kiện**: Đã cài đặt Chrome Extension  
**Luồng chính**:
1. Người dùng duyệt một trang web
2. Gặp từ không biết, chọn từ đó
3. Click vào icon extension hoặc menu ngữ cảnh
4. Xem nghĩa, ví dụ và phát âm
5. Chọn "Thêm vào thẻ học"
6. Chọn bộ thẻ học hoặc tạo bộ mới
7. Hệ thống lưu từ vựng và thông báo thành công

**Luồng thay thế**:
- Chế độ offline: lưu cục bộ và đồng bộ sau
- Tự động đề xuất bộ thẻ dựa trên ngữ cảnh

### Use Case 3: Học tập với Spaced Repetition
**Tác nhân chính**: Người dùng đã đăng nhập  
**Tiền điều kiện**: Có ít nhất một bộ thẻ với từ vựng  
**Luồng chính**:
1. Người dùng mở ứng dụng
2. Xem số thẻ cần học hôm nay
3. Bắt đầu phiên học tập
4. Hệ thống hiển thị thẻ từ vựng theo thuật toán SRS
5. Người dùng xem từ, cố gắng nhớ nghĩa
6. Lật thẻ để kiểm tra đáp án
7. Đánh giá mức độ nhớ (1-5)
8. Hệ thống cập nhật lịch ôn tập cho từ đó
9. Tiếp tục cho đến khi hoàn thành phiên học

**Luồng thay thế**:
- Học offline: đồng bộ kết quả khi có kết nối
- Dừng giữa phiên học và tiếp tục sau

### Use Case 4: Theo dõi tiến độ học tập
**Tác nhân chính**: Người dùng đã đăng nhập  
**Tiền điều kiện**: Đã hoàn thành ít nhất một phiên học  
**Luồng chính**:
1. Người dùng truy cập phần "Thống kê"
2. Xem tổng quan về tiến độ học tập
3. Xem biểu đồ học tập theo thời gian
4. Xem chi tiết về từng bộ thẻ
5. Xem danh sách từ khó/dễ
6. Xem dự đoán thời gian cần để học xong bộ thẻ

**Luồng thay thế**:
- Xuất dữ liệu thống kê
- Chia sẻ thành tích

### Use Case 5: Quản lý và chia sẻ bộ thẻ từ vựng
**Tác nhân chính**: Người dùng đã đăng nhập  
**Tiền điều kiện**: Đã tạo ít nhất một bộ thẻ  
**Luồng chính**:
1. Người dùng truy cập phần "Bộ thẻ"
2. Chọn bộ thẻ để quản lý
3. Thêm/sửa/xóa từ vựng trong bộ thẻ
4. Thiết lập bộ thẻ là riêng tư/công khai
5. Tạo liên kết chia sẻ
6. Gửi liên kết cho người khác

**Luồng thay thế**:
- Nhập/xuất bộ thẻ từ file
- Sao chép bộ thẻ công khai từ người dùng khác
```

## Kết luận

Tài liệu này cung cấp mô tả kỹ thuật chi tiết về ứng dụng học tiếng Anh sử dụng phương pháp Spaced Repetition. Các nhà phát triển nên tuân thủ các yêu cầu và quy ước được đề cập trong tài liệu để đảm bảo tính nhất quán và chất lượng của sản phẩm.

Việc thực hiện các chức năng cần đáp ứng đầy đủ yêu cầu chức năng và phi chức năng, đồng thời tuân theo kiến trúc hệ thống đã được thiết kế. Bất kỳ thay đổi lớn nào về thiết kế cũng cần được thảo luận và phê duyệt trước khi thực hiện.
