---
Author  : lizhihao
Class   : collection
---

# About Interview
 
# PHP
	php部分我是按照面试常问的问题总结的，下面的问题在面试中出场率很高。问题比较基础
	1、HTTP Keep-Alive的作用
	作用：Keep-Alive：使客户端到服务器端的连接持续有效，当出现对服务器的后继请求时，Keep-Alive功能避免了建立或者重新建立连接。Web服务器，基本上都支持HTTP Keep-Alive。
	缺点：对于提供静态内容的网站来说，这个功能通常很有用。但是，对于负担较重的网站来说，虽然为客户保留打开的连 接有一定的好处，但它同样影响了性能，因为在处理暂停期间，本来可以释放的资源仍旧被占用。当Web服务器和应用服务器在同一台机器上运行时，Keep- Alive功能对资源利用的影响尤其突出。
	解决：Keep-Alive: timeout=5, max=100
	timeout：过期时间5秒（对应httpd.conf里的参数是：KeepAliveTimeout），max是最多一百次请求，强制断掉连接。就是在timeout时间内又有新的连接过来，同时max会自动减1，直到为0，强制断掉。

# 数组相关----------|

1、php数组函数常见的那些? (array_merge、in_array的作用)
2、PHP中以array_开头的数组函数有哪些，并说出使用方法（至少6个）
 
一、数组操作的基本函数
复制代码
1 array_values($arr);       //获得数组的值
2 array_keys($arr);         //获得数组的键名
3 array_flip($arr);         //数组中的值与键名互换（如果有重复前面的会被后面的覆盖）
4 array_search('PHP',$arr); //检索给定的值，加true则是严格类型检查
5 array_reverse($arr);      //将数组中的元素翻转
6 in_array("apple", $arr);  //在数组中检索apple
7 array_key_exists("apple", $arr); // 检索给定的键名是否存在数组中
8 array_count_values($arr);        // 统计数组中所有值出现的次数
复制代码
二、数组的分段和填充
1 array_slice($arr, 0, 3);    //将数组中的一段取出，此函数忽略键名（数组的分段）
2 array_splice($arr, 0, 3，array("black","maroon"));    //将数组中的一段取出，返回的序列从原数组中删除
3 array_chunk($arr, 3, TRUE);   //将一个数组分割成多个，TRUE为保留原数组的键名（分割多个数组）
四、数组与栈，列队
1 array_push($arr, "apple", "pear");    //将一个或多个元素压入数组栈的末尾（入栈），返回入栈元素的个数
2 array_pop($arr);    // 将数组栈的最后一个元素弹出（出栈）
3 array_shift($arr);   //数组中第一个元素移出并返回（长度减1，其他元素向前移动一位，数字键名改为从零计数，文字键名不变）
4 array_unshift($arr,"a",array(1,2));  //在数组的开头插入一个或多个元素
六、数组的排序
1 sort($arr);       //由小到大，忽略键名       
2 rsort($arr);      //由大到小，忽略键名
3 asort($arr);     //由小到大，保留键名       
4 arsort($arr);    // 由大到小，保留键名
5 ksort($arr);     //按照键名正序排序           
6 krsort($arr);   // 按照键名逆序排序
七、数组的计算
复制代码
1 array_sum($arr);   //对数组内部的所有元素做求和运算（数组元素的求和）
2 array_merge($arr1, $arr2); //合并两个或多个（相同字符串键名，后面覆盖前面，相同的数字键名，后面的附加到后面）
3  
4 array_diff($arr1, $arr2);           //返回差集结果数组   array_diff_assoc($arr1, $arr2, $arr3);  //返回差集结果数组，键名也做比较
5 array_intersect($arr1, $arr2);  //返回交集结果数组    array_intersect_assoc($arr1, $arr2);   //返回交集结果数组，键名也做比较 
复制代码
八、其他的数组函数
1 array_unique($arr);   //移除数组中重复的值，新的数组中会保留原始的键名
2 shuffle($arr);             // 将数组的顺序打乱
 
PHP中几个输出函数echo，print()，print_r()，sprintf()，var_dump()的区别
1：echo：是语句不是函数，没有返回值，可输出多个变量值，不需要圆括号。不能输出数组和对象，只能打印简单类型(如int,string)。

2：print：是语句不是函数，有返回值 1 ，只能输出一个变量，不需要圆括号。不能输出数组和对象，只能打印简单类型(如int,string)。

3：print_r：是函数，可以打印复合类型，例如：stirng、int、float、array、object等，输出array时会用结构表示，而且可以通过print_r($str,true)来使print_r不输出而返回print_r处理后的值

4：printf：是函数，把文字格式化以后输出（参看C语言）

5：sprintf：是函数，跟printf相似，但不打印，而是返回格式化后的文字（该函数把格式化的字符串写写入一个变量中，而不是输出来），其    他的与printf一样。 

例如：  

1 $str = "Hello";    
2 $number = 123; 
3 $txt = sprintf("%s world. Day number %u",$str,$number)；
4 //输出： Hello world. Day number 123 
6：var_dump()：函数，输出变量的内容、类型或字符串的内容、类型、长度。常用来调试。

