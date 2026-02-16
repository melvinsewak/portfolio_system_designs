# Video Streaming Platform - Sequence Diagram

## Overview
Detailed sequence diagrams showing the flow of interactions between components for key use cases.

## 1. Video Playback Sequence

```
User     WebApp    API Gateway   Auth Service   Video Catalog   Streaming Service   CDN      DRM Service
 │          │            │              │              │                 │           │            │
 │──Search──►│            │              │              │                 │           │            │
 │          │──GET /search───────────────►│──────────────►│                │           │            │
 │          │◄───Results────────────────────────────────┤                │           │            │
 │◄─Results─┤            │              │              │                 │           │            │
 │          │            │              │              │                 │           │            │
 │──Select──►│            │              │              │                 │           │            │
 │  Video   │            │              │              │                 │           │            │
 │          │──GET /videos/{id}──────────►│──────────────►│                │           │            │
 │          │            │              │  Verify JWT   │                 │           │            │
 │          │            │              │◄─────────────┤                │           │            │
 │          │            │              │   Valid       │                 │           │            │
 │          │            │              │──────────────►│                │           │            │
 │          │◄───Video Metadata───────────────────────────┤                │           │            │
 │          │            │              │              │                 │           │            │
 │──Play────►│            │              │              │                 │           │            │
 │          │──GET /stream/{id}──────────►│──────────────┼─────────────────►│           │            │
 │          │            │              │              │   Check Access  │           │            │
 │          │            │              │              │◄────────────────┤           │            │
 │          │            │              │              │   Authorized    │           │            │
 │          │            │              │              │─────────────────►│           │            │
 │          │            │              │              │                 │──Request──►│            │
 │          │            │              │              │                 │  DRM      │            │
 │          │            │              │              │                 │  License  │            │
 │          │            │              │              │                 │           │──License───►│
 │          │            │              │              │                 │           │  Verify    │
 │          │            │              │              │                 │           │◄─License───┤
 │          │            │              │              │                 │◄─Manifest─┤            │
 │          │◄───HLS/DASH Manifest────────────────────────────────────────┤           │            │
 │          │            │              │              │                 │           │            │
 │◄Manifest─┤            │              │              │                 │           │            │
 │          │            │              │              │                 │           │            │
 │──GET────────────────────────────────────────────────────────────────────►│        │            │
 │ segments │            │              │              │                 │  .ts/.m4s │            │
 │◄─Video───────────────────────────────────────────────────────────────────┤  files │            │
 │ chunks   │            │              │              │                 │           │            │
 │          │            │              │              │                 │           │            │
 │──Report─────────────────────────────────────────────────────────────►│           │            │
 │Analytics │            │              │              │  (async)        │           │            │
```

## 2. Video Upload and Transcoding Sequence

```
Creator   WebApp   API Gateway   Auth Service   Upload Service   S3   Transcoding    CDN    Notification
                                                                      Service
  │         │          │              │                │         │        │           │          │
  │──Login──►│          │              │                │         │        │           │          │
  │         │──POST /auth─────────────►│                │         │        │           │          │
  │         │          │              │ Verify Creds   │         │        │           │          │
  │         │◄────JWT Token───────────┤                │         │        │           │          │
  │◄─Token──┤          │              │                │         │        │           │          │
  │         │          │              │                │         │        │           │          │
  │──Upload─►│          │              │                │         │        │           │          │
  │ Video   │          │              │                │         │        │           │          │
  │         │──POST /upload────────────┼────────────────►│         │        │           │          │
  │         │  (multipart)             │   Verify JWT   │         │        │           │          │
  │         │          │              │◄───────────────┤         │        │           │          │
  │         │          │              │    Valid       │         │        │           │          │
  │         │          │              │────────────────►│         │        │           │          │
  │         │          │              │                │──Upload─►│        │           │          │
  │         │          │              │                │  File   │        │           │          │
  │         │          │              │                │◄────────┤        │           │          │
  │         │          │              │                │  URL    │        │           │          │
  │         │          │              │                │         │        │           │          │
  │         │          │              │                │─Queue Transcode─►│           │          │
  │         │          │              │                │   Job   │        │           │          │
  │         │◄────Upload ID────────────┼────────────────┤         │        │           │          │
  │◄─Success┤          │              │                │         │        │           │          │
  │         │          │              │                │         │        │           │          │
  │         │          │              │                │         │  ┌─────┴─────┐     │          │
  │         │          │              │                │         │  │  Process  │     │          │
  │         │          │              │                │         │  │  Queue    │     │          │
  │         │          │              │                │         │  └─────┬─────┘     │          │
  │         │          │              │                │         │        │           │          │
  │         │          │              │                │         │◄───Get Job────┤     │          │
  │         │          │              │                │         │        │           │          │
  │         │          │              │                │         │        │ Transcode │          │
  │         │          │              │                │         │        │ (FFmpeg)  │          │
  │         │          │              │                │         │        │ Multiple  │          │
  │         │          │              │                │         │        │ Bitrates  │          │
  │         │          │              │                │         │        │           │          │
  │         │          │              │                │         │◄────Upload────┤     │          │
  │         │          │              │                │         │  Transcoded   │     │          │
  │         │          │              │                │         │  Files        │     │          │
  │         │          │              │                │         │────────────────►│    │          │
  │         │          │              │                │         │      Sync to   │    │          │
  │         │          │              │                │         │      CDN       │    │          │
  │         │          │              │                │◄────Job Complete─────┤   │    │          │
  │         │          │              │                │─────────────────────────────────►│         │
  │         │          │              │                │         │        │     │    Send Email   │
  │◄────────────────────────────────────────────Notification─────────────────────────────┤         │
  │ "Video processed"  │              │                │         │        │     │        │         │
```

