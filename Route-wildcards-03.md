# NestJS Route Wildcards

## কী এবং কেন

NestJS-এ pattern-based route-ও support করা হয়। এর মানে হলো, route path-এ exact string না লিখে একটা **pattern** ব্যবহার করা যায়, যেটার সাথে একাধিক ভিন্ন path match করতে পারে।

এর জন্য asterisk (`*`) কে wildcard হিসেবে ব্যবহার করা যায় — এটা path-এর শেষে থাকা যেকোনো combination of characters-কে match করতে পারে।

## Example

```typescript
@Get('abcd/*')
findAll() {
  return 'This route uses a wildcard';
}
```

এখানে `findAll()` method চলবে সেই সব route-এর জন্য, যেগুলো `abcd/` দিয়ে শুরু হয় — তারপর যত character-ই থাকুক না কেন।

অর্থাৎ `'abcd/*'` route path নিচের সবগুলোর সাথে match করবে:

- `abcd/`
- `abcd/123`
- `abcd/abc`
- ... ইত্যাদি

> **মনে রাখতে হবে:** hyphen (`-`) আর dot (`.`) কে string-based path-এ literally interpret করা হয় — এগুলো wildcard-এর মতো কোনো special behavior দেখায় না।

## Express আর Fastify-এর মধ্যে পার্থক্য

এই wildcard approach Express আর Fastify — দুই platform-এই কাজ করে, তবে কিছু গুরুত্বপূর্ণ পার্থক্য আছে।

### Express (v5) নিয়ে সতর্কতা

Express-এর latest release (v5)-এ routing system আগের চেয়ে অনেক বেশি strict হয়ে গেছে। তাই pure Express-এ route কাজ করানোর জন্য একটা **named wildcard** ব্যবহার করতে হয় — যেমন:

```
abcd/*splat
```

এখানে `splat` হলো শুধু wildcard parameter-এর একটা নাম — এর নিজস্ব কোনো special meaning নেই। এই নাম যা খুশি রাখা যায়, `splat` বাধ্যতামূলক না।

তবে যেহেতু Nest নিজেই Express-এর জন্য একটা **compatibility layer** provide করে, তাই Nest ব্যবহার করলে আগের মতোই শুধু asterisk (`*`) ব্যবহার করা যায় — named wildcard ছাড়াই।

### Route-এর মাঝখানে asterisk ব্যবহার করলে

যদি asterisk route-এর **মাঝখানে** কোথাও ব্যবহার করা হয় (শেষে না), তখন দুই platform ভিন্নভাবে behave করে:

- **Express** — এক্ষেত্রে named wildcard বাধ্যতামূলক, যেমন: `ab{*splat}cd`
- **Fastify** — এই ধরনের mid-route wildcard একেবারেই support করে না