可以通过function_exists('函数名称')进行测试

1 var_dump(function_exists('print'));  //bool(false)
2 
3 var_dump(function_exists('echo'));  //bool(false)
4 
5 var_dump(function_exists('print_r')); //bool(true)
不用新变量直接交换现有两个变量的值
复制代码
 1 1： 
 3     list($a, $b) = array($b, $a);
 4 2： 
 5     $a = $a . $b;
 5     $b = strlen( $b );
 6     $b = substr( $a, 0, (strlen($a) – $b ) );
 7     $a = substr( $a, strlen($b) );
 8 
 9 3:(必须用一个两个字符串都都不能出现的字符做为分隔符)
10     $a = $b.','.$a ;
11     $a = explode(',', $a);
12     $b = $a[1];
13     $a = $a[0];
14 
15 4：这个是当两个数都是数字的时候:
16     $a = $a + $b;
17     $b = $a – $b;
18     $a = $a – $b;
19 
20 5：借助数组
21     $a = array($a,$b);
22     $b = $a[0];
23     $a = $a[1];
复制代码
heredoc

Heredoc在正规的PHP文档中和技术书籍中一般没有详细讲述。他是一种Perl风格的字符串输出技术。使用heredoc技术可以实现界面与代码的准分离，比如phpwind模板。

heredoc的语法是用”<<<”加上自己定义成对的标签，在标签范围内的文字视为一个字符串

规则如下：

1、以<<<End开始标记开始，以End结束标记结束，结束标记必须顶头写，不能有缩进和空格，且在结束标记末尾要有分号 。开始标记和开始标记相同，比如常用大写的EOT、EOD、EOF来表示，也可以使用其他标记，只要保证开始标记和结束标记不在正文中出现就行。

2、位于开始标记和结束标记之间的变量可以被正常解析，但是函数则不可以。在heredoc中，变量不需要用连接符 . 或 , 来拼接，比如：

1 $a=2;
2 $b= <<<EOF
3 "zyf"$a
4 "zyf"
5 EOF;
6 echo $b; //结果连同双引号一起输出："zyf"2 "zyf"
3、heredoc常用在输出包含大量HTML语法文档的时候。他要比传统的echo输出精炼很多，如下所示：

复制代码
 1 function getHtml()
 2 {
 3     echo "<html>";
 4     echo "<head><title>Title</title></head>";
 5     echo "<body>Content</body>";
 6     echo "</html>;
 7 }
 8 
 9 function getHtml()
10 {
11 echo <<<EOT
12       <html>
13       <head><title>Title</title></head>
14       <body>Content</body>
15       </html>
16 EOT;
17 }
复制代码
 

写个函数来解决多线程同时读写一个文件的问题。
复制代码
 1 <?php
 2     $fp = fopen("/tmp/lock.txt","w+");
 3     if(flock($fp, LOCK_EX)){// 进行排它型锁定
 4         fwrite($fp,"Write something here\n");
 5         flock($fp, LOCK_UN);// 释放锁定
 6     }else{
 7         echo "Couldn't lock the file !";
 8     }
 9     fclose($fp);
10 ?>
复制代码
 

禁掉cookie的session使用方案，设置session过期的方法，对应函数：

通过 url 传值，把session id附加到url上（缺点：整个站点中不能有纯静态页面，因为纯静态页面session id 将无法继续传到下一页面）
通过隐藏表单，把session id 放到表单的隐藏文本框中同表单一块提交过去（缺点：不适用<a>标签这种直接跳转的非表单的情况）
直接配置php.ini文件,将php.ini文件里的session.use_trans_sid= 0设为1,（好像在win上不支持）
用文件、数据库等形式保存Session ID，在跨页过程中手动调用
复制代码
 1 第一种  setcookie() 直接用setcookie设置session id的生命周期。
 3     $lifetime=60; //保存1分钟 
 4     session_start(); 
 5     setcookie(session_name(), session_id(), time()+$lifetime, "/");
 6 第二种  session_set_cookie_params()    
 7     $lifetime=60;//保存1分钟
 8     session_set_cookie_params($lifetime);
 9     session_start();
10     session_regenerate_id(true);
11     其中session_regenerate_id();方法用于改变当前session_id的值，并保留session中数组的值。参数默认为 false,如果设置为true则改变session_id的值，并清空当前session数组。

 

json格式数据有哪些特点 

	JSON 一种轻量级的数据交换格式。它基于ECMAScript的一个子集。 JSON采用完全独立于语言的文本格式，但是也使用了类似于C语言家族的习惯（包括C、C++、C#、Java、JavaScript、Perl、 Python等）。这些特性使JSON成为理想的数据交换语言。 易于人阅读和编写，同时也易于机器解析和生成(网络传输速率)。

JSON的结构基于下面两点

1. "名称/值"对的集合 不同语言中，它被理解为对象(object)，记录(record)，结构(struct)，字典(dictionary)，哈希表(hash table)，键列表(keyed list)等
2. 值的有序列表 多数语言中被理解为数组(array) 
 
