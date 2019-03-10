# nestjs学习（二）
1. module
    * 定义子module

            // 文件目录
            src
                cats
                    dto
                        create-cat.dto.ts
                    interfaces
                        cat.interface.ts
                    cats.service.ts
                    cats.controller.ts
                    cats.module.ts
                app.module.ts
                main.ts
            
            // 在cats/cats.module.ts 文件下
            import { Module } from '@nestjs/common';
            import { CatsController } from './cats.controller';
            import { CatsService } from './cats.service';

            @Module({
                controllers: [CatsController],
                providers: [CatsService],
            })
            export class CatsModule {}
            
            // 在app.module.ts文件下
            import { Module } from '@nestjs/common';
            import { CatsModule } from './cats/cats.module';

            @Module({
                imports: [CatsModule],
            })
            export class ApplicationModule {}
    * Shared module
            
            import { Module } from '@nestjs/common';
            import { CatsController } from './cats.controller';
            import { CatsService } from './cats.service';

            @Module({
                controllers: [CatsController],
                providers: [CatsService],
                exports: [CatsService] //将当前service暴露出去，其他模块import此模块时都会得到CatService相同的实例
            })
            export class CatsModule {}
    * Dependency injection

            export class CatsModule {
                constructor(private readonly catsService: CatsService) {}
            }
    * Global modules
            
            import { Module, Global } from '@nestjs/common';
            import { CatsController } from './cats.controller';
            import { CatsService } from './cats.service';

            @Global() // 将此module定义为全局module
            @Module({
                controllers: [CatsController],
                providers: [CatsService],
                exports: [CatsService]
            })
            export class CatsModule {}
    * Dynamic modules
            
            import { Module, DynamicModule } from '@nestjs/common';
            import { createDatabaseProviders } from './database.providers';
            import { Connection } from './connection.provider';

            @Module({
                providers: [Connection],
            })
            export class DatabaseModule {
                static forRoot(entities = [], options?): DynamicModule {
                    const providers = createDatabaseProviders(options, entities);
                    return {
                        module: DatabaseModule,
                        providers: providers,
                        exports: providers,
                    };
                }
            }
2. middleware

    * 示例

            import { Injectable, NestMiddleware, MiddlewareFunction } from '@nestjs/common';

            @Injectable()
            export class LoggerMiddleware implements NestMiddleware {
                resolve(...args: any[]): MiddlewareFunction {
                    return (req, res, next) => {
                        console.log('Request...');
                        next();
                    };
                }
            }
    * 应用中间件
            
            // 在app.module.ts文件下
            import { Module, NestModule, RequestMethod, MiddlewareConsumer } from '@nestjs/common';
            import { LoggerMiddleware } from './common/middlewares/logger.middleware';
            import { CatsModule } from './cats/cats.module';

            @Module({
                imports: [CatsModule],
            })
            export class ApplicationModule implements NestModule {
                configure(consumer: MiddlewareConsumer) {
                    consumer
                        .apply(LoggerMiddleware)
                        .forRoutes('cats'); // 可以使用通配符限定一类的路由
                        // .forRoutes({ path: 'cats', method: RequestMethod.GET });
                        // .forRoutes(CatsController); 直接包括整个控制器
                        .exclude(
                            { path: 'cats', method: RequestMethod.GET },
                            { path: 'cats', method: RequestMethod.POST },
                        ) // 排除特定的路由
                        .with('ApplicationModule') // 传递resolve函数内的参数
                }
            }
    * async middleware

            import { Injectable, NestMiddleware, MiddlewareFunction } from '@nestjs/common';

            @Injectable()
            export class LoggerMiddleware implements NestMiddleware {
                async resolve(name: string): Promise<MiddlewareFunction> {
                    await someAsyncJob();

                    return async (req, res, next) => {
                        await someAsyncJob();
                        console.log(`[${name}] Request...`); // [ApplicationModule] Request...
                        next();
                    };
                }
            }
    * 函数式 middleware
            
            // 定义一个函数
            export function logger(req, res, next) {
                console.log(`Request...`);
                next();
            };
            // 在apply函数中 将logger作为参数传入即可
    * 全局中间件
            
            const app = await NestFactory.create(ApplicationModule);
            app.use(logger);
            await app.listen(3000);