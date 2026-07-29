## Routing

#### Create controller
```bash
nest g controller cats
```
![](https://imgur.com/iep4EBB.png)
---

#### cats.controller.ts
```bash
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
    @Get()
    findAll(): string{
        return 'This action returns all cats';
    }
}
```
---
