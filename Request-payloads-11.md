# NestJS Asynchronicity

## মূল ধারণা

Modern JavaScript-এর একটা বড় শক্তি হলো এর asynchronous data handling-এর উপর জোর দেওয়া। ঠিক এই কারণেই Nest `async` function-কে পুরোপুরি support করে।

নিয়ম হলো — প্রতিটা `async` function-কে অবশ্যই একটা `Promise` return করতে হয়। এর মাধ্যমে একটা **deferred value** (যেটা এখনই ready না, পরে resolve হবে) return করা যায়, আর Nest নিজে থেকেই সেটা automatically resolve করে নেয়।

## Example ১: `async`/`await` দিয়ে

```typescript
@Get()
async findAll(): Promise<any[]> {
  return [];
}
```

এই code সম্পূর্ণ valid। এখানে `findAll()` একটা `async` function, যেটা একটা `Promise<any[]>` return করছে। Nest নিজে থেকেই এই Promise resolve করে, resolved value দিয়ে response পাঠিয়ে দেয়।

## Example ২: RxJS Observable দিয়ে

Nest এখানেই থেমে থাকে না — route handler থেকে RxJS-এর [observable stream](https://rxjs-dev.firebaseapp.com/guide/observable)ও return করা যায়। Nest ভেতরে ভেতরে সেই stream-এ নিজে থেকে **subscribe** করে নেয়, আর stream complete হওয়ার পর সবশেষে emit হওয়া value দিয়ে response resolve করে।

```typescript
@Get()
findAll(): Observable<any[]> {
  return of([]);
}
```

এখানে `findAll()` একটা `Observable<any[]>` return করছে (RxJS-এর `of([])` ব্যবহার করে)। Nest এটাকেও ঠিক Promise-এর মতোই handle করে নেয়, আলাদা কোনো extra কাজ করতে হয় না।

## দুইটার মধ্যে কোনটা ব্যবহার করবো

দুইটা approach-ই সম্পূর্ণ valid — `async`/`Promise` এবং RxJS `Observable`। যেটা নিজের প্রয়োজন আর project-এর style-এর সাথে বেশি মানানসই মনে হয়, সেটাই বেছে নেওয়া যায়।
