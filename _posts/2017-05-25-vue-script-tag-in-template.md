---
id: 548
title: 'Vue template中包含script标签的写法'
date: '2017-05-25T00:27:43+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=548'
permalink: /2017/05/vue-script-tag-in-template
categories:
    - Javascript
---

最近项目中有一段谷歌广告代码 必须放在template中，但是vue会提示报错：

- Templates should only be responsible for mapping the state to the UI. Avoid placing tags with side-effects in your templates, such as such as &lt;script&gt;, as they will not be parsed.

所以尝试使用 createElement 的写法，果然解决了问题。。。代码如下

```
<pre class="EnlighterJSRAW" data-enlighter-group="" data-enlighter-highlight="" data-enlighter-language="html" data-enlighter-linenumbers="" data-enlighter-lineoffset="" data-enlighter-theme="" data-enlighter-title=""><script>
  export default {
    name: 'gb-ad',
    props: {
      unit: {
        type: String,
        required: true
      }
    },
    data() {
      return {
        // active is for fixing the bug that AD not showing when back to keep-alive page
        active: true
      }
    },
    render(createElement) {
      const self = this
      if (!this.active) {
        return
      }

      return createElement('div',
        {
          attrs: {
            id: self.unit
          }
        },
        [
          createElement('script', 'googletag.cmd.push(function() { googletag.display("' + self.unit + '"); });')
        ])
    },
    activated() {
      this.active = true
    },
    deactivated() {
      this.active = false
    }
  }
</script>
```