# Video Streaming Platform - ER Diagram

## Overview
Database schema and entity relationships for the video streaming platform.

## Entity Relationship Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           USER MANAGEMENT                                    │
└─────────────────────────────────────────────────────────────────────────────┘

┌──────────────────┐              ┌──────────────────┐
│      User        │              │  UserProfile     │
├──────────────────┤              ├──────────────────┤
│ PK: user_id      │──────────────│ PK: profile_id   │
│     email        │    1     1   │ FK: user_id      │
│     password_hash│              │     first_name   │
│     created_at   │              │     last_name    │
│     updated_at   │              │     avatar_url   │
│     is_verified  │              │     bio          │
│     role         │              │     country      │
│     status       │              │     language     │
└──────────────────┘              └──────────────────┘
         │                                 
         │ 1                               
         │                                 
         │ *                               
┌──────────────────┐              ┌──────────────────┐
│   Subscription   │              │  UserPreference  │
├──────────────────┤              ├──────────────────┤
│ PK: sub_id       │              │ PK: pref_id      │
│ FK: user_id      │              │ FK: user_id      │
│     plan_id      │              │     quality      │
│     start_date   │              │     autoplay     │
│     end_date     │              │     subtitle_lang│
│     status       │              │     audio_lang   │
│     payment_id   │              │     notification │
└──────────────────┘              └──────────────────┘


┌─────────────────────────────────────────────────────────────────────────────┐
│                        VIDEO CONTENT                                         │
└─────────────────────────────────────────────────────────────────────────────┘

┌──────────────────┐              ┌──────────────────┐
│      Video       │              │  VideoMetadata   │
├──────────────────┤              ├──────────────────┤
│ PK: video_id     │──────────────│ PK: metadata_id  │
│     title        │    1     1   │ FK: video_id     │
│     description  │              │     duration     │
│     upload_date  │              │     resolution   │
│     status       │              │     codec        │
│ FK: creator_id   │              │     bitrate      │
│     view_count   │              │     frame_rate   │
│     like_count   │              │     aspect_ratio │
│     thumbnail_url│              │     language     │
│     visibility   │              │     subtitles    │
└──────────────────┘              └──────────────────┘
         │                                 
         │ 1                               
         │                                 
         │ *                               
┌──────────────────┐              
│   VideoFile      │              
├──────────────────┤              
│ PK: file_id      │              
│ FK: video_id     │              
│     format       │              
│     quality      │   (HLS/DASH manifests)
│     s3_url       │              
│     cdn_url      │              
│     file_size    │              
│     created_at   │              
└──────────────────┘              


┌─────────────────────────────────────────────────────────────────────────────┐
│                        CATEGORIZATION                                        │
└─────────────────────────────────────────────────────────────────────────────┘

┌──────────────────┐              ┌──────────────────┐              ┌──────────────────┐
│    Category      │              │  VideoCategory   │              │      Tag         │
├──────────────────┤              ├──────────────────┤              ├──────────────────┤
│ PK: category_id  │──────────────│ PK: vc_id        │──────────────│ PK: tag_id       │
│     name         │    1     *   │ FK: video_id     │    *     *   │     name         │
│     description  │              │ FK: category_id  │              │     created_at   │
│     parent_id    │              └──────────────────┘              └──────────────────┘
│     icon_url     │                                                          │
└──────────────────┘              ┌──────────────────┐                       │
                                  │   VideoTag       │                       │
                                  ├──────────────────┤                       │
                                  │ PK: vt_id        │───────────────────────┘
                                  │ FK: video_id     │    *     *
                                  │ FK: tag_id       │
                                  └──────────────────┘


┌─────────────────────────────────────────────────────────────────────────────┐
│                        USER ENGAGEMENT                                       │
└─────────────────────────────────────────────────────────────────────────────┘

┌──────────────────┐              ┌──────────────────┐              ┌──────────────────┐
│   WatchHistory   │              │   VideoLike      │              │   Comment        │
├──────────────────┤              ├──────────────────┤              ├──────────────────┤
│ PK: history_id   │              │ PK: like_id      │              │ PK: comment_id   │
│ FK: user_id      │              │ FK: user_id      │              │ FK: user_id      │
│ FK: video_id     │              │ FK: video_id     │              │ FK: video_id     │
│     watch_date   │              │     like_type    │              │ FK: parent_id    │
│     duration     │              │     created_at   │              │     content      │
│     progress     │              └──────────────────┘              │     created_at   │
│     completed    │                                                │     updated_at   │
│     device_type  │                                                │     likes        │
└──────────────────┘              ┌──────────────────┐              │     status       │
                                  │   VideoRating    │              └──────────────────┘
                                  ├──────────────────┤
                                  │ PK: rating_id    │
                                  │ FK: user_id      │
                                  │ FK: video_id     │
                                  │     rating       │
                                  │     created_at   │
                                  └──────────────────┘


