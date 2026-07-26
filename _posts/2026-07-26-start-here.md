---
title: "সূচিপত্র — কোথা থেকে শুরু করবেন"
description: "ব্যাকএন্ড ও সিস্টেম ডিজাইন ইন্টারভিউয়ের ১২টা বিষয়ের বাংলা নোট, শেখার ক্রম অনুযায়ী সাজানো।"
date: 2026-07-26 15:00:00 +0600
categories: [Guide]
tags: [index, interview, roadmap]
pin: true
---

ব্যাকএন্ড ও সিস্টেম ডিজাইন ইন্টারভিউয়ের জন্য ১২টা বিষয়ের পূর্ণাঙ্গ নোট — **সহজ বাংলায়, বাস্তব উদাহরণ সহ**।

> **কাদের জন্য:** যারা সবে শুরু করেছেন বা ইন্টারভিউয়ের আগে দ্রুত রিভিশন দিতে চান।
>
> **যেভাবে লেখা:** প্রতিটা কনসেপ্টের সাথে একটা করে বাস্তব উপমা আছে (Kafka = খবরের কাগজের অফিস, Mutex = বাথরুমের ছিটকিনি, RSA = তালা-চাবি)। কোড ও টেকনিক্যাল টার্ম ইংরেজিতেই রাখা হয়েছে, শুধু ব্যাখ্যাটা বাংলায়।

---

## 📖 শেখার ক্রম

এই ক্রমে পড়লে একটার উপর আরেকটা দাঁড়াবে। তবে চাইলে যেকোনো পোস্ট আলাদা করেও পড়া যাবে।

### ধাপ ১ — ভিত্তি (Fundamentals)

| # | বিষয় | কী শিখবেন |
|---|---|---|
| ১ | **[SQL](/posts/sql/)** | একটা হাসপাতালের ডেটাবেজে ১৫টা প্র্যাকটিস প্রশ্ন — `JOIN`, `GROUP BY`, `HAVING`, সাবকোয়েরি |
| ২ | **[ডেটাবেজ ম্যানেজমেন্ট সিস্টেম](/posts/database-management-system/)** | ACID, Normalization (1NF–3NF), Indexing, B+ Tree, Sharding, Lock, Transaction |
| ৩ | **[অপারেটিং সিস্টেম](/posts/operating-system/)** | Process vs Thread, Deadlock, Virtual Memory, Paging, CPU Scheduling, Semaphore |
| ৪ | **[কম্পিউটার নেটওয়ার্কিং](/posts/computer-networking/)** | OSI মডেল, TCP vs UDP, DNS, NAT, HTTPS, Three-way Handshake, "google.com লিখলে কী হয়" |
| ৫ | **[টাইম কমপ্লেক্সিটি](/posts/time-complexity/)** | Big O, ডেটা স্ট্রাকচার ও অ্যালগরিদমের জটিলতার টেবিল |

### ধাপ ২ — প্রোগ্রামিং ও ডিজাইন

| # | বিষয় | কী শিখবেন |
|---|---|---|
| ৬ | **[Golang](/posts/golang/)** | Goroutine, Channel, `select`, Context, Mutex vs RWMutex, Worker Pool, Goroutine Leak |
| ৭ | **[ডিজাইন প্যাটার্ন](/posts/design-patterns/)** | SOLID, DRY, KISS, YAGNI + ১১টা প্যাটার্ন (Singleton, Factory, Strategy, Observer…) |
| ৮ | **[লো লেভেল ডিজাইন (LLD)](/posts/low-level-design/)** | ১৭টা ক্লাসিক ডিজাইন — Parking Lot, Elevator, ATM, BookMyShow, Splitwise, Uber |

### ধাপ ৩ — সিস্টেম ডিজাইন ও ইনফ্রা

