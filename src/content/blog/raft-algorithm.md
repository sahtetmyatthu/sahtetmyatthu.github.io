---
title: 'Raft Algorithm အကြောင်း'
description: 'Raft Algorithm အကြောင်း'
pubDate: 2026-03-14
tags: ['docker', 'devops', 'linux']
---

# 🚢 Raft Algorithm

ကျွန်တော်တို့ နေ့စဉ်သုံးတဲ့ app တွေ၊ database တွေမှာ server တစ်လုံးတည်း မဟုတ်ဘဲ server **အများကြီး** တပြိုင်နက် အလုပ်လုပ်နေကြတယ်။

ဒါပေမဲ့ server တွေ အများကြီးရှိရင် **ပြဿနာတစ်ခု** ပေါ်လာတယ် —

> ❓ Server တွေအားလုံး **တူညီတဲ့ data** ကို သဘောတူဖို့ ဘယ်လို လုပ်မလဲ?
> 

ဒီပြဿနာကို **Consensus** လို့ ခေါ်တယ်။ **Raft** က ဒီပြဿနာကို ဖြေရှင်းဖို့ Stanford University မှာ 2013 ခုနှစ်တွင် တည်ဆောက်ခဲ့တဲ့ algorithm တစ်ခုဖြစ်တယ်။

Raft ရဲ့ ထူးခြားချက်ကတော့ — **နားလည်ဖို့ လွယ်တယ်**။ 😊

---

## 🏛️ Server တွေမှာ Role ၃ မျိုး ရှိတယ်

**Leader** 👑

- အဓိက ကြီးကြပ်သူ
- Client တွေဆီကနေ request တွေ လက်ခံပြီး ကျန်တဲ့ server တွေကို ညွှန်ကြားတယ်

**Follower** 👤

- Leader ရဲ့ ညွှန်ကြားချက်ကို နာခံတဲ့သူ
- Leader ဆီကနေ heartbeat ("ငါ ရှိသေးတယ်" signal) မကြာခဏ လက်ခံနေရတယ်

**Candidate** 🙋

- Leader မရှိတော့ရင် "ငါ Leader ဖြစ်ချင်တယ်" ဆိုပြီး ကိုယ်စားလှယ်လောင်း ဖြစ်လာတဲ့သူ

---

## 🗳️ Leader ဘယ်လို ရွေးကောက်တင်မြှောက်တာလဲ?

ဒါကို **Leader Election** လို့ ခေါ်တယ်။ ရွေးကောက်ပွဲနဲ့ တူတယ်!

1. Follower တစ်ယောက်က Leader ဆီကနေ heartbeat ကြာကြာ မလာဘဲ ရပ်သွားရင် 👉 **"Leader ပျောက်သွားပြီ"** လို့ ယူဆတယ်
2. ဒီ Follower က **Candidate** ဖြစ်ပြီး ကျန်တဲ့ server တွေကို **မဲတောင်းတယ်**
3. Server တွေရဲ့ **တဝက်ကျော် (majority)** မဲရရင် 👉 **Leader** ဖြစ်တယ်
4. Leader သစ်က heartbeat တွေ ပြန်ပို့ပြီး လုပ်ငန်းဆောင်ရွက်တယ်

> 💡 Server 5 လုံးရှိရင် မဲ 3 ခု (တဝက်ကျော်) ရဖို့ လိုတယ်
> 

---

## 📋 Data ဘယ်လို ကူးယူတာလဲ?

ဒါကို **Log Replication** လို့ ခေါ်တယ်။

**ဥပမာ** — bank transaction မှာ "Alice ဆီကနေ ငွေ ၁၀၀ ထုတ်ပါ" ဆိုတဲ့ command တစ်ခုပါဆိုပါဆို:

1. **Client** က command ကို Leader ဆီ ပို့တယ်
2. **Leader** က ဒီ command ကို မိမိ log ထဲ မှတ်ပြီး Follower တွေဆီ ကူးပေးတယ်
3. **Follower တွေရဲ့ တဝက်ကျော်** က "ဟုတ်ကဲ့ လက်ခံပြီ" ဆိုမှသာ **confirmed** ဖြစ်တယ်
4. Server **အားလုံး** မှာ ဒီ command ကို apply လုပ်တယ်

> ✅ တဝက်ကျော် သဘောတူမှ confirmed ဖြစ်တဲ့အတွက် — server တချို့ ပျက်ကွက်ရင်တောင် လုပ်ငန်းက ဆက်မြောင်တယ်
> 

