---
title: "ডিজাইন প্যাটার্ন"
description: "SOLID, DRY, KISS, YAGNI এবং ১১টা ডিজাইন প্যাটার্ন — Go কোড উদাহরণ সহ।"
date: 2026-07-26 08:00:00 +0600
categories: [Programming]
tags: [design-patterns, solid, oop]
---

> **Design Pattern আসলে কী?**
>
> Design pattern হলো সফটওয়্যার ডিজাইনের **বারবার আসা সমস্যার প্রমাণিত সমাধান**। কেউ এগুলো আবিষ্কার করেনি — বরং অভিজ্ঞ ডেভেলপাররা লক্ষ্য করেছেন যে একই ধরনের সমস্যায় সবাই একই ধরনের সমাধানে পৌঁছান, তারপর সেগুলোর নাম দেওয়া হয়েছে।
>
> **বাস্তব উদাহরণ — রান্নার রেসিপি 📖**
> কেউ "বিরিয়ানি" আবিষ্কার করেনি — বহু বছরের অভিজ্ঞতায় এই রান্নার পদ্ধতিটা দাঁড়িয়েছে। এখন কেউ "বিরিয়ানি" বললেই সবাই বোঝে কী বোঝানো হচ্ছে।
>
> Design pattern-ও তেমনই — একজন ডেভেলপার "এখানে Observer ব্যবহার করি" বললে অন্যজন সাথে সাথে বুঝে যান। **এটাই প্যাটার্নের সবচেয়ে বড় সুবিধা — একটা সাধারণ ভাষা।**
>
> ---
>
> ## ⚠️ শুরুতেই একটা সতর্কতা
>
> **প্যাটার্ন হলো টুল, লক্ষ্য নয়।**
>
> জুনিয়ররা প্যাটার্ন শিখে সবচেয়ে বড় যে ভুলটা করে — **সব জায়গায় প্যাটার্ন ঢোকানোর চেষ্টা**। ২০ লাইনের একটা স্ক্রিপ্টে Factory + Strategy + Observer ঢোকালে কোডটা ২০০ লাইন হবে এবং কেউ বুঝতে পারবে না।
>
> **সঠিক পদ্ধতি:** সহজ কোড দিয়ে শুরু করুন। যখন সত্যিই একটা সমস্যা দেখা দেবে (যেমন "নতুন পেমেন্ট মেথড যোগ করতে গিয়ে ১০ জায়গায় কোড বদলাতে হচ্ছে"), **তখন** উপযুক্ত প্যাটার্ন প্রয়োগ করুন।
>
> ---
>
> ## তিনটা শ্রেণী
>
> | শ্রেণী | কী নিয়ে কাজ করে | প্যাটার্নগুলো |
> |---|---|---|
> | **Creational** | **object কীভাবে তৈরি হবে** | Singleton, Factory, Abstract Factory, Builder, Prototype |
> | **Structural** | **object গুলো কীভাবে জোড়া লাগবে** | Adapter, Facade, Decorator, Composite |
> | **Behavioral** | **object গুলো কীভাবে যোগাযোগ করবে** | Strategy, Observer |

---

# ভাগ ১ — মৌলিক নীতিগুলো

## SOLID নীতিসমূহ

SOLID হলো অবজেক্ট-অরিয়েন্টেড প্রোগ্রামিংয়ে **পরিচ্ছন্ন, রক্ষণাবেক্ষণযোগ্য ও স্কেলযোগ্য** কোড লেখার কিছু নীতি:

---

### S — Single Responsibility Principle (SRP)

**নীতি:** একটা ক্লাসের **পরিবর্তনের কারণ একটাই** থাকা উচিত — অর্থাৎ তার দায়িত্ব একটাই।

**বাস্তব উদাহরণ — রেস্টুরেন্টের কর্মী 🍽️**
```
❌ খারাপ: একজন লোক রান্না করেন, পরিবেশন করেন, হিসাব রাখেন,
          দোকান পরিষ্কার করেন এবং বিপণনও করেন

   → রান্নার নিয়ম বদলালে তার কাজ বদলাবে
   → হিসাবের নিয়ম বদলালেও তার কাজ বদলাবে
   → পাঁচটা আলাদা কারণে একজনকেই বদলাতে হচ্ছে! 😩

✅ ভালো: শেফ রান্না করেন, ওয়েটার পরিবেশন করেন,
        হিসাবরক্ষক হিসাব রাখেন
```

**কোডে:**
```go
// ❌ খারাপ — তিনটা দায়িত্ব একসাথে
type User struct{ Name, Email string }
func (u *User) Save()          { /* ডেটাবেজে লিখছে */ }
func (u *User) SendEmail()     { /* ইমেইল পাঠাচ্ছে */ }
func (u *User) ValidateEmail() { /* যাচাই করছে */ }

// ✅ ভালো — আলাদা দায়িত্ব, আলাদা ক্লাস
type User struct{ Name, Email string }
type UserRepository struct{}    // শুধু সংরক্ষণ
type EmailService struct{}      // শুধু ইমেইল
type UserValidator struct{}     // শুধু যাচাই
```

> **কীভাবে বুঝবেন SRP ভাঙছে:** ক্লাসের বর্ণনায় যদি **"এবং"** শব্দটা ব্যবহার করতে হয় — "এই ক্লাসটা ব্যবহারকারী সংরক্ষণ করে **এবং** ইমেইল পাঠায়" — তাহলে SRP ভাঙছে। ⚠️

---

### O — Open/Closed Principle (OCP)

**নীতি:** ক্লাস, মডিউল, ফাংশন — এগুলো **সম্প্রসারণের জন্য খোলা** থাকবে, কিন্তু **পরিবর্তনের জন্য বন্ধ**।

মানে — নতুন ফিচার যোগ করতে **নতুন কোড লিখুন, পুরনো কোড বদলাবেন না**।

**বাস্তব উদাহরণ — মোবাইল ফোন 📱**
```
নতুন ফিচার চান? → নতুন অ্যাপ ইনস্টল করুন ✅
                  (ফোনের মাদারবোর্ড খুলে সোল্ডারিং করতে হয় না!)
```

**কোডে:**
```go
// ❌ খারাপ — নতুন আকৃতি যোগ করতে এই ফাংশন বদলাতে হবে
func Area(shape string, dims []float64) float64 {
    if shape == "circle" { return 3.14 * dims[0] * dims[0] }
    if shape == "square" { return dims[0] * dims[0] }
    // নতুন: ত্রিভুজ? → এখানে আরেকটা if যোগ করতে হবে 😩
}

// ✅ ভালো — নতুন আকৃতি = নতুন struct, পুরনো কোডে হাত নেই
type Shape interface{ Area() float64 }

type Circle struct{ R float64 }
func (c Circle) Area() float64 { return 3.14 * c.R * c.R }

type Square struct{ S float64 }
func (s Square) Area() float64 { return s.S * s.S }

// নতুন যোগ করলাম — কোনো পুরনো কোড ছুঁইনি ✅
type Triangle struct{ B, H float64 }
func (t Triangle) Area() float64 { return 0.5 * t.B * t.H }
```

> **কেন এটা এত গুরুত্বপূর্ণ:** পুরনো কোড বদলানো মানেই **পুরনো বাগ ফিরে আসার ঝুঁকি**। যে কোডটা ৬ মাস ধরে ঠিকঠাক চলছে, তাতে হাত না দেওয়াই ভালো!

---

### L — Liskov Substitution Principle (LSP)

**নীতি:** Subtype গুলো তাদের base type-এর জায়গায় বসতে পারবে **প্রোগ্রামের সঠিকতা নষ্ট না করেই**।

আপনার যদি একটা base class থাকে (যেমন `Animal`) আর একটা subclass (যেমন `Dog`), তাহলে যেখানেই `Animal` ব্যবহার করেন সেখানে `Dog` বসিয়ে দিলে **ঠিকঠাক কাজ করা উচিত**।

**বিখ্যাত উদাহরণ — পাখি ও পেঙ্গুইন 🐦🐧**
```go
// ❌ LSP ভঙ্গ
type Bird interface{ Fly() }

type Sparrow struct{}
func (s Sparrow) Fly() { fmt.Println("উড়ছি!") }

type Penguin struct{}
func (p Penguin) Fly() { panic("পেঙ্গুইন উড়তে পারে না!") }  // 💥

// এখন এই কোড ভেঙে যাবে:
func MakeBirdsFly(birds []Bird) {
    for _, b := range birds { b.Fly() }   // Penguin এলে panic! 😱
}
```

**✅ সঠিক ডিজাইন:**
```go
type Bird interface{ Eat() }
type FlyingBird interface {
    Bird
    Fly()
}

type Sparrow struct{}   // FlyingBird বাস্তবায়ন করে
type Penguin struct{}   // শুধু Bird বাস্তবায়ন করে ✅
```

**LSP ভাঙার লক্ষণ:**
- Subclass-এ কোনো মেথড **খালি** রেখে দেওয়া বা `panic` করা
- Subclass-এর জন্য কোথাও `if type == Penguin` লেখা লাগছে
- Subclass base-এর চেয়ে **কড়া শর্ত** দিচ্ছে

