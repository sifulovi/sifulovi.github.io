---
title: "লো লেভেল ডিজাইন (LLD)"
description: "১৭টা ক্লাসিক ডিজাইন প্রশ্ন — Parking Lot, Elevator, ATM, BookMyShow, Splitwise, Uber।"
date: 2026-07-26 07:00:00 +0600
categories: [Programming]
tags: [lld, oop, design-patterns, interview]
---

> **LLD আসলে কী?**
>
> HLD (High Level Design)-এ আপনি ঠিক করেন **কয়টা সার্ভার লাগবে, কোন ডেটাবেজ, কোথায় ক্যাশ**।
> LLD-তে আপনি ঠিক করেন **কী কী ক্লাস থাকবে, কার ভেতরে কী থাকবে, কে কার সাথে কথা বলবে**।
>
> **বাস্তব উদাহরণ — একটা বাড়ি বানানো 🏠**
> - **HLD** = জমির নকশা, কয় তলা হবে, বিদ্যুৎ-পানির লাইন কোথা দিয়ে যাবে
> - **LLD** = প্রতিটা ঘরের মাপ, দরজা কোন দিকে খুলবে, সুইচবোর্ড কোথায় বসবে
>
> ---
>
> ## 🎯 ইন্টারভিউতে LLD প্রশ্নের উত্তর দেওয়ার ৫ ধাপ
>
> **ধাপ ১ — প্রশ্ন করুন (২-৩ মিনিট)।** সাথে সাথে কোড লিখতে শুরু করবেন না!
> > "পার্কিং লট কি বহুতল? কোন কোন ধরনের গাড়ি? পেমেন্ট কি লাগবে?"
>
> **ধাপ ২ — Entity খুঁজে বের করুন।** সমস্যাটার বর্ণনায় যে **বিশেষ্য (noun)** গুলো আছে, সেগুলোই সাধারণত ক্লাস।
> > "একটা **পার্কিং লটে** কয়েকটা **তলা** থাকে, প্রতিটা তলায় কিছু **স্পট**, যেখানে **গাড়ি** পার্ক করা হয়।"
> > → `ParkingLot`, `Level`, `ParkingSpot`, `Vehicle`
>
> **ধাপ ৩ — কাজ (method) ঠিক করুন।** বর্ণনার **ক্রিয়া (verb)** গুলোই সাধারণত মেথড।
> > "গাড়ি **পার্ক করা**, **বের করা**, খালি জায়গা **দেখানো**" → `parkVehicle()`, `unparkVehicle()`, `displayAvailability()`
>
> **ধাপ ৪ — সম্পর্ক আঁকুন।** কে কার ভেতরে থাকে (`has-a`), কে কার ধরন (`is-a`)।
>
> **ধাপ ৫ — Design Pattern প্রয়োগ করুন।** কোথায় Singleton, কোথায় Factory, কোথায় Strategy।
>
> ---
>
> ## 🔑 এই ডকুমেন্টে বারবার আসবে যে ৪টা প্যাটার্ন
>
> | প্যাটার্ন | কী করে | কোথায় দেখবেন |
> |---|---|---|
> | **Singleton** | পুরো অ্যাপে একটাই instance | প্রায় প্রতিটা `...System`, `...Service` ক্লাসে |
> | **Strategy** | আচরণ বদলানোর সুযোগ | `PaymentProcessor` interface |
> | **State** | অবস্থা অনুযায়ী আচরণ বদলায় | Vending Machine |
> | **Factory** | object তৈরির দায়িত্ব একজনের | Vehicle তৈরি |
>
> **Singleton কেন এত জায়গায়:** একটা পার্কিং লট সিস্টেমে **দুইটা `ParkingLot` object** থাকলে বিপদ — একজন বলবে জায়গা আছে, আরেকজন বলবে নেই! তাই পুরো অ্যাপে একটাই instance রাখা হয়।
>
> **`synchronized` কেন দরকার:** একই মুহূর্তে দুইজন একই সিট বুক করতে গেলে দুজনকেই সিট দিয়ে দেওয়া হবে! `synchronized` একসাথে একজনকেই ঢুকতে দেয়।

---

## ১. Parking Lot ডিজাইন করুন 🅿️

**বাস্তব প্রেক্ষাপট:** একটা শপিং মলের বহুতল পার্কিং। গাড়ি ঢুকলে খালি স্পট খুঁজে দিতে হবে, বের হলে স্পট খালি করতে হবে।

### ১. ParkingSpot (একটা পার্কিং জায়গা)
```
ফিল্ড:
  * vehicleType      → এই স্পটে কী ধরনের গাড়ি রাখা যাবে
  * isAvailable      → খালি আছে কিনা
  * parkingNumber    → স্পটের নম্বর (যেমন A-12)

মেথড:
  * isAvailable()         * getVehicleType()
  * getParkingNumber()    * parkVehicle()
  * unParkVehicle()       * getParkedVehicle()
```
> **বাস্তবে:** মাটিতে সাদা দাগ দিয়ে আঁকা একটা ঘর, যার গায়ে নম্বর লেখা।

### ২. Vehicle (Interface)
```
Vehicle (interface)
  ├── Car
  ├── Bike
  └── Truck
```
> **Interface কেন:** ভবিষ্যতে `ElectricCar` যোগ করতে চাইলে শুধু নতুন একটা ক্লাস বানালেই হবে — `ParkingSpot`-এর কোডে হাত দিতে হবে না। এটাই **Open/Closed Principle**।
>
> **বাস্তবে:** বাইকের জায়গায় ট্রাক রাখা যায় না — তাই টাইপ জানা জরুরি।

### ৩. Level (একটা তলা)
```
ফিল্ড:
  * List<ParkingSpot>    → এই তলার সব স্পট
  * floor                → তলা নম্বর

মেথড:
  * parkVehicle()        * unparkVehicle()
  * displayAvailability()
```

### ৪. ParkingLot (পুরো পার্কিং লট) — **Singleton**
```
ফিল্ড:
  * List<Level>          → সব তলা
  * ParkingLot instance  → 👈 Singleton

মেথড:
  * getInstance()        * addLevel()
  * parkVehicle()        * unparkVehicle()
  * displayAvailability()
```

**পুরো প্রবাহটা:**
```
গাড়ি এল → ParkingLot.parkVehicle(car)
              ↓ প্রতিটা Level-এ জিজ্ঞেস করে
          Level.parkVehicle(car)
              ↓ প্রতিটা Spot-এ জিজ্ঞেস করে
          ParkingSpot: "আমি খালি? আমার টাইপ কি Car?"
              ↓ হ্যাঁ হলে
          স্পট দখল হলো ✅ টিকিট দেওয়া হলো
```

**⚠️ ইন্টারভিউয়ে যেসব প্রশ্ন আসতে পারে:**
- "দুইটা গাড়ি একসাথে ঢুকলে?" → `synchronized` বা lock দরকার
- "টাকা কীভাবে হিসাব হবে?" → `Ticket` ক্লাসে `entryTime` রাখুন, `Pricing` strategy দিয়ে হিসাব করুন
- "খালি স্পট খুঁজতে সব ঘুরতে হবে?" → প্রতিটা Level-এ `Queue<ParkingSpot> freeSpots` রাখলে O(1)-এ পাওয়া যাবে ⚡

---

## ২. Vending Machine ডিজাইন করুন 🥤

**বাস্তব প্রেক্ষাপট:** একটা কোমল পানীয়ের মেশিন। টাকা ঢোকান, পণ্য বাছুন, পণ্য বের হয়, ভাংতি ফেরত আসে।

**এই ডিজাইনের মূল শিক্ষা — State Pattern।** মেশিনটা বিভিন্ন **অবস্থায়** থাকে, আর প্রতিটা অবস্থায় একই বাটনে চাপ দিলে **আলাদা আলাদা কাজ** হয়।

### ১. Product
```
* name, price
* getters ও setters
```

### ২. Inventory (স্টক)
```
* Map<Product, Integer> products    → কোন পণ্য কয়টা আছে

* addProduct()      * removeProduct()
* updateQuantity()  * getQuantity()      * isAvailable()
```

### ৩. VendingMachineState (Interface)
```
* selectProduct()
* addNote()
* addCoin()
* dispenseProduct()
* returnChange()
```