## 3. User Registration and Login Sequence

```
User      WebApp    API Gateway    Auth Service    User Service    DB    Email Service
 │           │            │              │               │          │          │
 │──Register─►│            │              │               │          │          │
 │           │──POST /register───────────►│               │          │          │
 │           │   (email, password)        │  Validate     │          │          │
 │           │            │              │  Input        │          │          │
 │           │            │              │──Check Email──►│          │          │
 │           │            │              │  Exists       │──Query───►│          │
 │           │            │              │               │◄─Result──┤          │
 │           │            │              │◄──Not Exists──┤          │          │
 │           │            │              │               │          │          │
 │           │            │              │  Hash         │          │          │
 │           │            │              │  Password     │          │          │
 │           │            │              │               │          │          │
 │           │            │              │──Create User──►│          │          │
 │           │            │              │               │──Insert──►│          │
 │           │            │              │               │◄─User ID─┤          │
 │           │            │              │◄──User Data───┤          │          │
 │           │            │              │               │          │          │
 │           │            │              │──Send Verification──────────────────►│
 │           │            │              │  Email        │          │          │
 │           │◄────Success Message───────┤               │          │          │
 │◄─Success──┤            │              │               │          │          │
 │           │            │              │               │          │          │
 │──Verify───────────────────────────────►│               │          │          │
 │  Email    │            │              │  Verify Token │          │          │
 │           │            │              │──Update User──►│          │          │
 │           │            │              │               │──Update──►│          │
 │           │◄────Account Verified───────┤               │          │          │
 │           │            │              │               │          │          │
 │──Login────►│            │              │               │          │          │
 │           │──POST /login──────────────►│               │          │          │
 │           │   (email, password)        │               │          │          │
 │           │            │              │──Get User─────►│          │          │
 │           │            │              │               │──Query───►│          │
 │           │            │              │               │◄─User────┤          │
 │           │            │              │◄──User Data───┤          │          │
 │           │            │              │               │          │          │
 │           │            │              │  Verify       │          │          │
 │           │            │              │  Password     │          │          │
 │           │            │              │  (bcrypt)     │          │          │
 │           │            │              │               │          │          │
 │           │            │              │  Generate     │          │          │
 │           │            │              │  JWT Token    │          │          │
 │           │            │              │               │          │          │
 │           │            │              │──Create Session►          │          │
 │           │            │              │               │──Insert──►│          │
 │           │◄────JWT + Refresh Token────┤               │          │          │
 │◄─Tokens───┤            │              │               │          │          │
```

## 4. Recommendation Generation Sequence

