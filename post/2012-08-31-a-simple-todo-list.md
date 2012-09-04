-----
{
    "title": "Project Minilist",
    "category": "Portfolio"
}
-----

<p class="meta">31 Aug 2012 - By MiniMee</p>

花了点时间把[Minilist Demo](http://blog.minimee.org/2012/08/30/about-minilist.html)的代码重新写了一遍，这次换了一个角度来思考问题。既然ul li列表是成对出现的，那么就可以采用正则匹配来替换生成列表，完整代码如下：

    <?php
    /*
     * 使用PHP通过正则匹配生成嵌套列表。
     * 
     * @author minimee <ilorn.mc@gmail.com>
     *
     */

    class lists {

        /**
         * 默认缩进为4个空格。
         *
         * @var int
         */    
        private $tab = 4;

        /**
         * 默认允许最大嵌套数。
         *
         * @var int
         */
        private $level = 4;

        /**
         * Todolist
         *
         * @var string
         */ 
        private $lists = NULL;

        /**
         * 初始化类
         *
         * @return void
         */
        public function __construct($lists)
        {
            $this->lists = $lists;
        }

        /**
         * 获取处理后的Todolist
         *
         * @return string
         */
        public function get_lists()
        {
            // 替换根列表
            $this->parents();

            // 替换子列表
            $this->children();
            
            // 最终列表
            $this->lists = '<ul>'.$this->lists.'</ul>';

            return $this->lists;
        }

        /**
         * 匹配替换根列表
         *
         * @return void
         */
        protected function parents()
        {
            $mregex = '/^\S.+\n((\s{'.$this->tab.',}.+\n)*)/im';

            $replacement = '<li>$0</li>';

            $this->lists = preg_replace($mregex,$replacement,$this->lists);
        }

        /**
         * 循环匹配替换子列表
         *
         * @return void
         */
        protected function children()
        {
            // 在允许的最大嵌套数内进行循环替换子列表
            for($i = 1; $i < $this->level; $i++){

                // 当前列表缩进宽度
                $tab1 = $this->tab*$i;

                // 当前列表的子列表缩进宽度
                $tab2 = $tab1+1;

                $regex = '/^\s{'.$tab1.'}\S.+\n((\s{'.$tab2.',}\S.+\n)*)/im';

                $replacement = '<ul><li>$0</li></ul>';

                $this->lists = preg_replace($regex,$replacement,$this->lists);
            }
        }
    }

    // 读取数据内容
    $contents = file_get_contents('mlist.txt');

    $list = new lists($contents);

    echo $list->get_lists();


这次代码比之前的代码更加简单明了，已经完全满足我自己的需要了，可能在嵌套层数方面可以改进一下。

-- EOF --
