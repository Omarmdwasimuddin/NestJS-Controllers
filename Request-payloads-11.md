# NestJS Request Payloads

## কেন দরকার হয়

আগের example-এ যে POST route handler বানানো হয়েছিল, সেটা client থেকে কোনো parameter accept করতো না। এখন `@Body()` decorator যোগ করে সেটা ঠিক করা হবে, যাতে client-এর পাঠানো data handler-এর ভেতরে access করা যায়।

## DTO (Data Transfer Object) কী

TypeScript ব্যবহার করলে, `@Body()` ব্যবহার করার আগে একটা **DTO schema** define করে নেওয়া দরকার। DTO হলো এমন একটা object, যেটা নির্ধারণ করে দেয় — network-এর মধ্য দিয়ে data কীভাবে পাঠানো উচিত।

## DTO Schema Define করার উপায়

DTO schema define করার জন্য দুইটা উপায় আছে:

1. **TypeScript interface** ব্যবহার করে
2. **Class** ব্যবহার করে

তবে এখানে **class ব্যবহার করাই recommended**।

### কেন Class, Interface না

- Class হলো JavaScript-এর ES6 standard-এর একটা অংশ। তাই compile হওয়ার পরও, compiled JavaScript-এ class real entity হিসেবে টিকে থাকে।
- অন্যদিকে, TypeScript interface transpile হওয়ার সময় সম্পূর্ণভাবে **সরিয়ে ফেলা হয়** — মানে runtime-এ interface-এর কোনো অস্তিত্ব থাকে না, তাই Nest runtime-এ সেটা reference করতে পারে না।
- এটা গুরুত্বপূর্ণ কারণ, Pipes-এর মতো feature গুলো কাজ করার জন্য runtime-এ variable-এর **metatype** access করতে পারা দরকার — আর সেটা শুধুমাত্র class দিয়েই সম্ভব, interface দিয়ে না।

## `CreateCatDto` Class তৈরি করা

```typescript
export class CreateCatDto {
  name: string;
  age: number;
  breed: string;
}
```

এখানে মাত্র তিনটা basic property আছে — `name`, `age`, আর `breed`।

## Controller-এ DTO ব্যবহার করা

এই নতুন তৈরি করা DTO এখন `CatsController`-এর ভেতরে ব্যবহার করা যায়।

```typescript
@Post()
async create(@Body() createCatDto: CreateCatDto) {
  return 'This action adds a new cat';
}
```

এখানে `@Body()` decorator পুরো request body-টাকে `createCatDto` parameter-এর মধ্যে inject করে দিচ্ছে, আর সেটার type হচ্ছে আমাদের বানানো `CreateCatDto` class।

## `ValidationPipe` নিয়ে Hint

Nest-এর `ValidationPipe` এমন সব property filter করে বাদ দিতে পারে, যেগুলো method handler-এর গ্রহণ করা উচিত না। এই ক্ষেত্রে, গ্রহণযোগ্য property গুলোর একটা **whitelist** বানানো যায় — আর সেই whitelist-এ না থাকা যেকোনো property automatically resulting object থেকে সরিয়ে দেওয়া হয়।

`CreateCatDto`-এর example-এ, whitelist হলো — `name`, `age`, আর `breed` property।

> বিস্তারিত জানতে চাইলে NestJS docs-এর Validation অংশের **Stripping properties** section-টা দেখা যেতে পারে।
| Class কেন ভালো | Runtime-এ টিকে থাকে, metatype access করা যায় (Pipes-এর জন্য দরকারি) |
| Interface কেন সমস্যা করে | Transpile হওয়ার সময় সরে যায়, runtime-এ থাকে না |
| Extra property handle করার উপায় | `ValidationPipe`-এর whitelist ফিচার দিয়ে অতিরিক্ত property strip করা |
