# nestjs学习（五）

## nest接入mongoose

1. 依赖下载

        npm install mongoose mongoose-auto-increment mongoose-paginate nestjs-typegoose typegoose --save-dev

2. database模块创建 (别忘了将模块导入最外层模块中)

    * mongoose.transform.ts // Mongoose 模块转换器
        
          import * as _mongoose from 'mongoose';
          import * as _mongoosePaginate from 'mongoose-paginate';
          import * as _mongooseAutoIncrement from 'mongoose-auto-increment';
          // import * as APP_CONFIG from '../app.config';

          // 各种 Hack
          _mongoose.set('useFindAndModify', false);
          (_mongoose as any).Promise = global.Promise;

          // 初始化翻页插件
          _mongooseAutoIncrement.initialize(_mongoose.connection);

          // 插件配置初始化
          (_mongoosePaginate as any).paginate.options = {
            limit: 16,
          };

          export const mongoose = _mongoose;
          export const mongoosePaginate = _mongoosePaginate;
          export const mongooseAutoIncrement = _mongooseAutoIncrement;
          export default mongoose;
    * database.provider.ts:

          import { mongoose } from '../../transforms/mongoose.transform';

          export const databaseProvider = {
            provide: ‘DbConnectionToken’,
            useFactory: async () => {
              // 连接数据库
              const connection = () => mongoose.connect(‘数据库路径名’, {
                useCreateIndex: true,
                useNewUrlParser: true,
                useFindAndModify: false,
                promiseLibrary: global.Promise,
              });

              // 连接错误
              mongoose.connection.on('error', error => {
                const timeout = 6;
                setTimeout(connection, timeout * 1000);
                setTimeout(() => console.warn(`数据库连接失败！将在 ${timeout}s 后重试`, error), 0);
              });

              // 连接成功
              mongoose.connection.on('open', () => {
                setTimeout(() => console.info('数据库连接成功'), 0);
              });
              return await connection();
            }
          }

    * database.module.ts

          import { Module, Global } from '@nestjs/common';
          import { databaseProvider } from './database.provider';

          @Global()
          @Module({
            providers: [databaseProvider],
            exports: [databaseProvider],
          })
          export class DatabaseModule {}

    * mongoose.interface.ts // Mongoose 和 Paginate 模型的兼容

          import { ModelType } from 'typegoose';
          import { PaginateModel, Document } from 'mongoose';

          export type TMongooseModel<T> = ModelType<T> & PaginateModel<T & Document>;

3. 实例测试
    * 创建article module
        
          // 文件目录
          -article
            -article.controller.ts
            -article.model.ts
            -article.module.ts
            -article.service.ts

          //model.ts
          import { prop, Typegoose, pre, plugin } from "typegoose";
          import { 
            IsString,
            IsNotEmpty,
            IsArray,
            IsDefined,
            IsIn,
            IsInt,
            ArrayNotEmpty,
            ArrayUnique,
          } from 'class-validator';
          import { 
            mongoosePaginate,
            mongooseAutoIncrement,
          } from "../../transforms/mongoose.transform";

          export class Meta {
            @IsInt()
            @prop({ default: 0 })
            likes: number;

            @IsInt()
            @prop({ default: 0 })
            views: number;

            @IsInt()
            @prop({ default: 0 })
            comments: number;
          }

          // findOneAndUpdate before触发器
          @pre<Article>('findOneAndUpdate', function(next) {
            this.findOneAndUpdate({}, {update_at: Date.now() });
          })

          // 插件加载
          @plugin(mongoosePaginate)
          @plugin(mongooseAutoIncrement.plugin, {
            model: Article.name,
            field: 'id',
            startAt: 1,
            incrementBy: 1,
          })

          export class Article extends Typegoose {
            // 标题
            @IsNotEmpty({ message: '文章标题？' })
            @IsString({ message: '字符串？' })
            @prop({ required: true, validate: /\S+/ })
            title: String;
            
            // 内容
            @IsNotEmpty({ message: '文章内容？' })
            @IsString({ message: '字符串？' })
            @prop({ required: true, validate: /\S+/ })
            content: String;

            // 其他元信息
            @prop()
            meta: Meta;

            // 发布日期
            @prop({ default: Date.now })
            create_at?: Date;

            // 最后修改日期
            @prop({ default: Date.now })
            update_at?: Date;
          }

          // module.ts
          @Module({
            // 引入TypegooseModule
            imports: [
              TypegooseModule.forFeature({
                typegooseClass: Article,
                schemaOptions: {
                  toObject: { getters: true },
                },
              }),
            ],
            providers: [ArticleService],
            controllers: [ArticleController],
            exports: [ArticleService]
          })

          // service.ts
          @Injectable()
          export class ArticleService {
            constructor(
              @InjectModel(Article) private readonly articleModel: TMongooseModel<Article>,
            ) {}

            async getList(query, options): Promise<PaginateResult<Article>> {
              return await this.articleModel.paginate(query, options);
            }
          }

          // controller.ts
          @Controller('article')
          export class ArticleController {
            constructor(private readonly articleService: ArticleService) {}
            /**
            * 获取文章列表
            * @param query
            */
            @Get()
            getArticles(@Query() query): Promise<PaginateResult<Article>>{
              return this.articleService.getList(query, {});
            }
          }

4. 结果

        {
          "docs": [
              {
                  "password": "",
                  "keywords": [],
                  "state": 1,
                  "public": 1,
                  "origin": 0,
                  "_id": "5c63c9dcde3b5d0beb8e47a1",
                  "meta": {
                      "likes": 0,
                      "views": 0,
                      "comments": 0,
                      "_id": "5c63c9dcde3b5d0beb8e47a2"
                  },
                  "title": "test",
                  "content": "test",
                  "create_at": "2019-02-13T07:40:12.211Z",
                  "update_at": "2019-02-13T07:40:12.211Z",
                  "id": 1,
                  "__v": 0
              }
          ],
          "total": 1,
          "limit": 16,
          "offset": 0,
          "page": 1,
          "pages": 1
      }

          