php获取文件内容的方法，对应的函数
1：file_get_contents得到文件的内容（可以以get和post的方式获取），整个文件读入一个字符串中
2：用fopen打开url, 以get方式获取内容（借助fgets()函数）
3：用fsockopen函数打开url（可以以get和post的方式获取），以get方式获取完整的数据，包括header和body
4：使用curl库获取内容，使用curl库之前，需要查看php.ini，查看是否已经打开了curl扩展
 
php魔术方法与魔术常量
方法：__construct()，__destruct()。    
方法重载：__call()，__callStatic()。
属性重载：__get()，__set()，__isset()，__unset()  //这四个只对类中私有受保护的成员属性有效
__toString()，__autoload();
_set_state()； //自 PHP 5.1.0 起当调用 var_export() 导出类时，此静态 方法会被调用。 本方法的唯一参数是一个数组。
 
__clone();       //通常用于对副本进行重新初始化，
说明：this指副本对象引用，that指原对象引用 等方法在 PHP 中被称为”魔术方法”。PHP 将所有以 __（两个下划线）开头的类方法保留为魔术方法。所以在定义类方法时，除了上述魔术方法，建议不要以 __ 为前缀。在命名自己的类方法时不能使用这些方法名，除非是想使用其魔术功能。
 
常量：
__LINK__      //文件中的当前行号。
__FILE__       //文件的完整路径和文件名。如果用在被包含文件中，则返回被包含的文件名。
__DIR__       //文件所在的目录。如果用在被包括文件中，则返回被包括的文件所在的目录，它等价于 dirname(__FILE__)。
 
__FUNCTION__       //函数名称。自 PHP 5 起本常量返回该函数被定义时的名字（区分大小写）。在 PHP 4 中该值总是小写字母的。
 __CLASS__              //类的名称。自 PHP 5 起本常量返回该类被定义时的名字（区分大小写）。在 PHP 4 中该值总是小写字母的。
 __METHOD__         //类的方法名（PHP 5.0.0 新加）。返回该方法被定义时的名字（区分大小写）。
 __NAMESPACE__   //当前命名空间的名称（大小写敏感）。这个常量是在编译时定义的（PHP 5.3.0 新增）
 
PHP 如何获取客户端的IP地址，

用$_SERVER获取的IP地址有什么问题？

$_SERVER['REMOTE_ADDR'] ;   通过全局数组来获得 

getenv('REMOTE_ADDR') ; 通过环境变量来获得

当客户机使用代理的时候获取不到真实的IP地址

 

写一个函数，可以遍历文件夹下的所有文件和文件夹。

复制代码
 1     function get_dir_info($path){
 2           $handle = opendir($path);//打开目录返回句柄
 3           while(($content = readdir($handle))!== false){
 4                 $new_dir = $path . DIRECTORY_SEPARATOR . $content;
 5                 if($content == '..' || $content == '.'){
 6                        continue;
 7                 }
 8                 if(is_dir($new_dir)){
 9                       echo "<br>目录：".$new_dir . '<br>';
10                       get_dir_info($new_dir);
11                 }else{
12                       echo "文件：".$path.':'.$content .'<br>';
13                 }
14           }
15       }
16       get_dir_info($dir);
复制代码
 

有mail.log的一个文档，内容为若干邮件地址，用’\n’分隔换行。挑选sina.com的地址（包括从文件读取、过滤到列印出来）。

思路1：用正则表达式（比较慢，效率地，不推荐用）

思路2：cat mail.log | grep sina.com

 

PHP缓存技术有哪些? tp是局部还是完全缓存?

1. 全页面静态化缓存，也就是将页面全部生成html静态页面，用户访问时直接访问的静态页面，而不会去走php服务器解析的流程
2. 页面部分缓存，将一个页面中不经常变的部分进行静态缓存，而经常变化的块不缓存，最后组装在一起显示
3. 数据缓存，通过一个id进行请求的数据,将数据缓存到一个php文件中,id和文件是对应的,下次通过这个id进行请求时 直接读php文件
4. 查询缓存，和数据缓存差不多,根据查询语句进行缓存;
5. 常用的缓存技术有：redis和memcache
个人认为tp应该是全局缓存 因为：tp缓存实在本地生成一个php文件来存储数据库中读取出来的数据
 
strlen()与mb_strlen的作用与区别
在PHP中，strlen与mb_strlen是求字符串长度的函数
PHP内置的字符串长度函数strlen无法正确处理中文字符串，它得到的只是字符串所占的字节数。对于GB2312的中文编码，strlen得到的值是汉字个数的2倍，而对于UTF-8编码的中文，就是3倍（在 UTF-8编码下，一个汉字占3个字节）。
 
采用mb_strlen函数可以较好地解决这个问题。mb_strlen的用法和strlen类似，只不过它有第二个可选参数用于指定字符编码。例如得到UTF-8的字符串$str长度，可以用 mb_strlen($str,'UTF-8')。如果省略第二个参数，则会使用PHP的内部编码。内部编码可以通过 mb_internal_encoding()函数得到。