| # | বিষয় | কী শিখবেন |
|---|---|---|
| ৯ | **[হাই লেভেল ডিজাইন (HLD)](/posts/high-level-design/)** | Load Balancer, Cache, CDN, CAP Theorem, Consistent Hashing, Rate Limiter, News Feed, Chat System |
| ১০ | **[অথেনটিকেশন ও অথরাইজেশন](/posts/authentication-authorization/)** | OAuth 2.0, OIDC, JWT, PKCE, RBAC vs ABAC, SSO, পাসওয়ার্ড হ্যাশিং |
| ১১ | **[Kafka](/posts/kafka/)** | Producer, Consumer Group, Partition, Offset, ISR, Replication |
| ১২ | **[Kubernetes](/posts/kubernetes/)** | Pod, Deployment vs StatefulSet, Service, Ingress, HPA, ConfigMap, RBAC, Helm |

---

## 🔍 বিষয় অনুযায়ী দ্রুত খোঁজা

**ডেটাবেজ ও ডেটা**
- [SQL কোয়েরি প্র্যাকটিস](/posts/sql/) · [DBMS তত্ত্ব](/posts/database-management-system/) · [Kafka (স্ট্রিমিং)](/posts/kafka/)

**কোর কম্পিউটার সায়েন্স**
- [অপারেটিং সিস্টেম](/posts/operating-system/) · [নেটওয়ার্কিং](/posts/computer-networking/) · [টাইম কমপ্লেক্সিটি](/posts/time-complexity/)

**সফটওয়্যার ডিজাইন**
- [ডিজাইন প্যাটার্ন](/posts/design-patterns/) · [LLD](/posts/low-level-design/) · [HLD](/posts/high-level-design/)

**ব্যাকএন্ড ও DevOps**
- [Golang](/posts/golang/) · [Kubernetes](/posts/kubernetes/) · [AuthN & AuthZ](/posts/authentication-authorization/)

---

## 💡 নোটগুলো কীভাবে ব্যবহার করবেন

**প্রথমবার পড়ছেন?**
উপরের ক্রম ধরে এগোন। প্রতিটা কনসেপ্টের **বাস্তব উদাহরণটা** আগে পড়ুন — তাহলে সংজ্ঞাটা মাথায় বসে যাবে।

**ইন্টারভিউয়ের আগের রাতে?**
প্রতিটা পোস্টের **একদম শেষে** একটা করে **"📌 দ্রুত রিভিশন চিট-শিট"** আছে — টেবিল, কমান্ড, আর "ইন্টারভিউয়ে সবচেয়ে বেশি আসা প্রশ্ন"। শুধু ঐটুকু পড়লেই দ্রুত ঝালিয়ে নেওয়া হয়ে যাবে।

**⚠️ চিহ্নগুলো খেয়াল করুন**
এই চিহ্ন দিয়ে জুনিয়রদের সবচেয়ে কমন ভুলগুলো আলাদা করে দেখানো হয়েছে। যেমন —
- JWT এনক্রিপ্টেড নয়, শুধু এনকোডেড — payload-এ গোপন তথ্য রাখবেন না
- টাকার হিসাবে `double` নয়, `BigDecimal` ব্যবহার করুন
- Flannel CNI-তে NetworkPolicy নীরবে কাজ করে না
- SQL-এ `NOT NULL` নয়, `IS NOT NULL` লিখতে হয়

---

## 📝 কিছু কথা

- এগুলো আমার মূল ইংরেজি নোটের **অনুবাদ + সম্প্রসারণ**।
- **সব কোড, কমান্ড ও টেকনিক্যাল টার্ম ইংরেজিতেই** রাখা হয়েছে — কারণ ইন্টারভিউ ও বাস্তব কাজে ওগুলোই ব্যবহৃত হয়।
- মূল নোটে কয়েকটা ভুল ছিল, সেগুলো ঠিক করে **নোট করে** দেওয়া হয়েছে (যেমন Kubernetes-এর Pod Security Policy এখন deprecated)।
- **[time-complexity.md](/posts/time-complexity/)** ফাইলটা মূলে খালি ছিল, তাই এটা নতুন করে লেখা।
