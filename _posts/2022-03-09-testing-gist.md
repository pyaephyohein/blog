---
layout: post
title: "testing gist"
comments: true
description: "testing gist"
keywords: "tech"
---

<pre id="show-json-from-git"></pre>

<script>
var url = 'https://raw.githubusercontent.com/cloudrouter/centos-repo/master/CentOS-Base.repo';
fetch(url)
.then(res => res.text())
.then((out) => {
  document.getElementById("show-json-from-git").innerText = out
})
.catch(err => { throw err });
</script>