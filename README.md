# Telegram API Proxy Worker

A simple Cloudflare Worker that securely proxies requests to the official [Telegram Bot API](https://core.telegram.org/bots/api)

This is useful when:
- You need to bypass restrictions on Telegram’s API (for example, in regions where Telegram is blocked)
- You don't like [Akhunds](https://en.wikipedia.org/wiki/Akhund)

## Deployment

1. Go to [Cloudflare Workers](https://workers.cloudflare.com/)
2. Create a new Worker
3. Paste in the code below
4. Deploy

### Worker Code

```js
export default {
  async fetch(request) {
      const url = new URL(request.url);
 
      const telegramAPI = "https://api.telegram.org" + url.pathname + url.search;

      const fetchOptions = {
          method: request.method,
          headers: request.headers,
      };

      if (request.method !== "GET" && request.method !== "HEAD") {
          fetchOptions.body = await request.text();
      }

      const response = await fetch(telegramAPI, fetchOptions);

      return new Response(response.body, {
          status: response.status,
          headers: response.headers,
      });
  }
};
```

## Usage

```
https://{your-worker-name}.workers.dev/{your-bot-token}/getMe
```

### Example

```
https://telegram-api.example.workers.dev/bot7049328752:AAE25roREFlucNVE90VGR4o7XApJ5yueHMk/getMe
```

```json
{
  "ok": true,
  "result": {
    "id": 7328752049,
    "is_bot": true,
    "first_name": "Example",
    "username": "example_bot",
    "can_join_groups": true,
    "can_read_all_group_messages": false,
    "supports_inline_queries": false,
    "can_connect_to_business": false,
    "has_main_web_app": false
  }
}
```