┌─────────────────────────────────────────────────────────────────────────────┐
│                        PLAYLISTS & COLLECTIONS                               │
└─────────────────────────────────────────────────────────────────────────────┘

┌──────────────────┐              ┌──────────────────┐
│    Playlist      │              │ PlaylistVideo    │
├──────────────────┤              ├──────────────────┤
│ PK: playlist_id  │──────────────│ PK: pv_id        │
│ FK: user_id      │    1     *   │ FK: playlist_id  │
│     name         │              │ FK: video_id     │
│     description  │              │     position     │
│     visibility   │              │     added_at     │
│     created_at   │              └──────────────────┘
│     updated_at   │                       │
│     video_count  │                       │ *
└──────────────────┘                       │
                                           │ 1
                                  ┌──────────────────┐
                                  │      Video       │
                                  └──────────────────┘


┌─────────────────────────────────────────────────────────────────────────────┐
│                        ANALYTICS & TRACKING                                  │
└─────────────────────────────────────────────────────────────────────────────┘

┌──────────────────┐              ┌──────────────────┐
│  ViewAnalytics   │              │EngagementMetrics│
├──────────────────┤              ├──────────────────┤
│ PK: analytics_id │              │ PK: metric_id    │
│ FK: video_id     │              │ FK: video_id     │
│ FK: user_id      │              │     date         │
│     timestamp    │              │     views        │
│     view_duration│              │     unique_views │
│     buffering    │              │     avg_watch_time│
│     quality      │              │     completion_rate│
│     bandwidth    │              │     likes        │
│     location     │              │     comments     │
│     device_info  │              │     shares       │
└──────────────────┘              └──────────────────┘

┌──────────────────┐              ┌──────────────────┐
│   SearchQuery    │              │  Recommendation  │
├──────────────────┤              ├──────────────────┤
│ PK: query_id     │              │ PK: rec_id       │
│ FK: user_id      │              │ FK: user_id      │
│     query_text   │              │ FK: video_id     │
│     timestamp    │              │     score        │
│     result_count │              │     reason       │
│     clicked_video│              │     created_at   │
└──────────────────┘              │     shown        │
                                  │     clicked      │
                                  └──────────────────┘


┌─────────────────────────────────────────────────────────────────────────────┐
│                        CONTENT MODERATION                                    │
└─────────────────────────────────────────────────────────────────────────────┘

┌──────────────────┐              ┌──────────────────┐
│  ModerationLog   │              │ ContentReport    │
├──────────────────┤              ├──────────────────┤
│ PK: log_id       │              │ PK: report_id    │
│ FK: video_id     │              │ FK: user_id      │
│ FK: moderator_id │              │ FK: video_id     │
│     action       │              │ FK: comment_id   │
│     reason       │              │     reason       │
│     timestamp    │              │     description  │
│     status       │              │     status       │
└──────────────────┘              │     created_at   │
                                  │     resolved_at  │
                                  └──────────────────┘


┌─────────────────────────────────────────────────────────────────────────────┐
│                        NOTIFICATIONS                                         │
└─────────────────────────────────────────────────────────────────────────────┘

┌──────────────────┐              ┌──────────────────┐
│  Notification    │              │NotificationPref  │
├──────────────────┤              ├──────────────────┤
│ PK: notif_id     │              │ PK: pref_id      │
│ FK: user_id      │              │ FK: user_id      │
│     type         │              │     email        │
│     title        │              │     push         │
│     message      │              │     sms          │
│     link         │              │     new_video    │
│     read         │              │     comments     │
│     created_at   │              │     likes        │
└──────────────────┘              │     live_stream  │
                                  └──────────────────┘