需要注意的是，mb_strlen并不是PHP核心函数，使用前需要确保在php.ini中加载了php_mbstring.dll，即确保“extension=php_mbstring.dll”这一行存在并且没有被注释掉，否则会出现未定义函 数的问题。
 
写一个函数，尽可能高效的从一个标准url中取出扩展名
$arr = parse_url('http://www.sina.com.cn/abc/de/fg.php?id=1');
 
$result = pathinfo($arr['path']);var_dump($arr);
 
var_dump($result['extension']);
 
php.ini 中safe mod关闭 影响哪些函数和参数，至少写6个？
move_uploaded_file()        exec()
system()                              passthru()
popen()                              fopen()
mkdir()                               rmdir()
rename()                            unlink()
copy()                                 chgrp()
chown()                              chmod()
touch()                               symlink()
link()                                   parse_ini_file()
set_time_limit()                  max_execution_time mail()
 

一群猴子排成一圈，按1，2，…，n依次编号。然后从第1只开始数，数到第m只,把它踢出圈，从它后面再开始数，再数到第m只，在把它踢出去…，如此不停 的进行下去，直到最后只剩下一只猴子为止，那只猴子就叫做大王。要求编程模拟此过程，输入m、n, 输出最后那个大王的编号。

复制代码
 1 <？php
 2  function fuhuan($allnum, $ti){
 3      $arr = array();
 4      for($i = 0; $i < $allnum; $i++){
 5          $arr[$i] = $i;
 6      }
 7  
 8      $nums = 1;
 9      while(count($arr) > 1){
10           foreach ($arr as $key => $value) {
11               if($nums == $ti){
12                   unset($arr[$key]);
13                   $nums = 1;
14               }else{
15                   $nums++;
16               }
17          }
18      }
19      $new_arr = array_values($arr);
20      var_dump($new_arr[0] + 1);
21  }
22  fuhuan(10,10);
复制代码
isset() 、empty()与is_null的区别

1、当变量未定义时，is_null() 和“参数本身”是不允许作为参数判断的，会报Notice警告错误；

2、empty , isset首先都会检查变量是否存在，然后对变量值进行检测。而is_null 和 “参数本身”只是直接检查变量值，是否为null，因此如果变量未定义就会出现错误！

3、isset()：仅当null和未定义，返回false；

4、empty()：""、0、"0"、NULL、FALSE、array(),未定义，均返回true；

5、is_null()：仅判断是否为null，未定义报警告；

6、变量本身作为参数，与empty()一致，但接受未定义变量时，报警告；

 

求两个文件的相对路径

复制代码
 1  getpath('/a/b/c/d/e.php', '/a/d/12/34/c.php');
 2   
 3 public function getpath($a, $b)
 4 {
 5      $aarr = explode('/', $a);
 6      $barr = explode('/', $b);
 7      $count = count($barr) - 2;
 8      $pathinfo = '';
 9      for($i = 1; $i <= $count; $i++){
10          if($aarr[$i] == $barr[$i]){
11               $pathinfo .= '../';
12          }else{
13               $pathinfo .= $barr[$i] . '/';
14          }
15      }
16      echo $pathinfo;
17 }
复制代码
 

MVC的优缺点
1、 MVC的优点 
		（1） 可以为一个模型在运行时同时建立和使用多个视图。变化-传播机制可以确保所有相关的视图及时得到模型数据变化，从而使所有关联的视图和控制器做到行为同步。 
		（2） 视图与控制器的可接插性，允许更换视图和控制器对象，而且可以根据需求动态的打开或关闭、甚至在运行期间进行对象替换。 
		（3） 模型的可移植性。因为模型是独立于视图的，所以可以把一个模型独立地移植到新的平台工作。需要做的只是在新平台上对视图和控制器进行新的修改。 
		（4） 潜在的框架结构。可以基于此模型建立应用程序框架，不仅仅是用在设计界面的设计中。 
2、 MVC的不足之处 
		（1） 增加了系统结构和实现的复杂性。对于简单的界面，严格遵循MVC，使模型、视图与控制器分离，会增加结构的复杂性，并可能产生过多的更新操作，降低运行效率。 
		（2） 视图与控制器间的过于紧密的连接。视图与控制器是相互分离，但确实联系紧密的部件，视图没有控制器的存在，其应用是很有限的，反之亦然，这样就妨碍了他们的独立重用。 
		（3） 视图对模型数据的低效率访问。依据模型操作接口的不同，视图可能需要多次调用才能获得足够的显示数据。对未变化数据的不必要的频繁访问，也将损害操作性能。 
		（4） 目前，一般高级的界面工具或构造器不支持MVC模式。改造这些工具以适应MVC需要和建立分离的部件的代价是很高的，从而造成使用MVC的困难。 
 
 
