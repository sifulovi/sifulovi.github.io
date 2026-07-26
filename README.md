# ইন্টারভিউ প্রস্তুতি — বাংলা নোট

ব্যাকএন্ড ও সিস্টেম ডিজাইন ইন্টারভিউয়ের ১২টা বিষয়ের বাংলা নোট।
[Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) থিম + Jekyll + GitHub Pages।

🔗 **সাইট:** https://sifulovi.github.io

---

## 🚀 প্রথমবার সেটআপ (৩ ধাপ)

### ধাপ ১ — GitHub-এ রিপো বানান

এটা একটা **user site**, তাই রিপোর নাম **অবশ্যই** `sifulovi.github.io` হতে হবে — নইলে সাইট ডোমেইনের রুটে দেখাবে না।

[github.com/new](https://github.com/new) → নাম দিন `sifulovi.github.io` → **Public** → README/gitignore/license কিছুই যোগ করবেন না (এখানে আগেই আছে)।

### ধাপ ২ — পুশ করুন

```bash
git remote add origin git@github.com:sifulovi/sifulovi.github.io.git
git branch -M main
git push -u origin main
```

### ধাপ ৩ — Pages চালু করুন

রিপোর **Settings → Pages → Build and deployment → Source** এ **GitHub Actions** বাছুন।

> ⚠️ এই ধাপটা বাদ দিলে Actions বিল্ড সফল হবে কিন্তু ডিপ্লয় ব্যর্থ হবে।

এরপর **Actions** ট্যাবে বিল্ড চলতে দেখবেন। ২-৩ মিনিটে সাইট লাইভ হয়ে যাবে।

---

## ✍️ নতুন পোস্ট লেখা

`_posts/` ফোল্ডারে `YYYY-MM-DD-slug.md` নামে ফাইল বানান:

```markdown
---
title: "পোস্টের শিরোনাম"
description: "এক লাইনের বর্ণনা — SEO ও প্রিভিউতে ব্যবহৃত হয়"
date: 2026-07-26 14:00:00 +0600
categories: [Fundamentals]
tags: [sql, database]
---

লেখা শুরু...
```

**নিয়মগুলো:**

| বিষয় | নিয়ম |
|---|---|
| ফাইলের নাম | `YYYY-MM-DD-slug.md` — তারিখ বাধ্যতামূলক |
| URL | `/posts/<slug>/` |
| `categories` | ASCII-তে লিখুন (`Fundamentals`) — বাংলা দিলে URL ভেঙে যায় |
| `tags` | ছোট হাতের ASCII |
| শিরোনাম | front matter-এ দিন, লেখার শুরুতে `# ...` **দেবেন না** (ডুপ্লিকেট H1 হবে) |
| `pin: true` | হোমপেজের সবার উপরে আটকে রাখে |

### পোস্টের ক্রম

হোমপেজে **নতুন তারিখ আগে** দেখায়, আর Chirpy-তে এই ক্রম বদলানোর কোনো অপশন নেই। শেখার ক্রম ঠিক রাখতে তাই সব পোস্টের তারিখ একই রেখে **সময় অবরোহী** করা হয়েছে:

```
15:00  (খালি — নতুন পোস্ট সবার উপরে বসাতে চাইলে এই স্লটটা ব্যবহার করুন)
14:00  sql
13:00  database-management-system
...
03:00  kubernetes
```

**নতুন পোস্ট মাঝখানে ঢোকাতে চাইলে** তার দুই পাশের পোস্টের মধ্যবর্তী একটা সময় দিন।

> ⚠️ **সময়টা অবশ্যই অতীতে হতে হবে।** Jekyll-এ `future: false` ডিফল্ট, তাই ভবিষ্যতের তারিখ/সময়ের পোস্ট **নীরবে প্রকাশিতই হয় না** — বিল্ড সফল দেখাবে, কিন্তু পোস্টটা সাইটে থাকবে না।
>
> এই ফাঁদে একবার পড়া হয়েছে: শুরুতে সময় ১১:০০–২৩:০০ দেওয়া হয়েছিল, বিল্ড চলেছিল ১৬:২০-এ — ফলে ৭টা পোস্ট গায়েব ছিল।
>
> সময় ফুরিয়ে গেলে (উপরে জায়গা না থাকলে) ঘণ্টার বদলে মিনিট ব্যবহার করুন (`14:30`), অথবা সবগুলোর তারিখ এক দিন পিছিয়ে দিয়ে আবার জায়গা বানান।
{: .prompt-warning }

---

## 🎨 এই রিপোতে যা কাস্টমাইজ করা হয়েছে

Chirpy-র ডিফল্ট starter-এর উপর যা যোগ করা হয়েছে:

| ফাইল | কেন |
|---|---|
| `_data/locales/bn.yml` | Chirpy-তে বাংলা লোকেল নেই — UI বাংলা করতে নিজে লেখা হয়েছে |
| `_includes/metadata-hook.html` | Noto Sans Bengali ওয়েবফন্ট লোড করে (থিমের অফিশিয়াল override hook) |
| `assets/css/jekyll-theme-chirpy.scss` | বাংলা ফন্ট প্রয়োগ + `line-height` বাড়ানো (বাংলা লিপিতে বেশি leading দরকার) |
| `_config.yml` | `lang: bn`, `timezone: Asia/Dhaka`, শিরোনাম ও সোশ্যাল লিংক |
| `.gitmodules` **মুছে ফেলা** | assets CDN থেকে আসছে, তাই `chirpy-static-assets` submodule লাগছে না |

**ফন্ট নিয়ে নোট:** CSS-এ ল্যাটিন ফন্ট (Lato) **আগে** রাখা হয়েছে, বাংলা ফন্ট পরে। তাই ইংরেজি লেখা ও কোড থিমের আসল চেহারাতেই থাকে, আর বাংলা অক্ষর স্বয়ংক্রিয়ভাবে Noto Sans Bengali-তে fallback করে।

---

## 💻 লোকালি প্রিভিউ (ঐচ্ছিক)

GitHub Actions ক্লাউডে বিল্ড করে, তাই **লোকালি Ruby লাগে না**। তবু আগে দেখতে চাইলে:

```bash
# Ruby ও bundler লাগবে
sudo apt install ruby-full build-essential zlib1g-dev   # Debian/Ubuntu

bundle install
bundle exec jekyll serve --livereload
# → http://127.0.0.1:4000
```

**পুশ করার আগে CI-এর মতো টেস্ট চালাতে চাইলে:**

```bash
bundle exec jekyll b
bundle exec htmlproofer _site --disable-external
```

> ⚠️ CI-তে `htmlproofer` চলে — **ভাঙা ইন্টারনাল লিংক থাকলে বিল্ড ফেল করবে**। পোস্টের মধ্যে অন্য পোস্টের লিংক দিতে হলে `.md` নয়, `/posts/<slug>/` লিখুন।

---

## 📄 লাইসেন্স

লেখাগুলো [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)।
Chirpy থিম [MIT](https://github.com/cotes2020/jekyll-theme-chirpy/blob/master/LICENSE) লাইসেন্সের অধীনে।
