# Video Streaming Platform - Context Diagram

## Overview
This diagram shows the video streaming platform in its operational context, illustrating all external entities that interact with the system.

## Context Diagram

```
                    ┌──────────────────────────────────────────┐
                    │                                          │
    ┌──────────┐    │                                          │    ┌──────────────┐
    │  Viewers │◄───┤                                          ├───►│   Content    │
    │ (End Users)│   │                                          │    │   Creators   │
    └──────────┘    │                                          │    └──────────────┘
                    │                                          │
    ┌──────────┐    │                                          │    ┌──────────────┐
    │  Mobile  │◄───┤     VIDEO STREAMING PLATFORM             ├───►│  Advertisers │
    │   Apps   │    │         (Netflix/YouTube)                │    │              │
    └──────────┘    │                                          │    └──────────────┘
                    │                                          │
    ┌──────────┐    │                                          │    ┌──────────────┐
    │Smart TVs │◄───┤                                          ├───►│   Payment    │
    │          │    │                                          │    │  Providers   │
    └──────────┘    │                                          │    │(Stripe/PayPal)│
                    │                                          │    └──────────────┘
    ┌──────────┐    │                                          │
    │  Web     │◄───┤                                          │    ┌──────────────┐
    │ Browsers │    │                                          ├───►│     CDN      │
    └──────────┘    │                                          │    │   Providers  │
                    │                                          │    │(CloudFront,  │
    ┌──────────┐    │                                          │    │  Akamai)     │
    │  Gaming  │◄───┤                                          │    └──────────────┘
    │ Consoles │    │                                          │
    └──────────┘    │                                          │    ┌──────────────┐
                    │                                          ├───►│  Analytics   │
    ┌──────────┐    │                                          │    │   Services   │
    │   Admin  │◄───┤                                          │    │(Google Analytics)│
    │  Portal  │    │                                          │    └──────────────┘
    └──────────┘    │                                          │
                    │                                          │    ┌──────────────┐
    ┌──────────┐    │                                          ├───►│     DRM      │
    │Moderators│◄───┤                                          │    │   Providers  │
    │          │    │                                          │    │(Widevine,    │
    └──────────┘    │                                          │    │ FairPlay)    │
                    │                                          │    └──────────────┘
                    │                                          │
                    │                                          │    ┌──────────────┐
                    │                                          ├───►│  Email/SMS   │
                    │                                          │    │   Services   │
                    │                                          │    │(SendGrid, Twilio)│
                    └──────────────────────────────────────────┘    └──────────────┘
```

## External Entities

### User-Facing Entities

#### 1. Viewers (End Users)
- **Role**: Primary users who consume video content
- **Interactions**:
  - Browse and search videos
  - Stream video content
  - Create playlists
  - Like, comment, and share videos
  - Manage subscriptions
  - Adjust playback settings
- **Communication**: HTTPS/REST APIs, WebSocket for real-time updates

#### 2. Mobile Apps
- **Platforms**: iOS, Android
- **Interactions**:
  - Native video playback
  - Offline downloads
  - Push notifications
  - Background playback
- **Communication**: RESTful APIs, gRPC

#### 3. Smart TVs
- **Platforms**: Samsung, LG, Roku, Fire TV
- **Interactions**:
  - High-quality streaming (4K, HDR)
  - Voice control integration
  - Remote control navigation
- **Communication**: HTTPS APIs

#### 4. Web Browsers
- **Support**: Chrome, Firefox, Safari, Edge
- **Interactions**:
  - Web-based video player
  - Responsive UI
  - Social sharing
- **Communication**: HTTPS, WebSocket

#### 5. Gaming Consoles
- **Platforms**: PlayStation, Xbox
- **Interactions**:
  - Dedicated apps
  - Controller navigation
  - Social features
- **Communication**: Platform-specific SDKs

### Content Management Entities

#### 6. Content Creators
- **Role**: Upload and manage video content
- **Interactions**:
  - Upload videos
  - Edit metadata (title, description, tags)
  - View analytics
  - Manage monetization
  - Respond to comments
- **Communication**: Upload APIs, Dashboard APIs

#### 7. Admin Portal
- **Role**: Platform administrators
- **Interactions**:
  - User management
  - Content moderation
  - System monitoring
  - Configuration management
- **Communication**: Admin APIs, monitoring dashboards

#### 8. Moderators
- **Role**: Content review and moderation
- **Interactions**:
  - Review flagged content
  - Enforce community guidelines
  - Take down policy-violating content
- **Communication**: Moderation APIs

### Business Entities

#### 9. Advertisers
- **Role**: Purchase ad placements
- **Interactions**:
  - Create ad campaigns
  - Target specific audiences
  - View ad performance metrics
  - Manage budgets
- **Communication**: Advertising APIs

#### 10. Payment Providers
- **Services**: Stripe, PayPal, Credit Card processors
- **Interactions**:
  - Process subscriptions
  - Handle one-time payments
  - Refund management
  - Payment verification
- **Communication**: Payment gateway APIs (HTTPS)

### Infrastructure Entities

#### 11. CDN Providers
- **Services**: CloudFront, Akamai, Cloudflare
- **Interactions**:
  - Distribute video content globally
  - Cache popular content
  - Provide edge locations
  - Handle DDoS protection
- **Communication**: CDN APIs, origin pull

#### 12. Analytics Services
- **Services**: Google Analytics, Mixpanel
- **Interactions**:
  - Track user behavior
  - Measure engagement metrics
  - Generate reports
  - A/B testing
- **Communication**: Analytics SDKs, tracking pixels

#### 13. DRM Providers
- **Services**: Google Widevine, Apple FairPlay, Microsoft PlayReady
- **Interactions**:
  - Content encryption
  - License verification
  - Key management
  - Device authentication
- **Communication**: DRM license servers

#### 14. Email/SMS Services
- **Services**: SendGrid, Twilio, Amazon SES
- **Interactions**:
  - Send transactional emails
  - Send promotional emails
  - SMS notifications
  - Email verification
- **Communication**: SMTP, REST APIs

## Data Flow Summary

### Inbound Data Flows
1. Video uploads from content creators
2. User requests (search, play, browse)
3. Payment information from payment providers
4. Analytics data from user devices
5. Moderation actions from moderators

### Outbound Data Flows
1. Video streams to viewers via CDN
2. Notifications via email/SMS services
3. Content to CDN for distribution
4. Analytics data to analytics services
5. Payment requests to payment providers
6. License requests to DRM providers

## Security Boundaries

1. **Authentication**: All user-facing entities require authentication
2. **Authorization**: Role-based access control for different entity types
3. **Encryption**: HTTPS for all communications, DRM for video content
4. **Rate Limiting**: Protect against abuse from external entities
5. **Input Validation**: Validate all inputs from external sources
