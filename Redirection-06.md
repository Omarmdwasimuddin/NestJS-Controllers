# NestJS Redirection

## কেন দরকার হয়

কখনো কখনো response-কে একটা নির্দিষ্ট URL-এ redirect করার দরকার হয় — যেমন কোনো পুরনো route থেকে নতুন route-এ পাঠানো, বা external site-এ নিয়ে যাওয়া।

## কীভাবে Redirect করা যায়

Response redirect করার জন্য দুইটা উপায় আছে:

1. **`@Redirect()` decorator ব্যবহার করে**
2. **Library-specific response object ব্যবহার করে** — `res.redirect()` সরাসরি call করে

## `@Redirect()` Decorator

`@Redirect()` দুইটা argument নেয়, দুইটাই **optional**:

- `url` — কোন URL-এ redirect হবে
- `statusCode` — কোন HTTP status code দিয়ে redirect হবে

যদি `statusCode` না দেওয়া হয়, তাহলে default value হিসেবে **302 (Found)** ব্যবহার হয়।

### Example

```typescript
@Get()
@Redirect('https://nestjs.com', 301)
```

এখানে এই route-এ কোনো `GET` request আসলে, সেটাকে `https://nestjs.com`-এ **301** status code দিয়ে redirect করা হবে।

> **Hint:** `Redirect` decorator-টা `@nestjs/common` package থেকে import করতে হয়।

## Dynamic Redirect URL / Status Code

কখনো কখনো redirect URL বা status code fixed না রেখে, কোনো condition অনুযায়ী **dynamically** ঠিক করার দরকার হয়। এর জন্য handler থেকে একটা object return করা যায়, যেটা `HttpRedirectResponse` interface (এটাও `@nestjs/common` থেকে আসে) follow করে।

**গুরুত্বপূর্ণ বিষয়:** handler থেকে যদি এভাবে কোনো value return করা হয়, তাহলে সেটা `@Redirect()` decorator-এ দেওয়া argument গুলোকে **override** করে দেয়।

### Example

```typescript
@Get('docs')
@Redirect('https://docs.nestjs.com', 302)
getDocs(@Query('version') version) {
  if (version && version === '5') {
    return { url: 'https://docs.nestjs.com/v5/' };
  }
}
```

এখানে যা ঘটছে:

- Default-এ `@Redirect('https://docs.nestjs.com', 302)` অনুযায়ী redirect হবে
- কিন্তু যদি query parameter হিসেবে `version=5` পাঠানো হয়, তাহলে `getDocs()` method একটা object return করছে (`{ url: 'https://docs.nestjs.com/v5/' }`), যেটা decorator-এ দেওয়া default URL-কে override করে দিয়ে user-কে `https://docs.nestjs.com/v5/`-এ পাঠাবে
