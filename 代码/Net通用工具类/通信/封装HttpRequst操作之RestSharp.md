# 封装HttpRequst操作之RestSharp

**RestSharp是一个轻量级的HTTP操作库，支持Rest规范，集成身份证验，多样化传参 ，实体映射等一系列实用的功能。**



**GITHUB下载地址**

<https://github.com/restsharp/RestSharp>



**基本用法**

```
`var` `client = ``new` `RestClient(``"http://example.com"``); ``IRestResponse response = client.Execute(request);``var` `content = response.Content;`
```



**添加参数**

```
`request.AddParameter(``"name"``, ``"value"``); ``// adds to POST or URL querystring based on Method``request.AddUrlSegment(``"id"``, ``"123"``); ``// replaces matching token in request.Resource``// add parameters for all properties on an object``request.AddObject(``object``);``// or just whitelisted properties``request.AddObject(``object``, ``"PersonId"``, ``"Name"``, ...);``// easily add HTTP Headers``request.AddHeader(``"header"``, ``"value"``);``// add files to upload (works with compatible verbs)``request.AddFile(``"file"``, path);`
```



**添加Cookie**

```
`  ``request.AddCookie(key,value);`
```



**异步操作**

```
`client.ExecuteAsync(request, response => {``    ``Console.WriteLine(response.Content);``});`
```



**我的封装**

```
`using` `System;``using` `System.Collections.Generic;``using` `System.Linq;``using` `System.Text;``using` `System.Threading.Tasks;``using` `RestSharp;``using` `SyntacticSugar;``using` `Infrastructure.DbModel;``using` `Infrastructure.Pub;` `namespace` `Infrastructure.Tool``{``    ``public` `class` `RestApi<T> ``where` `T:``class``,``new``()``    ``{``        ``public` `T Get(``string` `url, ``object` `pars)``        ``{``            ``var` `type = Method.GET;``            ``IRestResponse<T> reval = GetApiInfo(url, pars, type);``            ``return` `reval.Data;``        ``}``        ``public` `T Post(``string` `url, ``object` `pars)``        ``{``            ``var` `type = Method.POST;``            ``IRestResponse<T> reval = GetApiInfo(url, pars, type);``            ``return` `reval.Data;``        ``}``        ``public` `T Delete(``string` `url, ``object` `pars)``        ``{``            ``var` `type = Method.DELETE;``            ``IRestResponse<T> reval = GetApiInfo(url, pars, type);``            ``return` `reval.Data;``        ``}``        ``public` `T Put(``string` `url, ``object` `pars)``        ``{``            ``var` `type = Method.PUT;``            ``IRestResponse<T> reval = GetApiInfo(url, pars, type);``            ``return` `reval.Data;``        ``}` `        ``private` `static` `IRestResponse<T> GetApiInfo(``string` `url, ``object` `pars, Method type)``        ``{``            ``var` `request = ``new` `RestRequest(type);``            ``if` `(pars != ``null``)``                ``request.AddObject(pars);``            ``var` `client = ``new` `RestClient(RequestInfo.HttpDomain + url);``            ``client.CookieContainer = ``new` `System.Net.CookieContainer();``            ``IRestResponse<T> reval = client.Execute<T>(request);``            ``if` `(reval.ErrorException != ``null``)``            ``{``                ``PubMethod.WirteExp(``new` `Exception(reval.Content));``                ``throw` `new` `Exception(``"请求出错"``);``            ``}``            ``return` `reval;``        ``}``    ``}``}`
```



**用法**

```
`List<T> list=``new` `RestApi<List<T>>().Get(``"http:/home/GetJson"``,``new``{id=1} )`
```