> **বাস্তব উপমা:** আপনি "একটা কলম দিন" বললেন, কেউ একটা কাঠের টুকরো দিল যেটা দেখতে কলমের মতো কিন্তু লেখে না। এটাই LSP ভঙ্গ! ❌

---

### I — Interface Segregation Principle (ISP)

**নীতি:** ক্লায়েন্টকে এমন interface-এর উপর নির্ভর করতে **বাধ্য করা উচিত নয় যেগুলো সে ব্যবহারই করে না**। একটা বড় সাধারণ interface-এর চেয়ে **কয়েকটা ছোট নির্দিষ্ট interface** ভালো।

**বাস্তব উদাহরণ — রেস্টুরেন্টের মেনু 📋**
```
❌ খারাপ: ৫০০ পাতার একটাই মেনু —
          নাস্তা, দুপুর, রাত, চাইনিজ, ইতালিয়ান সব একসাথে
          গ্রাহক সকালে এসেও ৫০০ পাতা ঘাঁটবেন 😩

✅ ভালো: আলাদা আলাদা মেনু —
        সকালের নাস্তার মেনু, দুপুরের মেনু, পানীয়ের মেনু ✅
```

**কোডে:**
```go
// ❌ খারাপ — বিশাল interface
type Worker interface {
    Work()
    Eat()
    Sleep()
    AttendMeeting()
    WriteCode()
}

// এখন একটা রোবট কর্মী বানাতে চাইলে?
type Robot struct{}
func (r Robot) Work()          { /* ঠিক আছে */ }
func (r Robot) Eat()           { /* 😐 রোবট খায় না! */ }
func (r Robot) Sleep()         { /* 😐 ঘুমায়ও না! */ }
func (r Robot) AttendMeeting() { /* 😐 */ }

// ✅ ভালো — ছোট ছোট interface
type Workable interface{ Work() }
type Eatable   interface{ Eat() }
type Sleepable interface{ Sleep() }

type Robot struct{}   // শুধু Workable বাস্তবায়ন করবে ✅
type Human struct{}   // তিনটাই বাস্তবায়ন করবে ✅
```

> **💡 Go-এর দর্শন:** Go-তে ছোট interface-ই আদর্শ। সবচেয়ে জনপ্রিয় interface-এ মাত্র **একটা** মেথড:
> ```go
> type Writer interface { Write(p []byte) (n int, err error) }
> type Reader interface { Read(p []byte) (n int, err error) }
> ```

---

### D — Dependency Inversion Principle (DIP)

**নীতি:** উচ্চস্তরের মডিউল নিম্নস্তরের মডিউলের উপর নির্ভর করবে না; **দুটোই abstraction-এর উপর নির্ভর করবে**। Abstraction বিস্তারিতের উপর নির্ভর করবে না; বিস্তারিত abstraction-এর উপর নির্ভর করবে।

**উদাহরণ:** যদি একটা ক্লাসের বার্তা পাঠানোর দরকার হয় (যেমন notification), তাহলে সে **সরাসরি কোনো নির্দিষ্ট মেসেজিং সেবার উপর নির্ভর করবে না** (যেমন email বা SMS)। বরং একটা **interface**-এর উপর নির্ভর করবে (যেমন `MessageService`)।

```go
// ❌ খারাপ — সরাসরি নির্ভরতা
type OrderService struct {
    emailSender *EmailSender     // 👈 কংক্রিট ক্লাসের উপর নির্ভরশীল
}
func (o *OrderService) PlaceOrder() {
    o.emailSender.Send("অর্ডার সফল")
    // SMS পাঠাতে চাইলে? → এই ক্লাসটাই বদলাতে হবে 😩
    // টেস্ট করতে চাইলে? → আসলেই ইমেইল চলে যাবে! 💀
}

// ✅ ভালো — abstraction-এর উপর নির্ভরতা
type MessageService interface {
    Send(msg string) error
}

type OrderService struct {
    messenger MessageService     // 👈 interface-এর উপর নির্ভরশীল ✅
}

// এখন যেকোনো কিছু দেওয়া যাবে
orderSvc := OrderService{messenger: &EmailSender{}}
orderSvc := OrderService{messenger: &SMSSender{}}
orderSvc := OrderService{messenger: &FakeSender{}}   // টেস্টের জন্য ✅
```

**বাস্তব উদাহরণ — বৈদ্যুতিক প্লাগ 🔌**
```
আপনার ল্যাম্প সরাসরি বিদ্যুৎ কেন্দ্রের তারে সোল্ডার করা নেই!
মাঝখানে একটা প্লাগ ও সকেট (interface) আছে।

→ ল্যাম্প বদলাতে চান? প্লাগ খুলে নতুনটা লাগান ✅
→ বিদ্যুৎ কেন্দ্র বদলে গেল? ল্যাম্পের কিছু হবে না ✅
```

> **"Inversion" শব্দটা কেন:** স্বাভাবিকভাবে উচ্চস্তরের মডিউল নিম্নস্তরের উপর নির্ভর করে। DIP এই নির্ভরতার **দিকটা উল্টে দেয়** — এখন দুজনেই মাঝখানের interface-এর দিকে তাকিয়ে আছে।

---

## DRY (Don't Repeat Yourself)

**নীতি:** কোডে পুনরাবৃত্তি এড়িয়ে চলুন। যদি দেখেন একই কোড বা লজিক কয়েক জায়গায় লিখছেন, তাহলে সেটা **একটা জায়গায়** (একটা ফাংশন বা ক্লাসে) সরিয়ে নিন, যাতে পুনরায় ব্যবহার করা যায়।

```go
// ❌ খারাপ — একই যাচাই তিন জায়গায়
func RegisterUser(email string) {
    if !strings.Contains(email, "@") { return }
    // ...
}
func UpdateEmail(email string) {
    if !strings.Contains(email, "@") { return }    // পুনরাবৃত্তি!
    // ...
}
func SubscribeNewsletter(email string) {
    if !strings.Contains(email, "@") { return }    // আবার!
    // ...
}

// ✅ ভালো — এক জায়গায়
func IsValidEmail(email string) bool {
    return strings.Contains(email, "@")
}
```

**কেন এটা জরুরি:** ইমেইল যাচাইয়ের নিয়ম বদলাতে হলে **তিন জায়গায় বদলাতে হবে**। একটা ভুলে গেলেই বাগ। 🐛

**⚠️ কিন্তু DRY-কে বাড়াবাড়ি করবেন না!**
```
দুইটা কোড দেখতে একরকম মানেই তারা এক জিনিস নয়।

উদাহরণ:
  ব্যবহারকারীর বয়স যাচাই: age >= 18
  ড্রাইভিং লাইসেন্সের বয়স:  age >= 18

দেখতে একই! কিন্তু আগামীকাল লাইসেন্সের বয়স ২১ হয়ে গেলে?
একটা ফাংশনে মিলিয়ে দিলে দুইটাই বদলে যাবে! 💀
```
> **নিয়ম:** DRY প্রয়োগ করুন **জ্ঞানের (knowledge) পুনরাবৃত্তিতে**, শুধু **কোডের চেহারার** মিলে নয়।
>
> একটা বিখ্যাত উক্তি: *"A little duplication is far cheaper than the wrong abstraction."* — ভুল abstraction-এর চেয়ে সামান্য পুনরাবৃত্তি অনেক সস্তা।

---

## YAGNI (You Aren't Gonna Need It)

**নীতি:** যতক্ষণ না **সত্যিই দরকার হচ্ছে** ততক্ষণ কোনো ফিচার যোগ করবেন না। এখন যা লাগবে শুধু সেটাই বানান — ভবিষ্যতে হয়তো লাগবে ভেবে নয়।

**উদাহরণ:** ধরুন আপনি একটা user registration ফিচার বানাচ্ছেন, আর ভাবলেন — "হয়তো ভবিষ্যতে ব্যবহারকারীরা রেজিস্ট্রেশনের সময় প্রোফাইল ছবি দিতে চাইবে।" তাই আপনি প্রোফাইল ছবির জন্য বাড়তি কোড লিখতে শুরু করলেন, যদিও এখন সেটার দরকারই নেই।

**কেন এটা খারাপ:**
```
১. সময় নষ্ট — যে ফিচার হয়তো কখনো লাগবেই না, তাতে ২ দিন গেল
২. জটিলতা বাড়ল — কোড পড়া ও বোঝা কঠিন হলো
৩. রক্ষণাবেক্ষণের বোঝা — অব্যবহৃত কোডেও বাগ থাকে, টেস্ট লিখতে হয়
৪. ভুল অনুমান — বাস্তবে চাহিদা ভিন্ন হয়, লেখা কোডটা ফেলে দিতে হয় 🗑️
```

**বাস্তব উপমা — বাড়ি বানানো 🏠**
```
❌ "হয়তো ভবিষ্যতে ১০ জন অতিথি আসবেন, তাই ১০টা বাড়তি ঘর বানাই"
   → এখনই ৫০ লাখ টাকা বেশি খরচ
   → প্রতি মাসে পরিষ্কার-রক্ষণাবেক্ষণের খরচ
   → শেষে দেখা গেল কেউ আসেনি! 😩

✅ "এখন যতটুকু দরকার ততটুকুই বানাই। পরে লাগলে বাড়ানো যাবে।"
```

