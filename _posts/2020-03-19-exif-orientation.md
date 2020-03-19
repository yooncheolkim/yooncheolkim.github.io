---
layout: post
title: ""
date: 2020-03-19 22:30:00
tags: exif orientation js
description: exif
---

## 자바스크립트, img 태그에서 blob 가져와서 exif 데이터로 사진 똑바로 나오게 하기,

### exif, orientation
- 이미지 파일에서, exif 값으로 orientation 값을 가져올수 있다.
- 보통 스마트폰으로 찍은 사진은 스마트폰 회전 정도에 따라 이 orientation 값이 달라진다.
- https://github.com/blueimp/JavaScript-Load-Image

>javascript
{% highlight javascript %}
<script scr = "https://cdnjs.cloudflare.com/ajax/libs/blueimp-load-image/2.24.0/load-image.all.min.js"></script>

    var srcLeft = document.getElementById("left_img").src;
    fetch(srcLeft)
        .then(res=>res.blob())
        .then(blob=>{
            loadImage(
                blob,
                function(img,data){
                    $("#left_img").attr("src",img.toDataURL("image/jpeg"));
                },
                {orientation:true}
            )
        });
{% endhighlight %}