---

## 🛡️ Server ပျက်ရင် ဘာဖြစ်သလဲ?

| အခြေအနေ | Raft ဘာလုပ်သလဲ |
| --- | --- |
| Follower ပျက်ရင် | Leader က ကျန်တဲ့ Follower တွေနဲ့ ဆက်လုပ်တယ် |
| Leader ပျက်ရင် | Follower တွေ Election ပြုလုပ်ပြီး Leader သစ် ရွေးတယ် |
| Server 2/5 ပျက်ရင် | ကျန် 3 ဦးနဲ့ (majority) ဆက်လည်ပတ်နိုင်တယ် |
| Server 3/5 ပျက်ရင် | မဲပေမဲ့ majority မရ → ရပ်နားသည် |

---

## 🌍 Real-world မှာ ဘယ်နေရာတွေ သုံးသလဲ?

- **Kubernetes (etcd)** — Cloud system configuration
- **Apache Kafka** — Messaging platform
- **MongoDB** — Database replication
- **RabbitMQ** — Message queues
- **TiKV, CockroachDB** — Distributed databases

---

## 🥊 Raft vs Paxos

Raft မတိုင်ခင်က **Paxos** ဆိုတဲ့ algorithm တခု ရှိပြီးသားဖြစ်တယ်။ ဒါပေမဲ့ Paxos က နားလည်ဖို့ ကြာကြာ ခက်ခဲတယ်ဆိုပြီး Raft ကို ဖန်တီးခဲ့တာ။

|  | Raft | Paxos |
| --- | --- | --- |
| နားလည်ဖို့ | ✅ လွယ်တယ် | ❌ ခက်တယ် |
| Implement လုပ်ဖို့ | ✅ ရှင်းတယ် | ❌ ရှုပ်တယ် |
| Performance | ✅ ဒါနဲ့ ညီမျှတယ် | ✅ ကောင်းတယ် |

---

## 📝 အကျဉ်းချုပ်

Raft = **ရိုးရှင်းတဲ့ ခေါင်းဆောင်ရွေးပြီး data ကူးပြီး server တွေ တသမတ်ထဲ ထိန်းတဲ့ algorithm**

- 👑 **Leader** တစ်ယောက်က အားလုံးကို ကြီးကြပ်တယ်
- 🗳️ Leader ပျက်ရင် **မဲဆန္ဒ** ပြုလုပ်ပြီး Leader သစ် ရွေးတယ်
- 📋 Data တွေကို **Follower အများစုနဲ့** confirm ပြီးမှ save လုပ်တယ်
- 🛡️ Server တချို့ ပျက်ရင်တောင် **ဆက်မြောင်တယ်**

---

---

## 🌍 Real-World မှာ ဘယ်မှာ သုံးသလဲ? ဘာကြောင့် သုံးသလဲ?

### 1️⃣ Kubernetes — etcd

> ⚠️ **အထူးသတိပြုရန်** — Kubernetes ကိုယ်တိုင်က Raft ကို တိုက်ရိုက် အသုံးပြုတာ မဟုတ်ဘူး။ Kubernetes အောက်မှာ **etcd** ဆိုတဲ့ သီးခြား key-value database တစ်ခုက Raft ကို သုံးတယ်။ Kubernetes က etcd ကို API နဲ့ ခေါ်သုံးရုံပဲ။
> 

```
ဘယ်သူက ဘာသုံးသလဲ?

  [Kubernetes]          ← Raft မသုံးဘူး
       |
       |  API call
       |
       v
    [etcd]              ← ဒီမှာပဲ Raft သုံးတယ်
    /    |    \
  [S1] [S2] [S3]       ← etcd nodes တွေ Raft နဲ့ sync လုပ်တယ်

တစ်နည်းပြောရရင် —
  Kubernetes = မန်နေဂျာ (ဘာလုပ်ရမလဲ ဆုံးဖြတ်တယ်)
  etcd       = မှတ်တမ်း (ဆုံးဖြတ်ချက်တွေ သိမ်းတယ်)
  Raft       = မှတ်တမ်း မပျောက်အောင် ကာကွယ်တဲ့ နည်းလမ်း
```

**ဘာလဲ?** — Kubernetes က container တွေကို manage လုပ်တဲ့ system

**ဘာကြောင့် Raft လိုသလဲ?**

