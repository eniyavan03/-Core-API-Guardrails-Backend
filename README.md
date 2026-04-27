
# Core API & Guardrails Backend
## Project Overview

This project is a Spring Boot backend system that manages:
- Users
- Bots
- Posts
- Comments
It uses:
- PostgreSQL for persistent storage  
- Redis for fast operations (virality scoring, limits, cooldowns, notifications)
Goal:
Prevent spam, control bot interactions, and calculate post virality efficiently.

## System Workflow
### 1. Post Creation
- A user creates a post  
- Stored in PostgreSQL  
- Virality score initialized in Redis  
### 2. Comments API
POST /api/posts/{postId}/comments

User Comment:
- Adds +50 virality points

Bot Comment:
- Adds +1 virality point
- Controlled by guardrails:
  - Max depth = 20
  - Max 100 bot comments per post
  - Cooldown between bot and user
### 3. Likes
POST /api/posts/{postId}/like

- Adds +20 virality points
## Redis Usage

Redis is used for:

- Virality scoring  
- Bot comment limits  
- Cooldown control  
- Notification batching  

Example keys:
post:{postId}:virality_score  
post:{postId}:bot_count  
cooldown:bot_{botId}:user_{userId}  
user:{userId}:pending_notifs  
## Guardrails

1. Bot Limit  
   - Maximum 100 bot comments per post  

2. Depth Limit  
   - Maximum depth is 20  

3. Cooldown  
   - Prevents repeated bot replies to the same user  

4. Atomic Operations  
   - Redis ensures thread-safe updates and prevents race conditions  
## Scheduler (CRON Job)

- Runs every 5 minutes  
- Reads pending notifications from Redis  
- Sends summary message:  
  "Bot X and N others interacted with your post"  
- Clears notifications after processing  

## Edge Cases Handled

- Multiple bots commenting at the same time (race conditions handled using Redis)  
- Stateless backend design  
- Redis acts as gatekeeper before DB writes  
- Ensures bot limit is not exceeded  

## Tech Stack

- Java 17  
- Spring Boot  
- PostgreSQL  
- Redis  

## How to Run

1. Start PostgreSQL  

2. Start Redis:
   
3. Run the application:
## API Endpoints

Create User  
POST /api/users  

Create Bot  
POST /api/bots  

Create Post  
POST /api/posts  

Like Post  
POST /api/posts/{postId}/like  

Add Comment  
POST /api/posts/{postId}/comments  

## Virality Logic

User Comment  -> +50  
Bot Comment   -> +1  
Like          -> +20  

## Architecture

Controller → Service → Repository → PostgreSQL  
                     ↓  
                   Redis  
## Key Highlights

- High-performance backend using Redis  
- Strong guardrails to prevent spam  
- Background processing using scheduler  
- Handles concurrency safely  
- Clean layered architecture  

---

## Author

Eniyavan E