### ৪-৭. অবস্থাগুলো
```
IdleState          → কিছুই হচ্ছে না, অপেক্ষায়
ReadyState         → পণ্য বাছা হয়েছে, টাকার অপেক্ষা
DispenseState      → টাকা পুরো হয়েছে, পণ্য বের হচ্ছে
ReturnChangeState  → ভাংতি ফেরত দেওয়া হচ্ছে
```

### ৮-৯. Coin, Note (কয়েন ও নোট)

### ১০. VendingMachine — **Singleton**
```
ফিল্ড:
  * instance, currentState
  * idleState, readyState, dispenseState, returnChangeState
  * selectedProduct, totalAmount

মেথড:
  * getInstance()        * selectProduct()
  * insertCoin()         * insertNote()
  * dispenseProduct()    * returnChange()
  * setState()           * getIdleState() / getReadyState() / ...
  * getSelectedProduct() * setSelectedProduct() * resetSelectedProduct()
  * getAmount()          * resetAmount()
  * addCoin()            * addNote()
```

---

**State Pattern কেন এত গুরুত্বপূর্ণ — বাস্তব উদাহরণ:**

State Pattern ছাড়া কোডটা এমন হতো:
```java
void insertCoin(Coin c) {
    if (state == IDLE) {
        System.out.println("আগে পণ্য বাছুন!");
    } else if (state == READY) {
        totalAmount += c.getValue();
        if (totalAmount >= product.getPrice()) state = DISPENSE;
    } else if (state == DISPENSE) {
        System.out.println("অপেক্ষা করুন, পণ্য বের হচ্ছে");
    } else if (state == RETURN_CHANGE) {
        ...
    }
}
```
প্রতিটা মেথডে এই লম্বা `if-else`! নতুন একটা অবস্থা যোগ করতে হলে **সব মেথডে** গিয়ে `else if` যোগ করতে হবে। 😩

**State Pattern দিয়ে:**
```java
void insertCoin(Coin c) {
    currentState.addCoin(c);    // 👈 ব্যস! অবস্থা নিজেই জানে কী করতে হবে
}
```
নতুন অবস্থা যোগ করতে চাইলে শুধু নতুন একটা ক্লাস বানান — পুরনো কোডে হাত দিতে হবে না। ✅

**বাস্তব উপমা — লিফটের বাটন:**
লিফটের "দরজা খোলার" বাটনে চাপ দিলে —
- লিফট **থামা** অবস্থায় → দরজা খুলবে ✅
- লিফট **চলন্ত** অবস্থায় → কিছুই হবে না ❌

একই বাটন, কিন্তু অবস্থাভেদে আলাদা আচরণ। এটাই State Pattern।

---

## ৩. Elevator (লিফট) ডিজাইন করুন 🛗

**বাস্তব প্রেক্ষাপট:** ২০ তলা বিল্ডিংয়ে ৪টা লিফট। কেউ বাটন চাপলে কোন লিফট যাবে সেটা ঠিক করতে হবে।

### ১. Elevator
```
ফিল্ড:
  * direction              → এখন উপরে যাচ্ছে না নিচে
  * List<Request> requests → অপেক্ষমাণ অনুরোধগুলো
  * currentFloor, ID, capacity

মেথড:
  * addRequest()        * getNextRequest()
  * getCapacity()       * processRequests()
  * processRequest()    * run()
  * getCurrentFloor()
```

### ২. Request
```
* sourceFloor       → কোন তলা থেকে
* destinationFloor  → কোন তলায় যাবে
* getters ও setters
```

### ৩. Direction (enum)
```
UP, DOWN
```
> `IDLE` যোগ করা ভালো — যখন লিফট কোথাও যাচ্ছে না।

### ৪. ElevatorController
```
* List<Elevator> elevators
* requestElevator()
* findOptimalElevator()    ← 👈 এটাই আসল বুদ্ধির জায়গা
```

---

**`findOptimalElevator()` — ইন্টারভিউয়ের আসল প্রশ্ন:**

আপনি ৫ তলায় দাঁড়িয়ে উপরে যাওয়ার বাটন চাপলেন। ৪টা লিফটের অবস্থা:
```
লিফট A: ৩ তলায়, উপরে যাচ্ছে    → ✅ সবচেয়ে ভালো (পথেই পড়বে)
লিফট B: ৮ তলায়, নিচে নামছে      → ⚠️ চলবে, কিন্তু দিক উল্টো
লিফট C: ১০ তলায়, অলস            → ⚠️ চলবে, কিন্তু দূরে
লিফট D: ৪ তলায়, নিচে নামছে      → ❌ কাছে, কিন্তু ভুল দিকে
```

**সহজ অ্যালগরিদম:**
```
প্রতিটা লিফটের জন্য একটা স্কোর হিসাব করুন:
  ১. একই দিকে যাচ্ছে এবং পথে পড়বে  → স্কোর = দূরত্ব           (সবচেয়ে ভালো)
  ২. অলস (idle)                     → স্কোর = দূরত্ব + ১০
  ৩. উল্টো দিকে যাচ্ছে               → স্কোর = দূরত্ব + ১০০    (সবচেয়ে খারাপ)

সবচেয়ে কম স্কোরের লিফটটা বেছে নিন
```

**⚠️ যেসব বিষয় ইন্টারভিউয়ার জানতে চাইবেন:**
- **Starvation:** ১ তলার কেউ কি কখনো লিফট পাবে না? (সবসময় উপরের তলার লোকেরা নিয়ে যাচ্ছে) → সমাধান: অপেক্ষার সময় বিবেচনা করুন
- **Capacity:** লিফট ভরে গেলে নতুন অনুরোধ নেবে না
- **Thread:** প্রতিটা লিফট একটা আলাদা thread-এ `run()` করবে

---

## ৪. Tic-Tac-Toe ডিজাইন করুন ⭕❌

**বাস্তব প্রেক্ষাপট:** ৩×৩ ঘরের খেলা। দুইজন পালা করে O আর X বসায়।

### ১. Player
```
* name
* symbol   → 'X' বা 'O'
```

### ২. Board
```
ফিল্ড:
  * char[][] grid    → ৩×৩ ঘর
  * movesCount       → কয়টা চাল হয়েছে

মেথড:
  * initBoard()                      → সব ঘর খালি করা
  * makeMove(row, col, symbol)       → একটা চাল দেওয়া
  * isFull()                         → বোর্ড ভরে গেছে? (ড্র)
  * hasWinner()                      → কেউ জিতেছে?
  * printBoard()                     → বোর্ড দেখানো
```

### ৩. Game
```
* player1, player2
* board
* currentPlayer

* play()             → মূল লুপ
* switchPlayer()     → পালা বদল
* getValidInput()    → ইনপুট যাচাই
```

---

**`hasWinner()` কীভাবে লিখবেন:**
```
৩টা সারি চেক করুন     → grid[i][0] == grid[i][1] == grid[i][2]
৩টা কলাম চেক করুন    → grid[0][j] == grid[1][j] == grid[2][j]
২টা কর্ণ চেক করুন     → grid[0][0], grid[1][1], grid[2][2]
                        grid[0][2], grid[1][1], grid[2][0]
```

**💡 অপ্টিমাইজেশন (ইন্টারভিউয়ারকে মুগ্ধ করতে):**
প্রতিবার পুরো বোর্ড চেক না করে **শুধু শেষ চালের সারি, কলাম ও কর্ণ** চেক করুন — O(n²) থেকে O(n)।

আরও ভালো: প্রতিটা সারি-কলামের জন্য একটা কাউন্টার রাখুন। X বসালে +1, O বসালে −1। কোনো কাউন্টার +3 বা −3 হলেই বিজয়ী! → **O(1)** ⚡

**`getValidInput()` কেন আলাদা মেথড:** ব্যবহারকারী "abc" লিখলে বা ভরা ঘরে চাল দিতে চাইলে প্রোগ্রাম যেন ক্র্যাশ না করে।

**ইন্টারভিউয়ার জিজ্ঞেস করতে পারেন:** "n×n বোর্ডে কীভাবে বানাবেন?" → `Board`-এ `size` ফিল্ড রাখুন, hardcode করা ৩ সরিয়ে দিন।

---

## ৫. ATM ডিজাইন করুন 🏧

**বাস্তব প্রেক্ষাপট:** কার্ড ঢোকান, PIN দিন, টাকা তুলুন বা জমা দিন।

### ১. Account
```
* accountNumber, balance
* getAccountNumber(), getBalance(), credit(), debit()
```
> `credit()` = টাকা যোগ, `debit()` = টাকা বিয়োগ