Kubernetes မှာ node အများကြီး ရှိတယ်။ "ဒီ container က ဘယ် server မှာ run နေသလဲ" ဆိုတဲ့ configuration data ကို server အားလုံး **တစ်ထပ်တည်း** သိနေဖို့ လိုတယ်။ etcd က ဒီ data ကို Raft နဲ့ sync ထားပေးတာ။

```
[App တင်ချင်တယ်]
      |
      v
[etcd Leader] --> [etcd Follower 1]
              --> [etcd Follower 2]
                  (Raft နဲ့ sync)
      |
      v
Kubernetes: "OK, Server 3 မှာ run မယ်" ✅
(Server အားလုံး တူညီတဲ့ answer)
```

---

### 2️⃣ CockroachDB / TiDB — Distributed Database

**ဘာလဲ?** — Server အများကြီးပေါ်မှာ ပြန့်နေတဲ့ database

**ဘာကြောင့် Raft လိုသလဲ?**

ဘဏ် transaction တွေလို data မှားလို့မရတဲ့ ကိစ္စတွေမှာ server တစ်ခု ပြုတ်သွားရင်တောင် data မပျောက်ဖို့ Raft နဲ့ replicate ထားတယ်။

```
Alice က Bob ကို $100 လွှဲတယ်
        |
        v
[Leader DB] ---- ✅ Follower Bangkok
            \--- ✅ Follower Singapore
             \-- ✅ Follower Tokyo

3/3 confirm → "လွှဲပြီး" ✅
Server 1 ပြုတ်ရင်? → ကျန် 2 ခု quorum ရ → ဆက်လုပ်နိုင် ✅
```

---

### 3️⃣ Apache Kafka (KRaft mode)

**ဘာလဲ?** — Message များကို handle တဲ့ system (Line, Grab, Shopee တို့ သုံးတယ်)

**ဘာကြောင့် Raft လိုသလဲ?**

Kafka မှာ "ဘယ် topic က ဘယ် server မှာ ရှိသလဲ" ဆိုတဲ့ metadata ကို အားလုံး သိဖို့ Raft သုံးတယ်။ ZooKeeper ကို replace လုပ်ဖို့ KRaft ထွက်လာတာ။

```
မပြောင်းခင် (ZooKeeper ရှိတုန်း):
Kafka → ZooKeeper (သပ်သပ် system) → ရှုပ်တယ် 😩

ပြောင်းပြီး (KRaft):
Kafka → Raft built-in → ရိုးရှင်းတယ် 😊
```

---

### 4️⃣ MongoDB — Replica Set

**ဘာလဲ?** — အများဆုံးသုံးတဲ့ NoSQL database

**ဘာကြောင့် Raft လိုသလဲ?**

MongoDB က Primary (Leader) တစ်ခု၊ Secondary (Follower) တွေနဲ့ data ကို replicate ထားတယ်။ Primary ပြုတ်ရင် Raft-based election နဲ့ Secondary တစ်ခုက Primary ဖြစ်တယ်။ App က downtime မခံစားရဘူး။

```
ပုံမှန်:
[Primary 🔵] → [Secondary] [Secondary]

Primary ပြုတ်ရင်:
[Primary 💀]   [Secondary] [Secondary]
                    |
                election!
                    |
                    v
             [New Primary 🔵] ✅  (~10 seconds)
```

---

### 5️⃣ Consul — Service Discovery

**ဘာလဲ?** — Microservices တွေ တစ်ခုနဲ့တစ်ခု ဘယ်မှာ ရှိသလဲ သိဖို့ tool

**ဘာကြောင့် Raft လိုသလဲ?**

"Payment service က ဘယ် IP မှာ run နေသလဲ?" ဆိုတဲ့ info ကို server အားလုံး တူညီစွာ သိနေဖို့ Raft သုံးတယ်။ info မမှန်ရင် service တွေ ချိတ်ဆက်လို့မရဘူး။

```
Order Service: "Payment ဘယ်မှာလဲ?"
     |
     v
Consul (Raft cluster) → "192.168.1.5 မှာ" ✅
(Server 3 ခုလုံး တူညီတဲ့ answer ပေးတယ်)
```

---

### 📊 Summary Table

