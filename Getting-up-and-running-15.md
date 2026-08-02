# NestJS Getting Up and Running

## সমস্যাটা কী

`CatsController` পুরোপুরি define করে ফেললেও, Nest কিন্তু এখনো এই controller সম্পর্কে কিছুই জানে না। ফলে Nest automatically এই class-এর instance তৈরি করবে না — যতক্ষণ না এটাকে সঠিকভাবে register করা হচ্ছে।

## Controller-কে Module-এ Register করা

Controller সবসময় কোনো না কোনো **module**-এর অংশ হতে হয়। এই কারণেই `@Module()` decorator-এর ভেতরে একটা `controllers` array রাখা হয়, যেখানে controller গুলো register করতে হয়।

যেহেতু root `AppModule` ছাড়া এখনো অন্য কোনো module define করা হয়নি, তাই `CatsController`-কে এই `AppModule`-এই register করা হবে:

```typescript
import { Module } from '@nestjs/common';
import { CatsController } from './cats/cats.controller';

@Module({
  controllers: [CatsController],
})
export class AppModule {}
```

## এখানে আসলে কী ঘটছে

`@Module()` decorator ব্যবহার করে module class-এর সাথে কিছু **metadata** attach করা হয়েছে — এখানে সেই metadata বলছে, কোন কোন controller এই module-এর অংশ।

এই metadata-এর মাধ্যমেই Nest সহজে বুঝতে পারে, কোন কোন controller mount করতে হবে — এবং তখনই Nest সেই controller-এর instance তৈরি করে, request handle করার জন্য প্রস্তুত করে দেয়।

## সংক্ষেপে

| বিষয় | ব্যাখ্যা |
|---|---|
| শুধু controller define করলে যথেষ্ট কিনা | না, register না করা পর্যন্ত Nest সেটা চেনে না |
| Register করার জায়গা | Module-এর `@Module()` decorator-এর ভেতরে `controllers` array |
| Root module | `AppModule` |
| Register করার ফলাফল | Nest জানে কোন controller mount করতে হবে, instance তৈরি করে |
