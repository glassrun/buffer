---
name: buffer
description: "Post content to Buffer for social media scheduling. Use when: user wants to schedule social media posts, share content to Twitter/Facebook/LinkedIn, or manage social queue."
metadata: { "openclaw": { "emoji": "📱", "requires": { "bins": ["curl"] } } }
---

# Buffer Skill (GraphQL API)

Post content to Buffer for scheduling across social media platforms.

## Setup

Get your Buffer API Key:
1. Log in to Buffer
2. Go to https://publish.buffer.com/settings/api
3. Create an API key

Store it in `credentials/buffer.json`:
```json
{
  "access_token": "YOUR_API_KEY"
}
```

## Commands

### Post to Buffer (share immediately)

```bash
curl -s -X POST 'https://api.buffer.com' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -d '{"query": "mutation { createPost(input: { text: \"Your post\", channelId: \"CHANNEL_ID\", schedulingType: automatic, mode: shareNow }) { ... on PostActionSuccess { post { id text } } ... on MutationError { message } } }"}'
```

### Post to Buffer (schedule for later)

```bash
curl -s -X POST 'https://api.buffer.com' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -d '{"query": "mutation { createPost(input: { text: \"Your post\", channelId: \"CHANNEL_ID\", schedulingType: manual, mode: customScheduled, dueAt: \"2026-03-20T10:00:00Z\" }) { ... on PostActionSuccess { post { id text } } ... on MutationError { message } } }"}'
```

### Add to Queue

```bash
curl -s -X POST 'https://api.buffer.com' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -d '{"query": "mutation { createPost(input: { text: \"Your post\", channelId: \"CHANNEL_ID\", schedulingType: automatic, mode: addToQueue }) { ... on PostActionSuccess { post { id text } } ... on MutationError { message } } }"}'
```

### Get Profiles/Channels

```bash
# First get organization ID
curl -s -X POST 'https://api.buffer.com' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -d '{"query": "query { account { organizations { id } } }"}'

# Then get channels
curl -s -X POST 'https://api.buffer.com' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -d '{"query": "query { channels(input: {organizationId: \"ORG_ID\"}) { id name service } }"}'
```

## Scheduling Types

- `automatic` - Buffer picks optimal time
- `manual` - You specify the time

## Share Modes

- `shareNow` - Post immediately
- `addToQueue` - Add to queue
- `shareNext` - Post next in queue
- `customScheduled` - Schedule for specific time (use `dueAt`)
- `recommendedTime` - Buffer's recommended time

## Examples

**"Post to Buffer: Hello world!"**

```bash
curl -s -X POST 'https://api.buffer.com' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -d '{"query": "mutation { createPost(input: { text: \"Hello world! 🚀\", channelId: \"CHANNEL_ID\", schedulingType: automatic, mode: shareNow }) { ... on PostActionSuccess { post { id text } } ... on MutationError { message } } }"}'
```

## Notes

- Buffer API endpoint: https://api.buffer.com (not bufferapp.com)
- Uses GraphQL, not REST
- Requires Bearer token in Authorization header
- Rate limit: 60 requests per minute