| Product | သုံးတဲ့နေရာ | Raft မပါရင် ဘာဖြစ်မလဲ |
| --- | --- | --- |
| **Kubernetes etcd** | Config sync | Container တွေ ရှုပ်သွားမယ် |
| **CockroachDB** | Transaction | Data ပျောက်နိုင်တယ် |
| **Kafka KRaft** | Metadata | Message တွေ ပျောက်နိုင်တယ် |
| **MongoDB** | Replication | DB down ဖြစ်သွားနိုင်တယ် |
| **Consul** | Service info | Service တွေ ချိတ်မိတော့မှာ မဟုတ်ဘူး |

> 💡 **အနှစ်ချုပ်** — Data မှားလို့မရ + Server ပြုတ်ရင်တောင် ဆက်လုပ်ရမယ် ဆိုတဲ့ system မှာ Raft သုံးတယ်!
> 

---

*Source: Understanding the Raft Algorithm — A Beginner-Friendly Guide (Medium, Jan 2025)*

---

## 🔢 Quorum ဆိုတာ ဘာလဲ?

Raft မှာ **"အများစု သဘောတူမှသာ လုပ်မယ်"** ဆိုတဲ့ စည်းကမ်းရှိတယ် — ဒါကို **Quorum** လို့ခေါ်တယ်။

Formula အရ: **Quorum = (server အရေအတွက် / 2) + 1**

| Server အရေအတွက် | Quorum (အနည်းဆုံးလိုတဲ့ server) | ပျက်နိုင်သော server |
| --- | --- | --- |
| :-: | :-: | :-: |
| 3 | 2 | 1 |
| 5 | 3 | 2 |
| 7 | 4 | 3 |

> 💡 Server 5 လုံးထဲမှ 2 လုံးပျက်နေရင်တောင် ကျန် 3 လုံးနဲ့ Quorum ပြည့်တဲ့အတွက် system က ဆက်အလုပ်လုပ်နိုင်တယ်!
> 

### Quorum diagram — မျက်မြင်ကြည့်ကြစို့

```
✅ ပုံမှန် အခြေအနေ (Server 5 လုံးလုံး ကောင်းနေ)

   🟢 S1   🟢 S2   👑 Leader   🟢 S4   🟢 S5
    |       |         |           |       |
    └───────┴─────────┴───────────┴───────┘
              အားလုံး 5/5 → Quorum ✅

──────────────────────────────────────────

⚠️  Server 2 လုံး ပျက်သွားပြီ

   🟢 S1   💀 S2   👑 Leader   🟢 S4   💀 S5
    |               |           |
    └───────────────┴───────────┘
         ကျန် 3/5 → Quorum ✅ (ဆက်အလုပ်လုပ်နိုင်)

──────────────────────────────────────────

❌ Server 3 လုံး ပျက်သွားပြီ (Quorum မပြည့်)

   🟢 S1   💀 S2   💀 Leader   🟢 S4   💀 S5
    |                           |
    └───────────────────────────┘
         ကျန် 2/5 → Quorum မပြည့် ❌
         (system ရပ်တန့် — မှားတဲ့ data မသိမ်း)
```

> ⚠️ **အဓိကအချက်** — Quorum မပြည့်ရင် Raft က data ကို **save လုပ်ဖို့ငြင်းဆန်တယ်**
> 

---

## 🧮 Quorum နဲ့ Fault Tolerance — ဘာကြောင့် ဒီ formula လဲ?

### Formula နှစ်ခု

|  | Formula | N=3 | N=5 | N=7 |
| --- | --- | --- | --- | --- |
| **Quorum** | ⌊N/2⌋ + 1 | 2 | 3 | 4 |
| **Fault Tolerance** | ⌊N/2⌋ | 1 | 2 | 3 |

Fault Tolerance = N − Quorum ဖြစ်မအတာ ⌊N/2⌋ ပဲ

### ဘာကြောင့် Quorum = ⌊N/2⌋ + 1 လဲ?

Raft မှာ Leader နှစ်ယောက် တပြိုင်တည်း မဖြစ်နိုင်ရှိတယ်။ တစ်ဝက်ကျော် (N/2 + 1) မရဘဲ Leader မဖြစ်တည်း ယူဆက်ဆတ်း Leader 2 ယောက် တပြိုင်တည်း **မသပြသမြမဖြစ်နိုင်ဘူး**။

```
N=5 ဆိုပါစို့ group 2 ခုကွခ်ကွခ်ချယ်ကြည့်:

  Group A │ Group B
  ────────┼────────
    S1    │   S3
    S2    │   S4
          │   S5
  2 votes │ 3 votes

  Group A = 2/5 → Quorum (3) မပြည့် ❌
  Group B = 3/5 → Quorum (3) ပြည့်  ✅

  ∴ Leader တစ်ယောက်ပဲ ဖြစ်နိုင်! (conflict မရှိ)
```

