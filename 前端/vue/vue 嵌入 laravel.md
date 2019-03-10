# vue 嵌入 laravel

1. first configure package.json file

        {
            "private": true,
            "scripts": {
                "prod": "gulp --production",
                "dev": "gulp watch"
            },
            "devDependencies": {
                "bootstrap-sass": "^3.3.7",
                "gulp": "^3.9.1",
                "jquery": "^3.1.0",
                "laravel-elixir": "^6.0.0-9",
                "laravel-elixir-vue": "*",
                "laravel-elixir-vue-2": "^0.2.0",
                "laravel-elixir-webpack-official": "^1.0.2",
                "laravel-elixir-browsersync-official": "^1.0.0",
                "lodash": "^4.14.0",
                "vue": "^2.0.1",
                "vue-router": "^2.0.0",
                "axios": "*",
                "vuex": "^2.0.0"
            }
        }

2. then run `cnpm install` install package

3. mkdir js in _resources/assets_ then touch main.js and App.vue in _/js_ 
    >last __add your code__

4. configure gulp.js file

        const elixir = require('laravel-elixir');

        require('laravel-elixir-vue')

        /*
        |--------------------------------------------------------------------------
        | Elixir Asset Management
        |--------------------------------------------------------------------------
        |
        | Elixir provides a clean, fluent API for defining some basic Gulp tasks
        | for your Laravel application. By default, we are compiling the Sass
        | file for our application, as well as publishing vendor resources.
        |
        */

        elixir(mix => {
            mix.webpack('main.js');
        });


5. run `sudo gulp watch` compile js file will add mian.js in _public/js/_

