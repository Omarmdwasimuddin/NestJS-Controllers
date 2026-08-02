# NestJS Library-specific Approach

## এতক্ষণ কী শেখা হয়েছে

এতক্ষণ আমরা response manipulate করার Nest-এর **standard** পদ্ধতি নিয়ে কথা বলেছি। এখন দেখা যাক আরেকটা approach — **library-specific response object** ব্যবহার করা।

নির্দিষ্ট একটা response object inject করার জন্য `@Res()` decorator ব্যবহার করা হয়। পার্থক্যটা বোঝার জন্য, `CatsController`-কে নতুন করে লিখে দেখা যাক।

## Example

```typescript
import { Controller, Get, Post, Res, HttpStatus } from '@nestjs/common';
import { Response } from 'express';

@Controller('cats')
export class CatsController {
  @Post()
  create(@Res() res: Response) {
    res.status(HttpStatus.CREATED).send();
  }

  @Get()
  findAll(@Res() res: Response) {
     res.status(HttpStatus.OK).json([]);
  }
}
```

এখানে দেখা যাচ্ছে, `@Res() res: Response` দিয়ে সরাসরি Express-এর response object inject করা হচ্ছে, আর সেটার উপর সরাসরি `res.status(...)`, `res.send()`, `res.json(...)` — এই ধরনের native method call করা হচ্ছে।

## এই Approach-এর সুবিধা

এই পদ্ধতি কাজ করে, আর response object-এর উপর সম্পূর্ণ control দেয় — যেমন header manipulation, বা library-specific অন্যান্য feature access করার সুযোগ।

## কেন সতর্ক থাকা দরকার

তবে এই approach **সতর্কতার সাথে** ব্যবহার করা উচিত, কারণ সাধারণত এটা কম clear, আর এর সাথে কিছু downside যুক্ত।

### প্রধান সমস্যা: Platform-dependent হয়ে যাওয়া

Code platform-dependent হয়ে যায়, কারণ underlying library (Express, Fastify ইত্যাদি) ভেদে response object-এর API আলাদা আলাদা হতে পারে। ফলে এক platform-এর জন্য লেখা code অন্য platform-এ সরাসরি কাজ নাও করতে পারে।

### দ্বিতীয় সমস্যা: Testing কঠিন হয়ে যাওয়া

Testing-ও তুলনামূলক challenging হয়ে যায়, কারণ response object-কে mock করতে হয়, আর সেই সাথে আরও কিছু জিনিস সামলাতে হয়।

### তৃতীয় সমস্যা: Nest-এর কিছু Feature-এর সাথে Compatibility হারানো

এই approach ব্যবহার করলে, standard response handling-এর উপর নির্ভরশীল Nest-এর feature গুলোর সাথে compatibility হারিয়ে যায়। যেমন:

- **Interceptors**
- `@HttpCode()` decorator
- `@Header()` decorator

## সমাধান: `passthrough` Option

এই compatibility সমস্যা সমাধান করার জন্য `passthrough` option enable করা যায়:

```typescript
@Get()
findAll(@Res({ passthrough: true }) res: Response) {
  res.status(HttpStatus.OK);
  return [];
}
```

এই approach-এ, native response object-এর সাথে interact করা যায় (যেমন নির্দিষ্ট condition অনুযায়ী cookie বা header set করা), কিন্তু বাকি কাজটা framework-কেই করতে দেওয়া হয় — অর্থাৎ response আসলে পাঠানোর কাজটা এখনো Nest নিজে সামলায়, শুধু status/header-এর মতো জিনিস manually adjust করার সুযোগ থাকে।

## সংক্ষেপে

| বিষয় | ব্যাখ্যা |
|---|---|
| Inject করার decorator | `@Res()` |
| সুবিধা | Response object-এর উপর full control |
| প্রধান downside | Code platform-dependent হয়ে যায় |
| আরেকটা downside | Testing কঠিন হয়ে যায় (mocking লাগে) |
| Compatibility ক্ষতি | Interceptors, `@HttpCode()`, `@Header()`-এর সাথে কাজ করে না |
| সমাধান | `@Res({ passthrough: true })` — native access + Nest-এর সুবিধা দুটোই থাকে |
