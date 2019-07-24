# nestjs学习（一）
1. 核心文件目录
    
        src
            -app.controller.ts
            -app.module.ts
            -main.ts
2. 控制器controller
   
        import { Controller, Get, Res, Req, HttpStatus } from '@nestjs/common';

        @Controller()
        export class AppController {

            @Get()
            getHello(@Res() res) {
                res.status(HttpStatus.CREATED).send();
            }
            // async/await
            @Get()
            async getHello(@Res() res): Promise<any[]> {
                res.status(HttpStatus.OK).json([]);
            }
        }
3. 路由router
    >在controller修饰器中定于路由
        
        @Controller(‘cat’)
        // localhost:3000/cat
    >路由器通配符
        
        ?, +, *, -, ., and ()
    >路由参数

        @Get(':id')
        findOne(@Param() params) {
            console.log(params.id);
            return `This action returns a #${params.id} cat`;
        }
4. 装饰器decorator
    1. 参数
       * @Request()	req
       * @Response()	res
       * @Next()	next
       * @Session()	req.session
       * @Param(param?: string)	req.params / req.params[param]
       * @Body(param?: string)	req.body / req.body[param]
       * @Query(param?: string)	req.query / req.query[param]
       * @Headers(param?: string)	req.headers / req.headers[param]
    2. 请求方法
       * @Put(),
       * @Delete(),
       * @Patch(),
       * @Options(),
       * @Head(),
       * @All()
    3. 其他
       * @HttpCode(204) //状态码
       * @Header('Cache-Control', 'none') // 请求头
       * @Injectable() // 注入，用于provider
       * @Optional() // 表明不一定要传递该变量或对象等。。。

                @Injectable()
                export class HttpService<T> {
                    constructor(
                        @Optional() @Inject('HTTP_OPTIONS') private readonly httpClient: T,
                    ) {}
                }
        * @Inject('HTTP_OPTIONS') //基于属性的注入
        * @Module() //模块

5. 模型Model

        export class CreateCatDto {
            readonly name: string;
            readonly age: number;
            readonly breed: string;
        }

        @Post()
        async create(@Body() createCatDto: CreateCatDto) {
            return 'This action adds a new cat';
        }

6. provider

        // 在cats.service.ts文件中
        import { Injectable } from '@nestjs/common';
        import { Cat } from './interfaces/cat.interface';

        @Injectable()
        export class CatsService {
            private readonly cats: Cat[] = [];

            create(cat: Cat) {
                this.cats.push(cat);
            }

            findAll(): Cat[] {
                return this.cats;
            }
        }
        // 在cats.controller.ts文件中
        import { Controller, Get, Post, Body } from '@nestjs/common';
        import { CreateCatDto } from './dto/create-cat.dto';
        import { CatsService } from './cats.service';
        import { Cat } from './interfaces/cat.interface';

        @Controller('cats')
        export class CatsController {
            constructor(private readonly catsService: CatsService) {}

            @Post()
            async create(@Body() createCatDto: CreateCatDto) {
                this.catsService.create(createCatDto);
            }

            @Get()
            async findAll(): Promise<Cat[]> {
                return this.catsService.findAll();
            }
        }