> **⚠️ তবে ভারসাম্য রাখুন:** YAGNI মানে **খারাপ ডিজাইন** নয়। কোডটা এমনভাবে লিখুন যাতে **পরে সহজে বাড়ানো যায়** — কিন্তু এখনই বাড়িয়ে রাখবেন না।

---

## KISS (Keep It Simple, Stupid)

**নীতি:** KISS নীতি জোর দেয় যে **সরলতাই মূল কথা**। যতটা সম্ভব সহজ ও সরল কোড লিখুন, অপ্রয়োজনীয় জটিলতা এড়িয়ে চলুন। এতে কোড বোঝা, রক্ষণাবেক্ষণ করা ও ডিবাগ করা সহজ হয়।

```go
// ❌ "চতুর" কিন্তু দুর্বোধ্য
func f(x []int) int {
    return func(a []int) int {
        r := 0
        for _, v := range a { if v&1 == 0 { r += v } }
        return r
    }(x)
}

// ✅ সহজ ও স্পষ্ট
func SumEvenNumbers(numbers []int) int {
    sum := 0
    for _, n := range numbers {
        if n%2 == 0 {
            sum += n
        }
    }
    return sum
}
```

> **একটা গুরুত্বপূর্ণ সত্য:** কোড লেখা হয় একবার, কিন্তু **পড়া হয় শতবার**। তাই পড়ার সুবিধাকে অগ্রাধিকার দিন।
>
> **আরেকটা কথা মনে রাখুন:** *"ডিবাগ করা কোড লেখার চেয়ে দ্বিগুণ কঠিন। তাই আপনি যদি যত চতুরভাবে সম্ভব কোড লেখেন, তাহলে সংজ্ঞানুসারে আপনি সেটা ডিবাগ করার মতো যথেষ্ট বুদ্ধিমান নন।"* — Brian Kernighan

---

# ভাগ ২ — Creational Pattern (সৃষ্টিমূলক প্যাটার্ন)

> এই প্যাটার্নগুলো **object কীভাবে তৈরি হবে** সেটা নিয়ে কাজ করে।

## ১. Singleton Pattern 🔒

**সংজ্ঞা:** Singleton প্যাটার্ন নিশ্চিত করে যে একটা ক্লাস বা object-এর পুরো অ্যাপ্লিকেশনজুড়ে **মাত্র একটাই instance** থাকবে, এবং তাতে পৌঁছানোর একটা **গ্লোবাল পথ** দেয়।

এটা কাজে লাগে যখন কোনো শেয়ার করা রিসোর্সে (যেমন ডেটাবেজ সংযোগ) প্রবেশ নিয়ন্ত্রণ করতে চান।

```go
type Singleton struct {
    // প্রয়োজনীয় ফিল্ড
}

var instance *Singleton
var once sync.Once

// GetInstance singleton instance ফেরত দেয়
func GetInstance() *Singleton {
    once.Do(func() {
        instance = &Singleton{}
    })
    return instance
}
```

- `once.Do` নিশ্চিত করে instance **মাত্র একবারই** তৈরি হবে — এমনকি একাধিক goroutine একসাথে ডাকলেও ✅
- `GetInstance()` প্রতিবার **একই instance** ফেরত দেয়

---

**বাস্তব উদাহরণ — দেশের রাষ্ট্রপতি 🇧🇩**
```
একটা দেশে একজনই রাষ্ট্রপতি থাকতে পারেন।
যেকোনো নাগরিক "রাষ্ট্রপতি কে?" জিজ্ঞেস করলে একই উত্তর পাবেন ✅
দুইজন রাষ্ট্রপতি থাকলে বিশৃঙ্খলা হতো!
```

**আরেকটা উদাহরণ — অফিসের প্রিন্টার 🖨️**
একটা অফিসে একটাই প্রিন্টার। প্রতিটা কর্মচারী যদি নিজের জন্য আলাদা প্রিন্টার সংযোগ তৈরি করত, তাহলে ৫০টা সংযোগ হয়ে যেত। এর বদলে সবাই **একই সংযোগ** ব্যবহার করে।

**কোথায় ব্যবহার হয়:**
- ডেটাবেজ কানেকশন পুল
- লগার
- কনফিগারেশন ম্যানেজার
- ক্যাশ

**⚠️ `sync.Once` ছাড়া কী হয়:**
```go
// ❌ Race condition!
func GetInstance() *Singleton {
    if instance == nil {
        instance = &Singleton{}    // দুইটা goroutine একসাথে ঢুকে
    }                              // দুইটা instance বানিয়ে ফেলতে পারে! 💥
    return instance
}
```

**⚠️ Singleton-এর সমালোচনা (ইন্টারভিউয়ে বললে ভালো লাগবে):**
| সমস্যা | ব্যাখ্যা |
|---|---|
| **গ্লোবাল স্টেট** | যেকোনো জায়গা থেকে বদলানো যায় → বাগ খুঁজে পাওয়া কঠিন |
| **টেস্ট করা কঠিন** | mock করা যায় না, টেস্টের মধ্যে স্টেট থেকে যায় |
| **লুকানো নির্ভরতা** | ফাংশনের সিগনেচার দেখে বোঝা যায় না সে Singleton ব্যবহার করছে |
| **SRP ভঙ্গ** | ক্লাসটা নিজের কাজ করে **এবং** নিজের জীবনচক্রও ম্যানেজ করে |

> **💡 আধুনিক বিকল্প — Dependency Injection:** Singleton নিজে না বানিয়ে, একটা instance বানিয়ে যেখানে দরকার সেখানে **পাঠিয়ে দিন**। তাহলে টেস্টে সহজেই নকল instance দেওয়া যাবে ✅

---

## ২. Factory Method Pattern 🏭

**সমস্যাটা কল্পনা করুন:** আপনি একটা লজিস্টিকস অ্যাপ বানাচ্ছেন, যেটা শুরুতে শুধু **ট্রাক** দিয়ে পরিবহন করে। সময়ের সাথে সমুদ্রপথের পরিবহন কোম্পানিও যুক্ত হতে চাইছে।

কিন্তু কোডটা ট্রাকের সাথে **শক্তভাবে বাঁধা**, তাই জাহাজ যোগ করতে গেলে অ্যাপের অনেক অংশ নতুন করে লিখতে হবে। পরে আরও পরিবহন যোগ করলে একই সমস্যা হবে — কোড ভরে যাবে অগুনতি `if-else`-এ। 😩

**সমাধান:**
Factory Method প্যাটার্ন সরাসরি object তৈরির বদলে একটা **factory method** ব্যবহার করে। Truck বা Ship সরাসরি না বানিয়ে আপনি factory method ডাকবেন, সে-ই object তৈরির দায়িত্ব নেবে।

এতে নতুন পরিবহন যোগ করতে হলে **শুধু factory method বদলাতে হবে**, পুরো অ্যাপ নয়।

মূল কথা হলো — সব পরিবহনের ধরন (Truck, Ship) একটা **সাধারণ interface বা base class** ভাগ করবে। Base class-এর factory method এই টাইপটাই ফেরত দেবে, আর subclass গুলো ঠিক করবে কোন object বানাতে হবে।

```go
// Transport সব পরিবহনের সাধারণ interface
type Transport interface {
	Deliver() string
}

// Truck একটা পরিবহনের ধরন
type Truck struct{}

func (t *Truck) Deliver() string {
	return "স্থলপথে ট্রাকে করে পৌঁছে দেওয়া হচ্ছে"
}

// Ship আরেকটা পরিবহনের ধরন
type Ship struct{}

func (s *Ship) Deliver() string {
	return "সমুদ্রপথে জাহাজে করে পৌঁছে দেওয়া হচ্ছে"
}

// Logistics হলো base factory interface
type Logistics interface {
	CreateTransport() Transport
}

// RoadLogistics ট্রাক তৈরি করে
type RoadLogistics struct{}

func (r *RoadLogistics) CreateTransport() Transport {
	return &Truck{}
}

// SeaLogistics জাহাজ তৈরি করে
type SeaLogistics struct{}

func (s *SeaLogistics) CreateTransport() Transport {
	return &Ship{}
}

func main() {
	var logistics Logistics

	// সড়কপথে ট্রাক তৈরি
	logistics = &RoadLogistics{}
	transport := logistics.CreateTransport()
	fmt.Println(transport.Deliver())
	// আউটপুট: স্থলপথে ট্রাকে করে পৌঁছে দেওয়া হচ্ছে

	// সমুদ্রপথে জাহাজ তৈরি
	logistics = &SeaLogistics{}
	transport = logistics.CreateTransport()
	fmt.Println(transport.Deliver())
	// আউটপুট: সমুদ্রপথে জাহাজে করে পৌঁছে দেওয়া হচ্ছে
}
```

---