session与cookie的联系和区别（运行机制），session共享问题解决方案：
区别与联系：
	使用session_start()调用session，服务器端在生成session文件的同时生成session ID哈希值和默认值为PHPSESSID的session name，并向客户端发送变量为PHPSESSID(session name)(默认)值为一个128位的哈希值。服务器端将通过该cookie与客户端进行交互，session变量的值经php内部系列化后保存在服务器 机器上的文本文件中，和客户端的变量名默认情况下为PHPSESSID的coolie进行对应交互，即服务器自动发送了http 头:header(‘Set-Cookie: session_name()=session_id(); path=/’);即setcookie(session_name(),session_id());当从该页跳转到的新页面并调用 session_start()后,PHP将检查与给定ID相关联的服务器端存贮的session数据，如果没找到则新建一个数据集。
共享方案：
1：使用数据库保存session， 使用数据库来保存session，就算服务器宕机了也没事，session照样在。
问题：程序需要定制；每次请求都进行数据库读写开销不小，另外数据库是一个单点，可以做数据库的hash来解 决这个问题。       

2：使用 memcached来保存session， 这种方式跟数据库类似，内存存取性能比数据库好很多。

问题：程序需要定制，增加 了工作量；存入memcached中的数据都需要序列化，效率较低，断电或者重启电脑容易丢失数据；

3： 通过加密的cookie，在A服务器上登录，在用户的浏览器上添加加密的cookie，当用户访问B服务器时，检查有无Session，如果没有，就检验 Cookie是否有效，Cookie有效的话就在B服务器上重建session。简单，高效， 服务器的压力减小了，因为session数据不存在服务器磁盘上。根本就不会出现session读取不到的问题。

问题：网络请求占用很多。每次请求时，客户端都要通过cookie发送session数据给服务器，session中数据不能太多，浏览器对cookie 的大

小存在限制。不适合高访问量的情况，因为高访问量的情况下。

 

 正则表达式

匹配中文字符的正则表达式： [\u4e00-\u9fa5] 
匹配双字节字符(包括汉字在内)：[^\x00-\xff] 
匹配空行的正则表达式：\n[\s| ]*\r 
匹配HTML标记的正则表达式：/<(.*)>.*<\/\1>|<(.*) \/>/ 
匹配首尾空格的正则表达式：(^\s*)|(\s*$) 
匹配Email地址的正则表达式：\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)* 
匹配网址URL的正则表达式：^[a-zA-z]+://(\\w+(-\\w+)*)(\\.(\\w+(-\\w+)*))*(\\?\\S*)?$ 
匹配帐号是否合法(字母开头，允许5-16字节，允许字母数字下划线)：^[a-zA-Z][a-zA-Z0-9_]{4,15}$ 
匹配国内电话号码：(\d{3}-|\d{4}-)?(\d{8}|\d{7})? 
匹配腾讯QQ号：^[1-9]*[1-9][0-9]*$ 

 

--------------------------------------mysql部分-------------------------------------------

select * from table where (ID = 10)  or (ID = 32) or (ID = 22)  让结果按10, 32, 22的顺序检索出来？

Select *
from user_info
Where (ID IN (10, 32, 22))

order BY FIND_IN_SET(ID, '10, 32, 22') 


# linux系统部分

core文件是什么，有什么用？

 core是unix系统的内核。当你的程序出现内存越界的时候，操作系统会中止你的进程，并将当前内存状态倒出到core文件中，以便进一步分析。程序员可以通过core文件来找出问题所在。它记录了程序挂掉时详细的状态描述。
什么是core dump Core的意思是内存, Dump的意思是扔出来, 堆出来。开发和使用Unix程序时, 有时程序莫名其妙的down了, 却没有任何的提示(有时候会提示core dumped). 这时候可以查看一下有没有形如core.进程号的文件生成， 这个文件便是操作系统把程序down掉时的内存内容扔出来生成的，它可以做为调试程序的参考.
	core dump又叫核心转储, 当程序运行过程中发生异常, 程序异常退出时, 由操作系统把程序当前的内存状况存储在一个core文件中, 叫core dump。如何使用core文件 gdb -c core文件路径 [应用程序的路径]，进去后输入where回车, 就可以显示程序在哪一行当掉的, 在哪个函数中.
为什么没有core文件生成呢? core文件的生成跟你当前系统的环境设置有关系, 可以用下面的语句设置一下, 然后再运行程序便成生成core文件.
ulimit -c unlimited core文件生成的位置一般于运行程序的路径相同, 文件名一般为core.进程号
不用core文件，程序出了问题产生信号是否知道？答：内核向进程发信号嘛。

 

共享内存除了文件映射还有什么方式？

共享内存对象映射。

二者有什么区别：

答：内存映射文件是由一个文件到一块内存的映射，使应用程序可以通过内存指针对磁盘上的文件进行访问，其过程就如同对加载了文件的内存的访问，因此内存文件映射非常适合于用来管理大文件。

 

请解释下列10个shell命令的用途
top、ps、mv、find、df、cat、chmod、chgrp、grep、wc
top：命令是Linux下常用的性能分析工具，能够实时显示系统中各个进程的资源占用状况，类似于Windows的任务管理器。 
ps：查看进程 
mv：移动或者更改文件 
find：在子目录中搜索匹配的文件 
df：linux中df命令参数功能：检查文件系统的磁盘空间占用情况。
cat：把一个或多个文件内容显示到标准输出 

