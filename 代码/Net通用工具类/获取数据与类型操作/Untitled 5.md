**声名一个数据集合**

```
`var` `listString = ``new` `List<``string``>() { ``"a"``, ``"b"``, ``"c"` `};``//将listString存进Session`
```



**Sessionkey**

```
`string` `key = ``"cmkey"``;`
```



**获取实例**

```
`var` `seManager = CacheManager<List<``string``>>.GetInstance();``//设置Session类型为listString的类型`
```



**插入Session**

```
`seManager.Add(key,listString);`
```



**获取Session**

```
`List<``string``> seList=seManager[key];`
```



**其它方法**

```
`seManager.ContainsKey(key);``//是否存在Session``seManager.Remove(key);``//删除``seManager.RemoveAll(c=>c.Contains(``"sales_"``));``//删除key包含sales_缓存``seManager.GetAllKey();``//获取所有key`
```





```
`    ``/// <summary>``    ``/// ** 描述：session操作类``    ``/// ** 创始时间：2015-6-9``    ``/// ** 修改时间：-``    ``/// ** 作者：sunkaixuan``    ``/// </summary>``    ``/// <typeparam name="K">键</typeparam>``    ``/// <typeparam name="V">值</typeparam>``    ``public` `class` `SessionManager<V> ``    ``{``        ``private` `static` `readonly` `object` `_instancelock = ``new` `object``();``        ``private` `static` `SessionManager<V> _instance = ``null``;` `        ``public` `static` `SessionManager<V> GetInstance()``        ``{``            ``if` `(_instance == ``null``)``            ``{``                ``lock` `(_instancelock)``                ``{``                    ``if` `(_instance == ``null``)``                    ``{``                        ``_instance = ``new` `SessionManager<V>();``                    ``}``                ``}` `            ``}``            ``return` `_instance;``        ``}` `        ``public` `override` `void` `Add(``string` `key, V value)``        ``{``            ``context.Session.Add(key, value);``        ``}` `        ``public` `override` `bool` `ContainsKey(``string` `key)``        ``{``            ``return` `context.Session[key] != ``null``;``        ``}` `        ``public` `override` `V Get(``string` `key)``        ``{``            ``return` `(V)context.Session[key];``        ``}` `        ``public` `override` `IEnumerable<``string``> GetAllKey()``        ``{``            ``foreach` `(``var` `key ``in` `context.Session.Keys)``            ``{``                ``yield ``return` `key.ToString();``            ``}``        ``}` `        ``public` `override` `void` `Remove(``string` `key)``        ``{``            ``context.Session[key] = ``null``;``            ``context.Session.Remove(key);``        ``}` `        ``public` `override` `void` `RemoveAll()``        ``{``            ``foreach` `(``var` `key ``in` `GetAllKey())``            ``{``                ``Remove(key);``            ``}``        ``}` `        ``public` `override` `void` `RemoveAll(Func<``string``, ``bool``> removeExpression)``        ``{``            ``var` `allKeyList = GetAllKey().ToList();``            ``var` `removeKeyList = allKeyList.Where(removeExpression).ToList();``            ``foreach` `(``var` `key ``in` `removeKeyList)``            ``{``                ``Remove(key);``            ``}``        ``}` `        ``public` `override` `V ``this``[``string` `key]``        ``{``            ``get` `{ ``return` `(V)context.Session[key]; }``        ``}` `        ``public` `override` `void` `Add(``string` `key, V value, ``int` `cacheDurationInSeconds)``        ``{``            ``throw` `new` `NotImplementedException(``"session无法设置过期时间,请到webconfig更改设置"``);``        ``}``    ``}`
```


  