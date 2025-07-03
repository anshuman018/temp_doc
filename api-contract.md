# Education Module API Contract
## TapHealth DTx - Based on Education Module Revamp PRD

### Document Overview
This API contract outlines the backend endpoints for the Education Module according to the Education Module Revamp PRD. The new contract supports the 5-step user journey with enhanced features including time-based curation, quiz functionality, progress tracking, and exploration capabilities.

---

## Table of Contents
1. [API Overview](#api-overview)
2. [Data Models](#data-models)
3. [Core APIs](#core-apis)
4. [Legacy Support](#legacy-support)
5. [Response Formats](#response-formats)
6. [Error Handling](#error-handling)
7. [Changes from Current Implementation](#changes-from-current-implementation)

---

## API Overview

### Base URLs
- **V1**: `/api/dtx/v1/education/`
- **V2**: `/api/dtx/v2/education/`
- **Legacy**: `/api/dtx/education/` (backward compatibility)

### Authentication
All endpoints require Bearer token authentication via the `Authorization` header.

### Versioning Strategy
- **V1**: Current implementation with minimal changes for compatibility
- **V2**: Enhanced implementation aligned with PRD requirements
- **Legacy**: Maintains existing API for backward compatibility

---

## Data Models

### Education Plan Entry
```typescript
interface EducationPlanEntry {
  id: string;
  userId: string;
  date: string; // YYYY-MM-DD format
  title: string;
  description: string;
  script?: string; // Video transcript
  
  // Video information
  video: {
    url: string; // HLS streaming URL
    thumbnail: string;
    fullTime: string; // Total duration (MM:SS)
    playbackTime: string; // Current progress (MM:SS)
  };
  
  // Quiz data
  quiz: QuizQuestion[];
  
  // Status and metadata
  completionStatus: boolean;
  
  // New fields for PRD requirements
  metadata?: {
    collection: string; // "Nutrition", "Basics", "Lifestyle", etc.
    timeOfDay: TimeSlot; // "morning", "afternoon", "evening", "bedtime"
    difficulty: DifficultyLevel; // "easy", "moderate", "advanced"
    videoType: VideoType; // "basics", "lifestyle-tip", "deep-dive"
    estimatedWatchTime: number; // in minutes
  };
  
  // Progress tracking
  progress?: {
    quizAttempts: number;
    lastQuizScore: number;
    watchPercentage: number;
    needsReview: boolean;
  };
  
  createdAt: string;
  updatedAt: string;
}
```

### Quiz Question
```typescript
interface QuizQuestion {
  _id: string;
  question: string;
  options: string[];
  correctOption: string;
}
```

### Enums
```typescript
enum TimeSlot {
  MORNING = "morning",       // 12:30AM - 12PM
  AFTERNOON = "afternoon",   // 12PM - 4PM  
  EVENING = "evening",       // 4PM - 8PM
  BEDTIME = "bedtime"        // 8PM - 12:30AM
}

enum DifficultyLevel {
  EASY = "easy",
  MODERATE = "moderate", 
  ADVANCED = "advanced"
}

enum VideoType {
  BASICS = "basics",
  LIFESTYLE_TIP = "lifestyle-tip",
  DEEP_DIVE = "deep-dive",
  NUTRITION = "nutrition"
}
```

---

## Core APIs

### 1. Generate Daily Education Plan

#### V2 Enhanced Endpoint
```http
POST /api/dtx/v2/education/generate
```

**Description**: Generates a curated daily education plan with time-based organization according to the PRD's Step 1 requirements.

**Request Headers**:
```
Authorization: Bearer <token>
Content-Type: application/json
```

**Request Body**:
```json
{
  "generateForDate": "2025-07-03", // Optional: defaults to today
  "preferences": { // Optional: override user preferences
    "timeSlots": ["morning", "afternoon", "evening", "bedtime"],
    "maxVideosPerSlot": 2,
    "difficultyLevel": "moderate"
  }
}
```

**Response**:
```json
{
  "code": 200,
  "status": "success",
  "message": "Daily Education Plan Generated Successfully",
  "data": {
    "planDate": "2025-07-03",
    "timeSlots": {
      "morning": {
        "title": "Morning Game Plan",
        "timeRange": "12:30AM - 12PM",
        "videos": [
          {
            "id": "67aa587a3e05ebd502122187",
            "title": "Understanding A1C Levels",
            "description": "Learn what A1C means and why it matters for diabetes management",
            "video": {
              "url": "https://app-assets.thcdn.in/videos/a1c-levels/master.m3u8",
              "thumbnail": "https://app-assets.thcdn.in/thumbnails/a1c-levels.jpg",
              "fullTime": "03:45",
              "playbackTime": "00:00"
            },
            "metadata": {
              "collection": "Basics",
              "timeOfDay": "morning",
              "difficulty": "easy",
              "videoType": "basics",
              "estimatedWatchTime": 4
            },
            "quiz": [
              {
                "_id": "67aa587a3e05ebd50212214e",
                "question": "What does A1C measure?",
                "options": [
                  "Daily blood sugar",
                  "Average blood sugar over 3 months",
                  "Blood pressure"
                ],
                "correctOption": "Average blood sugar over 3 months"
              }
            ],
            "completionStatus": false,
            "progress": {
              "quizAttempts": 0,
              "lastQuizScore": 0,
              "watchPercentage": 0,
              "needsReview": false
            }
          }
        ]
      },
      "afternoon": {
        "title": "Afternoon Lift",
        "timeRange": "12PM - 4PM",
        "videos": []
      },
      "evening": {
        "title": "Evening Essentials", 
        "timeRange": "4PM - 8PM",
        "videos": []
      },
      "bedtime": {
        "title": "Bedtime Prep",
        "timeRange": "8PM - 12:30AM",
        "videos": []
      }
    },
    "summary": {
      "totalVideos": 4,
      "totalEstimatedTime": 15,
      "carriedFromYesterday": 1,
      "newlyGenerated": 3
    }
  }
}
```

#### V1 Compatibility Endpoint
```http
POST /api/dtx/v1/education/generate
```

**Response**: Same structure as current implementation for backward compatibility.

---

### 2. Get Daily Education Plan

#### V2 Enhanced Endpoint
```http
GET /api/dtx/v2/education?currentDate=2025-07-03
```

**Description**: Retrieves the curated daily education plan with time-slot organization (PRD Step 1).

**Query Parameters**:
- `currentDate` (required): Date in YYYY-MM-DD format
- `includeCompleted` (optional): Include completed videos. Default: `true`
- `timeSlot` (optional): Filter by specific time slot

**Response**:
```json
{
  "code": 200,
  "status": "success", 
  "message": "Daily Education Plan Retrieved Successfully",
  "data": {
    "planDate": "2025-07-03",
    "currentTimeSlot": "morning", // Based on current time
    "bufferPeriod": false, // true between 12:00-12:30 AM
    "timeSlots": {
      "morning": {
        "title": "Good Morning, [User Name]",
        "timeRange": "12:30AM - 12PM",
        "status": "active", // "active", "completed", "collapsed"
        "videos": [/* video objects */],
        "progress": {
          "completed": 1,
          "total": 2,
          "completionRate": 50
        }
      }
      // ... other time slots
    },
    "overallProgress": {
      "videosCompleted": 2,
      "totalVideos": 6,
      "quizzesPassed": 2,
      "currentStreak": 3
    }
  }
}
```

#### Buffer Period Response (12:00-12:30 AM)
```json
{
  "code": 200,
  "status": "success",
  "message": "Building Today's Plan",
  "data": {
    "bufferPeriod": true,
    "message": "Your personalized plan will appear here at 12:30 AM",
    "estimatedTime": "15 minutes",
    "previousDay": {
      "date": "2025-07-02",
      "completedVideos": 4,
      "totalVideos": 5
    }
  }
}
```

---

### 3. Quiz Management

#### Submit Quiz Answers
```http
POST /api/dtx/v2/education/quiz/submit
```

**Description**: Handles quiz submission and progress tracking (PRD Step 3 & 4).

**Request Body**:
```json
{
  "educationPlanId": "67aa587a3e05ebd502122187",
  "answers": [
    {
      "questionId": "67aa587a3e05ebd50212214e",
      "selectedOption": "Average blood sugar over 3 months"
    },
    {
      "questionId": "67aa587a3e05ebd50212214f", 
      "selectedOption": "Pancreas"
    },
    {
      "questionId": "67aa587a3e05ebd502122150",
      "selectedOption": "Blood sugar levels increase"
    }
  ],
  "source": "after_video" | "skip_to_quiz"
}
```

**Response**:
```json
{
  "code": 200,
  "status": "success",
  "message": "Quiz Completed Successfully",
  "data": {
    "score": 3,
    "totalQuestions": 3,
    "passed": true, // true if score = totalQuestions
    "results": [
      {
        "questionId": "67aa587a3e05ebd50212214e",
        "correct": true,
        "explanation": "A1C measures your average blood sugar over the past 2-3 months"
      }
    ],
    "lessonStatus": "completed", // "completed" | "needs_review"
    "nextAction": {
      "type": "next_lesson" | "retry_quiz" | "rewatch_video",
      "message": "Great job! You nailed it! This lesson is marked as complete.",
      "nextLessonId": "67aa587a3e05ebd502122188"
    },
    "progress": {
      "streakCount": 4,
      "completedToday": 3,
      "needsReview": []
    }
  }
}
```

#### Get Quiz for Skip-to-Quiz Flow
```http
GET /api/dtx/v2/education/{id}/quiz
```

**Description**: Retrieves quiz questions for the skip-to-quiz functionality (PRD Step 2).

**Response**:
```json
{
  "code": 200,
  "status": "success",
  "message": "Quiz Retrieved Successfully", 
  "data": {
    "educationPlanId": "67aa587a3e05ebd502122187",
    "title": "Understanding A1C Levels",
    "difficulty": "easy",
    "estimatedTime": "2 minutes",
    "questions": [
      {
        "_id": "67aa587a3e05ebd50212214e",
        "question": "What does A1C measure?",
        "options": [
          "Daily blood sugar",
          "Average blood sugar over 3 months", 
          "Blood pressure"
        ]
      }
    ],
    "allowSkipToQuiz": true,
    "videoWatched": false
  }
}
```

---

### 4. Video Progress Tracking

#### Update Video Playback
```http
PATCH /api/dtx/v2/education/video/playback
```

**Request Body**:
```json
{
  "id": "67aa587a3e05ebd502122187",
  "playbackTime": "01:38",
  "watchPercentage": 43.5,
  "sessionDuration": 98, // seconds watched in this session
  "isCompleted": false
}
```

**Response**:
```json
{
  "code": 200,
  "status": "success",
  "message": "Video Progress Updated Successfully",
  "data": {
    "currentProgress": "01:38",
    "totalDuration": "03:45", 
    "watchPercentage": 43.5,
    "canTakeQuiz": true // true if >80% watched or video completed
  }
}
```

#### Mark Video as Completed
```http
PATCH /api/dtx/v2/education/video/status?id=67aa587a3e05ebd502122187
```

**Description**: Marks video viewing as completed, enables quiz taking.

**Response**:
```json
{
  "code": 200,
  "status": "success",
  "message": "Video Completed Successfully",
  "data": {
    "videoCompleted": true,
    "quizUnlocked": true,
    "nextStep": "take_quiz"
  }
}
```

---

### 5. Explore Education Library

#### Get Education Collections
```http
GET /api/dtx/v2/education/explore
```

**Description**: Provides browsable education content library (PRD Step 5).

**Query Parameters**:
- `collection` (optional): Filter by collection name
- `difficulty` (optional): Filter by difficulty level
- `status` (optional): Filter by completion status

**Response**:
```json
{
  "code": 200,
  "status": "success",
  "message": "Education Library Retrieved Successfully",
  "data": {
    "collections": [
      {
        "name": "Basics",
        "description": "Fundamental diabetes knowledge",
        "totalVideos": 15,
        "completedVideos": 8,
        "estimatedTime": 45,
        "videos": [
          {
            "id": "67aa587a3e05ebd502122187",
            "title": "Understanding A1C Levels",
            "description": "Learn what A1C means and why it matters",
            "difficulty": "easy",
            "estimatedTime": 4,
            "status": "completed" | "in_progress" | "not_started" | "needs_review",
            "thumbnail": "https://app-assets.thcdn.in/thumbnails/a1c-levels.jpg"
          }
        ]
      },
      {
        "name": "Nutrition",
        "description": "Food and meal management",
        "totalVideos": 12,
        "completedVideos": 3,
        "estimatedTime": 38,
        "videos": []
      }
    ],
    "needsReview": [
      {
        "id": "67aa587a3e05ebd502122189",
        "title": "Carb Counting Basics",
        "reason": "Quiz failed - needs rewatch",
        "lastAttempt": "2025-07-02T15:30:00Z"
      }
    ],
    "recommended": [
      {
        "id": "67aa587a3e05ebd502122190",
        "title": "Exercise and Blood Sugar",
        "reason": "Based on your recent glucose logs",
        "priority": "high"
      }
    ]
  }
}
```

#### Get Single Video Summary
```http
GET /api/dtx/v2/education/{id}/summary
```

**Description**: Provides video preview information (PRD Step 2).

**Response**:
```json
{
  "code": 200,
  "status": "success",
  "message": "Video Summary Retrieved Successfully",
  "data": {
    "id": "67aa587a3e05ebd502122187",
    "title": "Understanding A1C Levels",
    "description": "Learn what A1C means and why it matters for diabetes management",
    "whatYoullLearn": [
      "What A1C measures in your body",
      "Normal vs. elevated A1C ranges",
      "How to improve your A1C levels"
    ],
    "metadata": {
      "collection": "Basics",
      "difficulty": "easy",
      "estimatedTime": 4,
      "videoType": "basics"
    },
    "video": {
      "thumbnail": "https://app-assets.thcdn.in/thumbnails/a1c-levels.jpg",
      "duration": "03:45",
      "currentProgress": "00:00"
    },
    "quiz": {
      "totalQuestions": 3,
      "passThreshold": 3,
      "alreadyTaken": false,
      "canSkipToQuiz": true
    },
    "actions": {
      "canStartLearning": true,
      "canSkipToQuiz": true,
      "canResume": false
    }
  }
}
```

---

### 6. Progress and Analytics

#### Get Learning Progress
```http
GET /api/dtx/v2/education/progress
```

**Query Parameters**:
- `period` (optional): "day", "week", "month". Default: "week"
- `startDate` (optional): Start date for custom range
- `endDate` (optional): End date for custom range

**Response**:
```json
{
  "code": 200,
  "status": "success",
  "message": "Learning Progress Retrieved Successfully",
  "data": {
    "period": "week",
    "startDate": "2025-06-27",
    "endDate": "2025-07-03",
    "summary": {
      "videosCompleted": 12,
      "quizzesPassed": 10,
      "currentStreak": 5,
      "totalWatchTime": 48, // minutes
      "averageQuizScore": 2.8
    },
    "dailyProgress": [
      {
        "date": "2025-07-03",
        "videosCompleted": 3,
        "quizzesPassed": 3,
        "watchTime": 12
      }
    ],
    "collections": [
      {
        "name": "Basics",
        "completionRate": 80,
        "videosCompleted": 12,
        "totalVideos": 15
      }
    ],
    "achievements": [
      {
        "type": "streak",
        "title": "5-Day Learning Streak!",
        "description": "You've completed lessons for 5 days straight",
        "earnedDate": "2025-07-03"
      }
    ]
  }
}
```

---

## Legacy Support

### Current API Endpoints (Maintained for Backward Compatibility)

#### Generate Education Plan (Legacy)
```http
POST /api/dtx/education/generate
```

#### Get Education Plan (Legacy)
```http
GET /api/dtx/education?startDate=2025-07-01&endDate=2025-07-07
```

#### Update Playback (Legacy)
```http
PATCH /api/dtx/education/video/playback
```

#### Update Status (Legacy)
```http
PATCH /api/dtx/education/video/status?id={id}
```

**Note**: Legacy endpoints maintain the exact same request/response format as the current implementation to ensure no breaking changes.

---

## Response Formats

### Success Response
```json
{
  "code": 200,
  "status": "success", 
  "message": "Operation completed successfully",
  "data": {
    // Response data
  }
}
```

### Error Response
```json
{
  "code": 400,
  "status": "error",
  "message": "Error description",
  "errors": [
    {
      "field": "currentDate",
      "message": "Invalid date format"
    }
  ]
}
```

---

## Error Handling

### Common Error Codes

| Code | Description | Example |
|------|-------------|---------|
| 400 | Bad Request | Invalid date format, missing required fields |
| 401 | Unauthorized | Missing or invalid authentication token |
| 404 | Not Found | Education plan not found for specified date |
| 409 | Conflict | Education plan already exists for this date |
| 500 | Internal Server Error | Database connection issues, unexpected errors |

### Specific Error Scenarios

#### Buffer Period Error
```json
{
  "code": 423,
  "status": "locked",
  "message": "Education plan generation in progress",
  "data": {
    "bufferPeriod": true,
    "availableAt": "2025-07-04T00:30:00Z",
    "message": "Your personalized plan will be ready in 15 minutes"
  }
}
```

#### Quiz Already Completed
```json
{
  "code": 409,
  "status": "conflict",
  "message": "Quiz already completed for this lesson",
  "data": {
    "previousScore": 3,
    "completedAt": "2025-07-03T10:30:00Z",
    "canRetake": true
  }
}
```

---

## Changes from Current Implementation

### Database Schema Enhancements
The following fields need to be added to the `EducationPlan` model:

```typescript
// New fields to add
metadata: {
  collection: string;
  timeOfDay: string;
  difficulty: string;
  videoType: string;
  estimatedWatchTime: number;
}

progress: {
  quizAttempts: number;
  lastQuizScore: number;
  watchPercentage: number;
  needsReview: boolean;
}
```

### New Service Methods Required

1. **Time-based Plan Generation**: Logic to organize videos by time of day
2. **Quiz Result Processing**: Enhanced quiz grading and feedback
3. **Progress Tracking**: Watch percentage and learning analytics
4. **Smart Recommendations**: Algorithm for suggesting next content
5. **Collection Management**: Grouping and filtering by content categories

### API Behavior Changes

1. **Daily Plan Structure**: Changed from flat list to time-slot organization
2. **Quiz Flow**: Enhanced with skip-to-quiz and retry mechanisms  
3. **Progress Tracking**: More granular video progress and analytics
4. **Exploration**: New browsable library with collections and filters
5. **Personalization**: Time-aware content delivery and recommendations

### Migration Strategy

1. **Phase 1**: Deploy V2 endpoints alongside existing V1/Legacy
2. **Phase 2**: Migrate client applications to use V2 endpoints
3. **Phase 3**: Deprecate legacy endpoints after migration period
4. **Phase 4**: Remove legacy endpoints after sufficient notice

---

## Implementation Notes

### Performance Considerations
- Cache daily plans to avoid regeneration on every request
- Implement pagination for explore endpoints
- Use database indexes on userId + date for quick lookups
- Consider CDN for video thumbnails and metadata

### Security
- Validate all user inputs and sanitize data
- Implement rate limiting on quiz submissions
- Ensure video URLs are properly secured and time-limited
- Log all educational activities for audit purposes

### Monitoring
- Track API response times and error rates
- Monitor video completion rates and quiz performance  
- Alert on buffer period failures or plan generation issues
- Measure user engagement metrics per time slot

---

This API contract provides a comprehensive foundation for implementing the Education Module Revamp while maintaining backward compatibility and supporting the enhanced user experience outlined in the PRD.
