# NestJS Response Headers

## কেন দরকার হয়

অনেক সময় response-এর সাথে custom header পাঠানোর দরকার হয় — যেমন caching control, content type, ইত্যাদি নির্দিষ্ট করার জন্য।

## কীভাবে Custom Header সেট করা যায়

Custom response header specify করার জন্য দুইটা উপায় আছে:

1. **`@Header()` decorator ব্যবহার করে**
2. **Library-specific response object ব্যবহার করে** — `res.header()` সরাসরি call করে

## Example: `@Header()` Decorator

```typescript
@Post()
@Header('Cache-Control', 'no-store')
create() {
  return 'This action adds a new cat';
}
```

এখানে `create()` handler-এ `@Header('Cache-Control', 'no-store')` বসানো হয়েছে — এর ফলে এই handler থেকে যে response যাবে, তার সাথে `Cache-Control: no-store` header attach থাকবে।

`@Header()` decorator দুইটা argument নেয়:

- প্রথমটা — header-এর নাম (যেমন `'Cache-Control'`)
- দ্বিতীয়টা — header-এর value (যেমন `'no-store'`)

> **Hint:** `Header` decorator-টা `@nestjs/common` package থেকে import করতে হয়।

```typescript
import { Header } from '@nestjs/common';
```

## দ্বিতীয় উপায়: Library-specific Response Object

যদি dynamically header set করার দরকার হয়, তাহলে `@Res()` দিয়ে inject করা response object-এর উপর সরাসরি `res.header()` call করা যায়। এই পদ্ধতি ব্যবহার করলে (আগের topic-এ যেমন বলা হয়েছে) handler টা **Library-specific mode**-এ চলে যায়, তাই response manually পাঠানোর দায়িত্বও তখন নিজের হয়ে যায়।
