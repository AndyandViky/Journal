# 在laravel中使用redis

## origin https://d.laravel-china.org/docs/5.5/redis

1. install composer require predis/predis

2. 配置: config/database.php

        'redis' => [

            'client' => 'predis',

            'default' => [
                'host' => env('REDIS_HOST', 'localhost'),
                'password' => env('REDIS_PASSWORD', null),
                'port' => env('REDIS_PORT', 6379),
                'database' => 0,
            ],

        ],

3. 集群配置： 看项目需求！

        'redis' => [

            'client' => 'predis',

            'clusters' => [
                'default' => [
                    [
                        'host' => env('REDIS_HOST', 'localhost'),
                        'password' => env('REDIS_PASSWORD', null),
                        'port' => env('REDIS_PORT', 6379),
                        'database' => 0,
                    ],
                ],
            ],

        ],

4. 如果你喜欢使用 redis 原生集群，你需要在配置文件中配置 options

        'redis' => [

            'client' => 'predis',

            'options' => [
                'cluster' => 'redis',
            ],

            'clusters' => [
                // ...
            ],

        ],

5. 其他参数 'read_write_timeout' => 60,

6. 使用

        use Illuminate\Support\Facades\Redis;

        $redis = Redis::connection(); 创建一个redis连接  Redis 实例

        Redis::pipeline(function ($pipe) { 
             for ($i = 0; $i < 1000; $i++) {
                 $pipe->set("key:$i", $i);
             }
         }); // 管道操作，在一次操作插入或者读出多次

         Redis::set('name', 'Taylor'); 
         $redis = Redis::get("name");