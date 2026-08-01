# NestJS Route Parameters

## কেন দরকার হয়

Static path দিয়ে route বানালে সেটা কাজ করে না, যখন request-এর অংশ হিসেবে dynamic data accept করার দরকার হয়। যেমন — `GET /cats/1` করে id `1` এর cat-টা fetch করতে চাইলে, `1`-এর জায়গায় যেকোনো number আসতে পারে, সেটা fixed থাকবে না।

এই ধরনের ক্ষেত্রে route path-এর মধ্যে **route parameter token** যোগ করে URL থেকে dynamic value capture করা যায়।

## কীভাবে Route Parameter Define করা যায়

`@Get()` decorator-এর ভেতরে `:id`-এর মতো token বসিয়ে route parameter define করা হয়। এরপর সেই parameter method-এর ভেতরে access করার জন্য `@Param()` decorator ব্যবহার করা হয়, যেটা method signature-এ বসাতে হয়।

> **Hint:** Parameter সহ route গুলো সবসময় static path-এর **পরে** declare করা উচিত। এটা না করলে, parameterized path গুলো static path-এর জন্য আসা traffic-কেও intercept করে ফেলতে পারে।

## Example ১: পুরো Params Object Access করা

```typescript
@Get(':id')
findOne(@Param() params: any): string {
  console.log(params.id);
  return `This action returns a #${params.id} cat`;
}
```

এখানে `@Param()` decorator method parameter `params`-কে decorate করছে। এর ফলে route-এর সব parameter, সেই `params` object-এর property হিসেবে method-এর ভেতরে access করা যায়। যেমন এখানে `id` parameter-টা access করা হচ্ছে `params.id` দিয়ে।

> **Hint:** `Param` decorator-টা `@nestjs/common` package থেকে import করতে হয়।

## Example ২: নির্দিষ্ট Parameter সরাসরি Access করা

Alternative হিসেবে, `@Param()` decorator-এ নির্দিষ্ট parameter token পাঠিয়ে দেওয়া যায় — তাহলে method body-এর ভেতরে সেই route parameter-টা সরাসরি নাম ধরে reference করা যায়, পুরো object-এর মধ্য দিয়ে না গিয়েই।

```typescript
@Get(':id')
findOne(@Param('id') id: string): string {
  return `This action returns a #${id} cat`;
}
```

এখানে `@Param('id')` করার ফলে সরাসরি `id` variable-টাই পাওয়া যাচ্ছে, `params.id` লেখার দরকার নেই।
| পুরো object access | `@Param() params: any` → `params.id` |
| নির্দিষ্ট parameter access | `@Param('id') id: string` → সরাসরি `id` |
| Order নিয়ে সতর্কতা | Parameterized route সবসময় static route-এর পরে declare করতে হবে |