### ဘာကြောင့် Fault Tolerance = ⌊N/2⌋ လဲ?

```
N=5:
Quorum    = 3  (ဆက်လည်ပတ်ဖို့)
Fault Tol = 5 - 3 = 2

✅ 2 လုံးပျက် → ကျန် 3 လုံး → Quorum OK
❌ 3 လုံးပျက် → ကျန် 2 လုံး → Quorum မပြည့် → ရပ်!

အရအများစု:
✅ ✅ ❌ ❌ ❌  ← ပျက် 3 လုံး → ကျန် 2/5 → STOP
✅ ✅ ✅ ❌ ❌  ← ပျက် 2 လုံး → ကျန် 3/5 → OK!
```

**အနှစ်ချုပ်** — Quorum က "conflict မဖြစ်ခြင်းကို တားဆီးဖို့"၊ Fault Tolerance က "Quorum ပြည့်ဖို့ ဘယ်နှစ်လုံးအထိ ပျက်လို့ရမလဲ" ဆိုတဲ့ ရလဒ်ပဲ!။ မှားတဲ့ data သိမ်းသွားတာထက် ရပ်နေတာက ပိုကောင်းတယ်!

---

## 🗺️ တစ်ချက်ကြည့်တာနဲ့ နားလည်အောင် — Diagram

### Server တွေ ဘယ်လို Role ရပ်တည်သလဲ

```
┌──────────────────────────────────────────────┐
│           Raft Cluster (Server 5 လုံး)        │
│                                              │
│   ┌────────┐   ┌────────┐   ┌────────┐      │
│   │Follower│   │ LEADER │   │Follower│      │
│   │  😴    │◄──│  👑    │──►│  😴    │      │
│   └────────┘   └────────┘   └────────┘      │
│                    │                         │
│            ┌───────┴───────┐                 │
│        ┌───┴───┐       ┌───┴───┐             │
│        │Follow.│       │Follow.│             │
│        │  😴   │       │  😴   │             │
│        └───────┘       └───────┘             │
│                                              │
│  Leader က အလယ်မှာရပ်ပြီး အားလုံးကို ညွှန်ကြားတယ် │
└──────────────────────────────────────────────┘
```

### Leader Election — မဲပေးပြီး Leader ရွေးတဲ့ပုံ

```
[အဆင့် 1] Leader မကြားရတော့ → Follower တစ်ယောက် Candidate ဖြစ်

     😴 Follower          😴 Follower
         │                    │
         │   ← heartbeat ပျောက် →  │
         │                    │
         ▼                    │
     🙋 Candidate ←───────────┘
         │
         │  "ငါ Leader ဖြစ်ချင်တယ်၊ မဲပေးပါ!"
         │
    ┌────┴────┐
    ▼         ▼
 😴 မဲပေး  😴 မဲပေး
    │         │
    └────┬────┘
         ▼
   မဲ 3/5 ရ → 👑 LEADER !
```

### Log Replication — Data ကူးယူတဲ့ပုံ

```
Client                 Leader              Followers
  │                      │                │   │   │
  │──"X=5 သိမ်းပေး"──►  │                │   │   │
  │                      │                │   │   │
  │              log မှာ ထည့်             │   │   │
  │                      │──── ကူးပေး ───►│   │   │
  │                      │──── ကူးပေး ────────►│   │
  │                      │──── ကူးပေး ────────────►│
  │                      │                │   │   │
  │             3/5 ✅ confirm ရ          │   │   │
  │                      │                │   │   │
  │◄── "သိမ်းပြီး!" ──────│                │   │   │
  │                      │                │   │   │
```

### Server ပျက်ရင် ဘာဖြစ်သလဲ

```
😴😴 👑 😴😴   ← ပုံမှန်အချိန်

😴😴 💀 😴😴   ← Leader ပျက်!

😴😴 🙋 😴😴   ← Candidate တက်

😴😴 👑 😴😴   ← မဲ 3/5 → Leader သစ်!
            (ပျမ်းမျှ 150ms အတွင်း)
```

> 💡 **အဓိကအချက်** — Server တဝက်ကျော် (majority) ရှင်သန်နေသရွေ့ Raft က ဆက်အလုပ်လုပ်နိုင်တယ်!
>