### ২. Card
```
* cardNumber, pin
* getCardNumber(), getPin()
```

### ৩. BankingSystem
```
* Map<String, Account> accounts
* createAccount(accountNumber, balance)
* getAccount(accountNumber)
* processTransaction(transaction)
```

### ৪. Transaction (abstract class)
```
abstract class Transaction {
    transactionId, account, amount
    abstract execute();
}
```

### ৫. WithdrawalTransaction extends Transaction
```
constructor(transactionId, account, amount) { super(...) }
execute() → account.debit(amount)
```

### ৬. DepositTransaction extends Transaction
```
constructor(transactionId, account, amount) { super(...) }
execute() → account.credit(amount)
```

### ৭. CashDispenser (টাকা বের করার যন্ত্র)
```
* cashAvailable
* public synchronized void dispenseCash(int amount)
```

### ৮. ATM
```
* bankingSystem, cashDispenser
* authenticateUser(card, pin)
* checkBalance(), withdrawCash(accountNumber, amount), depositCash()
```

---

**এই ডিজাইনের সবচেয়ে গুরুত্বপূর্ণ শিক্ষা — Command Pattern:**

`Transaction`-কে abstract class বানানোর কারণ কী?

```java
// ❌ এভাবে না লিখে
if (type == WITHDRAW) { account.debit(amount); }
else if (type == DEPOSIT) { account.credit(amount); }
else if (type == TRANSFER) { ... }

// ✅ এভাবে
transaction.execute();       // 👈 কোন ধরনের লেনদেন সেটা জানার দরকার নেই!
```

**এতে যে সুবিধা পাবেন:**
- নতুন লেনদেন (যেমন `TransferTransaction`) যোগ করা সহজ ✅
- সব লেনদেনের **ইতিহাস** রাখা যায় (একটা `List<Transaction>`-এ)
- **Undo** ফিচার বানানো সহজ (`undo()` মেথড যোগ করুন)

**`synchronized dispenseCash()` কেন অপরিহার্য:**
```
মেশিনে আছে ১০,০০০ টাকা।
দুইজন একসাথে ৮,০০০ করে তুলতে চাইল।

synchronized ছাড়া:
  A: ১০,০০০ ≥ ৮,০০০? হ্যাঁ ✅
  B: ১০,০০০ ≥ ৮,০০০? হ্যাঁ ✅
  → ১৬,০০০ টাকা বের হয়ে গেল! 💸 মেশিনে ছিল ১০,০০০!
```

**⚠️ বাস্তব ATM-এ আরও যা লাগে:**
- ভুল PIN ৩ বার → কার্ড ব্লক
- নোটের হিসাব (৫০০ + ১০০ = ৬০০ টাকা কীভাবে দেবে)
- Receipt printer
- সব লেনদেনের audit log

---

## ৬. Car Rental System ডিজাইন করুন 🚗

**বাস্তব প্রেক্ষাপট:** গাড়ি ভাড়ার দোকান। গ্রাহক তারিখ বলে গাড়ি খোঁজে, বুক করে, টাকা দেয়।

### ১. Car
```
* make, model, year, rentalPricePerDay, licencePlate, available
* বিভিন্ন getters ও setters
```

### ২. Customer
```
* name, drivingLicenceNumber
```

### ৩. Reservation
```
* reservationId, car, customer, startDate, endDate, totalPrice
* constructor, calculateTotalPrice(), সব getters
```

### ৪. RentalSystem — **Singleton**
```
ফিল্ড:
  * instance
  * Map<String, Car> cars
  * Map<String, Reservation> reservations
  * paymentProcessor

মেথড:
  * getInstance()          * addCar()            * removeCar()
  * searchCar()            * isCarAvailable()
  * makeReservation()      * cancelReservation()
  * processPayment()       * generateReservationId()
```

### ৫. PaymentProcessor (Interface) — **Strategy Pattern**
```
PaymentProcessor (interface)
  ├── CreditCardPaymentProcessor
  └── PayPalPaymentProcessor
```

---

**Strategy Pattern কেন — বাস্তব উদাহরণ:**

```java
// ❌ Strategy ছাড়া
void processPayment(String method, double amount) {
    if (method.equals("CREDIT_CARD")) {
        // Stripe API কল...
    } else if (method.equals("PAYPAL")) {
        // PayPal API কল...
    } else if (method.equals("BKASH")) {
        // bKash API কল...     ← নতুন পদ্ধতি = এই মেথড বদলাতে হবে 😩
    }
}

// ✅ Strategy দিয়ে
void processPayment(PaymentProcessor processor, double amount) {
    processor.process(amount);      // 👈 কে প্রসেস করছে জানার দরকার নেই
}
```

bKash যোগ করতে চান? শুধু `BkashPaymentProcessor` ক্লাসটা লিখুন — **RentalSystem-এর একটা লাইনও বদলাতে হবে না**। ✅

> **বাস্তব উপমা:** আপনি একটা কুরিয়ারে পার্সেল পাঠাচ্ছেন। আপনি বলেন "পাঠিয়ে দিন" — সে সড়কপথে পাঠাবে না আকাশপথে, সেটা তার ব্যাপার। আপনার শুধু ফলাফল দরকার।

**⚠️ `isCarAvailable()` আসলে জটিল:**
```
শুধু `available` boolean দিয়ে হবে না!
কারণ একটা গাড়ি ১ তারিখ থেকে ৫ তারিখ বুক থাকতে পারে,
কিন্তু ১০ তারিখ থেকে খালি।

সঠিক উপায়: সব reservation দেখে তারিখের ওভারল্যাপ চেক করুন —
  if (newStart < existingEnd && newEnd > existingStart) → সংঘর্ষ ❌
```

---

## ৭. Hotel Management System ডিজাইন করুন 🏨

**বাস্তব প্রেক্ষাপট:** হোটেলের রুম বুকিং, চেক-ইন, চেক-আউট, পেমেন্ট।

### ১. Room
```
* id, roomType, price, roomStatus
* book(), checkIn(), checkOut()
* getId(), getType(), getPrice(), getStatus()
```

### ২. RoomStatus (enum)
```
AVAILABLE, OCCUPIED, BOOKED
```
> **তিনটার পার্থক্য:** `BOOKED` = বুক করা আছে কিন্তু অতিথি এখনো আসেননি। `OCCUPIED` = অতিথি রুমে আছেন।

### ৩. RoomType (enum)
```
SINGLE, DOUBLE, DELUXE, SUITE
```

### ৪. Guest
```
* id, name, email, phoneNumber
* সব getters
```

### ৫. Reservation
```
* id, guest, room, checkInDate, checkOutDate, reservationStatus
* cancelReservation(), সব getters
```

### ৬. ReservationStatus (enum)
```
CONFIRMED, CANCELLED
```

### ৭. HotelManagementSystem — **Singleton**
```
ফিল্ড:
  * instance
  * Map<String, Room> rooms
  * Map<String, Guest> guests
  * Map<String, Reservation> reservations

মেথড:
  * addGuest(), getGuest(), addRoom(), getRoom()
  * public synchronized Reservation bookRoom(Guest, Room, checkInDate, checkOutDate)
  * public synchronized void cancelReservation(String reservationId)
  * public synchronized void checkIn(String reservationId)
  * public synchronized void checkOut(String reservationId, Payment payment)
```

### ৮. Payment (Interface)
```
boolean processPayment(double amount)

Payment (interface)
  ├── CashPayment
  └── CreditCardPayment
```

---

**রুমের জীবনচক্র — একটা রাষ্ট্রযন্ত্রের (state machine) মতো:**
```
   AVAILABLE ──bookRoom()──→ BOOKED ──checkIn()──→ OCCUPIED
       ↑                        │                      │
       └──cancelReservation()───┘                      │
       └────────────── checkOut() ──────────────────────┘
```

**⚠️ চারটা মেথডেই `synchronized` কেন:**
```
১০০ জন একসাথে ওয়েবসাইটে ঢুকে "শেষ রুমটা" বুক করতে চাইছে।
synchronized ছাড়া → ১০০ জনই বুকিং কনফার্মেশন পেয়ে যাবে! 💥
হোটেলে গিয়ে দেখবে একটাই রুম। 😱
```

