# 🚀 JavaScript Promise Combinators

## 🔹 Introduction
JavaScript provides several **Promise combinators** to handle multiple async tasks.  
They differ in **when they resolve/reject** and what results they return.  

---

## 1. `Promise.all`
- ✅ Resolves when **all promises resolve** → returns array of values.  
- ❌ Rejects immediately if **any promise rejects** (fail fast).  

```js
const p1 = Promise.resolve(10);
const p2 = Promise.resolve(20);
const p3 = Promise.reject("fail");

Promise.all([p1, p2])
  .then(values => console.log("✅ all resolved:", values)); // [10, 20]

Promise.all([p1, p2, p3])
  .then(values => console.log(values))
  .catch(err => console.log("❌ one failed:", err)); // "fail"
```

👉 **Use case:** When tasks are **independent** but you need **all results**.

---

## 2. `Promise.allSettled`
- Waits until **all promises settle** (resolved or rejected).  
- Never short-circuits.  
- Returns array of `{ status, value | reason }`.  

```js
const p1 = Promise.resolve(42);
const p2 = Promise.reject("error");
const p3 = Promise.resolve(100);

Promise.allSettled([p1, p2, p3])
  .then(results => console.log(results));
/*
[
  { status: "fulfilled", value: 42 },
  { status: "rejected", reason: "error" },
  { status: "fulfilled", value: 100 }
]
*/
```

👉 **Use case:** Collect **all outcomes**, e.g., logging, retries, batch jobs.  

---

## 3. `Promise.race`
- Resolves/rejects with the **first settled promise**.  
- Doesn’t wait for others.  

```js
const slow = new Promise(res => setTimeout(() => res("slow"), 2000));
const fast = new Promise(res => setTimeout(() => res("fast"), 1000));

Promise.race([slow, fast])
  .then(value => console.log(value)); // "fast"
```

👉 **Use case:** **Timeouts**, "first responder wins" scenarios.  

---

## 4. `Promise.any`
- Resolves with the **first fulfilled promise**.  
- Ignores rejections unless **all reject** → then rejects with `AggregateError`.  

```js
const p1 = Promise.reject("fail1");
const p2 = Promise.reject("fail2");
const p3 = Promise.resolve("success");

Promise.any([p1, p2, p3])
  .then(value => console.log(value)) // "success"
  .catch(err => console.log(err));   // AggregateError if all fail
```

👉 **Use case:** Only need **first success**, e.g., redundant API requests.  

---

## 🔹 Comparison Table

| Method              | Resolves When                                | Rejects When                              | Use Case |
|---------------------|-----------------------------------------------|-------------------------------------------|----------|
| `Promise.all`       | All promises resolve → array of results       | Any one rejects (immediate)                | Parallel tasks, need **all results** |
| `Promise.allSettled`| All promises settle (resolve/reject)          | Never rejects                              | Collect all outcomes |
| `Promise.race`      | First promise settles (resolve/reject)        | First rejection (if it comes first)        | Timeouts, fastest response |
| `Promise.any`       | First promise **fulfills**                    | All reject → `AggregateError`              | First success is enough |

---

## 🔹 Interview Mnemonics
- **All = All must succeed** (fail fast).  
- **AllSettled = Everyone reports in** (success + failure).  
- **Race = Winner decides** (first settle wins).  
- **Any = First success wins** (ignore losers unless all fail).  

---

## 🔹 Tricky Edge Cases & Interview Questions

### 1. What happens if you pass an **empty array**?
- `Promise.all([])` → Resolves immediately with `[]`.  
- `Promise.allSettled([])` → Resolves immediately with `[]`.  
- `Promise.race([])` → Stays **pending forever**.  
- `Promise.any([])` → Rejects immediately with `AggregateError: All promises were rejected`.

---

### 2. What if you pass **non-Promise values**?
All combinators treat non-Promise values as **already resolved Promises**.  

```js
Promise.all([1, 2, 3]).then(console.log); // [1, 2, 3]
Promise.any([1, Promise.reject("x")]).then(console.log); // 1
```

---

### 3. Difference between `Promise.race` and `Promise.any`?
- `race` → First settled (resolve OR reject).  
- `any` → First fulfilled (ignores rejections unless all reject).  

---

### 4. How to implement a **timeout with `Promise.race`**?
```js
function withTimeout(promise, ms) {
  const timeout = new Promise((_, reject) =>
    setTimeout(() => reject(new Error("⏰ Timeout!")), ms)
  );
  return Promise.race([promise, timeout]);
}
```

---

### 5. Which combinator ensures **no unhandled rejection warnings**?
- `Promise.allSettled` ensures all rejections are captured inside `{ status: "rejected" }`.  

---

✅ Quick recap before interview:
- Use **`all`** for all successes.  
- Use **`allSettled`** to always get results.  
- Use **`race`** for fastest result.  
- Use **`any`** for first success.  
- Remember tricky cases: empty array, non-promise values, timeout pattern.  

---
---

## 🔹 Polyfills for Promise Combinators

### 1. `Promise.all` Polyfill
```js
Promise.myAll = function (promises) {
  return new Promise((resolve, reject) => {
    let results = [];
    let completed = 0;

    if (promises.length === 0) return resolve([]);

    promises.forEach((p, i) => {
      Promise.resolve(p).then(val => {
        results[i] = val;
        completed++;
        if (completed === promises.length) {
          resolve(results);
        }
      }).catch(reject); // fail fast
    });
  });
};

---
Promise.myAllSettled = function (promises) {
  return new Promise((resolve) => {
    let results = [];
    let completed = 0;

    if (promises.length === 0) return resolve([]);

    promises.forEach((p, i) => {
      Promise.resolve(p)
        .then(val => {
          results[i] = { status: "fulfilled", value: val };
        })
        .catch(err => {
          results[i] = { status: "rejected", reason: err };
        })
        .finally(() => {
          completed++;
          if (completed === promises.length) {
            resolve(results);
          }
        });
    });
  });
};

---
Promise.myRace = function (promises) {
  return new Promise((resolve, reject) => {
    promises.forEach(p => {
      Promise.resolve(p).then(resolve).catch(reject);
    });
  });
};

---
Promise.myAny = function (promises) {
  return new Promise((resolve, reject) => {
    let errors = [];
    let rejectedCount = 0;

    if (promises.length === 0) {
      return reject(new AggregateError([], "All promises were rejected"));
    }

    promises.forEach((p, i) => {
      Promise.resolve(p)
        .then(resolve)
        .catch(err => {
          errors[i] = err;
          rejectedCount++;
          if (rejectedCount === promises.length) {
            reject(new AggregateError(errors, "All promises were rejected"));
          }
        });
    });
  });
};
---

