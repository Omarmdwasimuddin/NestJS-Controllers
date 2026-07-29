# NestJS Request Object

## কেন দরকার হয়

Handler-এর মধ্যে অনেক সময় client-এর পাঠানো request-এর details access করার দরকার হয় — যেমন query string, headers, body ইত্যাদি। Nest এই request object-টা underlying platform (default-এ Express) থেকে access করার সুযোগ দেয়।

## কীভাবে access করা যায়

Handler-এর signature-এ `@Req()` decorator ব্যবহার করে Nest-কে বলে দেওয়া যায় যে, request object-টা inject করে দিতে হবে।

```typescript
import { Controller, Get, Req } from '@nestjs/common';
import type { Request } from 'express';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(@Req() request: Request): string {
    return 'This action returns all cats';
  }
}
```

> **Tip:** উপরের example-এ `request: Request` টাইপ ব্যবহার করে Express-এর typing সুবিধা নিতে চাইলে, `@types/express` package টা install করে নিতে হবে। Install command is `npm install -D @types/express`

## Request object-এ কী থাকে

Request object আসলে HTTP request-টাকেই represent করে, আর এর ভেতরে থাকে —

- Query string
- Route parameters
- HTTP headers
- Body

বেশিরভাগ ক্ষেত্রেই এই সব property manually access করার দরকার পড়ে না। এর বদলে Nest-এর built-in dedicated decorator গুলো (যেমন `@Body()`, `@Query()`) ব্যবহার করাই সুবিধাজনক — এগুলো out of the box পাওয়া যায়।

## Platform-specific Decorator গুলোর List

| Decorator | কী represent করে |
|---|---|
| `@Request()`, `@Req()` | `req` |
| `@Response()`, `@Res()` * | `res` |
| `@Next()` | `next` |
| `@Session()` | `req.session` |
| `@Param(key?: string)` | `req.params` / `req.params[key]` |
| `@Body(key?: string)` | `req.body` / `req.body[key]` |
| `@Query(key?: string)` | `req.query` / `req.query[key]` |
| `@Headers(name?: string)` | `req.headers` / `req.headers[name]` |
| `@Ip()` | `req.ip` |
| `@HostParam()` | `req.hosts` |

## `@Res()` / `@Response()` নিয়ে বিশেষ নোট

Express আর Fastify — এই দুই ধরনের underlying HTTP platform-এর typings-এর সাথে compatibility রাখার জন্য Nest `@Res()` আর `@Response()` decorator provide করে।

কিছু গুরুত্বপূর্ণ পয়েন্ট:

- `@Res()` আসলে `@Response()`-এরই একটা **alias** — দুটো একই জিনিস।
- এই দুইটা decorator সরাসরি underlying native platform-এর response object interface expose করে।
- এগুলো ব্যবহার করার সময় corresponding library-এর typings (যেমন `@types/express`) import করে নেওয়া উচিত, তাহলে পুরো সুবিধা পাওয়া যাবে।
- **সবচেয়ে গুরুত্বপূর্ণ বিষয়:** কোনো method handler-এ `@Res()` বা `@Response()` inject করলে, সেই handler-এর জন্য Nest **Library-specific mode**-এ চলে যায়। এর মানে হলো, response manage করার পুরো দায়িত্ব তখন নিজেকেই নিতে হয়।
- এই mode ব্যবহার করলে অবশ্যই response object-এর উপর কোনো call করে response পাঠাতে হবে (যেমন `res.json(...)` বা `res.send(...)`) — নাহলে HTTP server hang হয়ে থাকবে, কোনো response client-এর কাছে যাবে না।
