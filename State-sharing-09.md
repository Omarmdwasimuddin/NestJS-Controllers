# NestJS State Sharing

## মূল ধারণা

অন্য programming language থেকে আসা developer-দের জন্য এই বিষয়টা প্রথমে অবাক লাগতে পারে — Nest-এ প্রায় সবকিছুই incoming request গুলোর মধ্যে **shared** থাকে। এর মধ্যে পড়ে —

- Database connection pool-এর মতো resource
- Global state সহ singleton service
- এবং আরও অনেক কিছু

## কেন এমন হয়

এর কারণ বুঝতে হলে জানতে হবে, Node.js request/response handle করার জন্য **Multi-Threaded Stateless Model** ব্যবহার করে না — যেখানে প্রতিটা request একটা আলাদা thread দিয়ে handle হয়। Node.js এর বদলে single-threaded event loop model অনুসরণ করে।

এর ফলে, Nest-এ **singleton instance** ব্যবহার করা সম্পূর্ণ নিরাপদ — কারণ একই instance সব request-এর মধ্যে share হলেও, আলাদা thread না থাকায় সেটার সাথে concurrency-জনিত সমস্যা তৈরি হয় না, যা traditional multi-threaded model-এ হতে পারতো।

## কখন Request-based Lifetime দরকার হতে পারে

তবে কিছু নির্দিষ্ট edge case আছে, যেখানে controller-এর জন্য request-based lifetime রাখাটা প্রয়োজনীয় হয়ে পড়ে। যেমন:

- **GraphQL application-এ per-request caching**
- **Request tracking**
- **Multi-tenancy implement করা**

এই ধরনের ক্ষেত্রে singleton behavior-এর বদলে, প্রতিটা request-এর জন্য আলাদা instance দরকার হয়।

> Injection scope নিয়ন্ত্রণ করার বিস্তারিত জানতে চাইলে NestJS-এর official docs-এর [Injection Scopes](https://docs.nestjs.com/fundamentals/injection-scopes) অংশটা দেখা যেতে পারে।
| ফলাফল | Singleton instance ব্যবহার করা safe |
| Exception (কখন আলাদা lifetime দরকার) | Per-request caching (GraphQL), request tracking, multi-tenancy |
| আরও জানতে | Injection Scopes ডকুমেন্টেশন |