```

## Entity Descriptions

### User Management

#### User
Core user entity with authentication credentials
- **user_id**: Primary key (UUID)
- **email**: Unique email address
- **password_hash**: Bcrypt hashed password
- **role**: user, creator, admin, moderator
- **status**: active, suspended, deleted

#### UserProfile
Extended user information
- **profile_id**: Primary key
- **user_id**: Foreign key to User
- Contains demographic and profile information

#### Subscription
User subscription plans
- **sub_id**: Primary key
- **plan_id**: Reference to subscription plan (free, basic, premium)
- **status**: active, cancelled, expired

### Video Content

#### Video
Main video entity
- **video_id**: Primary key (UUID)
- **creator_id**: Foreign key to User
- **status**: uploaded, processing, published, removed
- **visibility**: public, private, unlisted

#### VideoMetadata
Technical metadata about video
- **metadata_id**: Primary key
- **duration**: Video length in seconds
- **resolution**: 240p, 360p, 480p, 720p, 1080p, 4K
- **codec**: H.264, H.265, VP9, AV1

#### VideoFile
Transcoded video files
- **file_id**: Primary key
- **format**: HLS, DASH, MP4
- **quality**: Different bitrates/resolutions
- **s3_url**: Original storage location
- **cdn_url**: CDN distribution URL

### User Engagement

#### WatchHistory
Track user viewing behavior
- **history_id**: Primary key
- **progress**: Last watched position (seconds)
- **completed**: Boolean flag
- **device_type**: web, mobile, tv, console

#### Comment
User comments on videos
- **comment_id**: Primary key
- **parent_id**: For nested replies (self-referencing)
- **status**: published, hidden, deleted

#### VideoLike
Like/dislike tracking
- **like_type**: like, dislike

### Analytics

#### ViewAnalytics
Detailed view tracking (Cassandra/time-series DB)
- **timestamp**: View timestamp
- **buffering**: Buffering events count
- **location**: Geographic location
- **device_info**: Browser, OS, device type

#### EngagementMetrics
Aggregated daily metrics
- **completion_rate**: Percentage of viewers who watched till end
- **avg_watch_time**: Average viewing duration

## Relationships

1. **User → Video**: One-to-Many (Creator relationship)
2. **Video → VideoFile**: One-to-Many (Multiple formats/qualities)
3. **User → WatchHistory**: One-to-Many (Viewing history)
4. **User → Playlist**: One-to-Many (User playlists)
5. **Playlist → Video**: Many-to-Many (via PlaylistVideo)
6. **Video → Category**: Many-to-Many (via VideoCategory)
7. **Video → Tag**: Many-to-Many (via VideoTag)
8. **User → Comment**: One-to-Many (User comments)
9. **Video → Comment**: One-to-Many (Video comments)
10. **Comment → Comment**: Self-referencing (Nested replies)

## Indexing Strategy

### PostgreSQL Indexes
```sql
-- User table
CREATE INDEX idx_user_email ON User(email);
CREATE INDEX idx_user_created_at ON User(created_at);

-- Video table
CREATE INDEX idx_video_creator ON Video(creator_id);
CREATE INDEX idx_video_upload_date ON Video(upload_date);
CREATE INDEX idx_video_status ON Video(status);
CREATE INDEX idx_video_visibility ON Video(visibility);

-- WatchHistory
CREATE INDEX idx_watch_user_video ON WatchHistory(user_id, video_id);
CREATE INDEX idx_watch_date ON WatchHistory(watch_date);

-- Comment
CREATE INDEX idx_comment_video ON Comment(video_id);
CREATE INDEX idx_comment_user ON Comment(user_id);
CREATE INDEX idx_comment_created ON Comment(created_at);
```

### MongoDB Collections (Video Metadata)
```javascript
// Video metadata collection
db.videoMetadata.createIndex({ "video_id": 1 }, { unique: true })
db.videoMetadata.createIndex({ "tags": 1 })
db.videoMetadata.createIndex({ "upload_date": -1 })
db.videoMetadata.createIndex({ "view_count": -1 })

// Comments collection
db.comments.createIndex({ "video_id": 1, "created_at": -1 })
db.comments.createIndex({ "user_id": 1 })
db.comments.createIndex({ "parent_id": 1 })
```

### Cassandra Tables (Analytics)
```cql
-- View analytics (time-series data)
CREATE TABLE view_analytics (
    video_id UUID,
    view_date DATE,
    timestamp TIMESTAMP,
    user_id UUID,
    -- other columns
    PRIMARY KEY ((video_id, view_date), timestamp)
) WITH CLUSTERING ORDER BY (timestamp DESC);

-- Engagement metrics (aggregated)
CREATE TABLE engagement_metrics (
    video_id UUID,
    date DATE,
    -- metrics columns
    PRIMARY KEY (video_id, date)
) WITH CLUSTERING ORDER BY (date DESC);
```

## Data Partitioning

1. **User Data**: Partition by user_id hash
2. **Video Data**: Partition by video_id or creator_id
3. **Analytics**: Partition by date range (time-series)
4. **Comments**: Partition by video_id

## Data Retention

1. **User Data**: Indefinite (until account deletion)
2. **Video Content**: Indefinite
3. **Analytics Data**: 2 years detailed, aggregated forever
4. **Logs**: 90 days
5. **Session Data**: 30 days