**ইন্টারভিউয়ারের সম্ভাব্য প্রশ্ন:**
- "১০,০০০ রুম হলে `synchronized` কি bottleneck হবে?" → হ্যাঁ! তখন **রুম-প্রতি লক** ব্যবহার করুন, পুরো সিস্টেমে একটা লক নয়
- "খালি রুম খুঁজতে সব রুম ঘুরতে হবে?" → `Map<RoomType, Queue<Room>>` রাখলে দ্রুত হবে
- "চেক-আউটের সময় বিল কীভাবে হিসাব হবে?" → রাতের সংখ্যা × রুমের দাম + অতিরিক্ত সেবা

---

## ৮. Facebook-এর মতো সোশ্যাল মিডিয়া সাইট ডিজাইন করুন 👥

### ১. User
```
* id, name, email, password, profilePic, bio
* List<String> friends
* List<Post> posts
* সব getters ও setters
```

### ২. Comment
```
* id, userId, postId, content, timestamp
* সব getters
```

### ৩. Post
```
* id, userId, content, timestamp
* List<String> imageUrls, List<String> videoUrls
* List<String> likes
* List<Comment> comments
* সব getters
```

### ৪. Notification
```
* id, userId, notificationType, content, timestamp
* সব getters
```

### ৫. NotificationType (enum)
```
FRIEND_REQUEST, FRIEND_REQUEST_ACCEPTED, LIKE, COMMENT, MENTION
```

### ৬. SocialMediaService — **Singleton**
```
ফিল্ড:
  * instance
  * Map<String, User> users
  * Map<String, Post> posts
  * Map<String, List<Notification>> notifications

মেথড:
  * getInstance()
  * registerUser(User user)              → users.put(user.getId(), user)
  * loginUser(String email, String password)  → map-এ ইউজার আছে কিনা, ইমেইল-পাসওয়ার্ড মেলে কিনা
  * updateUserProfile(User user)
  * sendFriendRequest(senderId, receiverId)   → FRIEND_REQUEST notification বানিয়ে map-এ যোগ
  * acceptFriendRequest(userId, friendId)     → দুজনের friends তালিকায় যোগ + notification
  * createPost(Post post)
  * getNewsfeed(String userId)
  * likePost(userId, postId)
  * commentOnPost(Comment comment)
  * addNotification(userId, notification)
  * getNotifications(String userId)
  * generateNotificationId()
```

---

**`getNewsfeed()` — এটাই আসল চ্যালেঞ্জ:**

**সহজ উপায় (Pull মডেল):**
```
১. ব্যবহারকারীর বন্ধুদের তালিকা নিন
২. প্রতিটা বন্ধুর সব পোস্ট আনুন
৩. সময় অনুযায়ী সাজান
৪. প্রথম ২০টা ফেরত দিন
```
**সমস্যা:** ৫০০ বন্ধু থাকলে প্রতিবার ফিড খুললে ৫০০ জনের পোস্ট আনতে হবে! 🐌

**ভালো উপায় (Push মডেল / Fan-out on Write):**
```
কেউ পোস্ট করলে সাথে সাথে তার সব বন্ধুর "ফিড বাক্সে" পোস্টটা ঢুকিয়ে দিন।
ফিড খুললে শুধু নিজের বাক্সটা পড়লেই হলো ⚡
```
**সমস্যা:** সেলিব্রিটির ১ কোটি ফলোয়ার থাকলে একটা পোস্ট ১ কোটি বাক্সে লিখতে হবে! 💥

**বাস্তবে যা করা হয় (Hybrid):**
- সাধারণ ব্যবহারকারী → **Push**
- সেলিব্রিটি → **Pull** (ফিড খোলার সময় তাদের পোস্ট আলাদা করে আনা হয়)

**⚠️ ডিজাইনের কিছু ভুল যা ইন্টারভিউয়ার ধরবেন:**
```
❌ User ক্লাসে password সাদা টেক্সটে → hash করে রাখুন (bcrypt)
❌ Post-এ List<String> likes → ১ কোটি লাইক হলে? Set ব্যবহার করুন, বা শুধু count
❌ User-এ List<Post> posts → ১০ বছরের পোস্ট মেমরিতে? আলাদা করে কোয়েরি করুন
```

---

## ৯. Restaurant Management System ডিজাইন করুন 🍽️

### ১. Order
```
* id, List<MenuItem> orderItems, orderStatus, timestamp, totalAmount
* setStatus(), getId(), getItems(), getTimestamp(), getAmount()
```

### ২. MenuItem
```
* id, name, price, description, available
* সব getters
```

### ৩. Reservation
```
* id, customerName, contactNumber, partySize, reservationTime
```

### ৪. OrderStatus (enum)
```
PENDING → PREPARING → READY → COMPLETED
                  ↘ CANCELLED
```

### ৫. Staff
```
* id, name, contactNumber, role
```

### ৬. Payment
```
PaymentMethod (enum): CASH, CREDIT_CARD, MOBILE_PAY
Payment:              id, amount, method, status
PaymentStatus (enum): PENDING, COMPLETED, FAILED
```

### ৭. RestaurantManagementSystem — **Singleton**
```
ফিল্ড:
  * instance
  * List<MenuItem> menu
  * Map<Integer, Order> orders
  * List<Reservation> reservations
  * Map<Integer, Payment> payments
  * List<Staff> staffs

মেথড:
  * getInstance()                    // Singleton
  * addMenuItem(), removeMenuItem(), List<MenuItem> getMenu()
  * placeOrder()
  * notifyKitchen()
  * makeReservation(), cancelReservation()
  * processPayment()
  * addStaff(), removeStaff(), notifyStaff()
```

---

**`notifyKitchen()` ও `notifyStaff()` — Observer Pattern-এর ইঙ্গিত:**

```java
// ❌ শক্ত সংযোগ (tight coupling)
void placeOrder(Order order) {
    orders.put(order.getId(), order);
    kitchen.newOrder(order);              // রান্নাঘরকে জানাও
    waiter.newOrder(order);               // ওয়েটারকে জানাও
    inventorySystem.deductItems(order);   // স্টক কমাও
    // নতুন কেউ জানতে চাইলে এখানে আরও লাইন যোগ করতে হবে 😩
}

// ✅ Observer Pattern দিয়ে
void placeOrder(Order order) {
    orders.put(order.getId(), order);
    notifyObservers(order);          // 👈 যারা সাবস্ক্রাইব করেছে সবাই খবর পাবে
}
```

> **বাস্তব উপমা:** রেস্টুরেন্টে একটা **ঘণ্টা** 🔔। অর্ডার এলে ঘণ্টা বাজে — রান্নাঘর, ওয়েটার, ক্যাশিয়ার সবাই শুনতে পায়। ম্যানেজারকে আলাদা করে প্রত্যেককে গিয়ে বলতে হয় না।

**অর্ডারের অবস্থার প্রবাহ:**
```
গ্রাহক অর্ডার দিল      → PENDING
রান্নাঘর শুরু করল      → PREPARING
রান্না শেষ            → READY      (ওয়েটারকে খবর দাও 🔔)
টেবিলে পৌঁছাল         → COMPLETED
```

---

## ১০. Airline Management System ডিজাইন করুন ✈️

### ১. Aircraft
```
* model, totalSeats
```

### ২. Passenger
```
* id, name, email, phoneNumber
```

### ৩. Flight
```
Flight:
  * flightNumber, source, destination
  * departureTime, arrivalTime
  * List<Seat> availableSeats
  * সব getters

FlightSearch:
  * List<Flight> flights
  * searchFlight(source, destination, date)
```

### ৪. Seat
```
Seat:              number, seatType, seatStatus
SeatType (enum):   ECONOMY, PREMIUM_ECONOMY, BUSINESS_CLASS, FIRST_CLASS
SeatStatus (enum): AVAILABLE, RESERVED, OCCUPIED
```
> **RESERVED আর OCCUPIED-এর পার্থক্য:** RESERVED = বুক করা আছে। OCCUPIED = যাত্রী বোর্ড করেছেন।

### ৫. Booking
```
Booking:
  * id, passenger, flight, seat, bookingStatus, price
  * cancel(), getBookingId()

BookingManager:
  * instance, Map<String, Booking> bookings, lock
  * createBooking(), cancelBooking()

BookingStatus (enum): CONFIRMED, CANCELLED, PENDING, EXPIRED
```

### ৬. Payment
```
PaymentMethod (enum): CASH, CREDIT_CARD, MOBILE_PAY
Payment:              id, amount, method, status
PaymentStatus (enum): PENDING, COMPLETED, FAILED
PaymentProcessor:     instance, processPayment()
```

