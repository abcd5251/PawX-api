# WebSocket API Documentation

## Overview

We provides a real-time WebSocket API for receiving Twitter user updates, including tweets, profile changes, and follower updates.

## Connection

### Endpoint

```
wss://your-server-address/ws/[clientId]
```

## Subscribing to Users

### Subscribe to a User

To receive updates for a specific Twitter user, send a subscription message:

```json
{
  "type": "subscribe",
  "twitterUsername": "elonmusk"
}
```

### Unsubscribe from a User

To stop receiving updates:

```json
{
  "type": "unsubscribe",
  "twitterUsername": "elonmusk"
}
```

## Message Types

### 1. User Update Message

When a subscribed user posts a tweet or updates their profile, you'll receive:

```json
{
  "type": "user-update",
  "data": {
    "twitterUser": {
      "id": "44196397",
      "name": "Elon Musk",
      "screenName": "elonmusk",
      "location": "Texas, USA",
      "description": "Tesla, SpaceX, Neuralink, X",
      "website": "https://twitter.com/elonmusk",
      "friendsCount": 500,
      "statusesCount": 43210,
      "profileImageUrlHttps": "https://pbs.twimg.com/profile_images/...",
      "profileBannerUrl": "https://pbs.twimg.com/profile_banners/...",
      "protectedAt": null
    },
    "changes": {
      "lastTweetId": {
        "old": "1798234567890123456",
        "new": "1798234567890123789"
      }
    },
    "status": {
      "created_at": "Wed Dec 10 12:34:56 +0000 2025",
      "id_str": "1798234567890123789",
      "full_text": "Just launched Starship! 🚀",
      "user": {
        "id_str": "44196397",
        "name": "Elon Musk",
        "screen_name": "elonmusk"
      },
      "entities": {
        "hashtags": [],
        "urls": [],
        "user_mentions": [],
        "media": []
      },
      "retweet_count": 0,
      "favorite_count": 0,
      "lang": "en"
    }
  }
}
```

### 2. User Full Tweet Message (API Users Only)

After the initial `user-update` message, API users **may** receive an enhanced message with full tweet details. This message is sent when:

- The tweet text is truncated
- The tweet is a reply (includes the replied-to tweet)
- The tweet quotes another tweet (includes the quoted tweet)

```json
{
  "type": "user-full-tweet",
  "data": {
    "userId": "2201704636",
    "status": {
      "created_at": "Mon Dec 16 11:33:09 +0000 2025",
      "id_str": "2000891948434120849",
      "full_text": "@alliekmiller aaaa",
      "truncated": false,
      "user": {
        "id_str": "2201704636",
        "name": "bunela",
        "screen_name": "yqffz56498982"
      },
      "entities": {
        "hashtags": [],
        "symbols": [],
        "urls": [],
        "user_mentions": [
          {
            "screen_name": "alliekmiller",
            "name": "Allie K. Miller",
            "id_str": "39289455",
            "indices": [0, 13]
          }
        ]
      },
      "in_reply_to_status_id_str": "2000671547380404606",
      "in_reply_to_user_id_str": "39289455",
      "in_reply_to_screen_name": "alliekmiller",
      "retweet_count": 0,
      "favorite_count": 0,
      "lang": "en"
    },
    "relatedFullTweets": [
      {
        "id": "2000671547380404606",
        "userId": "39289455",
        "text": "Voice AI is going to explode in 2026...",
        "fullText": "Voice AI is going to explode in 2026. Here's what I'm seeing...",
        "truncated": true,
        "entities": {
          "hashtags": [],
          "symbols": [],
          "urls": [],
          "user_mentions": []
        },
        "medias": [
          {
            "id_str": "2000671542464704512",
            "type": "photo",
            "media_url_https": "https://pbs.twimg.com/media/G8PQKvjXcAAupvN.jpg",
            "url": "https://t.co/d6ATSgNWTD",
            "sizes": {
              "large": { "h": 2048, "w": 1536, "resize": "fit" }
            }
          }
        ],
        "favoriteCount": 1166,
        "retweetCount": 134,
        "createdAt": "2025-12-15T20:57:22.000Z",
        "updatedAt": "2025-12-16T11:33:11.311Z",
        "bookmarkCount": 865,
        "viewCount": 106742,
        "quoteCount": 58,
        "replyCount": 216,
        "conversationId": "2000671547380404606",
        "user": {
          "id": "39289455",
          "name": "Allie K. Miller",
          "screenName": "alliekmiller",
          "followersCount": 78246
        }
      }
    ]
  }
}
```

## Data Fields

### `twitterUser` Object

