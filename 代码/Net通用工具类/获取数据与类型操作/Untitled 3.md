# 缓存 HttpRuntime.Cache



**声名一个数据集合**

```
`var` `listString = ``new` `List<``string``>() { ``"a"``, ``"b"``, ``"c"` `};``//将listString存进缓存`
```



**缓存key**

```
`string` `key = ``"cmkey"``;`
```



**获取实例**

```
`var` `cacheManager = CacheManager<List<``string``>>.GetInstance();``//设置缓存类型为listString的类型`
```



**插入缓存**

```
`cacheManager.Add(key,listString, 60* 30);``//过期30分钟`
```



**获取缓存**

```
`List<``string``> cacheList=cacheManager[key];`
```



**其它方法**

```
`cacheManager.ContainsKey(key);``//是否存在缓存``cacheManager.Remove(key);``//删除``cacheManager.RemoveAll(c=>c.Contains(``"sales_"``));``//删除key包含sales_缓存``cacheManager.GetAllKey();``//获取所有key`
```





**代码：**

```
`    ``/// <summary>``    ``/// ** 描述：缓存操作类``    ``/// ** 创始时间：2015-6-9``    ``/// ** 修改时间：-``    ``/// ** 作者：sunkaixuan``    ``/// </summary>``    ``/// <typeparam name="K">键</typeparam>``    ``/// <typeparam name="V">值</typeparam>``    ``public` `class` `CacheManager<V> ``    ``{` `        ``#region 全局变量``        ``private` `static` `CacheManager<V> _instance = ``null``;``        ``private` `static` `readonly` `object` `_instanceLock = ``new` `object``();``        ``#endregion` `        ``#region 构造函数` `        ``private` `CacheManager() { }``        ``#endregion` `        ``#region  属性``        ``/// <summary>         ``        ``///根据key获取value     ``        ``/// </summary>         ``        ``/// <value></value>      ``        ``public` `override` `V ``this``[``string` `key]``        ``{``            ``get` `{ ``return` `(V)HttpRuntime.Cache[CreateKey(key)]; }``        ``}``        ``#endregion` `        ``#region 公共函数` `        ``/// <summary>         ``        ``/// key是否存在       ``        ``/// </summary>         ``        ``/// <param name="key">key</param>         ``        ``/// <returns> ///  存在<c>true</c> 不存在<c>false</c>.        /// /// </returns>         ``        ``public` `override` `bool` `ContainsKey(``string` `key)``        ``{``            ``return` `HttpRuntime.Cache[CreateKey(key)] != ``null``;``        ``}` `        ``/// <summary>         ``        ``/// 获取缓存值         ``        ``/// </summary>         ``        ``/// <param name="key">key</param>         ``        ``/// <returns></returns>         ``        ``public` `override` `V Get(``string` `key)``        ``{``            ``return` `(V)HttpRuntime.Cache.Get(CreateKey(key));``        ``}` `        ``/// <summary>         ``        ``/// 获取实例 （单例模式）       ``        ``/// </summary>         ``        ``/// <returns></returns>         ``        ``public` `static` `CacheManager<V> GetInstance()``        ``{``            ``if` `(_instance == ``null``)``                ``lock` `(_instanceLock)``                    ``if` `(_instance == ``null``)``                        ``_instance = ``new` `CacheManager<V>();``            ``return` `_instance;``        ``}` `        ``/// <summary>         ``        ``/// 插入缓存(默认20分钟)        ``        ``/// </summary>         ``        ``/// <param name="key"> key</param>         ``        ``/// <param name="value">value</param>          ``        ``public` `override` `void` `Add(``string` `key, V value)``        ``{``            ``Add(key, value, Minutes*20);``        ``}` `        ``/// <summary>         ``        ``/// 插入缓存        ``        ``/// </summary>         ``        ``/// <param name="key"> key</param>         ``        ``/// <param name="value">value</param>         ``        ``/// <param name="cacheDurationInSeconds">过期时间单位秒</param>         ``        ``public` `override`  `void` `Add(``string` `key, V value, ``int` `cacheDurationInSeconds)``        ``{``            ``Add(key, value, cacheDurationInSeconds, CacheItemPriority.Default);``        ``}` `        ``/// <summary>         ``        ``/// 插入缓存.         ``        ``/// </summary>         ``        ``/// <param name="key">key</param>         ``        ``/// <param name="value">value</param>         ``        ``/// <param name="cacheDurationInSeconds">过期时间单位秒</param>         ``        ``/// <param name="priority">缓存项属性</param>         ``        ``public` `void` `Add(``string` `key, V value, ``int` `cacheDurationInSeconds, CacheItemPriority priority)``        ``{``            ``string` `keyString = CreateKey(key);``            ``HttpRuntime.Cache.Insert(keyString, value, ``null``,``             ``DateTime.Now.AddSeconds(cacheDurationInSeconds), Cache.NoSlidingExpiration, priority, ``null``);``        ``}` `        ``/// <summary>         ``        ``/// 插入缓存.         ``        ``/// </summary>         ``        ``/// <param name="key">key</param>         ``        ``/// <param name="value">value</param>         ``        ``/// <param name="cacheDurationInSeconds">过期时间单位秒</param>         ``        ``/// <param name="priority">缓存项属性</param>         ``        ``public`  `void` `Add(``string` `key, V value, ``int``         ``cacheDurationInSeconds, CacheDependency dependency, CacheItemPriority priority)``        ``{``            ``string` `keyString = CreateKey(key);``            ``HttpRuntime.Cache.Insert(keyString, value,``             ``dependency, DateTime.Now.AddSeconds(cacheDurationInSeconds), Cache.NoSlidingExpiration, priority, ``null``);``        ``}` `        ``/// <summary>         ``        ``/// 删除缓存         ``        ``/// </summary>         ``        ``/// <param name="key">key</param>         ``        ``public` `override` `void` `Remove(``string` `key)``        ``{``            ``HttpRuntime.Cache.Remove(CreateKey(key));``        ``}` `        ``/// <summary>``        ``/// 清除所有缓存``        ``/// </summary>``        ``public` `override` `void` `RemoveAll()``        ``{``            ``System.Web.Caching.Cache cache = HttpRuntime.Cache;``            ``IDictionaryEnumerator CacheEnum = cache.GetEnumerator();``            ``ArrayList al = ``new` `ArrayList();``            ``while` `(CacheEnum.MoveNext())``            ``{``                ``al.Add(CacheEnum.Key);``            ``}``            ``foreach` `(``string` `key ``in` `al)``            ``{``                ``cache.Remove(key);``            ``}``        ``}` `        ``/// <summary>``        ``/// 清除所有包含关键字的缓存``        ``/// </summary>``        ``/// <param name="removeKey">关键字</param>``        ``public` `override` `void` `RemoveAll(Func<``string``, ``bool``> removeExpression)``        ``{``            ``System.Web.Caching.Cache _cache = HttpRuntime.Cache;``            ``var` `allKeyList = GetAllKey();``            ``var` `delKeyList = allKeyList.Where(removeExpression).ToList();``            ``foreach` `(``var` `key ``in` `delKeyList)``            ``{``                ``HttpRuntime.Cache.Remove(key); ;``            ``}``        ``}` `        ``/// <summary>``        ``/// 获取所有缓存key``        ``/// </summary>``        ``/// <returns></returns>``        ``public` `override` `IEnumerable<``string``> GetAllKey()``        ``{``            ``IDictionaryEnumerator CacheEnum = HttpRuntime.Cache.GetEnumerator();``            ``while` `(CacheEnum.MoveNext())``            ``{``                ``yield ``return` `CacheEnum.Key.ToString();``            ``}``        ``}``        ``#endregion` `        ``#region 私有函数` `        ``/// <summary>         ``        ``///创建KEY   ``        ``/// </summary>         ``        ``/// <param name="key">Key</param>         ``        ``/// <returns></returns>         ``        ``private` `string` `CreateKey(``string` `key)``        ``{``            ``return` `"http_cache_"``+key.ToString();``        ``}` `        ``#endregion`    `    ``}`
```