### ৭. AirlineManagementSystem
```
* List<Flight> flights, List<Aircraft> aircrafts
* flightSearch, bookingManager, paymentProcessor
* addFlight(), addAircraft(), searchFlight()
* bookFlight(), cancelFlight(), processPayment()
```

---

**`PENDING` আর `EXPIRED` স্ট্যাটাস দুটো কেন — বাস্তব দৃশ্য:**

```
আপনি সিট বাছলেন → অবস্থা PENDING, সিটটা ১৫ মিনিটের জন্য আটকে রাখা হলো
        ↓
পেমেন্ট করলেন    → CONFIRMED ✅
        ↓ (অথবা)
১৫ মিনিট পার হলো → EXPIRED, সিট আবার খালি হয়ে গেল
```

**এটা না থাকলে কী হতো:** কেউ সিট বেছে পেমেন্ট পেজে গিয়ে ব্রাউজার বন্ধ করে দিল → সিটটা **চিরকালের জন্য আটকে থাকত**, কেউ বুক করতে পারত না! 💀

**`BookingManager`-এ `lock` কেন আলাদা ফিল্ড:**
```java
// পুরো ক্লাসে একটা লক না দিয়ে, সিট-প্রতি লক দিন
private final Map<String, Object> seatLocks = new ConcurrentHashMap<>();

synchronized(seatLocks.get(seatId)) {
    // এখন শুধু এই সিটের জন্যই অপেক্ষা, অন্য সিটের বুকিং চলতে থাকবে ⚡
}
```

**ইন্টারভিউয়ারের প্রিয় প্রশ্ন:** "দুইজন একই সিট একই সেকেন্ডে বুক করলে?" → এই লকিংটাই উত্তর।

---

## ১১. Digital Wallet ডিজাইন করুন 💳

### ১. Account
```
* id, user, balance, accountNumber, currency
* List<Transaction> transactions
* deposit(BigDecimal amount), withdraw(BigDecimal amount)
* addTransaction(), সব getters
```

**⚠️ খুব গুরুত্বপূর্ণ — `BigDecimal` কেন, `double` কেন নয়:**
```java
double a = 0.1, b = 0.2;
System.out.println(a + b);      // 0.30000000000000004  😱
```
`double` বাইনারিতে দশমিক সংখ্যা ঠিকমতো রাখতে পারে না! টাকার হিসাবে এই সামান্য ভুল লক্ষ লক্ষ লেনদেনে জমে বিশাল গরমিল তৈরি করবে।

**নিয়ম: টাকার হিসাবে সবসময় `BigDecimal` (বা পয়সাকে integer হিসেবে) ব্যবহার করুন।** এটা ইন্টারভিউয়ে বললে আপনার নম্বর বেড়ে যাবে ✅

### ২. User
```
* id, name, email, phoneNumber, List<Account> accounts
* addAccount(), removeAccount(), getId()
```

### ৩. PaymentMethod (abstract class)
```
abstract class PaymentMethod {
    id, user
    abstract processPayment();
}
  ├── BankAccount:  accountNumber, routingNumber, processPayment()
  └── CreditCard:   cardNumber, expirationDate, cvv, processPayment()
```

### ৪. Currency (enum)
```
INR, USD, EUR, JPY
```

### ৫. CurrencyConverter
```
* Map<Currency, BigDecimal> exchangeRates
* convert()
```

### ৬. Transaction
```
* id, sourceAccount, destinationAccount, currency, amount, timestamp
* সব getters
```

### ৭. DigitalWallet — **Singleton**
```
ফিল্ড:
  * instance
  * Map<String, User> users
  * Map<String, Account> accounts
  * Map<String, PaymentMethod> paymentMethods

মেথড:
  * createUser(), getUser()
  * createAccount(), getAccount()
  * createPaymentMethod(), getPaymentMethod()
  * public synchronized void transferFunds(
        Account sourceAccount, Account destinationAccount,
        BigDecimal amount, Currency currency)
  * getTransactionHistory(Account account)
```

---

**`transferFunds()` — ACID-এর Atomicity মনে আছে?**
```java
synchronized void transferFunds(Account from, Account to, BigDecimal amount, Currency c) {
    // ১. যাচাই
    if (from.getBalance().compareTo(amount) < 0)
        throw new InsufficientFundsException();

    // ২. মুদ্রা রূপান্তর (দরকার হলে)
    BigDecimal converted = converter.convert(amount, from.getCurrency(), c);

    // ৩. দুইটা কাজ — হয় দুটোই, নয়তো একটাও নয়!
    from.withdraw(amount);
    to.deposit(converted);

    // ৪. ইতিহাসে লিখুন
    Transaction t = new Transaction(...);
    from.addTransaction(t);
    to.addTransaction(t);
}
```

**⚠️ Deadlock-এর ফাঁদ:**
```
থ্রেড ১: A → B টাকা পাঠাচ্ছে (A লক, তারপর B লক চায়)
থ্রেড ২: B → A টাকা পাঠাচ্ছে (B লক, তারপর A লক চায়)
                → দুজনেই চিরকাল অপেক্ষা! 💀
```
**সমাধান:** সবসময় **অ্যাকাউন্ট নম্বরের ক্রমে** লক নিন — ছোট আইডি আগে। তাহলে দুই থ্রেডই একই ক্রমে লক নেবে, deadlock হবে না। ✅

**`Transaction` ইতিহাস কেন অপরিবর্তনীয় (immutable) রাখবেন:** আর্থিক রেকর্ড কখনো মোছা বা বদলানো যাবে না — ভুল হলে একটা **উল্টো লেনদেন** (reversal) যোগ করতে হয়। এটাই ব্যাংকিংয়ের নিয়ম।

---

## ১২. BookMyShow ডিজাইন করুন 🎬

### ১. Movie
```
* id, title, description, duration
* সব getters
```

### ২. Theater
```
* id, name, location, List<Show> shows
```

### ৩. Show
```
* id, movie, theater, startTime, endTime
* Map<String, Seat> seats
* সব getters
```
> **খেয়াল করুন:** সিট `Show`-এর ভেতরে, `Theater`-এর ভেতরে নয়। কারণ একই থিয়েটারের একই সিট **দুপুরের শো-তে বুক, রাতের শো-তে খালি** থাকতে পারে!

### ৪. Booking
```
Booking:
  * id, user, show, List<Seat> seats, totalPrice, status
  * সব getters

BookingStatus (enum): PENDING, CONFIRMED, CANCELLED
```

### ৫. Seat
```
Seat:              id, row, column, type, price, status
SeatType (enum):   NORMAL, PREMIUM
SeatStatus (enum): AVAILABLE, BOOKED
```

### ৬. User
```
* id, name, email
```

### ৭. MovieTicketSystem — **Singleton**
```
ফিল্ড:
  * instance, List<Movie> movies, List<Theater> theaters
  * Map<String, Show> shows, Map<String, Booking> bookings

মেথড:
  * getInstance(), addMovie(), addTheater(), addShow()
  * getMovies(), getTheaters(), getShow(show)
  * public synchronized Booking bookTickets(User user, Show show, List<Seat> selectedSeats)
  * areSeatsAvailable(Show show, List<Seat> selectedSeats)
  * markSeatsAsBooked(Show show, List<Seat> selectedSeats)
  * calculateTotalPrice(List<Seat> selectedSeats)
  * generateBookingId()
  * confirmBooking(String bookingId), cancelBooking(String bookingId)
  * markSeatsAsAvailable(Show show, List<Seat> seats)
```

---

**`bookTickets()` — সবচেয়ে গুরুত্বপূর্ণ মেথড:**

```java
public synchronized Booking bookTickets(User user, Show show, List<Seat> seats) {
    // ১. সব সিট এখনো খালি আছে কিনা যাচাই
    if (!areSeatsAvailable(show, seats)) {
        throw new SeatsNotAvailableException();
    }
    // ২. সাথে সাথে দখল করুন
    markSeatsAsBooked(show, seats);
    // ৩. বুকিং বানান
    Booking booking = new Booking(generateBookingId(), user, show, seats,
                                  calculateTotalPrice(seats), PENDING);
    bookings.put(booking.getId(), booking);
    return booking;
}
```

