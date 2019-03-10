# css扩展学习

1. flex布局
    * 父容器  

          display: flex;
          flex-direction: column; // 属性决定主轴的方向
          flex-wrap: nowrap; // 属性定义是否可以换行
          flex-flow: column nowrap; // lex-direction属性和flex-wrap属性的简写形式
          justify-content: center; // 属性定义了项目在主轴上的对齐方式
          align-items: center; // 属性定义了交叉轴上如何对齐
          align-content: center; // 属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用
    * 子容器

          order: 1; // 排序比重
          flex-grow: 1; // 占位比重
          flex-shrink: 1; // 缩放比重
          flex-basis: auto; // 该属性定义了在分配多余空间之前，项目占据的主轴空间（main size）
          flex: 0 1 auto; // grow,shrink,basis的混合属性
          align-self: center; // 定义子容器的交叉轴对齐样式，可覆盖父容器
2. scss
  
    * 变量
          
          $background: #ccc;
          .content {
            background: $background;
          }
          // 变量需要镶嵌在字符串之中
          $side : left;
          .rounded {
            border-#{$side}-radius: 5px;
          }
    * 计算功能

          body {
            margin: (14px/2);
            top: 50px + 10px;
          }
    * 嵌套

          div {
            h1 {
              color: red;
            }
          }
          // 属性嵌套， 例如 border-color
          div {
            border: {  // 注意border后的冒号
              color: red;
            }
          }

    * 注释

          标准的CSS注释 /* comment */ ，会保留到编译后的文件。
          单行注释 // comment，只保留在SASS源文件中，编译后被省略。
          在/*后面加一个感叹号，表示这是"重要注释"。即使是压缩模式编译，也会保留这行注释，通常可以用于声明版权信息。

    * 继承

          // class2继承class1
          .class1 {

          }
          .class2 {
            @extend .class1;
          }
    * Mixin（代码重用）

          @mixin left {
            float: left;
            margin-left: 10px;
          }
          div {
            @include left;
          }

          // 也可以指定参数
          @mixin left($value: 10px) {
            float: left;
            margin-top: $value;
          }
          div {
            @include left(20px);
          }


    * 颜色函数

          lighten(#cc3, 10%) // #d6d65c 
          darken(#cc3, 10%) // #a3a329
          grayscale(#cc3) // #808080
          complement(#cc3) // #33c
    * 插入文件

          @import "path/filename.scss";
    * 高级用法
        * 条件语句

              p {
                @if 1+1 == 2 {
                  border: 1px solid;
                } @else {
                  border: 2px solid;  
                }
              }
        * 循环语句

              @for $i from 1 to 10 {
                .border-#{$i} {
                  border: #{$i}px solid blue;
                }
              }

              $i: 6;
              @while $i > 0 {
                $i: $i - 2;
              }

              @each $member in a, b, c, d {
                .#{$member} {
                  border: 1px solid;
                }
              }

        * 自定义函数 

              @function double($n) {
                @return $n * 2;
              }
              div {
                width: double(5px);
              }