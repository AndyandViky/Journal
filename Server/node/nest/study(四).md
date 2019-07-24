# nestjs学习（四）
1. Guards
    >确定请求是否应由路由处理程序处理,在每个中间件之后执行，但在任何pipe之前执行。
    * Authorization guard

            import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
            import { Observable } from 'rxjs';

            @Injectable()
            export class AuthGuard implements CanActivate {
                canActivate(
                    context: ExecutionContext,
                ): boolean | Promise<boolean> | Observable<boolean> {
                    const request = context.switchToHttp().getRequest();
                    return validateRequest(request);
                }
            }

    * Role-based authentication

            import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
            import { Observable } from 'rxjs';

            @Injectable()
            export class RolesGuard implements CanActivate {
                canActivate(
                    context: ExecutionContext,
                ): boolean | Promise<boolean> | Observable<boolean> {
                    return true;
                }
            }

            // 绑定
            @Controller('cats')
            @UseGuards(RolesGuard)
            export class CatsController {}

            // 全局 绑定
            app.useGlobalGuards(new RolesGuard());

            // 模块绑定
            import { Module } from '@nestjs/common';
            import { APP_GUARD } from '@nestjs/core';

            @Module({
                providers: [
                    {
                        provide: APP_GUARD,
                        useClass: RolesGuard,
                    },
                ],
            })
            export class ApplicationModule {}
    * Reflector
            
            @Post()
            @ReflectMetadata('roles', ['admin']) //使用ReflectMetadata装饰器
            async create(@Body() createCatDto: CreateCatDto) {
                this.catsService.create(createCatDto);
            }
            // 直接使用不太好， 建立自己的decorator  
            // roles.decorator.ts
            import { ReflectMetadata } from '@nestjs/common';
            export const Roles = (...roles: string[]) => ReflectMetadata('roles', roles);
            // 这样就可以直接使用  @Roles('admin')

            // roles.guard.ts 示例
            import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
            import { Observable } from 'rxjs';
            import { Reflector } from '@nestjs/core';

            @Injectable()
            export class RolesGuard implements CanActivate {
                constructor(private readonly reflector: Reflector) {}

                canActivate(context: ExecutionContext): boolean {
                    const roles = this.reflector.get<string[]>('roles', context.getHandler());
                    if (!roles) {
                        return true;
                    }
                    const request = context.switchToHttp().getRequest();
                    const user = request.user;
                    const hasRole = () => user.roles.some((role) => roles.includes(role));
                    return user && user.roles && hasRole();
                }
            }

2. Interceptors 拦截器
    * 功能
        * 在方法执行之前/之后绑定额外的逻辑
        * 转换函数返回的结果
        * 转换从函数抛出的异常
        * 扩展基本函数行为
        * 根据所选条件完全覆盖函数（例如缓存目的）
    * 拦截示例
            
            import { Injectable, NestInterceptor, ExecutionContext } from '@nestjs/common';
            import { Observable } from 'rxjs';
            import { tap, map } from 'rxjs/operators';

            @Injectable()
            export class LoggingInterceptor implements NestInterceptor {
                intercept(
                    context: ExecutionContext,
                    call$: Observable<any>,
                ): Observable<any> {
                    console.log('Before...');

                    const now = Date.now();
                    return call$.pipe(
                        tap(() => console.log(`After... ${Date.now() - now}ms`)),
                    );
                    // return call$.pipe(map(data => ({ data }))); 返回一个map
                }
            }

            // 类绑定，方法绑定，全局绑定useGlobalInterceptors()
            @UseInterceptors(LoggingInterceptor)

            // 模块绑定
            import { Module } from '@nestjs/common';
            import { APP_INTERCEPTOR } from '@nestjs/core';

            @Module({
                providers: [
                    {
                        provide: APP_INTERCEPTOR,
                        useClass: LoggingInterceptor,
                    },
                ],
            })
            export class ApplicationModule {}
3. 自定义路由装饰器
        
        // 自定义装饰器
        import { createParamDecorator } from '@nestjs/common';

        export const User = createParamDecorator((data, req) => {
            return req.user;
        });

        // data 可以接收string， 也可以接受pipe
        @Get()
        async findOne(@User(new ValidationPipe()) user: UserEntity) {
            console.log(user);
        }
 ##### 大致流程图
![大致流程图](https://img-blog.csdnimg.cn/20190223160207666.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FuZHlWaWt5,size_16,color_FFFFFF,t_70)