chmod：改变文件属性 

chgrp：改变用户分组 

grep：在文件内进行搜索 

wc：命令的功能为统计指定文件中的字节数、字数、行数, 并将统计结果显示输出。

 

Linux文件属性有哪些？（共十位）

-rw-r--r--那个是权限符号，总共是- --- --- ---这几个位。

第一个短横处是文件类型识别符：-表示普通文件；c表示字符设备（character）；b表示块设备（block）；d表示目录 （directory）；l表示链接文件（link）；后面第一个三个连续的短横是用户权限位（User），第二个三个连续短横是组权限位 （Group），第三个三个连续短横是其他权限位（Other）。每个权限位有三个权限，r（读权限），w（写权限），x（执行权限）。如果每个权限位都 有权限存在，那么满权限的情况就是：-rwxrwxrwx；权限为空的情况就是- --- --- ---。

权限的设定可以用chmod命令，其格式位：chmod ugoa+/-/=rwx filename/directory。例如：

一个文件aaa具有完全空的权限- --- --- ---。

chmod u+rw aaa（给用户权限位设置读写权限，其权限表示为：- rw- --- ---）

chmod g+r aaa（给组设置权限为可读，其权限表示为：- --- r-- ---）

chmod ugo+rw aaa（给用户，组，其它用户或组设置权限为读写，权限表示为：- rw- rw- rw-）

如果aaa具有满权限- rwx rwx rwx。

chmod u-x aaa（去掉用户可执行权限，权限表示为：- rw- rwx rwx）

如果要给aaa赋予制定权限- rwx r-x r-x，命令为：

chmod u=rwx，go=rx aaa

 
linux查询命令
1：find / -name "文件名"    在目录结构中搜索文件，并执行指定的操作。
 
2：grep
 
3：local 文件名  ---他是 'find -name' 的另一种写法，但要比后者快得多，原因在于它不搜索具体目录，而是搜索一个数据库（/var/lib/locatedb），这个数据库中含 有本地所有文件信息。Linux系统自动创建这个数据库，并且每天自动更新一次，所以改命令查不到最新变动过的文件。为了避免这种情况，可以在使用locate之前，先使用updatedb命令，手动更新数据库。
 
4. whereis ---是定位可执行文件、源代码文件、帮助文件在文件系统中的位置。whereis命令只能用于程序名的搜索，而且只搜索二进制文件（参数-b）、man说明文件（参数-m）和源代码文件（参数-s）。如果省略参数，则返回所有信息。
 
5：which  作用是在PATH变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果。也就是说，使用which命令，就可以看到某个系统命令是否存在，以及执行的到底是哪一个位置的命令。

# 服务器部分

 
Apache与Nginx的优缺点比较 
1、nginx相对于apache的优点： 
轻量级，比apache 占用更少的内存及资源。高度模块化的设计，编写模块相对简单 
抗并发，nginx 处理请求是异步非阻塞，多个连接（万级别）可以对应一个进程，而apache 则是阻塞型的，是同步多进程模型，一个连接对应一个进程，在高并发下nginx 能保持低资源低消耗高性能 
nginx处理静态文件好，Nginx 静态处理性能比 Apache 高 3倍以上 

2、apache 相对于nginx 的优点： 
apache 的rewrite 比nginx 的rewrite 强大 ，模块非常多，基本想到的都可以找到 ，比较稳定，少bug ，nginx 的bug 相对较多 
 
3：原因：这得益于Nginx使用了最新的epoll（Linux 2.6内核）和kqueue（freebsd）网络I/O模型，而Apache则使用的是传统的select模型。目前Linux下能够承受高并发访问的 Squid、Memcached都采用的是epoll网络I/O模型。 处理大量的连接的读写，Apache所采用的select网络I/O模型非常低效。
 
 
cgi 与fastcgi的区别
cgi在2000年或更早的时候用得比较多， 以前web服务器一般只处理静态的请求，web服务器会根据这次请求的内容，然后会fork一个新进程来运行外部c程序 （或perl脚本...）， 这个进程会把处理完的数据返回给web服务器，最后web服务器把内容发送给用户，刚才fork的进程也随之退出。 如果下次用户还请求改动态脚本，那么web服务器又再次fork一个新进程，周而复始的进行。

后来出现了一种更高级的方式是， web服务器可以内置perl解释器或php解释器。 也就是说这些解释器做成模块的方式，web服务器会在启动的时候就启动这些解释器。 当有新的动态请求进来时，web服务器就是自己解析这些perl或php脚本，省得重新fork一个进程，效率提高了。

fastcgi的方式是，web服务器收到一个请求时，他不会重新fork一个进程（因为这个进程在web服务器启动时就开启了，而且不会退 出），web服务器直接把内容传递给这个进程（进程间通信，但fastcgi使用了别的方式，tcp方式通信），这个进程收到请求后进行处理，把结果返回 给web服务器，最后自己接着等待下一个请求的到来，而不是退出。 
fastcgi跟cgi的区别是：
在web服务器方面
在对数据进行处理的进程方面
cgi         fork一个新的进程进行处理                                           
读取参数，处理数据，然后就结束生命期
fastcgi   用tcp方式跟远程机子上的进程或本地进程建立连接       
	要开启tcp端口，进入循环，等待数据的到来，处理数据