**বাস্তব উদাহরণ — রেস্টুরেন্টের অর্ডার 🍕**
```
আপনি বললেন: "একটা পিৎজা দিন"

আপনাকে জানতে হয় না:
  - কোন চুলায় বানানো হবে
  - কোন শেফ বানাবেন
  - কী কী উপকরণ লাগবে

আপনি শুধু বলেন "পিৎজা", আর পিৎজা পেয়ে যান ✅
রান্নাঘর (factory) বাকিটা সামলায়।
```

**নতুন ফিচার যোগ করা কত সহজ দেখুন:**
```go
// বিমান পরিবহন যোগ করতে চাই

// ধাপ ১: নতুন Transport
type Plane struct{}
func (p *Plane) Deliver() string { return "আকাশপথে বিমানে পৌঁছে দেওয়া হচ্ছে" }

// ধাপ ২: নতুন Factory
type AirLogistics struct{}
func (a *AirLogistics) CreateTransport() Transport { return &Plane{} }

// ব্যস! পুরনো কোডের একটা লাইনও বদলাতে হয়নি ✅
```
এটাই **Open/Closed Principle** বাস্তবে! 🎯

---

## ৩. Abstract Factory Pattern 🏭🏭

**সংজ্ঞা:** Abstract Factory হলো একটা creational প্যাটার্ন, যা **সম্পর্কিত বা পরস্পর নির্ভরশীল object-এর পরিবার** তৈরি করার interface দেয় — কংক্রিট ক্লাসের নাম উল্লেখ না করেই।

**Families of Products:** এটা একগুচ্ছ সম্পর্কিত object তৈরি করে। যেমন — একটা GUI অ্যাপ্লিকেশনে abstract factory বিভিন্ন অপারেটিং সিস্টেমের (Windows বা macOS) জন্য button, checkbox এবং text field তৈরি করতে পারে।

Abstract Factory-কে **Factory Method-এর উপরে বসানো একটা উঁচু স্তরের abstraction** হিসেবে ভাবুন।

```go
// Product Interfaces
type Truck interface {
    Deliver() string
}

type Ship interface {
    Deliver() string
}

// Abstract Factory Interface
type LogisticsFactory interface {
    CreateTruck() Truck
    CreateShip() Ship
}

// ট্রাকের কংক্রিট বাস্তবায়ন
type StandardTruck struct{}
func (s *StandardTruck) Deliver() string {
    return "সাধারণ ট্রাকে মালামাল পৌঁছে দেওয়া হচ্ছে"
}

type ExpressTruck struct{}
func (e *ExpressTruck) Deliver() string {
    return "এক্সপ্রেস ট্রাকে মালামাল পৌঁছে দেওয়া হচ্ছে"
}

// জাহাজের কংক্রিট বাস্তবায়ন
type CargoShip struct{}
func (c *CargoShip) Deliver() string {
    return "কার্গো জাহাজে পণ্য পৌঁছে দেওয়া হচ্ছে"
}

type TankerShip struct{}
func (t *TankerShip) Deliver() string {
    return "ট্যাংকার জাহাজে তরল পৌঁছে দেওয়া হচ্ছে"
}

// কংক্রিট Factory বাস্তবায়ন
type StandardLogisticsFactory struct{}
func (s *StandardLogisticsFactory) CreateTruck() Truck {
    return &StandardTruck{}
}
func (s *StandardLogisticsFactory) CreateShip() Ship {
    return &CargoShip{}
}

type ExpressLogisticsFactory struct{}
func (e *ExpressLogisticsFactory) CreateTruck() Truck {
    return &ExpressTruck{}
}
func (e *ExpressLogisticsFactory) CreateShip() Ship {
    return &TankerShip{}
}

// Client Code
func main() {
    var factory LogisticsFactory

    // সাধারণ লজিস্টিকস ব্যবহার
    factory = &StandardLogisticsFactory{}
    standardTruck := factory.CreateTruck()
    cargoShip := factory.CreateShip()

    println(standardTruck.Deliver())  // সাধারণ ট্রাকে মালামাল...
    println(cargoShip.Deliver())      // কার্গো জাহাজে পণ্য...

    // এক্সপ্রেস লজিস্টিকস ব্যবহার
    factory = &ExpressLogisticsFactory{}
    expressTruck := factory.CreateTruck()
    tankerShip := factory.CreateShip()

    println(expressTruck.Deliver())   // এক্সপ্রেস ট্রাকে মালামাল...
    println(tankerShip.Deliver())     // ট্যাংকার জাহাজে তরল...
}
```

---

**Factory Method বনাম Abstract Factory — মূল পার্থক্য:**

| | Factory Method | Abstract Factory |
|---|---|---|
| কী বানায় | **একটা** product | **একগুচ্ছ সম্পর্কিত** product |
| কতগুলো মেথড | ১টা (`CreateTransport()`) | কয়েকটা (`CreateTruck()`, `CreateShip()`) |
| উদ্দেশ্য | কোন ধরনের object বানাব | **একই পরিবারের** object গুলো একসাথে |

**বাস্তব উদাহরণ — আসবাবপত্রের সেট 🛋️**
```
Factory Method:  "একটা চেয়ার দিন"
                 → আধুনিক চেয়ার বা ভিক্টোরিয়ান চেয়ার

Abstract Factory: "আধুনিক স্টাইলের পুরো সেট দিন"
                 → আধুনিক চেয়ার + আধুনিক সোফা + আধুনিক টেবিল ✅
                   (সবগুলো একই স্টাইলের — মিলে যায়!)
```

**সবচেয়ে গুরুত্বপূর্ণ সুবিধা — সামঞ্জস্য নিশ্চিত হয়:**
```
❌ Abstract Factory ছাড়া:
   ভুল করে আধুনিক চেয়ার + ভিক্টোরিয়ান টেবিল কিনে ফেললেন
   → দেখতে বিদঘুটে লাগবে! 😩

✅ Abstract Factory দিয়ে:
   একটা factory বাছলেই তার সব product একই পরিবারের ✅
```

**বাস্তব প্রয়োগ — ক্রস-প্ল্যাটফর্ম UI:**
```go
type UIFactory interface {
    CreateButton() Button
    CreateCheckbox() Checkbox
    CreateTextField() TextField
}

// WindowsUIFactory → সব Windows স্টাইলের
// MacUIFactory     → সব Mac স্টাইলের

// অ্যাপ চালুর সময় একবার factory বাছুন,
// বাকি পুরো অ্যাপ জানেই না সে কোন OS-এ চলছে ✅
```

---

## ৪. Builder Pattern 🔨

**সংজ্ঞা:** Builder Pattern ব্যবহার করা হয় **জটিল object ধাপে ধাপে** তৈরি করতে — একবারে সব নয়। এতে অনেক অংশ বা অপশনযুক্ত object বানানো সহজ হয়।

**উদাহরণ:** কল্পনা করুন আপনি একটা পিৎজা বানাচ্ছেন। একটা মাত্র constructor-এর বদলে একটা `PizzaBuilder` রাখতে পারেন, যেখানে —
- পিৎজার আকার ঠিক করবেন
- পনির যোগ করবেন
- পেপারোনি যোগ করবেন
- সবশেষে পিৎজাটা বানাবেন

```go
// Pizza struct
type Pizza struct {
    size         string
    hasCheese    bool
    hasPepperoni bool
}

// Pizza Builder
type PizzaBuilder struct {
    pizza Pizza
}

// পিৎজা বানানোর মেথডগুলো
func (b *PizzaBuilder) SetSize(size string) *PizzaBuilder {
    b.pizza.size = size
    return b                          // 👈 নিজেকে ফেরত দিচ্ছে
}

func (b *PizzaBuilder) AddCheese() *PizzaBuilder {
    b.pizza.hasCheese = true
    return b
}

func (b *PizzaBuilder) AddPepperoni() *PizzaBuilder {
    b.pizza.hasPepperoni = true
    return b
}

func (b *PizzaBuilder) Build() Pizza {
    return b.pizza
}

func main() {
    builder := &PizzaBuilder{}
    pizza := builder.SetSize("মাঝারি").AddCheese().AddPepperoni().Build()
    fmt.Printf("তৈরি হলো একটা পিৎজা: %+v\n", pizza)
}
```

**সুবিধাগুলো:**
- **Separation of Concerns:** object তৈরির প্রক্রিয়া object থেকে আলাদা থাকে
- **Improved Readability:** কীভাবে object তৈরি হচ্ছে তা স্পষ্ট দেখা যায়
- **Flexibility:** বেশি কোড না বদলেই object-এর বিভিন্ন সংস্করণ বানানো যায়
- **Easy Maintenance:** তৈরির প্রক্রিয়া বদলালেও কোডের অন্য অংশে প্রভাব পড়ে না

---

**সমস্যাটা প্রথমে দেখুন — Telescoping Constructor:**
```go
// ❌ ভয়ংকর
func NewPizza(size string, cheese, pepperoni, mushroom, olive,
              onion, extraCheese bool, crust string) Pizza { }

// ব্যবহারের সময়:
pizza := NewPizza("বড়", true, false, true, false, true, false, "পাতলা")
//                        ↑     ↑      ↑      ↑     ↑      ↑
//        এগুলো কী কী?? কে মনে রাখবে?? 🤯
```

