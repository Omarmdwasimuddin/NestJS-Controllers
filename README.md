## NestJS-Controllers

#### Create controller
```bash
nest g controller [name]
```
![](https://imgur.com/ZxCZdUs.png)

---

#### src/user/user.controller.ts
```bash
import { Controller, Get } from '@nestjs/common';

@Controller('user')
export class UserController {
    @Get()
    getUser(): string{
        return 'User data fetched successfully!';
    }
}
```
---

#### Terminal run
```bash
npm run start:dev
```
---

#### Output
![](https://imgur.com/r24ecIc.png)