举个例子: 服务端现在有个10万个字单词， 客户每次会发来一个字符串，问以这个字符串为前缀的单词有多少个。 那么可以写一个程序，这个程序会建一棵trie树，然后每次用户请求过来时可以直接到这个trie去查找。 但是如果以cgi的方式的话，这次请求结束后这课trie也就没了，等下次再启动该进程时，又要新建一棵trie树，这样的效率就太低下了。   而用fastcgi的方式的话，这课trie树在进程启动时建立，以后就可以直接在trie树上查询指定的前缀了。

 

select, poll和epoll的区别
	select
		select最早于1983年出现在4.2BSD中，它通过一个select()系统调用来监视多个文件描述符的数组，当select()返回后，该数组中就绪的文件描述符便会被内核修改标志位，使得进程可以获得这些文件描述符从而进行后续的读写操作。
		select目前几乎在所有的平台上支持，其良好跨平台支持也是它的一个优点，事实上从现在看来，这也是它所剩不多的优点之一。
		select的一个缺点在于单个进程能够监视的文件描述符的数量存在最大限制，在Linux上一般为1024，不过可以通过修改宏定义甚至重新编译内核的方式提升这一限制。
		另外，select()所维护的存储大量文件描述符的数据结构，随着文件描述符数量的增大，其复制的开销也线性增长。同时，由于网络响应时间的延迟 使得大量TCP连接处于非活跃状态，但调用select()会对所有socket进行一次线性扫描，所以这也浪费了一定的开销。
	poll
		poll在1986年诞生于System V Release 3，它和select在本质上没有多大差别，但是poll没有最大文件描述符数量的限制。 
		poll和select同样存在一个缺点就是，包含大量文件描述符的数组被整体复制于用户态和内核的地址空间之间，而不论这些文件描述符是否就绪，它的开销随着文件描述符数量的增加而线性增大。 
		另外，select()和poll()将就绪的文件描述符告诉进程后，如果进程没有对其进行IO操作，那么下次调用select()和poll() 的时候将再次报告这些文件描述符，所以它们一般不会丢失就绪的消息，这种方式称为水平触发（Level Triggered）。
	epoll
		直到Linux2.6才出现了由内核直接支持的实现方法，那就是epoll，它几乎具备了之前所说的一切优点，被公认为Linux2.6下性能最好的多路I/O就绪通知方法。

		epoll可以同时支持水平触发和边缘触发（Edge Triggered，只告诉进程哪些文件描述符刚刚变为就绪状态，它只说一遍，如果我们没有采取行动，那么它将不会再次告知，这种方式称为边缘触发），理论上边缘触发的性能要更高一些，但是代码实现相当复杂。

		epoll同样只告知那些就绪的文件描述符，而且当我们调用epoll_wait()获得就绪文件描述符时，返回的不是实际的描述符，而是一个代表 就绪描述符数量的值，你只需要去epoll指定的一个数组中依次取得相应数量的文件描述符即可，这里也使用了内存映射（mmap）技术，这样便彻底省掉了 这些文件描述符在系统调用时复制的开销。

		另一个本质的改进在于epoll采用基于事件的就绪通知方式。在select/poll中，进程只有在调用一定的方法后，内核才对所有监视的文件描 述符进行扫描，而epoll事先通过epoll_ctl()来注册一个文件描述符，一旦基于某个文件描述符就绪时，内核会采用类似callback的回调 机制，迅速激活这个文件描述符，当进程调用epoll_wait()时便得到通知。

 

# Memcache和Redis区别

	Redis中，并不是所有的数据都一直存储在内存中的，这是和Memcached相比一个最大的区别。
	Redis在很多方面具备数据库的特征，或者说就是一个数据库系统，而Memcached只是简单的K/V缓存。
	他们的扩展都需要做集群；实现方式：master-slave、Hash。
	在100k以上的数据中，Memcached性能要高于Redis。
	如果要说内存使用效率，使用简单的key-value存储的话，Memcached的内存利用率更高，而如果Redis采用hash结构来做key-value存储，由于其组合式的压缩，其内存利用率会高于Memcached。当然，这和你的应用场景和数据特性有关。
	如果你对数据持久化和数据同步有所要求，那么推荐你选择Redis，因为这两个特性Memcached都不具备。即使你只是希望在升级或者重启系统后缓存数据不会丢失，选择Redis也是明智的。
	Redis和Memcache在写入性能上面差别不大，读取性能上面尤其是批量读取性能上面Memcache更强





# MTSQL
mysql inodb 模型
	innodb在一定程度上实现了行锁的功能，在功能上与ORACLE差不多。但innodb行锁的实现与oracle不相同，innodb的行锁加在索引上，如果没有索引，整张表都会被锁定，下面是实验过程。
	innodb 使用 B+ tree / B- tree 作为数据结构