| Field                  | Type         | Description                       |
| ---------------------- | ------------ | --------------------------------- |
| `id`                   | string       | Twitter user ID                   |
| `name`                 | string       | Display name                      |
| `screenName`           | string       | Username (handle)                 |
| `location`             | string       | User's location                   |
| `description`          | string       | Bio/description                   |
| `website`              | string       | User's website URL                |
| `friendsCount`         | number       | Number of accounts following      |
| `statusesCount`        | number       | Total tweet count                 |
| `profileImageUrlHttps` | string       | Profile picture URL               |
| `profileBannerUrl`     | string       | Banner image URL                  |
| `protectedAt`          | Date \| null | Timestamp if account is protected |

### `changes` Object

Contains fields that changed since last update:

```typescript
{
  [fieldName: string]: {
    old: any,
    new: any
  }
}
```

Common change fields:

- `statusesCount` - Tweet count changed
- `lastTweetId` - New tweet posted
- `name` - Display name changed
- `description` - Bio changed
- `profileImageUrlHttps` - Avatar changed
- `profileBannerUrl` - Banner changed
- `friendsCount` - Following count changed

### `status` Object (Tweet)

Full tweet object when a new tweet is posted.

**Note:** The `status` object in both `user-update` and `user-full-tweet` messages uses the same standard format below. However, items in the `relatedFullTweets` array use a different extended format with additional fields.

**Standard format (used in both user-update and user-full-tweet status):**

| Field                       | Type           | Description                            |
| --------------------------- | -------------- | -------------------------------------- |
| `created_at`                | string         | Tweet timestamp                        |
| `id_str`                    | string         | Tweet ID                               |
| `full_text`                 | string         | Complete tweet text                    |
| `entities`                  | object         | Hashtags, URLs, mentions, media        |
| `extended_entities`         | object         | Additional media info (photos, videos) |
| `in_reply_to_status_id_str` | string \| null | ID of tweet being replied to           |
| `in_reply_to_user_id_str`   | string \| null | ID of user being replied to            |
| `in_reply_to_screen_name`   | string \| null | Username being replied to              |
| `quoted_status_id_str`      | string         | ID of quoted tweet                     |
| `quoted_status_permalink`   | object         | URL info for quoted tweet              |
| `retweet_count`             | number         | Number of retweets                     |
| `favorite_count`            | number         | Number of likes                        |
| `retweeted_status`          | object         | Original tweet if this is a retweet    |
| `lang`                      | string         | Language code                          |

**Extended format (items in relatedFullTweets array only):**

Items in the `relatedFullTweets` array have additional fields beyond the standard format:

| Field                      | Type           | Description                                                                      |
| -------------------------- | -------------- | -------------------------------------------------------------------------------- |
| `id`                       | string         | Tweet ID                                                                         |
| `userId`                   | string         | User ID who posted                                                               |
| `text`                     | string         | Original tweet text if retweet, otherwise tweet text (may be truncated)          |
| `fullText`                 | string         | Original full text if retweet of long tweet, otherwise complete untruncated text |
| `truncated`                | boolean        | Whether the text is truncated                                                    |
| `createdAt`                | string         | ISO 8601 timestamp                                                               |
| `updatedAt`                | string         | ISO 8601 timestamp of last update                                                |
| `entities`                 | object         | Hashtags, symbols, URLs, mentions (same as standard)                             |
| `medias`                   | array \| null  | Array of media objects (photos, videos)                                          |
| `inReplyToStatusIdStr`     | string \| null | ID of tweet being replied to                                                     |
| `inReplyToUserIdStr`       | string \| null | ID of user being replied to                                                      |
| `inReplyToUserScreenName`  | string \| null | Username being replied to                                                        |
| `quotedStatusIdStr`        | string \| null | ID of quoted tweet                                                               |
| `quotedUserIdStr`          | string \| null | ID of user whose tweet was quoted                                                |
| `quotedUserScreenName`     | string \| null | Username of quoted tweet author                                                  |
| `retweetedStatusIdStr`     | string \| null | ID of retweeted tweet                                                            |
| `retweetedUserIdStr`       | string \| null | ID of original tweet author (for retweets)                                       |
| `retweetedUserScreenName`  | string \| null | Username of original tweet author (for retweets)                                 |
| `retweetedStatusCreatedAt` | string \| null | Timestamp of original tweet (for retweets)                                       |
| `favoriteCount`            | number         | Number of likes                                                                  |
| `retweetCount`             | number         | Number of retweets                                                               |
| `replyCount`               | number         | Number of replies                                                                |
| `quoteCount`               | number         | Number of quote tweets                                                           |
| `viewCount`                | number \| null | Number of views (if available)                                                   |
| `bookmarkCount`            | number \| null | Number of bookmarks (if available)                                               |
| `conversationId`           | string         | ID of the conversation thread                                                    |
| `mentionedUsers`           | array          | Array of user objects mentioned in the tweet                                     |
| `user`                     | object         | Full user object of the tweet author                                             |
| `notetweetEntities`        | object         | Extended entities for long-form tweets (Note)                                    |

