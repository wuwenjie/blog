---
id: 144
title: 如何更改Json.NET的序列化规则
date: '2019-08-15T18:03:29+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/143-revision-v1/'
permalink: '/?p=144'
---

**我想要使序列化出来的JSON都是小写，可以通过建立 `LowercaseContractResolver：``DefaultContractResolver`**

**`来实现， 创建Custom <strong>ContractResolver可以对很多东西进行更改，可以自定义规则。。。 如果你只要针对某个的话，可以用 JsonProperty 或者使用CustomJsonConverter: JsonConverter。。</strong>`**

**改变规则：**

<figure class="wp-block-embed"><div class="wp-block-embed__wrapper">http://stackoverflow.com/questions/6288660/net-ensuring-json-keys-are-lowercase </div></figure><figure class="wp-block-embed"><div class="wp-block-embed__wrapper">http://stackoverflow.com/questions/12501805/how-to-set-json-net-contractserializer-for-a-certain-specific-type-instead-of-gl </div></figure><figure class="wp-block-embed"><div class="wp-block-embed__wrapper">http://stackoverflow.com/questions/25183710/serialize-json-property-name </div></figure>**CustomJsonConverter:**

<figure class="wp-block-embed"><div class="wp-block-embed__wrapper">http://blog.maskalik.com/asp-net/json-net-implement-custom-serialization/ </div></figure>**DATE TIME: http://cgeers.com/2011/09/25/writing-a-custom-json-net-datetime-converter/**

You can create a custom contract resolver for this. The following contract resolver will convert all keys to lowercase:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">public class LowercaseContractResolver : DefaultContractResolver
{
    protected override string ResolvePropertyName(string propertyName)
    {
        return propertyName.ToLower();
    }
}
```

Usage:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var settings = new JsonSerializerSettings();
settings.ContractResolver = new LowercaseContractResolver();
var json = JsonConvert.SerializeObject(authority, Formatting.Indented, settings);
```

Wil result in:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">{"username":"Mark","apitoken":"xyzABC1234"}
```

- - - - - -

If you always want to serialize using the `LowercaseContractResolver`, consider wrapping it in a class to avoid repeating yourself:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">public class LowercaseJsonSerializer
{
    private static readonly JsonSerializerSettings Settings = new JsonSerializerSettings
    {
        ContractResolver = new LowercaseContractResolver()
    };

    public static string SerializeObject(object o)
    {
        return JsonConvert.SerializeObject(o, Formatting.Indented, Settings);
    }

    public class LowercaseContractResolver : DefaultContractResolver
    {
        protected override string ResolvePropertyName(string propertyName)
        {
            return propertyName.ToLower();
        }
    }
}
```

Which can be used like this:

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="generic" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title="">var json = LowercaseJsonSerializer.SerializeObject(new { Foo = "bar" });
// { "foo": "bar" }
```

- - - - - -

## ASP.NET MVC4 / WebAPI

If you are using ASP.NET MVC4 / WebAPI, you can use a `CamelCasePropertyNamesContractResolver`from Newtonsoft.Json library which included by default.