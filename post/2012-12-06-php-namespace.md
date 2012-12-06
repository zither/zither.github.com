-----
{
    "title": "PHP命名空间的一个疑问",
    "category": "PHP"
}
-----

<p class="meta">06 Dec 2012 - By MiniMee</p>

在编程的时候遇到这样一个疑问，话不多说直接上代码：

    namespace Test {

        class test {}
    }

    namespace chou {

        use Test\test;

        $test = 'test';
        $tmp = new $test();
    }

当我试图动态实例化类时引发了“Class not found”错误，我自己的理解是PHP的命名空间只影响类、函数和常量，貌似可以说得通，然后我又尝试写成下面的样子：

    namespace chou {

        use Test\test;

        class mee {

            public $class = 'test';

            public function getClass()
            {
                return new $this->class();
            }
        }

        $tmp = new mee();
        $tmp->getClass();
    }

运行之后仍然会触发“Class not found”错误，两个例子中如果不通过变量实例化则可以正确运行。把手册里关于命名空间的内容重新阅读了一遍，没有找到相关说明，望高手解惑。

-- EOF --
