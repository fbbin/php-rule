
## PHP 编码规范：  

---

1、 **UTF-8 without BOM** -- 文件应该使用 Unicode（UTF-8）编码保存，且不包含字节序标记（BOM）[[Why?]](https://www.zhihu.com/question/20167122)；  
  
2、 **Spaces for indentation** -- 缩进请使用 4 个空格，禁止使用 tab [[Why?]](http://ooxx.me/tabs-vs-spaces.orz)；  
  
3、 **LF Line Ending** -- 换行要使用 Unix 换行符（为了在 Windows、Linux、OS X 三大平台中更好的兼容，现在 Linux、OS X 已统一为 Unix 换行符，这么做主要是为了 Windows）；

`以上 3 条为开发中的重要原则`

---  

4、 代码块标记请使用 `<?php ?>`，禁止使用短标签，保持最好的兼容；PHP 文件只需带开始标记 `<?php` ，禁止带结束标记 `?>`；[[Why?]](https://www.zhihu.com/question/20989781)

5、 注释要简洁明了、能描述清楚业务逻辑为佳，禁止无意义的注释：  

```
有意义的注释：
1. $member = $db->getMemberInfo($id); //根据用户ID获取用户信息
2. if ($member['status'] == 1) //如果用户状态为禁用

无意义的注释：
1. $member = $db->getMemberInfo($id); //读取一条数据
2. if ($member['status'] == 1) //如果用户状态等于1
```
  
6、 [类名] 首字母大写，有多个单词时，禁止使用下划线，应该使用骆驼命名法，例如：  
  
```    
AdminController.php  
OpenAdminController.php  
```
  
7、 [方法名] 和 [变量名] 均使用骆驼命名法，例如：  
  
```        
getUserInfo()  
updateUserInfoById()
```
  
8、 对于类中的方法名，在遵循上面原则的基础上，如果是private 则必须以下划线开头，例如：

```
private function _getUserPermission() {}
```  
  
9、 提交代码前，请进行格式化操作，格式化推荐工具：[phpfmt](https://github.com/phpfmt/fmt)，也可以使用 phpfmt 的插件，支持如下编辑器 / IDE：
    
  - Sublime Text 3
  - Vim
  - PHPStorm  
  
格式化参数如下：
  
```
{
	"autocomplete": true, //意义不明，如有知道请告知
	"enable_auto_align": false, //当格式化对象为配置文件时，等号对齐要设为 true ***重要
	"indent_with_space": true, //缩进转为空格
	"psr1_naming": false, //不启用 psr1 规范的命名方式（方法名会变成骆驼命名法）
	"psr2": true, //启用 psr2 规范
	"version": 4 //意义不明，如有知道请告知
}
```

格式化的好处：
  
- 会把 namespace、use、class、method 用空行隔开，并且按照 alphabet 字典排序
- 会清理掉有 use 引入，却没有使用过的 use 对象
- 会帮你把 tab 转义为空格
- 会让代码更清晰易读

10、合理使用命名空间、自动加载等机制，合理引入 SDK 文件等；

11、变量必须初始化后再使用，从用户那里接收过来的参数必须强制转换类型；

12、数据库相关：  

```
1. 只需要一条数据时，禁止查询全表；
2. 禁止用 select * 这种效率低下的方式，要明确写出需要的字段；
3. 能用 group by 解决的问题，禁止用 distinct；
4. 禁止把 join 时，该写在 on 条件里面的，写到 where 去；也同样禁止把该写在 where 条件里面的，写到 on 去；
5. 和金钱有关的字段，应使用 decimal；
6. 如果知道该字段不可能出现负值，请使用 unsigned；
7. 经常查询的字段，合理建立索引；
8. 禁止把任何字段设置为 [允许空]，请设为 [不为空] 且默认空字符串 ""；
9. 日期字段，如果是 1970 - 2037 年内的时间，请设置为 unsigned 且用 int （性能最佳），
  如果是日志类型的，只是显示作用的，那么，请设为 datetime；
10.如果你只需要判断数据是否存在，而不需要取出数据，使用子查询更加高效 select exists(select 1 from table where xxx)；
11.能用 between 的时候不要用 >=、<=、in、or，能用 in 的时候不要用 or
```

12、尽量使你的代码结构优雅、简洁、清晰、高效，例如：

12.1、高效率类型转换

```
$string = '1';
$string -= 0; //快速从 string 转为 int
$string .= ''; //快速从 int 转为 string
$string = '3.14159265358';
$string -= 0; //同理，快速从 string 转为 float
```
12.2、 合理使用引用来减少内存占用或赋值

```
$array = [
    'a' => '1',
    'b' => 2,
];
foreach ($array as &v) {
    $v['a'] -= 0; //已经转换为 int
    $v['c'] = 3; //添加新字段到数组中
}
var_dump($array); // ['a' => 1, 'b' => 2, 'c' => 3]
```
12.3、让你的代码结构更清晰

```
错误结构 1：
if ($a == 1) {
    $b = 0;
} else {
    $b = 1;
}

正确结构 1：
$b = 1;
if ($a == 1) {
    $b = 0;
}

错误结构 2：
if ($result) {
    return true;
} else {
    return false;
}

if ($result) {
    return true;
}
return false;

正确结构 2：
if (!$result) {
    return false;
}
return true; //请保持方法结尾返回的是 true

错误结构 3：
if ($object) {
    // todo...
} else {
    // todo...
}

正确结构 3：
if (!$object) {
    // todo...
    return false;
}

//todo...
```
12.4、 在 PHP 中，数组的性能很高，因此要正确使用数组：

```
错误方式 1：
switch($index) {
    case 1:
        $b = 'ABC'; break;
    case 2:
        $b = 'DEF'; break;
    default:
        $b = 'GHI'; break;
}

错误方式 2：
if ($index == 1) {
    $b = 'ABC';
} else if ($index == 2) {
    $b = 'DEF';
} else {
    $b = 'GHI';
}

正确方式：
$array = ['ABC', 'DEF'];
$b = isset($array[$index]) ? $array[$index - 1] : 'GHI';
```

12.5、 因为公司服务器的 PHP 版本较新，因此数组推荐使用新的方式，不要使用旧的方式

```
不推荐方式：
$arr = array(1, 2, 3);

推荐方式：
$arr = [1, 2, 3];
```
12.6、 如果只需要判断字符串中 [存在] 某个字符，请使用 strpos / stripos !== false

12.7、 如果取出的数据比较庞大，使用后应 unset

12.8、 需要 echo 数据时，使用 echo a, b, c 效率比 echo a; echo b; echo c; 和 echo a . b . c 性能更高

12.9、 定义或输出大量字符串或 HTML 时，可以使用 PHP 的 heredoc，[参考](http://php.net/manual/zh/language.types.string.php#language.types.string.syntax.heredoc)

12.10、PHP 的时区系统会导致 strtotime 等不可预料的问题，推荐在 php.ini 文件里把 timezone 设置为 PRC或者Asia/Shanghai

13、在使用redis获取数据时，在使用如：hgetall, smembers, lrange, zrevrange ,zrange, 等命令时一定要加范围参数；注意：`keys info*`这种模糊查找的绝对不能使用

14、对于在一个PHP CLASS文件中需要多处用到字符串，尽量在CLASS的顶部进行赋值定义，代码逻辑中直接使用类成员来处理，如：

	class DemoAsync
	{
	    /**
	     * 用户全站等级Redis KEY
	     *
	     * @var hash
	     */
	    private static $userSiteLevelInfoKey = "user:slevel:%d";
	    
	    /**
	     * 系统内部调用使用
	     *
	     * @param array $booter
	     * @return Treasure
	     */
	    public static function instance($booter, $server){}
	   
15、