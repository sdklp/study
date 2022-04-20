# 扩展函数名细 



**【IsInRange】**  

```
`int` `num = 100;``//以前写法``if` `(num > 100 & num < 1000) { }``//现在写法``if` `(num.IsInRange(100, 1000)) { } ``//datetime类型也支持`
```





**【IsNullOrEmpty】**

```
`object` `s = ``""``;``//以前写法``if` `(s == ``null` `|| ``string``.IsNullOrEmpty(s.ToString())) { }``//现在写法``if` `(s.IsNullOrEmpty()) { }``//更顺手了没有 }`
```



**【IsIn】**

```
`string` `value = ``"a"``;``//以前写法我在很多项目中看到``if` `(value == ``"a"` `|| value == ``"b"` `|| value == ``"c"``) { ``}``//现在写法``if` `(value.IsIn(``"a"``, ``"b"``, ``"c"``)) { ``            ` `}`
```



**【IsContainsIn】**

```
`//以前写法``if` `(``"abcd"``.Contains(``"abc"``) || ``"abcd"``.Contains(``"xxx"``))``{ ``            ` `}``//现在写法``if` `(``"abcd"``.IsContainsIn(``"abc"``, ``"xxx"``)) { ``            ` `}`
```



**【IsValuable与IsNullOrEmpty相反】**

```
`string` `ss = ``""``;``//以前写法``if` `(!``string``.IsNullOrEmpty(ss)) { }``//现在写法``if` `(s.IsValuable()) { }`
```







**IsIDcard**

```
`if` `(``"32061119810104311x"``.IsIDcard()){``}`
```



**IsTelephone**

```
`if` `(``"0513-85669884"``.IsTelephone()){``}`
```



**IsMatch 节约你引用Regex的命名空间了**

if ("我中国人12".IsMatch(@"人\d{2}")) { }





**下面还有很多太简单了的就不介绍了**

//IsZero

//IsInt

//IsNoInt

//IsMoney 

//IsEamil 

//IsMobile 

​         





**源码：**

```
`    ``/// <summary>``    ``/// ** 描述：逻辑判段是什么？``    ``/// ** 创始时间：2015-5-29``    ``/// ** 修改时间：-``    ``/// ** 作者：sunkaixuan``    ``/// ** 使用说明：http://www.cnblogs.com/sunkaixuan/p/4539654.html``    ``/// </summary>``    ``public` `static` `class` `IsWhatExtenions``    ``{``        ``/// <summary>``        ``/// 值在的范围？``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <param name="begin">大于等于begin</param>``        ``/// <param name="end">小于等于end</param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsInRange(``this` `int` `thisValue, ``int` `begin, ``int` `end)``        ``{``            ``return` `thisValue >= begin && thisValue <= end;``        ``}``        ``/// <summary>``        ``/// 值在的范围？``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <param name="begin">大于等于begin</param>``        ``/// <param name="end">小于等于end</param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsInRange(``this` `DateTime thisValue, DateTime begin, DateTime end)``        ``{``            ``return` `thisValue >= begin && thisValue <= end;``        ``}` `        ``/// <summary>``        ``/// 在里面吗?``        ``/// </summary>``        ``/// <typeparam name="T"></typeparam>``        ``/// <param name="thisValue"></param>``        ``/// <param name="values"></param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsIn<T>(``this` `T thisValue, ``params` `T[] values)``        ``{``            ``return` `values.Contains(thisValue);``        ``}` `        ``/// <summary>``        ``/// 在里面吗?``        ``/// </summary>``        ``/// <typeparam name="T"></typeparam>``        ``/// <param name="thisValue"></param>``        ``/// <param name="values"></param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsContainsIn(``this` `string` `thisValue, ``params` `string``[] inValues)``        ``{``            ``return` `inValues.Any(it => thisValue.Contains(it));``        ``}` `        ``/// <summary>``        ``/// 是null或""?``        ``/// </summary>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsNullOrEmpty(``this` `object` `thisValue)``        ``{``            ``if` `(thisValue == ``null` `|| thisValue == DBNull.Value) ``return` `true``;``            ``return` `thisValue.ToString() == ``""``;``        ``}``        ``/// <summary>``        ``/// 是null或""?``        ``/// </summary>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsNullOrEmpty(``this` `Guid? thisValue)``        ``{``            ``if` `(thisValue == ``null``) ``return` `true``;``            ``return` `thisValue == Guid.Empty;``        ``}``        ``/// <summary>``        ``/// 是null或""?``        ``/// </summary>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsNullOrEmpty(``this` `Guid thisValue)``        ``{``            ``if` `(thisValue == ``null``) ``return` `true``;``            ``return` `thisValue == Guid.Empty;``        ``}` `        ``/// <summary>``        ``/// 有值?(与IsNullOrEmpty相反)``        ``/// </summary>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsValuable(``this` `object` `thisValue)``        ``{``            ``if` `(thisValue == ``null``) ``return` `false``;``            ``return` `thisValue.ToString() != ``""``;``        ``}``        ``/// <summary>``        ``/// 有值?(与IsNullOrEmpty相反)``        ``/// </summary>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsValuable(``this` `IEnumerable<``object``> thisValue)``        ``{``            ``if` `(thisValue == ``null` `|| thisValue.Count() == 0) ``return` `false``;``            ``return` `true``;``        ``}` `        ``/// <summary>``        ``/// 是零?``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsZero(``this` `object` `thisValue)``        ``{``            ``return` `(thisValue == ``null` `|| thisValue.ToString() == ``"0"``);``        ``}` `        ``/// <summary>``        ``/// 是INT?``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsInt(``this` `object` `thisValue)``        ``{``            ``if` `(thisValue == ``null``) ``return` `false``;``            ``return` `Regex.IsMatch(thisValue.ToString(), ``@"^\d+$"``);``        ``}``        ``/// <summary>``        ``/// 不是INT?``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsNoInt(``this` `object` `thisValue)``        ``{``            ``if` `(thisValue == ``null``) ``return` `true``;``            ``return` `!Regex.IsMatch(thisValue.ToString(), ``@"^\d+$"``);``        ``}` `        ``/// <summary>``        ``/// 是金钱?``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsMoney(``this` `object` `thisValue)``        ``{``            ``if` `(thisValue == ``null``) ``return` `false``;``            ``double` `outValue = 0;``            ``return` `double``.TryParse(thisValue.ToString(), ``out` `outValue);``        ``}` `        ``/// <summary>``        ``/// 是时间?``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsDate(``this` `object` `thisValue)``        ``{``            ``if` `(thisValue == ``null``) ``return` `false``;``            ``DateTime outValue = DateTime.MinValue;``            ``return` `DateTime.TryParse(thisValue.ToString(), ``out` `outValue);``        ``}` `        ``/// <summary>``        ``/// 是邮箱?``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsEamil(``this` `object` `thisValue)``        ``{``            ``if` `(thisValue == ``null``) ``return` `false``;``            ``return` `Regex.IsMatch(thisValue.ToString(), ``@"^[\w-]+(\.[\w-]+)*@[\w-]+(\.[\w-]+)+$"``);``        ``}` `        ``/// <summary>``        ``/// 是手机?``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsMobile(``this` `object` `thisValue)``        ``{``            ``if` `(thisValue == ``null``) ``return` `false``;``            ``return` `Regex.IsMatch(thisValue.ToString(), ``@"^\d{11}$"``);``        ``}` `        ``/// <summary>``        ``/// 是座机?``        ``/// </summary>``        ``public` `static` `bool` `IsTelephone(``this` `object` `thisValue)``        ``{``            ``if` `(thisValue == ``null``) ``return` `false``;``            ``return` `System.Text.RegularExpressions.Regex.IsMatch(thisValue.ToString(), ``@"^(\(\d{3,4}\)|\d{3,4}-|\s)?\d{8}$"``);` `        ``}` `        ``/// <summary>``        ``/// 是身份证?``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsIDcard(``this` `object` `thisValue)``        ``{``            ``if` `(thisValue == ``null``) ``return` `false``;``            ``return` `System.Text.RegularExpressions.Regex.IsMatch(thisValue.ToString(), ``@"^(\d{15}$|^\d{18}$|^\d{17}(\d|X|x))$"``);``        ``}` `        ``/// <summary>``        ``/// 是传真?``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsFax(``this` `object` `thisValue)``        ``{``            ``if` `(thisValue == ``null``) ``return` `false``;``            ``return` `System.Text.RegularExpressions.Regex.IsMatch(thisValue.ToString(), ``@"^[+]{0,1}(\d){1,3}[ ]?([-]?((\d)|[ ]){1,12})+$"``);``        ``}` `        ``/// <summary>``        ``///是适合正则匹配?``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <param name="begin">大于等于begin</param>``        ``/// <param name="end">小于等于end</param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsMatch(``this` `object` `thisValue, ``string` `pattern)``        ``{``            ``if` `(thisValue == ``null``) ``return` `false``;``            ``Regex reg = ``new` `Regex(pattern);``            ``return` `reg.IsMatch(thisValue.ToString());``        ``}``        ``/// <summary>``        ``/// 是true?``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsTrue(``this` `object` `thisValue)``        ``{``            ``return` `Convert.ToBoolean(thisValue);``        ``}``        ``/// <summary>``        ``/// 是false?``        ``/// </summary>``        ``/// <param name="thisValue"></param>``        ``/// <returns></returns>``        ``public` `static` `bool` `IsFalse(``this` `object` `thisValue)``        ``{``            ``return` `!Convert.ToBoolean(thisValue);``        ``}``    ``}`
```