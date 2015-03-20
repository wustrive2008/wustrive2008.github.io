title: text
date: 2015-03-20 22:35:25
tags: [text]
---

## text文章
- 来了来了
- 书上说
- 叮叮当当

```
    // Caption
    $('.article-entry').each(function(i){
    $(this).find('img').each(function(){
    if ($(this).parent().hasClass('fancybox')) return;

    var alt = this.alt;

    // if (alt) $(this).after('<span class="caption">' + alt + '</span>'); 这个去掉

    $(this).wrap('<a href="' + this.src + '" title="' + alt + '" class="fancybox"></a>');
    });

    $(this).find('.fancybox').each(function(){
    $(this).attr('rel', 'article' + i);
    });
    });

```


    
```
    print("fdsfdsfds")
```


