---
title: 'Docker exec vs attach နှိုင်းယှဉ်ချက်'
description: 'Container ထဲ ဝင်ရောက်နည်း ၂ မျိုးကို လက်တွေ့ လေ့လာပါ'
pubDate: 2026-03-14
tags: ['docker', 'devops', 'linux']
---

# 🐳 Docker exec vs attach — လက်တွေ့ သင်ခန်းစာ

# 🎯 ဒီ guide ကဘာအတွက်လဲ?

Docker container တစ်ခုထဲ **ဝင်ရောက်နည်း ၂ မျိုး** ကို လက်တွေ့ command တွေနဲ့အတူ နားလည်အောင် ရှင်းပြပေးမှာပါ။

> 💡 **သုံးနည်း** — ညာဘက် အပေါ်ထောင့်မှ `...` ကိုနှိပ်ပြီး command တွေကို copy ကူးကာ terminal မှာ run ကြည့်ပါ
> 

---

# 📦 အဆင့် ၁ — Test Container တည်ဆောက်ပါ

အောက်ပါ command ကို **အရင်ဆုံး** run ပါ။ ဒါမှ နောက် command တွေ အကုန် အလုပ်လုပ်မှာ။

```bash
docker run -dit --name mybox ubuntu /bin/bash
```

> ဘာဖြစ်သွားတာလဲ?
> 

> - `ubuntu` image ကနေ container တစ်ခု ဖွင့်လိုက်တယ်
> 

> - `--name mybox` — နာမည်ပေးလိုက်တယ်
> 

> - `-dit` — background မှာ run နေမယ်
> 

```bash
# Run နေသလား စစ်ကြည့်ပါ
docker ps
```

> ✅ `mybox` ဆိုတဲ့ container STATUS က `Up` ဖြစ်နေရင် အဆင်သင့်ဖြစ်ပြီ!
> 

---

# 🔵 `exec` — Container ထဲ ဝင်ရောက်နည်း (ပထမမျိုး)

## exec ဆိုတာ ဘာလဲ?

Container ထဲမှာ **process အသစ်တစ်ခု** ဖွင့်ပြီး ဝင်တာပါ။ မူလ container ကို မထိဘဲ သုံးနိုင်တယ်။

## လက်တွေ့ စမ်းကြည့်ပါ

**ဆင့် ၁ — Container ထဲ ဝင်ပါ**

```bash
docker exec -it mybox /bin/bash
```

**ဆင့် ၂ — Container ထဲမှာ command တွေ run ကြည့်ပါ**

```bash
echo "ကျွန်တော် container ထဲမှာ ရှိနေတယ်!"
```

```bash
ls /
```

```bash
cat /etc/os-release
```

**ဆင့် ၃ — ထွက်ပါ**

```bash
exit
```

**ဆင့် ၄ — Container ရပ်သွားလားစစ်ပါ**

```bash
docker ps
```

> ✅ `mybox` ကဆက် run နေနိုင်ပါတယ်! `exit` လုပ်လိုက်ရင်တောင် container မရပ်ဘူး — ဒါကျ `exec` ရဲ့ အကောင်းဆုံးအချက်!
> 

---

# 🟡 `attach` — Container ထဲ ဝင်ရောက်နည်း (ဒုတိယမျိုး)

## attach ဆိုတာ ဘာလဲ?

Container ရဲ့ **မူလ main process** ကိုတိုက်ရိုက် ချိတ်ဆက်တာ။ ဒီ process ကို ပိတ်လိုက်ရင် container တစ်ခုလုံး ရပ်သွားနိုင်တယ်!

## လက်တွေ့ စမ်းကြည့်ပါ

**ဆင့် ၁ — Container ကို attach ဖြင့် ဝင်ပါ**

```bash
docker attach mybox
```

**ဆင့် ၂ — Command တွေ run ကြည့်ပါ**

```bash
echo "attach နဲ့ ဝင်နေတယ်"
```

```bash
pwd
```

**ဆင့် ၃ — ⚠️ ဤနည်းနဲ့ ထွက်ပါ (သတိထားပါ!)**

```
Ctrl+P ကိုနှိပ်ပါ
ပြီးရင် Ctrl+Q ကိုနှိပ်ပါ
```

> ⚠️ `exit` သို့မဟုတ် `Ctrl+C` နှိပ်မိရင် container တစ်ခုလုံး ရပ်သွားမည်!
> 

**ဆင့် ၄ — Container ရပ်မသွားကြောင်း စစ်ပါ**

```bash
docker ps
```

---

# ⚖️ exec နဲ့ attach — ဘာကွာသလဲ?

| အချက် | exec | attach |
| --- | --- | --- |
| ဘာကိုဝင်တာ | Process အသစ် | Main process (PID 1) |
| `exit` နှိပ်ရင် | Shell ပဲပိတ်, container မရပ် ✅ | Container ရပ်နိုင် ⚠️ |
| ဘေးကင်းသလား | ✅ အမြဲဘေးကင်း | ⚠️ သတိထားမှ |
| Session များများဖွင့်လို့ | ✅ ရတယ် | ❌ မရဘူး |

---

# 🏆 ဘယ်အချိန် ဘာသုံးမလဲ?

## exec သုံးသင့်တဲ့ အချိန်

**ကိစ္စ ၁ — Log ကြည့်ချင်တဲ့အခါ**

```bash
docker exec -it mybox /bin/bash
# ဝင်ပြီးရင်
ls /var/log/
```

**ကိစ္စ ၂ — Shell မဝင်ဘဲ တိုက်ရိုက် command run ချင်တဲ့အခါ**

```bash
# ဥပမာ — file ရှိသလား စစ်တာ
docker exec mybox ls /app

# ဥပမာ — config file ကြည့်တာ
docker exec mybox cat /etc/hosts
```

**ကိစ္စ ၃ — Database container မှာ query လုပ်ချင်တဲ့အခါ**

```bash
# MySQL container ဆိုရင်
docker exec -it <container_id> mysql -u root -p
```

## attach သုံးသင့်တဲ့ အချိန်

**ကိစ္စ — Live output ကြည့်ချင်တဲ့အခါ**

```bash
# Background မှာ run နေတဲ့ process ရဲ့ output ကြည့်တာ
docker attach mybox
# ကြည့်ပြီးရင် Ctrl+P → Ctrl+Q နဲ့ ထွက်ပါ
```

---

# 🧹 အဆင့် နောက်ဆုံး — Cleanup

စမ်းပြီးရင် container ကို ဖျက်ပါ

```bash
docker stop mybox
```

```bash
docker rm mybox
```

```bash
# ကွင်းသွားကြောင်း စစ်ပါ
docker ps -a
```

---

# 📌 မှတ်ထားသင့်တဲ့ အဓိက အချက်

> 🥇 **exec** — Production container ကို debug လုပ်ရာမှာ အမြဲသုံးပါ။ ဘေးကင်းတယ်။
> 

> ⚠️ **attach** — Main process ကို တိုက်ရိုက်ကြည့်ချင်မှ သုံးပါ။ `Ctrl+C` မနှိပ်မိဖို့ သတိထားပါ။
> 

> 🔑 **attach မှ ထွက်နည်း** — `Ctrl+P` ပြီး `Ctrl+Q` (container မရပ်ဘဲ ထွက်လို့ရ)
>