**Builder দিয়ে কত পরিষ্কার:**
```go
pizza := NewPizzaBuilder().
    SetSize("বড়").
    AddCheese().
    AddMushroom().
    AddOnion().
    SetCrust("পাতলা").
    Build()
// প্রতিটা লাইন নিজেই বলে দিচ্ছে কী হচ্ছে ✅
```

**`return b` কেন — Method Chaining:**
প্রতিটা মেথড builder-টাকেই ফেরত দিচ্ছে, তাই একটার পর একটা মেথড **শৃঙ্খলাবদ্ধভাবে** ডাকা যাচ্ছে। একে বলে **fluent interface**।

**বাস্তব উদাহরণ — Subway স্যান্ডউইচ 🥪**
```
"একটা স্যান্ডউইচ দিন" বলে চুপ করে থাকেন না।
ধাপে ধাপে বলেন:
  → কোন রুটি?
  → কী মাংস?
  → কী কী সবজি?
  → কোন সস?
  → তারপর তৈরি হয় ✅
```

**কখন Builder ব্যবহার করবেন:**
- ৪+ প্যারামিটার আছে
- অনেক প্যারামিটার ঐচ্ছিক
- একই ক্লাসের অনেক রকম রূপ দরকার
- তৈরির প্রক্রিয়ায় যাচাই বা ধাপ আছে

---

## ৫. Prototype Pattern 🐑

**সংজ্ঞা:** Prototype Pattern আপনাকে একটা বিদ্যমান object (যাকে বলে **prototype**) **কপি করে** নতুন object বানাতে দেয়।

শূন্য থেকে নতুন instance বানানোর বদলে একটা prototype instance-এর **ক্লোন** করা হয় — এতে সময় ও রিসোর্স বাঁচে, বিশেষত জটিল object-এর ক্ষেত্রে।

```go
// Prototype interface
type Prototype interface {
    Clone() Prototype
}

// ConcretePrototype
type ConcretePrototype struct {
    Name string
}

// Clone মেথড
func (p *ConcretePrototype) Clone() Prototype {
    return &ConcretePrototype{Name: p.Name}
}

func main() {
    original := &ConcretePrototype{Name: "মূল"}
    clone := original.Clone()

    fmt.Println("মূল:", original.Name)                       // মূল: মূল
    fmt.Println("ক্লোন:", clone.(*ConcretePrototype).Name)   // ক্লোন: মূল
}
```

---

**কখন এটা কাজে লাগে — বাস্তব উদাহরণ:**

**উদাহরণ ১ — ডকুমেন্ট টেমপ্লেট 📄**
```
আপনার অফিসে একটা চিঠির টেমপ্লেট আছে —
লোগো, ঠিকানা, ফরম্যাটিং সব সেট করা।

নতুন চিঠি লিখতে চাইলে?
❌ শূন্য থেকে সব আবার সেট করা (২০ মিনিট)
✅ টেমপ্লেটটার একটা কপি নিয়ে শুধু বিষয়বস্তু বদলানো (২ মিনিট) ⚡
```

**উদাহরণ ২ — গেমের শত্রু 👾**
```go
// একটা শত্রু বানাতে অনেক খরচ — 3D মডেল লোড, টেক্সচার, অ্যানিমেশন
zombiePrototype := LoadZombie()      // ৫০০ ms লাগল 🐌

// এখন ১০০টা জম্বি দরকার
for i := 0; i < 100; i++ {
    zombie := zombiePrototype.Clone()   // ০.১ ms প্রতিটা ⚡
    zombie.SetPosition(randomPos())
}
```

**⚠️ সবচেয়ে বড় ফাঁদ — Shallow Copy বনাম Deep Copy:**

```go
type Person struct {
    Name    string
    Address *Address    // 👈 একটা পয়েন্টার!
}

// ❌ Shallow Copy — বিপজ্জনক
func (p *Person) Clone() *Person {
    return &Person{Name: p.Name, Address: p.Address}
    //                            👆 একই Address object শেয়ার হচ্ছে!
}

clone := original.Clone()
clone.Address.City = "চট্টগ্রাম"
fmt.Println(original.Address.City)   // "চট্টগ্রাম" — মূলটাও বদলে গেল! 😱

// ✅ Deep Copy — নিরাপদ
func (p *Person) Clone() *Person {
    newAddr := &Address{City: p.Address.City, Street: p.Address.Street}
    return &Person{Name: p.Name, Address: newAddr}   // নতুন Address ✅
}
```

> **উপমা:** Shallow copy হলো — বাড়ির **ছবি** কপি করা, কিন্তু দুই ছবিতেই **একই আসল বাড়ির** দিকে ইঙ্গিত। একটা বাড়ি রং করলে দুই ছবিতেই বদল দেখাবে!
> Deep copy হলো — **আসল বাড়িটারই আরেকটা নকল** বানানো। ✅

---

# ভাগ ৩ — Structural Pattern (গঠনমূলক প্যাটার্ন)

> এই প্যাটার্নগুলো **object গুলো কীভাবে একসাথে জোড়া লাগবে** সেটা নিয়ে কাজ করে।

## ৬. Adapter Pattern 🔌

**সংজ্ঞা:** Adapter Pattern **অসামঞ্জস্যপূর্ণ interface গুলোকে একসাথে কাজ করতে** দেয়। এটা দুইটা ভিন্ন interface-এর মধ্যে **সেতু** হিসেবে কাজ করে — বিদ্যমান কোড না বদলেই তাদের মধ্যে যোগাযোগ সহজ করে।

```go
// Target interface — আমাদের কোড যেটা প্রত্যাশা করে
type Target interface {
    Request() string
}

// Adaptee — এর interface আলাদা
type Adaptee struct{}

func (a *Adaptee) SpecificRequest() string {
    return "Adaptee: Specific Request"
}

// Adapter — Target interface বাস্তবায়ন করে
type Adapter struct {
    adaptee *Adaptee
}

func (a *Adapter) Request() string {
    return a.adaptee.SpecificRequest()     // 👈 অনুবাদ করে দিচ্ছে
}

func main() {
    adaptee := &Adaptee{}
    adapter := &Adapter{adaptee: adaptee}

    fmt.Println(adapter.Request())   // আউটপুট: Adaptee: Specific Request
}
```

---

**বাস্তব উদাহরণ — ট্রাভেল অ্যাডাপ্টার 🔌**
```
আপনার বাংলাদেশি চার্জারে দুই পিনের প্লাগ।
আমেরিকার সকেটে তিন পিনের ছিদ্র।

চার্জার বদলাবেন? ❌ (দামি, আর দেশে ফিরে আবার লাগবে)
সকেট বদলাবেন?   ❌ (হোটেলের দেয়াল ভাঙতে পারবেন না!)

সমাধান: একটা ছোট **অ্যাডাপ্টার** ✅
        দুই পক্ষের কেউই বদলাল না, কাজ হয়ে গেল
```

**বাস্তব প্রোগ্রামিং উদাহরণ — পুরনো লাইব্রেরি ব্যবহার:**
```go
// আপনার কোড এই interface প্রত্যাশা করে
type PaymentProcessor interface {
    Pay(amount float64) error
}

// কিন্তু তৃতীয় পক্ষের লাইব্রেরির interface একদম আলাদা
type OldStripeLib struct{}
func (s *OldStripeLib) MakePayment(cents int, currency string) (bool, string) {
    // ...
}

// ✅ Adapter দিয়ে সেতু বানান
type StripeAdapter struct {
    stripe *OldStripeLib
}

func (a *StripeAdapter) Pay(amount float64) error {
    cents := int(amount * 100)                  // 👈 রূপান্তর
    ok, msg := a.stripe.MakePayment(cents, "BDT")
    if !ok {
        return errors.New(msg)                  // 👈 এররের ধরনও রূপান্তর
    }
    return nil
}
```

**কখন Adapter ব্যবহার করবেন:**
- তৃতীয় পক্ষের লাইব্রেরির interface আপনার সাথে মেলে না
- পুরনো (legacy) কোড নতুন সিস্টেমে ব্যবহার করতে হবে
- একই কাজের কয়েকটা লাইব্রেরির মধ্যে সহজে বদল করতে চান

> **💡 বাড়তি সুবিধা:** Adapter থাকলে ভবিষ্যতে Stripe থেকে PayPal-এ যেতে চাইলে শুধু নতুন একটা adapter লিখলেই হবে — **আপনার মূল কোডে হাত দিতে হবে না** ✅

---

## ৭. Facade Pattern 🎭

**সংজ্ঞা:** Facade Pattern একটা **জটিল সাবসিস্টেমের জন্য সরল interface** দেয়। এটা সিস্টেমের জটিলতা লুকিয়ে রেখে ব্যবহারকারীকে সহজে কাজ করার সুযোগ দেয়।

জটিল কোডকে একটা সহজ interface-এর পেছনে আবদ্ধ করে ব্যবহারকারীর জন্য জটিলতা কমায়। Facade ক্লায়েন্ট কোডকে জটিল সাবসিস্টেম থেকে আলাদা করে দেয় — ফলে ক্লায়েন্টকে প্রভাবিত না করেই সিস্টেম বদলানো সহজ হয়।

