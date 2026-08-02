# NestJS Query Parameters

## কেন দরকার হয়

Route-এ query parameter handle করার জন্য `@Query()` decorator ব্যবহার করা যায় — এর মাধ্যমে incoming request থেকে query string-এর value গুলো extract করা যায়।

## Example: Basic Query Parameter

ধরা যাক, `age` আর `breed` — এই query parameter অনুযায়ী cats-এর list filter করতে হবে। প্রথমে `CatsController`-এ query parameter গুলো define করা যাক:

```typescript
@Get()
async findAll(@Query('age') age: number, @Query('breed') breed: string) {
  return `This action returns all cats filtered by age: ${age} and breed: ${breed}`;
}
```

এখানে `@Query()` decorator ব্যবহার করে query string থেকে `age` আর `breed`-এর value extract করা হচ্ছে।

উদাহরণস্বরূপ, নিচের request-টা করলে:

```
GET /cats?age=2&breed=Persian
```

তার ফলাফল হবে — `age` হবে `2`, আর `breed` হবে `Persian`।

## জটিল Query Parameter নিয়ে কাজ করা

যদি application-এ আরও জটিল ধরনের query parameter handle করতে হয় — যেমন nested object বা array:

```
?filter[where][name]=John&filter[where][age]=30
?item[]=1&item[]=2
```

তাহলে এই ধরনের data ঠিকভাবে parse করার জন্য, HTTP adapter (Express অথবা Fastify)-এ একটা উপযুক্ত **query parser** configure করে দিতে হয়।

### Express-এর ক্ষেত্রে

Express-এ `extended` parser ব্যবহার করা যায়, যেটা rich query object support করে:

```typescript
const app = await NestFactory.create<NestExpressApplication>(AppModule);
app.set('query parser', 'extended');
```

### Fastify-এর ক্ষেত্রে

Fastify-এ `querystringParser` option ব্যবহার করা যায়:

```typescript
const app = await NestFactory.create<NestFastifyApplication>(
  AppModule,
  new FastifyAdapter({
    querystringParser: (str) => qs.parse(str),
  }),
);
```

> **Hint:** `qs` হলো একটা querystring parser, যেটা nesting আর array — দুইটাই support করে। এটা install করার জন্য `npm install qs` command ব্যবহার করা যায়।
| Express-এর সমাধান | `app.set('query parser', 'extended')` |
| Fastify-এর সমাধান | `FastifyAdapter`-এ `querystringParser` option |
| জটিল parsing-এর জন্য package | `qs` (`npm install qs`) |
