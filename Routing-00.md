## NestJS Routing

## Controller কী

`@Controller()` decorator দিয়ে একটা basic controller define করা হয়। এর ভেতরে আমরা চাইলে একটা optional route path prefix দিতে পারি।

```typescript
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(): string {
    return 'This action returns all cats';
  }
}
```

এখানে `cats` হলো prefix। এই prefix দেওয়ার সুবিধা হলো — related route গুলোকে একসাথে group করা যায়, আর বারবার একই path repeat করতে হয় না। যেমন, cat entity সংক্রান্ত সব route যদি `/cats` path-এর নিচে রাখতে চাই, তাহলে প্রতিটা route-এ আলাদাভাবে `/cats` লেখার দরকার নেই — controller-এ একবার prefix দিলেই হয়ে যায়।

#### Create controller
```bash
nest g controller cats
```
![](https://imgur.com/iep4EBB.png)

---

#### cats.controller.ts
```bash
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
    @Get()
    findAll(): string{
        return 'This action returns all cats';
    }
}
```
---

#### Output
![](https://imgur.com/ow8dCRy.png)

---

## Route Path কীভাবে তৈরি হয়

`findAll()` method-এর আগে বসানো `@Get()` decorator Nest-কে বলে দেয় যে, এটা একটা নির্দিষ্ট HTTP endpoint-এর জন্য handler। এই endpoint নির্ধারিত হয় দুইটা জিনিস দিয়ে:

- HTTP request method (এখানে GET)
- Route path

**Route path = Controller-এর prefix + Method decorator-এ দেওয়া path**

#### cats.controller.ts
```bash
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
    @Get('breed')
    findAll(): string{
        return 'This action returns all cats';
    }
}
```
![](https://imgur.com/sl13e4q.png)

---

যখন `GET /cats`-এ কোনো request আসে, Nest সেটাকে user-defined `findAll()` method-এ route করে দেয়। এখানে method-এর নাম (`findAll`) সম্পূর্ণ arbitrary — Nest এই নামের সাথে কোনো নির্দিষ্ট গুরুত্ব যুক্ত করে না। শুধু route bind করার জন্য একটা method declare করতে হয়, নাম যা খুশি দেওয়া যায়।

## Response কীভাবে কাজ করে

`findAll()` method একটা string return করে, আর সেটার সাথে automatically **200 status code** পাঠানো হয়। কেন এমন হয় বুঝতে হলে জানতে হবে, Nest response manipulate করার জন্য দুইটা আলাদা option দেয়:

### ১. Standard way (recommended)

এই built-in পদ্ধতিতে —

- Request handler যদি একটা JavaScript object বা array return করে, সেটা automatically JSON-এ **serialize** হয়ে যায়।
- কিন্তু handler যদি কোনো primitive type return করে (যেমন string, number, boolean), তাহলে Nest সেই value-টা serialize করার চেষ্টা না করে সরাসরি পাঠিয়ে দেয়।

এর ফলে response handling খুবই সহজ হয়ে যায় — শুধু value return করলেই হয়, বাকিটা Nest নিজে সামলে নেয়।

এছাড়া, response-এর status code by default সবসময় **200** থাকে, শুধু POST request-এর ক্ষেত্রে সেটা **201** হয়। এই behavior সহজেই বদলানো যায় handler-level-এ `@HttpCode(...)` decorator যোগ করে।

### ২. Library-specific way

এখানে library-specific (যেমন Express) response object সরাসরি ব্যবহার করা যায়, যেটা method handler-এর signature-এ `@Res()` decorator দিয়ে inject করা হয় — যেমন `findAll(@Res() response)`।

এই approach ব্যবহার করলে সেই object-এর native response handling method গুলো সরাসরি ব্যবহার করার সুযোগ থাকে। যেমন Express-এর ক্ষেত্রে, response এভাবে বানানো যায়:

```typescript
response.status(200).send();
```

---