```go
// সাবসিস্টেমের ক্লাসগুলো
type Light struct{}
func (l *Light) TurnOn() {
    fmt.Println("বাতি জ্বালানো হলো")
}

type MusicSystem struct{}
func (m *MusicSystem) PlayMusic() {
    fmt.Println("গান চালু হলো")
}

type Projector struct{}
func (p *Projector) Start() {
    fmt.Println("প্রজেক্টর চালু হলো")
}

// Facade
type HomeTheaterFacade struct {
    light       *Light
    musicSystem *MusicSystem
    projector   *Projector
}

func NewHomeTheaterFacade() *HomeTheaterFacade {
    return &HomeTheaterFacade{
        light:       &Light{},
        musicSystem: &MusicSystem{},
        projector:   &Projector{},
    }
}

func (h *HomeTheaterFacade) WatchMovie() {
    h.light.TurnOn()
    h.musicSystem.PlayMusic()
    h.projector.Start()
    fmt.Println("সিনেমা দেখার জন্য প্রস্তুত!")
}

func main() {
    facade := NewHomeTheaterFacade()
    facade.WatchMovie()          // 👈 একটা কল, সব কাজ ✅
}
```

---

**বাস্তব উদাহরণ — গাড়ি স্টার্ট করা 🚗**
```
আপনি চাবি ঘোরান → গাড়ি চালু হয় ✅

ভেতরে যা ঘটে (আপনার জানার দরকার নেই):
  → ব্যাটারি থেকে বিদ্যুৎ যায়
  → স্টার্টার মোটর ঘোরে
  → ফুয়েল পাম্প চালু হয়
  → স্পার্ক প্লাগ জ্বলে
  → ইঞ্জিন চালু হয়
  → অল্টারনেটর ব্যাটারি চার্জ করে

চাবিটাই হলো **Facade** — একটা সহজ ইন্টারফেস ✅
```

**আরেকটা বাস্তব উদাহরণ — অনলাইন অর্ডার:**
```go
// ❌ Facade ছাড়া — ক্লায়েন্টকে সব জানতে হবে
inventory.CheckStock(productID)
payment.Charge(userID, amount)
shipping.CreateLabel(address)
email.SendConfirmation(userID)
analytics.TrackPurchase(productID)
// ৫টা সিস্টেম, ক্রম মনে রাখতে হবে, এরর সামলাতে হবে 😩

// ✅ Facade দিয়ে
orderFacade.PlaceOrder(userID, productID, address)
// একটা কল ✅
```

**⚠️ Facade বনাম Adapter — পার্থক্যটা মনে রাখুন:**

| | Adapter | Facade |
|---|---|---|
| উদ্দেশ্য | **অসামঞ্জস্য** ঠিক করা | **জটিলতা** লুকানো |
| কতগুলো ক্লাস মোড়ে | সাধারণত ১টা | **অনেকগুলো** |
| interface | বিদ্যমান interface-এ মানানো | **নতুন সরল** interface |
| উপমা | ট্রাভেল অ্যাডাপ্টার | গাড়ির চাবি |

> **💡 মনে রাখুন:** Facade মূল সাবসিস্টেমগুলো **লুকিয়ে ফেলে না** — কেউ চাইলে সরাসরি সেগুলো ব্যবহার করতেই পারে। Facade শুধু **সাধারণ কাজগুলো সহজ করে দেয়** ✅

---

## ৮. Decorator Pattern 🎁

**সংজ্ঞা:** Decorator Pattern আপনাকে একটা বিদ্যমান object-এর **গঠন না বদলেই নতুন কার্যকারিতা যোগ** করতে দেয়। এটা মূল object-কে **মুড়ে (wrap)** তার আচরণ বাড়িয়ে দেয়।

- **নমনীয়:** একাধিক decorator মিলিয়ে object-এর আচরণের বিভিন্ন রূপ তৈরি করা যায়
- **Single Responsibility:** ক্লাসগুলো নিজেদের নির্দিষ্ট কাজেই মনোযোগী থাকে, বাড়তি ফিচার বাইরে থেকে যোগ হয়

```go
// Base interface
type Coffee interface {
	Cost() float64
	Description() string
}

// কংক্রিট বাস্তবায়ন
type SimpleCoffee struct{}

func (c *SimpleCoffee) Cost() float64 {
	return 2.0
}

func (c *SimpleCoffee) Description() string {
	return "সাধারণ কফি"
}

// Decorator
type MilkDecorator struct {
	coffee Coffee              // 👈 ভেতরে আরেকটা Coffee ধরে রাখছে
}

func (d *MilkDecorator) Cost() float64 {
	return d.coffee.Cost() + 0.5
}

func (d *MilkDecorator) Description() string {
	return d.coffee.Description() + ", দুধ সহ"
}

// আরেকটা Decorator
type SugarDecorator struct {
	coffee Coffee
}

func (d *SugarDecorator) Cost() float64 {
	return d.coffee.Cost() + 0.2
}

func (d *SugarDecorator) Description() string {
	return d.coffee.Description() + ", চিনি সহ"
}

func main() {
	// সাধারণ কফি
	myCoffee := &SimpleCoffee{}
	fmt.Println(myCoffee.Description(), "দাম", myCoffee.Cost())
	// সাধারণ কফি দাম 2

	// দুধ যোগ করলাম
	myCoffeeWithMilk := &MilkDecorator{coffee: myCoffee}
	fmt.Println(myCoffeeWithMilk.Description(), "দাম", myCoffeeWithMilk.Cost())
	// সাধারণ কফি, দুধ সহ দাম 2.5

	// চিনিও যোগ করলাম
	myCoffeeWithMilkAndSugar := &SugarDecorator{coffee: myCoffeeWithMilk}
	fmt.Println(myCoffeeWithMilkAndSugar.Description(), "দাম",
	            myCoffeeWithMilkAndSugar.Cost())
	// সাধারণ কফি, দুধ সহ, চিনি সহ দাম 2.7
}
```

---

**মোড়ানোর ধারণাটা ছবিতে দেখুন:**
```
┌─────────────────────────────────┐
│  SugarDecorator (+০.২)          │
│  ┌───────────────────────────┐  │
│  │  MilkDecorator (+০.৫)     │  │
│  │  ┌─────────────────────┐  │  │
│  │  │  SimpleCoffee (২.০) │  │  │
│  │  └─────────────────────┘  │  │
│  └───────────────────────────┘  │
└─────────────────────────────────┘
        মোট = ২.৭ ✅
```

**বাস্তব উদাহরণ — শীতের পোশাক 🧥**
```
আপনি (base) → গেঞ্জি পরলেন → সোয়েটার পরলেন → জ্যাকেট পরলেন

প্রতিটা স্তর নতুন কিছু যোগ করছে (উষ্ণতা)
কিন্তু আপনি আপনিই আছেন — কেউ আপনাকে বদলায়নি ✅
আর ইচ্ছেমতো স্তর যোগ-বিয়োগ করা যায় ✅
```

**কেন Inheritance দিয়ে করা যায় না:**
```
৪টা অপশন থাকলে (দুধ, চিনি, ক্রিম, ক্যারামেল) —
সব সমন্বয়ের জন্য ক্লাস লাগবে:

CoffeeWithMilk
CoffeeWithSugar
CoffeeWithMilkAndSugar
CoffeeWithMilkAndCream
CoffeeWithMilkAndSugarAndCream
... মোট ২⁴ = ১৬টা ক্লাস! 🤯

১০টা অপশন হলে? ১০২৪টা ক্লাস! 💀

Decorator দিয়ে: মাত্র ১০টা decorator ক্লাস ✅
```

**বাস্তবে কোথায় দেখবেন:**
```go
// Go-এর HTTP middleware ঠিক এই প্যাটার্ন!
handler := LoggingMiddleware(
              AuthMiddleware(
                 RateLimitMiddleware(
                    myHandler)))
```
প্রতিটা middleware একটা decorator — মূল handler-কে না বদলেই তার আচরণ বাড়াচ্ছে ✅

---

## ৯. Composite Pattern 🌳

**সংজ্ঞা:** Composite Pattern একটা structural প্যাটার্ন, যা আপনাকে **একক object এবং object-এর গুচ্ছকে একইভাবে** ব্যবহার করতে দেয়।

এটা একটা **গাছের মতো কাঠামো** তৈরি করে, যেখানে একক object (leaf node) এবং object-এর দল (composite node) — দুটোকেই একইভাবে ব্যবহার করা যায়।

```go
// Component interface
type FileSystemComponent interface {
	GetName() string
}

// Leaf (পাতা — যার সন্তান নেই)
type File struct {
	name string
}

func (f *File) GetName() string {
	return f.name
}

// Composite (যার ভেতরে অন্যরা থাকতে পারে)
type Directory struct {
	name     string
	children []FileSystemComponent
}

func (d *Directory) GetName() string {
	return d.name
}

func (d *Directory) Add(child FileSystemComponent) {
	d.children = append(d.children, child)
}

func (d *Directory) ShowContents() {
	fmt.Println("ফোল্ডার:", d.name)
	for _, child := range d.children {
		fmt.Println(" -", child.GetName())
	}
}

func main() {
	// ফাইল তৈরি
	file1 := &File{name: "File1.txt"}
	file2 := &File{name: "File2.txt"}

	// ফোল্ডার তৈরি করে ফাইল যোগ
	dir := &Directory{name: "আমার ফোল্ডার"}
	dir.Add(file1)
	dir.Add(file2)

	// ফোল্ডারের ভেতরটা দেখানো
	dir.ShowContents()
}
```