```
User    WebApp   API Gateway   Recommendation   User Service   Graph DB   ML Model   Cache
                               Service                                    Service   (Redis)
 │        │          │              │                 │            │         │         │
 │─Browse─►│          │              │                 │            │         │         │
 │ Home   │          │              │                 │            │         │         │
 │        │──GET /recommendations───►│                 │            │         │         │
 │        │          │              │──Check Cache────────────────────────────────────►│
 │        │          │              │                 │            │         │◄─Miss───┤
 │        │          │              │                 │            │         │         │
 │        │          │              │──Get User Profile────────────►         │         │
 │        │          │              │                 │──Query─────►│         │         │
 │        │          │              │                 │◄─Profile───┤         │         │
 │        │          │              │◄──Watch History─┤            │         │         │
 │        │          │              │                 │            │         │         │
 │        │          │              │──Get Similar Users───────────►│         │         │
 │        │          │              │                 │   Graph    │         │         │
 │        │          │              │                 │   Query    │         │         │
 │        │          │              │◄──Similar Users──────────────┤         │         │
 │        │          │              │                 │            │         │         │
 │        │          │              │──Request Predictions─────────────────────►│         │
 │        │          │              │  (User features,            │         │         │
 │        │          │              │   Similar users)             │         │         │
 │        │          │              │                 │            │◄─Predict┤         │
 │        │          │              │◄──Recommended Videos─────────────────────┤         │
 │        │          │              │                 │            │         │         │
 │        │          │              │──Cache Results──────────────────────────────────►│
 │        │          │              │  (TTL: 1 hour)  │            │         │         │
 │        │◄───Video Recommendations─┤                 │            │         │         │
 │◄─Show──┤          │              │                 │            │         │         │
 │Videos  │          │              │                 │            │         │         │
```

## 5. Content Moderation Sequence

```
Creator  Upload    Queue     Moderation   ML Service   Human     Notification
         Service             Service                   Reviewer  Service
  │         │        │           │            │          │            │
  │─Upload──►│        │           │            │          │            │
  │ Video   │        │           │            │          │            │
  │         │──Queue─►│           │            │          │            │
  │         │  Job   │──Dequeue──►│            │          │            │
  │         │        │           │──Analyze───►│          │            │
  │         │        │           │  Content   │          │            │
  │         │        │           │            │ ML       │            │
  │         │        │           │            │ Models:  │            │
  │         │        │           │            │ -NSFW    │            │
  │         │        │           │            │ -Violence│            │
  │         │        │           │            │ -Text    │            │
  │         │        │           │◄─Results───┤          │            │
  │         │        │           │            │          │            │
  │         │        │           │ ┌──────────┐          │            │
  │         │        │           │ │ Decision │          │            │
  │         │        │           │ └────┬─────┘          │            │
  │         │        │           │      │                │            │
  │         │        │           │  Low Risk              │            │
  │         │        │           │──Approve Video         │            │
  │         │        │           │──────────────────────────────────────►│
  │◄────────────────────────────────────Video Approved Notification────┤
  │         │        │           │      │                │            │
  │         │        │           │  Medium Risk          │            │
  │         │        │           │──Flag for Review──────►│            │
  │         │        │           │                       │ Manual     │
  │         │        │           │                       │ Review     │
  │         │        │           │◄──Decision────────────┤            │
  │         │        │           │                       │            │
  │         │        │           │  High Risk            │            │
  │         │        │           │──Auto Reject          │            │
  │         │        │           │──────────────────────────────────────►│
  │◄────────────────────────────────────Video Rejected Notification────┤
  │         │        │           │                       │            │
```

## Key Flows Summary

1. **Video Playback**: Authentication → Metadata Retrieval → DRM License → Manifest → Stream Chunks
2. **Video Upload**: Authentication → Upload → Transcoding Queue → Processing → CDN Distribution → Notification
3. **Registration/Login**: Input Validation → User Creation → Email Verification → Token Generation
4. **Recommendations**: Cache Check → User Profile → Similarity Analysis → ML Prediction → Cache Update
5. **Moderation**: Content Upload → ML Analysis → Risk Assessment → Human Review (if needed) → Decision

## Performance Considerations

1. **Caching**: Multi-level caching for metadata, recommendations, and user sessions
2. **Async Processing**: Video transcoding and moderation run asynchronously
3. **CDN**: Video content served from edge locations
4. **Load Balancing**: Requests distributed across multiple instances
5. **Database Optimization**: Read replicas for heavy read operations