# SQL:
	DISTINCT
	SELECT * FROM Websites WHERE name NOT BETWEEN 'A' AND 'H';
	SELECT name AS n, country AS c FROM Websites;
	SELECT Websites.name, Websites.url, access_log.count, access_log.date FROM Websites, access_log WHERE Websites.id=access_log.site_id and Websites.name="菜鸟教程";
	SELECT column_name(s) FROM table1 JOIN table2 ON table1.column_name=table2.column_name;
	SELECT country FROM Websites UNION SELECT country FROM apps ORDER BY country;
	SELECT * INTO WebsitesBackup2016 FROM Websites WHERE country='CN';

	TRUNCATE TABLE table_name;// del data
	DROP DATABASE database_name; //del index, table, db
	SELECT Websites.name,COUNT(access_log.aid) AS nums FROM access_log LEFT JOIN Websites ON access_log.site_id=Websites.id GROUP BY Websites.name;
正则:

# NEXUSGUARD

---------------------------
- haproxy
- varnish
- nginx
- lvs
---------------------------
lvs
keep alive	
Puppet
Jenkins :
supervisor
	用Python开发的一套通用的进程管理程序，能将一个普通的命令行进程变为后台daemon，并监控进程状态，异常退出时能自动重启。

Consul
	http://10.6.8.71/wiki/monitoring_system
nagios
Sensu: 	
	http://10.6.8.71/wiki/SensuMonitoringSystem
----------------------------

来自 Google 的高可用架构理念与实践
https://blog.coding.net/blog/architecture-concept-and-practice-from-Google

SLA: Service-Level Agreement的缩写，意思是服务等级协议。
NXG Downtime peryear 10 hour,

 内部只有99.99% 以上的服务才会配备 SRE，SRE 是必须在接到报警 5 分钟之内上线处理问题的，否则报警系统自动升级到下一个 SRE。如果还没有，直接给老板发报警。

但是从99.9%往上，就基本超出了人力的范畴，考验的是业务的自愈能力，架构的容灾,容错设计，灾备系统的完善等等。


MTBF: Mean time between Failures。 用通俗的话讲，就是一个东西有多不可靠，多长时间坏一次。
MTTR: Mean time to recover。意思就是一旦坏了，恢复服务的时间需要多长。
Availability = function(MTBF, MTTR)

提高MTBF， 降低MTTR；
MTBF 最大的因素： 升级；
一、提高冗余度，多实例运行，用资源换可用性。
	方案一： 提高冗余度，多实例运行，用资源换可用性。N + 2 ,一个热备，一个升级
	方案二： 实例之间必须对等、独立。
	方案三： 流量控制能力非常重要。
		Isolation。A 用户发来的请求可能和 B 用户发来的请求同时处理的时候有冲突，需要隔离。
		Quarantine。用户 A 发来的请求可能资源消耗超标，必须能将这类请求钉死在有限的几个节点上，从而顾全大局。
		Query-of-death。大家都遇到过吧。上线之后一个用户发来个一个异常请求直接搞挂服务。连续多发几个，整个集群都挂没了，高可用还怎么做到？那么，对这种类型的防范就是要在死掉几台服务器之后可以自动屏蔽类似的请求。需要结合业务去具体分析。
二、变更管理（Change Management）
	第一点：线下测试（Offline Test）
	第二点：灰度发布
	第三点：服务必须对回滚提供支持





js优化加载顺序
数据结构
tcp/ip 长连接与短连接 
hadoop
	-HDFS
	-MapReduce
		HDFS在集群上实现了分布式文件系统，MR在集群上实现了分布式计算和任务处理。
	-HBase
		Hbase就是Hadoop database
	-Hive
		Hive中所有的数据都存储在HDFS中
 - 	Storm
 - 	Spark
 - 	Druid
 - 	Kafaka
 - 	Couchbase
 - 	Cassandra
 - 	ActiveMQ
 - 	MongoDB
 -	设计模式
 -	高并发
 -	docker
 -	python
 -	kubernetes
 -	数据库技巧

# JAVA

	1. java 基本数据类型string 和string类，是不同的。一个是
		从存储位置上来说：
		基本数据类型存储在栈区，String属于对象类型存储在堆区。
		从参数传递方面来说：
		基本数据类型采用的是值传递，而String采用的是引用传递。
		不过由于String内部实现采用了final关键字，这就导致对于String的每次更改都是在创建新的String对象，所以如果一个函数对传进来的String类型做了更改实际上是不会影响到传进来前的String对象的值得。
	2. java 文件名必须和文件中唯一的Public 类相同；
	3. java 一个文件中，只能有一个public 类；
	4. abstract 不能实例化对象；

# technology:
	分布式缓存
	持久化存储
	分布式消息系统
	搜索引擎
	实时计算
	离线计算
	分布式文件系统
	日志收集系统
	监控系统
	数据仓库
	CDN系统
	负载均衡系统
	消息推送系统
	自动化运维系统