**ধাপ ১ ও ২ কেন একই `synchronized` ব্লকে থাকতেই হবে:**
```
নাহলে:
  A: সিট ৫ খালি? হ্যাঁ ✅
  B: সিট ৫ খালি? হ্যাঁ ✅     ← A এখনো দখল করেনি!
  A: দখল করল
  B: দখল করল                 ← দুইজনেই সিট ৫ পেল! 💥
```
এই সমস্যাকে বলে **Check-Then-Act race condition** — ইন্টারভিউয়ে এটা উল্লেখ করলে ভালো নম্বর পাবেন।

**PENDING থেকে CONFIRMED:** বুকিং প্রথমে `PENDING` থাকে, পেমেন্ট সফল হলে `confirmBooking()` ডেকে `CONFIRMED` করা হয়। ১০ মিনিটে পেমেন্ট না হলে `cancelBooking()` + `markSeatsAsAvailable()`।

**⚠️ বড় স্কেলে:** `synchronized` পুরো সিস্টেম আটকে দেবে। তখন **Redis-এ distributed lock** বা **ডেটাবেজে `SELECT ... FOR UPDATE`** ব্যবহার করতে হয়।

---

## ১৩. Splitwise ডিজাইন করুন 💰

**বাস্তব প্রেক্ষাপট:** বন্ধুরা মিলে ঘুরতে গেলেন। কেউ হোটেলের বিল দিল, কেউ খাবারের। শেষে কে কাকে কত দেবে হিসাব করতে হবে।

### ১. Split (খরচ ভাগের ধরন)
```
abstract class Split {
    user, amount
    abstract getAmount(), setAmount(), getUser()
}
  ├── EqualSplit:   getAmount(), setAmount()          → সবাই সমান
  ├── ExactSplit:   getAmount()                        → নির্দিষ্ট টাকা
  └── PercentSplit: percentage, getAmount(), getPercentage()  → শতাংশে
```

**তিনটার বাস্তব উদাহরণ — ১,২০০ টাকার বিল, ৩ বন্ধু:**
| ধরন | কীভাবে ভাগ | উদাহরণ |
|---|---|---|
| `EqualSplit` | সবাই সমান | প্রত্যেকে ৪০০ টাকা |
| `ExactSplit` | যে যা খেয়েছে | করিম ৬০০, রহিম ৪০০, সাকিব ২০০ |
| `PercentSplit` | শতাংশে | করিম ৫০%, রহিম ৩০%, সাকিব ২০% |

### ২. Expense
```
* id, amount, description, paidBy, List<Split> splits
* addSplit(), সব getters
```

### ৩. Group
```
* id, name, List<User> members, List<Expense> expenses
* addMember(), addExpense(), সব getters
```

### ৪. User
```
* id, emailId, name, Map<String, Double> balances
* সব getters
```
> `balances` map-এ থাকে: "রহিমের সাথে আমার হিসাব +৫০০" মানে রহিম আমাকে ৫০০ দেবে।

### ৫. Transaction
```
* id, sender, receiver, amount
```

### ৬. SplitwiseService — **Singleton**
```
ফিল্ড:
  * instance, Map<String, User> users, Map<String, Group> groups

মেথড:
  * getInstance(), addUser(user), addGroup(group)
  * addExpense(String groupId, Expense expense)
  * splitExpense(Expense expense)
  * updateBalances(Expense expense)
  * updateBalance(User user1, User user2, double amount)
  * getBalanceKey(User user1, User user2)
  * settleBalance(String userId1, String userId2)
  * createTransaction(User sender, User receiver, double amount)
```

---

**`getBalanceKey()` কেন দরকার — একটা সূক্ষ্ম কিন্তু গুরুত্বপূর্ণ ব্যাপার:**

করিম ও রহিমের মধ্যে হিসাব রাখতে হবে। কিন্তু key কী হবে?
```
"করিম_রহিম"  নাকি  "রহিম_করিম"?
```
দুইটা আলাদা key হলে হিসাব দুই জায়গায় ছড়িয়ে যাবে! 😱

**সমাধান — সবসময় একই ক্রম:**
```java
String getBalanceKey(User u1, User u2) {
    // আইডি অনুযায়ী সাজিয়ে নিন — ক্রম যাই হোক, key একই হবে
    return u1.getId().compareTo(u2.getId()) < 0
         ? u1.getId() + ":" + u2.getId()
         : u2.getId() + ":" + u1.getId();
}
```

---

**`settleBalance()` — Splitwise-এর সবচেয়ে চতুর ফিচার:**

```
হিসাব দাঁড়াল:
  করিম → রহিমকে দেবে ৫০০
  রহিম → সাকিবকে দেবে ৫০০
  সাকিব → করিমকে দেবে ৫০০

মোট ৩টা লেনদেন। কিন্তু আসলে? সবাই সমান! কোনো টাকাই দেওয়ার দরকার নেই! ✅
```

আরেকটা উদাহরণ:
```
করিম → রহিমকে ৩০০
রহিম → সাকিবকে ৩০০
        ↓ সরলীকরণ
করিম → সাকিবকে ৩০০      (২টা লেনদেনের বদলে ১টা) ⚡
```

**অ্যালগরিদম (Debt Simplification):**
```
১. প্রত্যেকের নিট হিসাব বের করুন (পাওনা − দেনা)
২. যাদের নিট ঋণাত্মক (দেনাদার) আর ধনাত্মক (পাওনাদার) — দুই তালিকা বানান
৩. সবচেয়ে বড় দেনাদারকে সবচেয়ে বড় পাওনাদারের সাথে মেলান
৪. যতক্ষণ না সবার হিসাব শূন্য হয়
```
এটা একটা **greedy অ্যালগরিদম** — লেনদেনের সংখ্যা সর্বনিম্ন করে।

---

## ১৪. Snake & Ladder গেম ডিজাইন করুন 🎲

### ১. Snake
```
* start, end          → start > end (নিচে নামায়)
* getStart(), getEnd()
```

### ২. Ladder
```
* start, end          → start < end (উপরে ওঠায়)
* getStart(), getEnd()
```

### ৩. Dice
```
* minVal, maxVal
* getRoll()
```

### ৪. Board
```
* boardSize, List<Snake> snakes, List<Ladder> ladders
* initializeSnakesAndLadders()
* getBoardSize()
* getNewPositionAfterSnakeOrLadder()
```

### ৫. Player
```
* name, position
* getName(), getPosition(), setPosition()
```

### ৬. SnakeAndLadderGame
```
* board, List<Player> players, dice, currentPlayerIndex
* play(), isGameOver()
```

### ৭. GameManager — **Singleton**
```
* instance, List<SnakeAndLadderGame> games
* getInstance(), startNewGame()
```

---

**`getNewPositionAfterSnakeOrLadder()` — খেয়াল রাখার বিষয়:**

```java
int getNewPosition(int position) {
    for (Snake s : snakes)
        if (s.getStart() == position) return s.getEnd();
    for (Ladder l : ladders)
        if (l.getStart() == position) return l.getEnd();
    return position;
}
```

**⚠️ চেইন প্রতিক্রিয়া:** একটা সিঁড়ি বেয়ে ৪০-এ উঠলেন, কিন্তু ৪০ নম্বরেই একটা সাপের মুখ! নিয়ম অনুযায়ী চেইন হবে কিনা সেটা আগে ঠিক করুন।

**`play()`-এর মূল লুপ:**
```
যতক্ষণ না খেলা শেষ:
    বর্তমান খেলোয়াড় ডাইস ঘোরাল
    নতুন অবস্থান = পুরনো + ডাইস
    যদি নতুন অবস্থান > বোর্ডের আকার → চাল বাতিল (ঠিক ১০০-এ পৌঁছাতে হবে)
    সাপ বা সিঁড়ি চেক করুন
    অবস্থান আপডেট করুন
    ১০০-এ পৌঁছালে → জয়! 🎉
    নাহলে পরের খেলোয়াড়
```

**⚠️ `initializeSnakesAndLadders()`-এ যাচাই করুন:**
- সাপ: `start > end` হতেই হবে
- সিঁড়ি: `start < end` হতেই হবে
- একই ঘরে সাপের মুখ আর সিঁড়ির গোড়া থাকতে পারবে না
- ঘর ১-এ সাপ থাকতে পারবে না, শেষ ঘরে সিঁড়ি থাকতে পারবে না

**`GameManager` কেন Singleton:** একটা সার্ভারে একসাথে অনেক খেলা চলতে পারে — সবগুলো এক জায়গা থেকে ম্যানেজ করা।

---

## ১৫. Online Shopping System ডিজাইন করুন 🛒

