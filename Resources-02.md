# NestJS Resources

## কেন দরকার হয়

আগে আমরা cats resource fetch করার জন্য একটা endpoint বানিয়েছিলাম (GET route)। কিন্তু বাস্তবে সাধারণত শুধু data fetch করলেই চলে না — নতুন record তৈরি করার জন্যও একটা endpoint দরকার হয়। এর জন্য POST handler ব্যবহার করা হয়।

## Example

```typescript
import { Controller, Get, Post } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Post()
  create(): string {
    return 'This action adds a new cat';
  }

  @Get()
  findAll(): string {
    return 'This action returns all cats';
  }
}
```

এখানে দেখা যাচ্ছে, একই `CatsController`-এর ভেতরে দুইটা আলাদা handler:

- `@Post()` → নতুন cat যোগ করার জন্য (`POST /cats`)
- `@Get()` → সব cat fetch করার জন্য (`GET /cats`)

দুইটার route path একই (`/cats`), কিন্তু HTTP method আলাদা বলে Nest এদের আলাদা handler হিসেবে চেনে।

## Standard HTTP Method Decorator গুলো

Nest সব standard HTTP method-এর জন্য আলাদা আলাদা decorator provide করে:

| Decorator | HTTP Method |
|---|---|
| `@Get()` | GET |
| `@Post()` | POST |
| `@Put()` | PUT |
| `@Delete()` | DELETE |
| `@Patch()` | PATCH |
| `@Options()` | OPTIONS |
| `@Head()` | HEAD |

এছাড়াও `@All()` decorator আছে, যেটা দিয়ে একটা endpoint বানানো যায় যেটা **সব HTTP method** handle করতে পারবে — অর্থাৎ GET, POST, PUT ইত্যাদি যেকোনো method-এ request আসলেই সেই একই handler এটাকে respond করবে।
