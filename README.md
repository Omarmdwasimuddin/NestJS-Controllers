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
    getUser(){
        return 'User data fetched successfully!';
    }
}
```
---

#### Output
![](https://imgur.com/r24ecIc.png)