### ১. Product
```
* id, name, price, description, quantity
* updateQuantity(), isAvailable(), সব getters
```

### ২. OrderItem
```
* product, quantity
* getProduct(), getQuantity()
```
> **`Product` আর `OrderItem` আলাদা কেন:** `Product` = ক্যাটালগে থাকা পণ্য। `OrderItem` = "এই অর্ডারে এই পণ্যের ৩টা"। একই পণ্য বিভিন্ন অর্ডারে বিভিন্ন পরিমাণে থাকতে পারে।

### ৩. OrderStatus (enum)
```
PENDING → PROCESSING → SHIPPING → DELIVERED
                    ↘ CANCELLED
```

### ৪. Order
```
* id, user, List<OrderItem> items, totalAmount, orderStatus
* calculateTotalAmount(), setStatus()
* getId(), getUser(), getItems(), getAmount(), getStatus()
```

### ৫. User
```
* id, name, email, password, List<Order> orders
* addOrder(), সব getters
```

### ৬. ShoppingCart
```
* Map<String, OrderItem> items
* addItem(), removeItem(), updateItemQuantity(), getItems(), clear()
```
> **Map কেন, List নয়:** একই পণ্য দুইবার যোগ করলে দুইটা এন্ট্রি না হয়ে **পরিমাণ বাড়া** উচিত। Map-এ `productId` key রাখলে সেটা সহজ। ✅

### ৭. Payment
```
Payment (interface): processPayment()
  └── CreditCardPayment: processPayment()
```

### ৮. OnlineShoppingService — **Singleton**
```
ফিল্ড:
  * instance
  * Map<String, User> users
  * Map<String, Product> products
  * Map<String, Order> orders

মেথড:
  * getInstance(), registerUser(), addUser()
  * addProduct(), getProduct(), searchProduct()
  * public synchronized Order placeOrder(User user, ShoppingCart cart, Payment payment)
  * getOrder(), generateOrderId()
```

---

**`placeOrder()` — ধাপে ধাপে যা করতে হবে:**
```java
synchronized Order placeOrder(User user, ShoppingCart cart, Payment payment) {
    // ১. স্টক যাচাই — কার্টে থাকা সব পণ্য এখনো আছে?
    for (OrderItem item : cart.getItems()) {
        if (!item.getProduct().isAvailable(item.getQuantity()))
            throw new OutOfStockException();
    }

    // ২. অর্ডার বানান
    Order order = new Order(generateOrderId(), user, cart.getItems(), PENDING);
    order.calculateTotalAmount();

    // ৩. পেমেন্ট করান
    if (!payment.processPayment(order.getAmount()))
        throw new PaymentFailedException();

    // ৪. স্টক কমান
    for (OrderItem item : cart.getItems())
        item.getProduct().updateQuantity(-item.getQuantity());

    // ৫. সংরক্ষণ ও কার্ট খালি
    orders.put(order.getId(), order);
    user.addOrder(order);
    cart.clear();
    order.setStatus(PROCESSING);
    return order;
}
```

**⚠️ ইন্টারভিউয়ে যেসব প্রশ্ন আসবে:**

**"স্টক যাচাই আর স্টক কমানোর মাঝে অন্য কেউ কিনে ফেললে?"**
→ এজন্যই `synchronized`। বড় স্কেলে ডেটাবেজ ট্রানজেকশন বা optimistic locking (version নম্বর) ব্যবহার করুন।

**"পেমেন্ট সফল হলো কিন্তু ধাপ ৪-এ সার্ভার ক্র্যাশ করল?"**
→ গ্রাহকের টাকা কাটা গেল কিন্তু অর্ডার হলো না! 💀 সমাধান: পুরোটা একটা **ডেটাবেজ ট্রানজেকশনে** রাখুন, বা **Saga pattern** ব্যবহার করুন (ব্যর্থ হলে টাকা ফেরত দেওয়ার ধাপ)।

**"ফ্ল্যাশ সেলে ১ লক্ষ মানুষ একসাথে?"**
→ `synchronized` দিয়ে হবে না। Redis-এ স্টক কাউন্টার রাখুন (atomic decrement), বা কিউ ব্যবহার করুন।

---

## ১৬. Ride Sharing App ডিজাইন করুন 🚕

### ১. Driver
```
* id, name, contact, licencePlate, location, status
* সব getters
```

### ২. DriverStatus (enum)
```
AVAILABLE, BUSY
```

### ৩. Location
```
* latitude, longitude
```

### ৪. Passenger
```
* id, name, contact, location
* সব getters
```

### ৫. Ride
```
* id, passenger, driver, source, destination, rideStatus, fare
* setDriver(), setStatus(), setFare(), সব getters
```

### ৬. RideStatus (enum)
```
REQUESTED → ACCEPTED → IN_PROGRESS → COMPLETED
                    ↘ CANCELLED
```

### ৭-৮. Payment
```
Payment:              id, ride, amount, paymentStatus
PaymentStatus (enum): PENDING, COMPLETE, FAILED
```

### ৯. RideService — **Singleton**
```
ফিল্ড:
  * instance
  * Map<String, Passenger> passengers
  * Map<String, Driver> drivers
  * Map<String, Ride> rides
  * Queue<Ride> requestedRides

মেথড:
  * addPassenger(), addDriver()
  * requestRide(Passenger passenger, Location source, Location destination)
  * acceptRide(Driver driver, Ride ride)
  * startRide(Ride ride), completeRide(Ride ride), cancelRide(Ride ride)
  * notifyDrivers(Ride ride)
  * notifyPassenger(Ride ride), notifyDriver(Ride ride)
  * calculateFare(Ride ride)
  * calculateDistance(), calculateDuration()
  * processPayment(), generateRideId()
```

---

**পুরো প্রবাহটা — একটা রাইডের যাত্রা:**
```
১. যাত্রী requestRide()          → Ride তৈরি, status = REQUESTED
                                    Queue-তে যোগ
২. notifyDrivers()               → কাছাকাছি সব ড্রাইভারকে খবর 🔔
৩. একজন ড্রাইভার acceptRide()    → status = ACCEPTED, driver.status = BUSY
   notifyPassenger()             → "আপনার গাড়ি আসছে!"
৪. ড্রাইভার এসে startRide()      → status = IN_PROGRESS
৫. গন্তব্যে completeRide()        → status = COMPLETED
                                    calculateFare() → processPayment()
                                    driver.status = AVAILABLE
```

**`notifyDrivers()` — সবচেয়ে চ্যালেঞ্জিং অংশ:**

**সহজ কিন্তু ভুল উপায়:** সব ড্রাইভারকে খবর পাঠান।
> ঢাকায় ৫০,০০০ ড্রাইভার আছে। উত্তরার একটা রাইডের জন্য মিরপুরের ড্রাইভারকে খবর দেওয়ার মানে হয় না! 😩

**সঠিক উপায় — জিওগ্রাফিক ইনডেক্সিং:**
```
১. যাত্রীর অবস্থান থেকে ৩ কিমি ব্যাসার্ধের মধ্যে ড্রাইভার খুঁজুন
২. তাদের মধ্যে AVAILABLE যারা
৩. দূরত্ব অনুযায়ী সাজিয়ে সবচেয়ে কাছের ৫ জনকে খবর দিন
```

এর জন্য ব্যবহার হয় **QuadTree**, **Geohash**, বা **Redis GEO** কমান্ড — সাধারণ `List` দিয়ে করলে প্রতিবার ৫০,০০০ ড্রাইভার স্ক্যান করতে হবে।

**`calculateFare()` — ভাড়ার সূত্র:**
```
ভাড়া = বেস ভাড়া
      + (দূরত্ব × প্রতি কিমি হার)
      + (সময় × প্রতি মিনিট হার)
      × সার্জ মাল্টিপ্লায়ার        ← ব্যস্ত সময়ে ১.৫x, ২x
```

**`calculateDistance()`** — সরলরেখার দূরত্ব (Haversine সূত্র) নাকি আসল রাস্তার দূরত্ব? বাস্তবে Google Maps API লাগে।

**⚠️ যেসব বিষয় ইন্টারভিউয়ার তুলবেন:**
- "দুই ড্রাইভার একসাথে accept করলে?" → `synchronized` বা atomic compare-and-set
- "কেউ accept না করলে?" → টাইমআউটের পর ব্যাসার্ধ বাড়িয়ে আবার চেষ্টা
- "ড্রাইভারের অবস্থান কত ঘন ঘন আপডেট হবে?" → প্রতি ৪-৫ সেকেন্ডে

