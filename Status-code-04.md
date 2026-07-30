# NestJS Status Code

## Default Status Code

আগেই বলা হয়েছে, response-এর default status code সবসময় **200** থাকে — শুধুমাত্র POST request-এর ক্ষেত্রে এটা default-এ **201** হয়।

## Status Code পরিবর্তন করা

এই default behavior সহজেই বদলানো যায় — handler-level-এ `@HttpCode(...)` decorator ব্যবহার করে।

```typescript
@Post()
@HttpCode(204)
create() {
  return 'This action adds a new cat';
}
```

এখানে `create()` method একটা POST handler, যেটার default status code হতো 201। কিন্তু `@HttpCode(204)` বসানোর ফলে এখন এই handler সবসময় **204** status code return করবে।

> **Hint:** `HttpCode` decorator-টা `@nestjs/common` package থেকে import করতে হয়।

```typescript
import { HttpCode } from '@nestjs/common';
```

## যখন Status Code Dynamic হওয়া দরকার

`@HttpCode(...)` দিয়ে যেটা set করা হয়, সেটা **static** — মানে একটা fixed value। কিন্তু বাস্তবে অনেক সময় status code বিভিন্ন factor-এর উপর depend করে, সেটা fixed হতে পারে না।

এই ধরনের ক্ষেত্রে দুইটা উপায় আছে:

- **Library-specific response object ব্যবহার করা** — `@Res()` দিয়ে inject করে, নিজে থেকে dynamically status code set করা যায়
- **Error-এর ক্ষেত্রে exception throw করা** — কোনো সমস্যা হলে সরাসরি exception throw করলে Nest সেটা অনুযায়ী appropriate error status code পাঠিয়ে দেয়