---

**বাস্তব উদাহরণ — আপনার কম্পিউটারের ফাইল সিস্টেম 📁**
```
Documents/                    ← Composite (ফোল্ডার)
├── resume.pdf                ← Leaf (ফাইল)
├── photos/                   ← Composite
│   ├── vacation.jpg          ← Leaf
│   └── family/               ← Composite
│       └── eid.jpg           ← Leaf
└── notes.txt                 ← Leaf
```

**সবচেয়ে সুন্দর দিকটা:** আপনি যখন ডান-ক্লিক করে "Delete" চাপেন, তখন **একটা ফাইল নাকি পুরো ফোল্ডার** — তাতে কিছু যায়-আসে না। একই কমান্ড দুটোতেই কাজ করে ✅

**আরেকটা পরিচিত উদাহরণ — কোম্পানির সংগঠন 🏢**
```
CEO                        ← Composite
├── CTO                    ← Composite
│   ├── ব্যাকএন্ড টিম লিড   ← Composite
│   │   ├── ডেভেলপার ১     ← Leaf
│   │   └── ডেভেলপার ২     ← Leaf
│   └── ডিজাইনার           ← Leaf
└── CFO                    ← Composite
    └── হিসাবরক্ষক          ← Leaf

"মোট বেতন কত?" — CEO থেকে জিজ্ঞেস করলেই
পুরো গাছ ঘুরে যোগফল বেরিয়ে আসবে ✅
```

**Composite-এর আসল শক্তি — রিকার্সিভ অপারেশন:**
```go
// আকার হিসাব — ফাইল আর ফোল্ডার একইভাবে সামলানো যাচ্ছে
func (f *File) GetSize() int {
    return f.size                       // পাতা: সরাসরি
}

func (d *Directory) GetSize() int {
    total := 0
    for _, child := range d.children {
        total += child.GetSize()        // 👈 রিকার্সিভ! ✅
    }
    return total
}
```
> ক্লায়েন্ট কোড শুধু `component.GetSize()` ডাকবে — ভেতরে কী আছে জানার দরকার নেই। এটাই প্যাটার্নের সৌন্দর্য।

**কোথায় ব্যবহার হয়:**
- ফাইল সিস্টেম
- UI কম্পোনেন্ট (একটা Panel-এর ভেতরে Button, আরেকটা Panel...)
- মেনু ও সাবমেনু
- গাণিতিক এক্সপ্রেশন গাছ
- কোম্পানির শ্রেণিবিন্যাস

---

# ভাগ ৪ — Behavioral Pattern (আচরণগত প্যাটার্ন)

> এই প্যাটার্নগুলো **object গুলো কীভাবে যোগাযোগ করবে** সেটা নিয়ে কাজ করে।

## ১০. Strategy Pattern 🎯

**সংজ্ঞা:** Strategy Pattern একটা behavioral প্যাটার্ন, যা **runtime-এ কোনো অ্যালগরিদমের আচরণ বেছে নেওয়ার** সুযোগ দেয়।

এটা একগুচ্ছ অ্যালগরিদম সংজ্ঞায়িত করে, প্রতিটাকে আলাদাভাবে আবদ্ধ করে এবং **একে অপরের সাথে বদলযোগ্য** করে তোলে — যে কোড এগুলো ব্যবহার করছে তা না বদলেই।

```go
// Strategy interface
type PaymentStrategy interface {
	Pay(amount float64)
}

// কংক্রিট strategy গুলো
type CreditCard struct {
	cardNumber string
}

func (c *CreditCard) Pay(amount float64) {
	fmt.Printf("ক্রেডিট কার্ড %s দিয়ে %.2f টাকা পরিশোধ করা হলো\n",
	           c.cardNumber, amount)
}

type PayPal struct {
	email string
}

func (p *PayPal) Pay(amount float64) {
	fmt.Printf("PayPal অ্যাকাউন্ট %s দিয়ে %.2f টাকা পরিশোধ করা হলো\n",
	           p.email, amount)
}

// Context
type ShoppingCart struct {
	strategy PaymentStrategy
}

func (sc *ShoppingCart) SetPaymentStrategy(strategy PaymentStrategy) {
	sc.strategy = strategy
}

func (sc *ShoppingCart) Checkout(amount float64) {
	sc.strategy.Pay(amount)         // 👈 কোন strategy জানার দরকার নেই ✅
}

func main() {
	cart := &ShoppingCart{}

	// ক্রেডিট কার্ড ব্যবহার
	cart.SetPaymentStrategy(&CreditCard{cardNumber: "1234-5678-9876-5432"})
	cart.Checkout(100.00)

	// PayPal-এ বদলে গেলাম
	cart.SetPaymentStrategy(&PayPal{email: "user@example.com"})
	cart.Checkout(50.00)
}
```

---

**সমস্যাটা প্রথমে দেখুন:**
```go
// ❌ Strategy ছাড়া — if-else-এর জঙ্গল
func Checkout(method string, amount float64) {
    if method == "credit_card" {
        // ৫০ লাইন কোড
    } else if method == "paypal" {
        // ৪০ লাইন কোড
    } else if method == "bkash" {
        // ৬০ লাইন কোড
    } else if method == "nagad" {
        // 😩 আরও বাড়ছে...
    }
}
// একটা ফাংশন ২০০ লাইন, টেস্ট করা দুঃস্বপ্ন, নতুন যোগ করা ঝুঁকিপূর্ণ
```

**বাস্তব উদাহরণ — গুগল ম্যাপে পথ 🗺️**
```
একই গন্তব্য, কিন্তু কীভাবে যাবেন?
  🚗 গাড়িতে    → Strategy 1
  🚌 বাসে       → Strategy 2
  🚶 হেঁটে      → Strategy 3
  🚴 সাইকেলে    → Strategy 4

আপনি শুধু আইকনে ক্লিক করে strategy বদলে দেন ✅
অ্যাপের বাকি সব কিছু একই থাকে
```

**আরেকটা উদাহরণ — সাজানোর অ্যালগরিদম:**
```go
type SortStrategy interface { Sort([]int) []int }

// ছোট তালিকা → InsertionSort (কম ওভারহেড)
// বড় তালিকা → QuickSort
// প্রায় সাজানো → BubbleSort (এক্ষেত্রে দ্রুত)

func SmartSort(data []int) []int {
    var strategy SortStrategy
    if len(data) < 10 {
        strategy = &InsertionSort{}
    } else {
        strategy = &QuickSort{}
    }
    return strategy.Sort(data)     // runtime-এ সিদ্ধান্ত ✅
}
```

**Strategy বনাম State — খুব কাছাকাছি কিন্তু আলাদা:**

| | Strategy | State |
|---|---|---|
| কে বদলায় | **ক্লায়েন্ট** বাছে | **object নিজেই** বদলে যায় |
| Strategy গুলো কি একে অপরকে জানে? | ❌ না | ✅ হ্যাঁ (পরের state কে জানে) |
| উদ্দেশ্য | একই কাজের **ভিন্ন উপায়** | **ভিন্ন অবস্থায় ভিন্ন আচরণ** |

---

## ১১. Observer Pattern 👁️

**সংজ্ঞা:** Observer Pattern object গুলোর মধ্যে একটা **এক-থেকে-বহু (one-to-many)** সম্পর্ক তৈরি করে।

এই প্যাটার্নে একটা object (**subject**) তার নির্ভরশীল object গুলোর (**observers**) একটা তালিকা রাখে এবং **অবস্থার যেকোনো পরিবর্তনে স্বয়ংক্রিয়ভাবে তাদের জানিয়ে দেয়**।

```go
// Observer interface
type Observer interface {
	Update(data string)
}

// Subject interface
type Subject interface {
	Register(observer Observer)
	Unregister(observer Observer)
	Notify()
	SetData(data string)
}

// কংক্রিট subject
type NewsPublisher struct {
	observers []Observer
	data      string
}

func (n *NewsPublisher) Register(observer Observer) {
	n.observers = append(n.observers, observer)
}

func (n *NewsPublisher) Unregister(observer Observer) {
	for i, obs := range n.observers {
		if obs == observer {
			n.observers = append(n.observers[:i], n.observers[i+1:]...)
			break
		}
	}
}

func (n *NewsPublisher) Notify() {
	for _, observer := range n.observers {
		observer.Update(n.data)
	}
}

// কংক্রিট subject-এ SetData বাস্তবায়ন
func (n *NewsPublisher) SetData(data string) {
	n.data = data
	n.Notify()                  // 👈 ডেটা বদলালেই সবাইকে জানিয়ে দাও ✅
}

// কংক্রিট observer
type NewsChannel struct {
	name string
}

func (n *NewsChannel) Update(data string) {
	fmt.Printf("%s খবর পেল: %s\n", n.name, data)
}

func main() {
	publisher := &NewsPublisher{}
	channel1 := &NewsChannel{name: "চ্যানেল ১"}
	channel2 := &NewsChannel{name: "চ্যানেল ২"}

	publisher.Register(channel1)
	publisher.Register(channel2)

	publisher.SetData("ব্রেকিং নিউজ!")   // সব observer খবর পাবে ✅
	// আউটপুট:
	//   চ্যানেল ১ খবর পেল: ব্রেকিং নিউজ!
	//   চ্যানেল ২ খবর পেল: ব্রেকিং নিউজ!
}
```