---

## ১৭. Food Delivery Service ডিজাইন করুন 🍔

### ১. MenuItem
```
* id, name, description, price, isAvailable
* setAvailability()
```

### ২. Restaurant
```
* id, name, address, List<MenuItem> menu
* addMenuItem(), removeMenuItem(), getId(), getMenu()
```

### ৩. Customer
```
* id, name, email, phoneNumber
* সব getters
```

### ৪. DeliveryAgent
```
* id, name, phoneNumber, isAvailable
* setAvailable(), getId(), getAvailable()
```

### ৫. Order
```
OrderItem:
  * menuItem, quantity

Order:
  * id, customer, restaurant, List<OrderItem> items, status, deliveryAgent
  * addItem(), removeItem(), setStatus(), getStatus()
  * assignDeliveryAgent(), getId()

OrderStatus (enum):
  PENDING → CONFIRMED → PREPARING → OUT_FOR_DELIVERY → DELIVERED
                                                    ↘ CANCELLED
```

### ৬. FoodDeliveryService — **Singleton**
```
ফিল্ড:
  * instance
  * Map<String, Restaurant> restaurants
  * Map<String, Customer> customers
  * Map<String, Order> orders
  * Map<String, DeliveryAgent> agents

মেথড:
  * getInstance()
  * registerUser(), registerRestaurant(), registerDeliveryAgent()
  * getAvailableRestaurants()
  * placeOrder(String customerId, String restaurantId, List<OrderItem> items)
  * notifyRestaurant(Order order)
  * updateOrderStatus(String orderId, OrderStatus status)
  * notifyCustomer(Order order)
  * cancelOrder(String orderId)
  * assignDeliveryAgent(Order order), notifyDeliveryAgent(Order order)
  * generateOrderId()
```

---

**তিন পক্ষের সমন্বয় — এটাই এই ডিজাইনের মূল কথা:**

```
      ┌──────────┐
      │ গ্রাহক    │  অর্ডার দেয়, ট্র্যাক করে
      └────┬─────┘
           ↓
   ┌───────────────────┐
   │ FoodDeliveryService│  ← মধ্যস্থতাকারী (Mediator)
   └───┬───────────┬───┘
       ↓           ↓
  ┌─────────┐  ┌──────────────┐
  │রেস্টুরেন্ট│  │ ডেলিভারি এজেন্ট│
  └─────────┘  └──────────────┘
```

**এটাই Mediator Pattern।** তিন পক্ষ সরাসরি একে অপরের সাথে কথা বলে না — সবাই কেন্দ্রীয় সার্ভিসের মাধ্যমে যোগাযোগ করে।

> **বাস্তব উপমা:** বিমানবন্দরের **কন্ট্রোল টাওয়ার** 🗼। প্লেনগুলো একে অপরের সাথে সরাসরি কথা বলে না — সবাই টাওয়ারের সাথে কথা বলে। নাহলে বিশৃঙ্খলা হতো।

**অর্ডারের প্রতিটা ধাপে কী ঘটে:**
```
PENDING           → placeOrder(), notifyRestaurant() 🔔
CONFIRMED         → রেস্টুরেন্ট গ্রহণ করল, notifyCustomer() 🔔
PREPARING         → রান্না চলছে
                  → assignDeliveryAgent() + notifyDeliveryAgent() 🔔
                    (রান্না শেষের ঠিক আগে এজেন্ট পাঠানো হয়!)
OUT_FOR_DELIVERY  → এজেন্ট খাবার তুলে নিয়েছে, notifyCustomer() 🔔
DELIVERED         → পৌঁছে গেছে, agent.setAvailable(true)
```

**⚠️ `assignDeliveryAgent()` কখন ডাকবেন — একটা সূক্ষ্ম সিদ্ধান্ত:**
- **খুব আগে ডাকলে** → এজেন্ট রেস্টুরেন্টে বসে থাকবে, সময় নষ্ট
- **খুব দেরিতে ডাকলে** → খাবার তৈরি হয়ে ঠান্ডা হয়ে যাবে

বাস্তবে রান্নার আনুমানিক সময় হিসাব করে ঠিক সময়ে এজেন্ট পাঠানো হয়।

**`cancelOrder()`-এর নিয়ম:** `PREPARING` অবস্থায় চলে গেলে সাধারণত বাতিল করা যায় না (বা টাকা ফেরত পাওয়া যায় না) — কারণ রেস্টুরেন্টের খরচ হয়ে গেছে।

---

## 📌 দ্রুত রিভিশন চিট-শিট

### এই ১৭টা ডিজাইনে যেসব প্যাটার্ন ব্যবহার হয়েছে

| প্যাটার্ন | কোথায় | কেন |
|---|---|---|
| **Singleton** | প্রায় সব `...System`, `...Service` | পুরো অ্যাপে একটাই instance |
| **Strategy** | `PaymentProcessor`, `Payment` | আচরণ সহজে বদলানো |
| **State** | Vending Machine | অবস্থা অনুযায়ী আলাদা আচরণ |
| **Command** | ATM-এর `Transaction` | কাজকে object বানানো, ইতিহাস রাখা |
| **Observer** | `notifyKitchen()`, `notifyDrivers()` | একজনের পরিবর্তনে অনেকে খবর পাওয়া |
| **Factory** | `Vehicle` তৈরি | object তৈরির দায়িত্ব একজনের |
| **Mediator** | Food Delivery Service | অনেক পক্ষের সমন্বয় |

### প্রায় প্রতিটা ডিজাইনে যে ৩টা জিনিস থাকবেই

```
১. একটা মূল Entity ক্লাস       → Car, Room, Product, Seat
২. কয়েকটা enum (অবস্থা/ধরন)   → OrderStatus, SeatType
৩. একটা Service/System ক্লাস   → Singleton, সব ম্যানেজ করে
```

### `synchronized` কোথায় লাগবেই

যেখানে **"খালি আছে কিনা দেখা"** আর **"দখল করা"** — এই দুইটা কাজ পরপর হয়:
- সিট বুকিং 🎬✈️
- রুম বুকিং 🏨
- পার্কিং স্পট 🅿️
- স্টক কমানো 🛒
- টাকা তোলা 🏧

> **নিয়ম:** Check এবং Act **একই লকের ভেতরে** থাকতে হবে।

### ইন্টারভিউয়ে যেসব কথা বললে নম্বর বাড়ে

1. ✅ "টাকার জন্য `double` নয়, **`BigDecimal`** ব্যবহার করব" — নির্ভুলতার সমস্যা
2. ✅ "এখানে **check-then-act race condition** আছে, তাই lock দরকার"
3. ✅ "**Interface** ব্যবহার করছি যাতে ভবিষ্যতে নতুন ধরন যোগ করা সহজ হয়" (Open/Closed)
4. ✅ "**enum** ব্যবহার করছি, String নয় — টাইপ সেফটি পাওয়া যাবে"
5. ✅ "বড় স্কেলে `synchronized` bottleneck হবে, তখন **distributed lock** লাগবে"
6. ✅ "সব ফিল্ড `private`, শুধু getter দিয়ে অ্যাক্সেস" (Encapsulation)

### যে ভুলগুলো এড়িয়ে চলবেন

1. ❌ প্রশ্ন না করে সাথে সাথে কোড লেখা শুরু
2. ❌ সব কিছু একটা বিশাল ক্লাসে ঠেসে দেওয়া (God Object)
3. ❌ প্রয়োজন ছাড়াই ৫টা ডিজাইন প্যাটার্ন ঢোকানো
4. ❌ `String status = "PENDING"` — enum ব্যবহার করুন
5. ❌ Concurrency-র কথা একেবারেই না বলা
6. ❌ পাসওয়ার্ড সাদা টেক্সটে রাখা

### SOLID নীতিগুলো (এক লাইনে)

| | পুরো নাম | মানে |
|---|---|---|
| **S** | Single Responsibility | একটা ক্লাসের একটাই কাজ |
| **O** | Open/Closed | সম্প্রসারণে খোলা, পরিবর্তনে বন্ধ |
| **L** | Liskov Substitution | সন্তান ক্লাস পিতার জায়গায় বসতে পারবে |
| **I** | Interface Segregation | ছোট ছোট interface, বড় একটা নয় |
| **D** | Dependency Inversion | কংক্রিট ক্লাস নয়, interface-এর উপর নির্ভর করুন |
