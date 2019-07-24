# nestjs学习（三）
1. 异常过滤器
   * Base exceptions
            
            // 传入字符串
            @Post()
            async create(@Body() createCatDto: CreateCatDto) {
                throw new HttpException('Forbidden', HttpStatus.FORBIDDEN);
            }
            // {
            //    "statusCode": 403,
            //    "message": "Forbidden"
            // }

            // 传入object
            @Post()
            async create(@Body() createCatDto: CreateCatDto) {
                throw new HttpException({
                    status: HttpStatus.FORBIDDEN,
                    error: 'This is a custom message',
                }, 403);
            }
            // {
            //   "status": 403,
            //   "error": "This is a custom message"
            // }
    * 自定义异常

            export class ForbiddenException extends HttpException {
                constructor() {
                    super('Forbidden', HttpStatus.FORBIDDEN);
                }
            }
    * nest提供异常
        * BadRequestException
        * UnauthorizedException
        * NotFoundException
        * ForbiddenException
        * NotAcceptableException
        * RequestTimeoutException
        * ConflictException
        * GoneException
        * PayloadTooLargeException
        * UnsupportedMediaTypeException
        * UnprocessableEntityException
        * InternalServerErrorException
        * NotImplementedException
        * BadGatewayException
        * ServiceUnavailableException
        * GatewayTimeoutException
  
    * 自定义异常response

            import { ExceptionFilter, Catch, ArgumentsHost } from '@nestjs/common';
            import { HttpException } from '@nestjs/common';

            @Catch(HttpException) // @Catch() 此时为捕获所有异常
            export class HttpExceptionFilter implements ExceptionFilter {
                catch(exception: HttpException, host: ArgumentsHost) {
                    const ctx = host.switchToHttp();
                    const response = ctx.getResponse();
                    const request = ctx.getRequest();
                    const status = exception.getStatus();

                    response
                    .status(status)
                    .json({
                        statusCode: status,
                        timestamp: new Date().toISOString(),
                        path: request.url,
                    });
                }
            }

            // 绑定在方法上
            @Post()
            @UseFilters(new HttpExceptionFilter()) // 或者 @UseFilters(HttpExceptionFilter）这种方法更好
            async create(@Body() createCatDto: CreateCatDto) {
                throw new ForbiddenException();
            }

            // 绑定在类上
            @UseFilters(new HttpExceptionFilter())
            export class CatsController {}

            // 绑定为全局
            app.useGlobalFilters(new HttpExceptionFilter());

            // 直接从模块中绑定过滤器
            import { Module } from '@nestjs/common';
            import { APP_FILTER } from '@nestjs/core';
            @Module({
                providers: [
                    {
                        provide: APP_FILTER,
                        useClass: HttpExceptionFilter,
                    },
                ],
            })
            export class ApplicationModule {}
2. Pipes 
    >管道将输入数据转换为所需的输出。此外，它可以处理验证数据，运行于exception域下， 和exception filters一样可以方法绑定，控制器绑定，全局绑定，另外还可以属性绑定
    * ValidationPipe
            
            import * as Joi from 'joi';
            import { PipeTransform, Injectable, ArgumentMetadata, BadRequestException } from '@nestjs/common';

            @Injectable()
            export class JoiValidationPipe implements PipeTransform {
                constructor(private readonly schema) {}

                transform(value: any, metadata: ArgumentMetadata) {
                    const { error } = Joi.validate(value, this.schema);
                    if (error) {
                        throw new BadRequestException('Validation failed');
                    }
                    return value;
                }
            }
            // ArgumentMetadata 接口
            // export interface ArgumentMetadata {
            //     readonly type: 'body' | 'query' | 'param' | 'custom';
            //     readonly metatype?: new (...args) => any;
            //     readonly data?: string;
            // }

            // 绑定
            @Post()
            @UsePipes(new JoiValidationPipe(createCatSchema))
            async create(@Body() createCatDto: CreateCatDto) {
                this.catsService.create(createCatDto);
            }
    * Class validator  // npm i --save class-validator class-transformer

            // 在模型类中
            import { IsString, IsInt } from 'class-validator';

            export class CreateCatDto {
                @IsString()
                readonly name: string;

                @IsInt()
                readonly age: number;

                @IsString()
                readonly breed: string;
            }

            // 创建pip文件
            import { PipeTransform, Injectable, ArgumentMetadata, BadRequestException } from '@nestjs/common';
            import { validate } from 'class-validator';
            import { plainToClass } from 'class-transformer';

            @Injectable()
            export class ValidationPipe implements PipeTransform<any> {
                async transform(value, { metatype }: ArgumentMetadata) {
                    if (!metatype || !this.toValidate(metatype)) {
                        return value;
                    }
                    const object = plainToClass(metatype, value);
                    const errors = await validate(object);
                    if (errors.length > 0) {
                        throw new BadRequestException('Validation failed');
                    }
                    return value;
                }

                private toValidate(metatype): boolean {
                    const types = [String, Boolean, Number, Array, Object];
                    return !types.find((type) => metatype === type);
                }
            }

            // 绑定在方法上
            @Post()
            @UsePipes(ValidationPipe)
            async create(@Body() createCatDto: CreateCatDto) {
                this.catsService.create(createCatDto);
            }

            // 绑定在类上
            @UsePipes(ValidationPipe)
            export class CatsController {}

            // 绑定为全局
            app.useGlobalPipes(new ValidationPipe());

            // 直接从模块中绑定过滤器
            import { Module } from '@nestjs/common';
            import { APP_PIPE } from '@nestjs/core';

            @Module({
                providers: [
                    {
                        provide: APP_PIPE,
                        useClass: CustomGlobalPipe,
                    },
                ],
            })
            export class ApplicationModule {}
    * Transformer pipe 

            import { PipeTransform, Injectable, ArgumentMetadata, HttpStatus, BadRequestException } from '@nestjs/common';

            @Injectable()
            export class ParseIntPipe implements PipeTransform<string, number> {
                transform(value: string, metadata: ArgumentMetadata): number {
                    const val = parseInt(value, 10);
                        if (isNaN(val)) {
                        throw new BadRequestException('Validation failed');
                    }
                    return val;
                }
            }

            // 绑定
            @Get(':id')
            async findOne(@Param('id', new ParseIntPipe()) id) {
                return await this.catsService.findOne(id);
            }