---

**বাস্তব উদাহরণ — খবরের কাগজের গ্রাহক 📰**
```
আপনি একটা পত্রিকার গ্রাহক হলেন (Register)
    ↓
প্রতিদিন সকালে কাগজ আপনার দরজায় পৌঁছে যায় (Notify)
    ↓
আপনি আর চান না? গ্রাহকপদ বাতিল করলেন (Unregister)

গুরুত্বপূর্ণ: আপনাকে প্রতিদিন গিয়ে জিজ্ঞেস করতে হয় না
            "আজ নতুন কাগজ বেরিয়েছে?" — তারাই পাঠিয়ে দেয় ✅
```

**Push বনাম Pull — এটাই মূল পার্থক্য:**
```
❌ Pull (Polling): প্রতিটা observer বারবার জিজ্ঞেস করে
   "কিছু বদলেছে?" ... "কিছু বদলেছে?" ... 😩
   → CPU ও নেটওয়ার্কের অপচয়

✅ Push (Observer): subject নিজেই জানিয়ে দেয়
   → দক্ষ, রিয়েল-টাইম ⚡
```

**যেখানে আপনি রোজ Observer প্যাটার্ন ব্যবহার করছেন:**
```javascript
// ব্রাউজারের ইভেন্ট
button.addEventListener('click', handler);   // Register
// ক্লিক হলেই handler ডাকা হবে → Observer!

// YouTube-এর সাবস্ক্রিপশন 🔔
// Facebook-এর notification
// Excel-এর সূত্র (একটা সেল বদলালে অন্যগুলো আপডেট হয়)
// Redux, RxJS, Vue-এর reactivity
```

**একটা বাস্তব প্রয়োগ — শেয়ার বাজার:**
```go
stockPrice.Register(&MobileApp{})       // মোবাইলে আপডেট
stockPrice.Register(&EmailAlert{})      // ইমেইল পাঠাবে
stockPrice.Register(&AutoTrader{})      // স্বয়ংক্রিয় কেনাবেচা
stockPrice.Register(&Dashboard{})       // চার্ট আপডেট

stockPrice.SetPrice(150.50)             // চারজনই সাথে সাথে জানবে ✅
```

**⚠️ Observer-এর সমস্যাগুলো:**

| সমস্যা | ব্যাখ্যা | সমাধান |
|---|---|---|
| **Memory leak** | observer unregister না করলে সে মেমরিতে থেকে যায় | Weak reference, বা সবসময় unregister করুন |
| **অপ্রত্যাশিত ক্রম** | কোন observer আগে জানবে তা নিশ্চিত নয় | ক্রমের উপর নির্ভর করবেন না |
| **চেইন প্রতিক্রিয়া** | একজনের আপডেটে আরেকজন আপডেট হয়ে অসীম লুপ! | সাবধানে ডিজাইন করুন |
| **ধীর observer** | একজন ধীর হলে সবাই আটকে যাবে | **অ্যাসিঙ্ক্রোনাসভাবে** জানান ✅ |

**⚠️ থ্রেড নিরাপত্তা:** `observers` slice-এ একসাথে পড়া ও লেখা হলে race condition হবে। `sync.RWMutex` ব্যবহার করুন।

---

## 📌 দ্রুত রিভিশন চিট-শিট

### সব প্যাটার্ন এক নজরে

| প্যাটার্ন | শ্রেণী | এক লাইনে | বাস্তব উপমা |
|---|---|---|---|
| **Singleton** | Creational | একটাই instance | দেশের রাষ্ট্রপতি |
| **Factory Method** | Creational | object তৈরির দায়িত্ব একজনের | রেস্টুরেন্টে অর্ডার |
| **Abstract Factory** | Creational | একই পরিবারের object গুচ্ছ | আসবাবের পূর্ণ সেট |
| **Builder** | Creational | ধাপে ধাপে object তৈরি | Subway স্যান্ডউইচ |
| **Prototype** | Creational | কপি করে নতুন object | ডকুমেন্ট টেমপ্লেট |
| **Adapter** | Structural | অসামঞ্জস্য মেটানো | ট্রাভেল অ্যাডাপ্টার |
| **Facade** | Structural | জটিলতা লুকিয়ে সরল করা | গাড়ির চাবি |
| **Decorator** | Structural | মুড়ে নতুন ফিচার যোগ | শীতের পোশাকের স্তর |
| **Composite** | Structural | একক ও গুচ্ছকে একইভাবে দেখা | ফাইল সিস্টেম |
| **Strategy** | Behavioral | runtime-এ অ্যালগরিদম বদলানো | গুগল ম্যাপের পথ |
| **Observer** | Behavioral | একজন বদলালে অনেকে জানে | পত্রিকার গ্রাহক |

### যেসব প্যাটার্ন গুলিয়ে যায়

| জোড়া | পার্থক্য |
|---|---|
| **Factory vs Abstract Factory** | একটা product vs একগুচ্ছ সম্পর্কিত product |
| **Adapter vs Facade** | অসামঞ্জস্য মেটানো vs জটিলতা লুকানো |
| **Decorator vs Inheritance** | runtime-এ যোগ vs compile-time-এ স্থির |
| **Strategy vs State** | ক্লায়েন্ট বাছে vs object নিজেই বদলায় |
| **Builder vs Factory** | ধাপে ধাপে তৈরি vs এক কলে তৈরি |

### SOLID মনে রাখার সংক্ষিপ্ত রূপ

```
S → একটা ক্লাসের একটাই কাজ
O → নতুন কোড লিখুন, পুরনো বদলাবেন না
L → সন্তান ক্লাস পিতার জায়গায় বসতে পারবে
I → ছোট ছোট interface, বড় একটা নয়
D → কংক্রিট নয়, interface-এর উপর নির্ভর করুন
```

### কোন সমস্যায় কোন প্যাটার্ন

| সমস্যা | প্যাটার্ন |
|---|---|
| শুধু একটা instance চাই | **Singleton** |
| object তৈরির লজিক ছড়িয়ে আছে | **Factory** |
| Constructor-এ ১০টা প্যারামিটার | **Builder** |
| জটিল object বারবার বানাতে খরচ বেশি | **Prototype** |
| লাইব্রেরির interface মেলে না | **Adapter** |
| সাবসিস্টেম খুব জটিল | **Facade** |
| ফিচার যোগ করতে ক্লাস বিস্ফোরণ | **Decorator** |
| গাছের মতো কাঠামো | **Composite** |
| অনেকগুলো if-else অ্যালগরিদম | **Strategy** |
| একজন বদলালে অনেককে জানাতে হবে | **Observer** |

### ইন্টারভিউয়ে যেসব কথা বললে নম্বর বাড়ে

1. ✅ "এখানে Strategy ব্যবহার করব, কারণ ভবিষ্যতে নতুন পেমেন্ট মেথড যোগ করা সহজ হবে" — **কারণ সহ বলুন**
2. ✅ "Singleton-এর টেস্টিং সমস্যা আছে, তাই Dependency Injection ব্যবহার করব" — **ট্রেড-অফ জানুন**
3. ✅ "এখানে প্যাটার্ন লাগবে না, সহজ কোডই যথেষ্ট" — **অতিরিক্ত ইঞ্জিনিয়ারিং এড়ানোও দক্ষতা**
4. ✅ "Deep copy না shallow copy — এটা নিশ্চিত করতে হবে" — **সূক্ষ্ম বিষয়ে খেয়াল**
5. ✅ "Observer-এ unregister না করলে memory leak হবে"

### যে ভুলগুলো এড়িয়ে চলবেন

1. ❌ সব জায়গায় প্যাটার্ন ঢোকানো (**Pattern Fever** 🤒)
2. ❌ প্যাটার্নের নাম মুখস্থ কিন্তু **কেন** ব্যবহার করছেন তা না জানা
3. ❌ ট্রেড-অফ না বোঝা — প্রতিটা প্যাটার্নেরই দাম আছে
4. ❌ সমস্যা না থাকলেও "ভবিষ্যতে লাগতে পারে" ভেবে প্যাটার্ন যোগ করা (**YAGNI ভঙ্গ**)
5. ❌ Singleton-কে গ্লোবাল ভেরিয়েবলের মতো যত্রতত্র ব্যবহার

> **শেষ কথা:** সেরা কোড সেটাই যেটা **পরের ডেভেলপার সহজে বুঝতে পারে**। প্যাটার্ন সেই লক্ষ্যে সাহায্য করলে ব্যবহার করুন — বাধা দিলে নয়। 🎯