### `relatedFullTweets` Array

Array of full tweet objects that provide context for the main tweet. Each object uses the extended format (see table above) with additional fields beyond the standard status format.

This allows you to display complete conversation context without additional API calls.

### `entities` Object

```typescript
{
  "hashtags": [
    {
      "text": "launch",
      "indices": [0, 7]
    }
  ],
  "urls": [
    {
      "url": "https://t.co/xyz",
      "expanded_url": "https://example.com/full-url",
      "display_url": "example.com/full-url",
      "indices": [10, 33]
    }
  ],
  "user_mentions": [
    {
      "screen_name": "NASA",
      "name": "NASA",
      "id_str": "11348282",
      "indices": [15, 20]
    }
  ],
  "media": [
    {
      "id_str": "1234567890",
      "media_url": "http://pbs.twimg.com/media/xyz.jpg",
      "media_url_https": "https://pbs.twimg.com/media/xyz.jpg",
      "url": "https://t.co/xyz",
      "display_url": "pic.twitter.com/xyz",
      "expanded_url": "https://twitter.com/user/status/123/photo/1",
      "type": "photo",
      "sizes": {
        "large": { "w": 1024, "h": 768, "resize": "fit" },
        "medium": { "w": 600, "h": 450, "resize": "fit" },
        "small": { "w": 340, "h": 255, "resize": "fit" },
        "thumb": { "w": 150, "h": 150, "resize": "crop" }
      }
    }
  ]
}
```

## Example Client Implementation

### Basic Implementation (Webapp)

```javascript
const ws = new WebSocket('ws://localhost:8080/ws/my-client-id')

// Connection opened
ws.addEventListener('open', (event) => {
  console.log('Connected to WebSocket')

  // Subscribe to a user
  ws.send(
    JSON.stringify({
      type: 'subscribe',
      twitterUsername: 'elonmusk'
    })
  )
})

// Listen for messages
ws.addEventListener('message', (event) => {
  const message = JSON.parse(event.data)

  if (message.type === 'user-update') {
    const { twitterUser, changes, status } = message.data

    // Check if it's a new tweet
    if (status) {
      console.log(`New tweet from @${twitterUser.screenName}:`)
      console.log(status.full_text)
    }

    // Check for profile changes
    if (changes.name) {
      console.log(`Name changed: ${changes.name.old} → ${changes.name.new}`)
    }

    if (changes.profileImageUrlHttps) {
      console.log('Profile picture updated!')
    }
  }
})

// Connection closed
ws.addEventListener('close', (event) => {
  console.log('Disconnected from WebSocket')
  // Implement reconnection logic here
})

// Error handling
ws.addEventListener('error', (error) => {
  console.error('WebSocket error:', error)
})
```

## Best Practices

1. **Deduplication**: Implement client-side deduplication using tweet IDs
2. **Reconnection**: Implement exponential backoff for reconnections
3. **Subscription Management**: Track active subscriptions and resubscribe on reconnect
4. **Memory Management**: Clean up old messages to prevent memory leaks
5. **Error Logging**: Log all errors and connection issues for debugging
6. **Heartbeat**: Implement ping/pong to detect dead connections
7. **Handle Both Message Types** (API users): The `user-update` arrives first (fast), then `user-full-tweet` arrives with complete context. Design your UI to handle both:
   - Show tweet immediately from `user-update`
   - Enhance with context when `user-full-tweet` arrives

## Troubleshooting

### Not Receiving Updates

- Verify the user is subscribed (check subscription confirmation)
- Ensure the Twitter user is public (not protected)
- Check if the user is in the subscribed list
- Verify WebSocket connection is still active

### Duplicate Messages

- The server implements deduplication, but network issues may cause duplicates
- Implement client-side deduplication using tweet `id_str`

### Missing Fields

- Some fields may be `null` if data is unavailable
- Always check for field existence before accessing nested properties

### Not Receiving Full Tweet Data

- `user-full-tweet` messages are **only sent to API users** (requires API authentication)
- `user-full-tweet` is **only sent when needed** (truncated tweets, replies, or quotes)
- Simple standalone tweets will NOT trigger a `user-full-tweet` message
