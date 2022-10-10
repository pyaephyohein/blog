---
layout: post
title: "How to use git multi account in one machine."
comments: true
description: "How to use git multi account in one machine."
keywords: "git"
---



###Git Config 


ပထမဆုံး ```~/.gitconfig``` မှာ Folder path ပြင်ရပါမယ်။ ဘယ်လို ပြင်ရမလဲ ဆိုရင် သင့်မှာ work and personal ဆိုပြီး git account နှစ်ခု ရှိတယ်ဆိုပါစို့၊ work folder က Work နဲ့ သက်ဆိုင်တဲ့ folderဖြစ်ပြီး Project folder က Personal code တွေ သိမ်းဆည်းတဲ့ folder လို့ ယူဆ လိုက်ပါမယ်။  ပထမဆုံး ```.gitconfig-work``` နဲ့ ```.gitconfig-personal``` ဆိုပြီး အဆင်ပြေတဲ့ နေရာမှာ file နှစ်ခု တည်ဆောက်လိုက်ပါမယ်။ အဲ့ဒီ file နှစ်ခု ကို  ```.gitconfig``` ကနေ link ချိတ်ပါမယ်။ 

example -

```text
[includeIf "gitdir:~/Project/"]
  path = ~/.gitconfig-personal
[includeIf "gitdir:~/work/"]
  path = ~/.gitconfig-work
```

သက်ဆိုင်ရာ file မှာ သက်ဆိုင်ရာ username နဲ့ email ထည့်ရင် အဆင်ပြေပါပြီ 

example ```.gitconfig-personal```
```text
[user]
 name = your_username
 email = your_email
```
```.gitconfig-work``` ကို လည်း အထက်ပါတိုင်း ပြင်ဆင်လိုက်ရင် သက်ဆိုင်ရာ folder မှာ ```git init``` ပြုလုပ်တိုင်း သက်ဆိုင်ရာ git config file ကို အသုံးပြုသွားမှာ ဖြစ်ပါတယ်