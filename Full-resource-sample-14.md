# NestJS Full Resource Sample

## কেন এই Example

এতক্ষণ আমরা যে যে decorator আলাদা আলাদা ভাবে শিখেছি — `@Get()`, `@Post()`, `@Put()`, `@Delete()`, `@Body()`, `@Param()`, `@Query()` — এখন সেগুলো একসাথে ব্যবহার করে একটা সম্পূর্ণ, basic controller বানানো হয়েছে। এই controller-টা internal data access আর manipulate করার জন্য কয়েকটা method provide করে।

## পুরো Code

```typescript
import { Controller, Get, Query, Post, Body, Put, Param, Delete } from '@nestjs/common';
import { CreateCatDto, UpdateCatDto, ListAllEntities } from './dto';

@Controller('cats')
export class CatsController {
  @Post()
  create(@Body() createCatDto: CreateCatDto) {
    return 'This action adds a new cat';
  }

  @Get()
  findAll(@Query() query: ListAllEntities) {
    return `This action returns all cats (limit: ${query.limit} items)`;
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    return `This action returns a #${id} cat`;
  }

  @Put(':id')
  update(@Param('id') id: string, @Body() updateCatDto: UpdateCatDto) {
    return `This action updates a #${id} cat`;
  }

  @Delete(':id')
  remove(@Param('id') id: string) {
    return `This action removes a #${id} cat`;
  }
}
```

## প্রতিটা Method ভেঙে বোঝা

### `create()` — নতুন Cat যোগ করা

```typescript
@Post()
create(@Body() createCatDto: CreateCatDto) {
  return 'This action adds a new cat';
}
```

- Route: `POST /cats`
- Client-এর পাঠানো request body-টা `@Body()` দিয়ে `CreateCatDto` type হিসেবে আসছে
- এটা নতুন একটা cat তৈরি করার জন্য ব্যবহার হয়

### `findAll()` — সব Cat Fetch করা (Query সহ)

```typescript
@Get()
findAll(@Query() query: ListAllEntities) {
  return `This action returns all cats (limit: ${query.limit} items)`;
}
```

- Route: `GET /cats`
- `@Query()` দিয়ে পুরো query string object হিসেবে `ListAllEntities` type-এ আসছে
- এখানে যেমন `query.limit` ব্যবহার করে দেখানো হয়েছে, ঠিক তেমনি অন্য যেকোনো query field access করা যায়

### `findOne()` — নির্দিষ্ট একটা Cat Fetch করা

```typescript
@Get(':id')
findOne(@Param('id') id: string) {
  return `This action returns a #${id} cat`;
}
```

- Route: `GET /cats/:id`
- `@Param('id')` দিয়ে URL-এর ভেতর থাকা `id` সরাসরি access করা হচ্ছে
- এই route static `GET /cats`-এর **পরে** declare করা হয়েছে, যাতে static route intercept না হয়

### `update()` — নির্দিষ্ট Cat Update করা

```typescript
@Put(':id')
update(@Param('id') id: string, @Body() updateCatDto: UpdateCatDto) {
  return `This action updates a #${id} cat`;
}
```

- Route: `PUT /cats/:id`
- এখানে দুইটা decorator একসাথে ব্যবহার হচ্ছে — `@Param('id')` দিয়ে কোন cat update হবে সেটা বোঝা যাচ্ছে, আর `@Body()` দিয়ে নতুন data (`UpdateCatDto`) আসছে

### `remove()` — নির্দিষ্ট Cat মুছে ফেলা

```typescript
@Delete(':id')
remove(@Param('id') id: string) {
  return `This action removes a #${id} cat`;
}
```

- Route: `DELETE /cats/:id`
- `@Param('id')` দিয়ে কোন cat delete হবে সেটা বোঝানো হচ্ছে

## Nest CLI নিয়ে Hint

Nest CLI একটা generator (schematic) provide করে, যেটা এই ধরনের সব boilerplate code **automatically** তৈরি করে দিতে পারে। এর ফলে এই সব manually লেখার দরকার পড়ে না, আর overall developer experience-ও অনেক ভালো হয়।

> বিস্তারিত জানতে চাইলে NestJS docs-এর [CRUD generator](https://docs.nestjs.com/recipes/crud-generator) recipe দেখা যেতে পারে।

## সংক্ষেপে

| Method | HTTP Method | Route | কাজ |
|---|---|---|---|
| `create()` | POST | `/cats` | নতুন cat যোগ করা |
| `findAll()` | GET | `/cats` | সব cat fetch করা (query সহ) |
| `findOne()` | GET | `/cats/:id` | নির্দিষ্ট cat fetch করা |
| `update()` | PUT | `/cats/:id` | নির্দিষ্ট cat update করা |
| `remove()` | DELETE | `/cats/:id` | নির্দিষ্ট cat delete করা |
