# NestJS Sub-domain Routing

## কেন দরকার হয়

কখনো কখনো একটা controller-কে শুধুমাত্র নির্দিষ্ট একটা subdomain থেকে আসা request-এর জন্য কাজ করাতে হয়। যেমন `admin.example.com` থেকে আসা request আলাদাভাবে handle করা, `example.com`-এর সাধারণ request থেকে।

এর জন্য `@Controller()` decorator একটা `host` option নিতে পারে, যেটা দিয়ে বলে দেওয়া যায় — incoming request-এর HTTP host একটা নির্দিষ্ট value-এর সাথে match করলেই কেবল এই controller কাজ করবে।

## Example: Fixed Host

```typescript
@Controller({ host: 'admin.example.com' })
export class AdminController {
  @Get()
  index(): string {
    return 'Admin page';
  }
}
```

এখানে `AdminController` শুধু তখনই কাজ করবে, যখন request `admin.example.com` host থেকে আসবে। অন্য কোনো host (যেমন সরাসরি `example.com`) থেকে request এলে এই controller সেটা handle করবে না।

> **Warning:** Fastify nested router support করে না। তাই sub-domain routing ব্যবহার করতে চাইলে, default Express adapter ব্যবহার করাই recommended।

## Dynamic Host Parameter

Route `path`-এ যেভাবে token ব্যবহার করে dynamic value capture করা যায়, ঠিক একইভাবে `host` option-এও token ব্যবহার করা যায় — host name-এর নির্দিষ্ট position-এ dynamic value capture করার জন্য।

এভাবে declare করা host parameter, `@HostParam()` decorator দিয়ে access করা যায়, যেটা method signature-এ বসাতে হয়।

### Example: Dynamic Host

```typescript
@Controller({ host: ':account.example.com' })
export class AccountController {
  @Get()
  getInfo(@HostParam('account') account: string) {
    return account;
  }
}
```

এখানে `:account` হলো একটা host parameter token। এর মানে হলো, subdomain-এর জায়গায় যা-ই আসুক না কেন (যেমন `john.example.com`, `mary.example.com`), সেই value `account` নামে capture হয়ে যাবে। এরপর `@HostParam('account')` দিয়ে সেই value সরাসরি `getInfo()` method-এর ভেতরে access করা যাচ্ছে।
