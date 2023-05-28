---
title: শর্তসাপেক্ষ Rendering
---

<Intro>

আপনার components গুলোতে প্রায়শই বিভিন্ন শর্তের উপর নির্ভর করে বিভিন্ন জিনিস প্রদর্শন করতে হবে।
React এর মধ্যে, আপনি শর্তসাপেক্ষে জাভাস্ক্রিপ্ট syntax ব্যবহার করে JSX render করতে পারেন যেমন `if` statements, `&&`, and `? :` operators.

</Intro>

<YouWillLearn>

* একটি শর্তের উপর নির্ভর করে কিভাবে বিভিন্ন JSX return দিতে হয়
* কিভাবে শর্তসাপেক্ষে JSX এর একটি অংশ অন্তর্ভুক্ত বা বাদ দিতে হয়
* বহুল ব্যবহৃত শর্তসাপেক্ষ syntax এর শর্টকাট যেগুলো আপনি React এর codebase এ প্রায়শই দেখতে পাবেন।

</YouWillLearn>

## শর্তসাপেক্ষে JSX Return করতেছে {/*conditionally-returning-jsx*/}

ধরা যাক আপনার একটি `PackingList` component আছে যেটা render করতেছে কিছু `Item`s, যেগুলো pack করা বা না করা হিসাবে চিহ্নিত করা যেতে পারে:

<Sandpack>

```js
function Item({ name, isPacked }) {
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

লক্ষ্য করুন যে কিছু `Item` components তাদের `isPacked` prop কে `false` এর পরিবর্তে `true` হিসেবে সেট করেছে। আপনি packed items গুলোতে একটি চেকমার্ক(✔) যোগ করতে চাচ্ছেন যদি `isPacked={true}` হয়।

আপনি নিম্নোক্তভাবে এটাকে একটা [`if`/`else` statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) হিসেবে লিখতে পারেন:

```js
if (isPacked) {
  return <li className="item">{name} ✔</li>;
}
return <li className="item">{name}</li>;
```

যদি `isPacked` prop টি `true` হয়, তাহলে এই code গুলো **একটা ভিন্ন JSX tree return করে।** এই পরিবর্তনের সাথে, কিছু আইটেমের শেষে একটি চেকমার্ক যুক্ত হয়:

<Sandpack>

```js
function Item({ name, isPacked }) {
  if (isPacked) {
    return <li className="item">{name} ✔</li>;
  }
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

উভয় ক্ষেত্রেই return হওয়া কোড edit করে দেখুন এবং দেখুন কিভাবে ফলাফল পরিবর্তন হয়ে যায়!

লক্ষ করুন, আপনি কীভাবে জাভাস্ক্রিপ্টের `if` এবং `return` statements ব্যবহার করে branching logic তৈরি করছেন। React এ JavaScript দ্বারা কন্ট্রোল ফ্লো (যেমন শর্তাদি) নিয়ন্ত্রণ করা হয়।

### `null` ব্যবহার করে শর্তানুযায়ী কোন কিছুই return না করা {/*conditionally-returning-nothing-with-null*/}

কিছু পরিস্থিতিতে, আপনি কোন কিছুই render করতে চাইবেন না। উদাহরণস্বরূপ, ধরুন আপনি packed items গুলোকে মোটেই দেখাতে চান না। যেহেতু, একটি component কে অবশ্যই কিছু না কিছু রিটার্ন করতে হবে। এই ক্ষেত্রে, আপনি `null` return করতে পারেন।

```js
if (isPacked) {
  return null;
}
return <li className="item">{name}</li>;
```

যদি `isPacked` true হয়, তাহলে component কিছুই return করবে না, `null`। অন্যথায়, এটি render করার জন্য JSX return করবে।

<Sandpack>

```js
function Item({ name, isPacked }) {
  if (isPacked) {
    return null;
  }
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

বাস্তবে, একটা component থেকে সাধারণত `null` return করা হয় না কারণ এটি render করার চেষ্টা করতেছে এমন ডেভেলপারকে এই ব্যাপারটি আশ্চর্য করতে পারে। প্রায়শই, আপনি শর্তসাপেক্ষে মূল component এর JSX-এ component টি অন্তর্ভুক্ত করবেন অথবা বাদ দিবেন। এখানে দেখুন কিভাবে এটি করতে হয়!

## শর্তানুযায়ী JSX এর অন্তর্ভুক্তিকরণ {/*conditionally-including-jsx*/}

আগের উদাহরণে, আপনি নিয়ন্ত্রণ করেছেন কোন (যদি থাকে!) JSX tree টা component দ্বারা return দেওয়া হবে। আপনি ইতিমধ্যেই render output কিছু পুনরাবৃত্তি লক্ষ্য করেছেন:

```js
<li className="item">{name} ✔</li>
```

অনেকটা অনুরূপ দ্বিতীয়টার সাথে

```js
<li className="item">{name}</li>
```

উভয় শর্তাধীন branches এটা return করে `<li className="item">...</li>`:

```js
if (isPacked) {
  return <li className="item">{name} ✔</li>;
}
return <li className="item">{name}</li>;
```

যদিও এই পুনরাবৃত্তি ক্ষতিকর নয়, তবে এটা আপনার code কে maintain করা কঠিন করে তুলতে পারে। ধরুন, আপনি `className` টা পরিবর্তন করতে চাচ্ছেন? আপনাকে আপনার code এর দুটি জায়গায় এই একই কাজটা করতে হবে! এইরকম পরিস্থিতিতে, আপনি শর্তানুযায়ী সামান্য কিছু JSX যোগ করে আপনার code কে আরও বেশী [DRY.](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) করতে পারেন।

### শর্তসাপেক্ষ (ternary) operator (`? :`) {/*conditional-ternary-operator--*/}

জাভাস্ক্রিপ্টে শর্তমূলক এক্সপ্রেশন লেখার জন্য একটি সংক্ষিপ্ত syntax রয়েছে। -- [conditional operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) or "ternary operator".

এটার পরিবর্তে:

```js
if (isPacked) {
  return <li className="item">{name} ✔</li>;
}
return <li className="item">{name}</li>;
```

আপনি এটি লিখতে পারেন:

```js
return (
  <li className="item">
    {isPacked ? name + ' ✔' : name}
  </li>
);
```

আপনি এটি এভাবে পড়তে পারেন *"if `isPacked` is true, then (`?`) render `name + ' ✔'`, otherwise (`:`) render `name`"*.

<DeepDive>

#### এই দুটি উদাহরণ কি সম্পূর্ণরূপে একই? {/*are-these-two-examples-fully-equivalent*/}

আপনি যদি একটি object-oriented programming ব্যাকগ্রাউন্ড থেকে এসে থাকেন, আপনি ভাবতে পারেন যে উপরের দুটি উদাহরণ সূক্ষ্মভাবে ভিন্ন কারণ তাদের মধ্যে একটি `<li>` এর দুটি ভিন্ন "instances" তৈরি করতে পারে। কিন্তু JSX এর উপাদানগুলি "instances" নয় কারণ তারা কোনও অভ্যন্তরীণ state কে ধরে রাখে না এবং তারা প্রকৃত DOM nodes নয়। এগুলি হল অনেকটা blueprints এর মতো হালকা বর্ণনা। সুতরাং এই দুটি উদাহরণ, আসলে, সম্পূর্ণরূপে একই। [Preserving and Resetting State](/learn/preserving-and-resetting-state) আরও ডিটেইলসে আলোচনা করে এটা কিভাবে কাজ করে সে সম্পর্কে।

</DeepDive>

এখন ধরা যাক আপনি সম্পূর্ণ আইটেমের text গুলোকে অন্য আর একটি HTML ট্যাগে মোড়াতে চান, যেমন `<del>` ট্যাগটি দ্বারা সেই টেক্সট গুলোকে মুছে ফেলার জন্য। আপনি আরও নতুন লাইন এবং বন্ধনী যোগ করতে পারেন যাতে প্রতিটি ক্ষেত্রে আরও JSX নেস্ট করা সহজ হয়:

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {isPacked ? (
        <del>
          {name + ' ✔'}
        </del>
      ) : (
        name
      )}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

এই স্টাইলটি সিম্পল শর্তগুলির জন্য ভালোভাবে কাজ করে, কিন্তু এটিকে পরিমিত পরিমাণে ব্যবহার করুন। যদি আপনার components গুলো খুব বেশি নেস্টেড কন্ডিশনাল মার্কআপের কারণে অগোছালো হয়ে যায়, সেক্ষেত্রে কোড ক্লিন রাখার জন্য চাইল্ড components গুলোকে extract করার ব্যাপারটি বিবেচনা করুন। React এ, মার্কআপ আপনার কোডের একটি অংশ, তাই আপনি জটিল expressions গুলোকে গুছিয়ে রাখতে variables এবং functions মতো টুলস গুলো ব্যবহার করতে পারেন।

### লজিক্যাল AND অপারেটর (`&&`) {/*logical-and-operator-*/}

আরেকটি সাধারণ শর্টকাট যা আপনি দেখে থাকবেন তা হলো [জাভাস্ক্রিপ্ট লজিক্যাল AND (`&&`) অপারেটর।](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND#:~:text=The%20logical%20AND%20(%20%26%26%20)%20operator,it%20returns%20a%20Boolean%20value.) React components গুলোর ভিতরে, যখন আপনি নির্দিষ্ট শর্তটি সত্য হলে কিছু JSX render করতে চান, **অথবা কিছুই render করতে চান না। ** `&&` এর মাধ্যমে, আপনি শর্তসাপেক্ষে চেকমার্ক render করতে পারেন শুধুমাত্র যদি `isPacked` `সত্য` হয়:

```js
return (
  <li className="item">
    {name} {isPacked && '✔'}
  </li>
);
```

আপনি এটিকে এভাবে পড়তে পারেন *"if `isPacked`, then (`&&`)  চেকমার্ক render করুন, অন্যথায়, কিছুই render করবেন না।"*.

এটির কার্যপ্রক্রিয়া এখানে দেখানো হলো:

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked && '✔'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

একটি [জাভাস্ক্রিপ্ট && এক্সপ্রেশন](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND) তার ডান দিকের মান (আমাদের ক্ষেত্রে, চেকমার্ক) return করে যদি বাম দিকে (আমাদের শর্ত) `সত্য` হয়। But if the condition is `false`, the whole expression becomes `false`. React considers `false` as a "hole" in the JSX tree, just like `null` or `undefined`, and doesn't render anything in its place.


<Pitfall>

**Don't put numbers on the left side of `&&`.**

To test the condition, JavaScript converts the left side to a boolean automatically. However, if the left side is `0`, then the whole expression gets that value (`0`), and React will happily render `0` rather than nothing.

For example, a common mistake is to write code like `messageCount && <p>New messages</p>`. It's easy to assume that it renders nothing when `messageCount` is `0`, but it really renders the `0` itself!

To fix it, make the left side a boolean: `messageCount > 0 && <p>New messages</p>`.

</Pitfall>

### Conditionally assigning JSX to a variable {/*conditionally-assigning-jsx-to-a-variable*/}

When the shortcuts get in the way of writing plain code, try using an `if` statement and a variable. You can reassign variables defined with [`let`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let), so start by providing the default content you want to display, the name:

```js
let itemContent = name;
```

Use an `if` statement to reassign a JSX expression to `itemContent` if `isPacked` is `true`:

```js
if (isPacked) {
  itemContent = name + " ✔";
}
```

[Curly braces open the "window into JavaScript".](/learn/javascript-in-jsx-with-curly-braces#using-curly-braces-a-window-into-the-javascript-world) Embed the variable with curly braces in the returned JSX tree, nesting the previously calculated expression inside of JSX:

```js
<li className="item">
  {itemContent}
</li>
```

This style is the most verbose, but it's also the most flexible. Here it is in action:

<Sandpack>

```js
function Item({ name, isPacked }) {
  let itemContent = name;
  if (isPacked) {
    itemContent = name + " ✔";
  }
  return (
    <li className="item">
      {itemContent}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Like before, this works not only for text, but for arbitrary JSX too:

<Sandpack>

```js
function Item({ name, isPacked }) {
  let itemContent = name;
  if (isPacked) {
    itemContent = (
      <del>
        {name + " ✔"}
      </del>
    );
  }
  return (
    <li className="item">
      {itemContent}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

If you're not familiar with JavaScript, this variety of styles might seem overwhelming at first. However, learning them will help you read and write any JavaScript code -- and not just React components! Pick the one you prefer for a start, and then consult this reference again if you forget how the other ones work.

<Recap>

* In React, you control branching logic with JavaScript.
* You can return a JSX expression conditionally with an `if` statement.
* You can conditionally save some JSX to a variable and then include it inside other JSX by using the curly braces.
* In JSX, `{cond ? <A /> : <B />}` means *"if `cond`, render `<A />`, otherwise `<B />`"*.
* In JSX, `{cond && <A />}` means *"if `cond`, render `<A />`, otherwise nothing"*.
* The shortcuts are common, but you don't have to use them if you prefer plain `if`.

</Recap>



<Challenges>

#### Show an icon for incomplete items with `? :` {/*show-an-icon-for-incomplete-items-with--*/}

Use the conditional operator (`cond ? a : b`) to render a ❌ if `isPacked` isn’t `true`.

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked && '✔'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

<Solution>

<Sandpack>

```js
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {name} {isPacked ? '✔' : '❌'}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

</Solution>

#### Show the item importance with `&&` {/*show-the-item-importance-with-*/}

In this example, each `Item` receives a numerical `importance` prop. Use the `&&` operator to render "_(Importance: X)_" in italics, but only for items that have non-zero importance. Your item list should end up looking like this:

* Space suit _(Importance: 9)_
* Helmet with a golden leaf
* Photo of Tam _(Importance: 6)_

Don't forget to add a space between the two labels!

<Sandpack>

```js
function Item({ name, importance }) {
  return (
    <li className="item">
      {name}
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          importance={9} 
          name="Space suit" 
        />
        <Item 
          importance={0} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          importance={6} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

<Solution>

This should do the trick:

<Sandpack>

```js
function Item({ name, importance }) {
  return (
    <li className="item">
      {name}
      {importance > 0 && ' '}
      {importance > 0 &&
        <i>(Importance: {importance})</i>
      }
    </li>
  );
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
      <ul>
        <Item 
          importance={9} 
          name="Space suit" 
        />
        <Item 
          importance={0} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          importance={6} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

</Sandpack>

Note that you must write `importance > 0 && ...` rather than `importance && ...` so that if the `importance` is `0`, `0` isn't rendered as the result!

In this solution, two separate conditions are used to insert a space between the name and the importance label. Alternatively, you could use a fragment with a leading space: `importance > 0 && <> <i>...</i></>` or add a space immediately inside the `<i>`:  `importance > 0 && <i> ...</i>`.

</Solution>

#### Refactor a series of `? :` to `if` and variables {/*refactor-a-series-of---to-if-and-variables*/}

This `Drink` component uses a series of `? :` conditions to show different information depending on whether the `name` prop is `"tea"` or `"coffee"`. The problem is that the information about each drink is spread across multiple conditions. Refactor this code to use a single `if` statement instead of three `? :` conditions.

<Sandpack>

```js
function Drink({ name }) {
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{name === 'tea' ? 'leaf' : 'bean'}</dd>
        <dt>Caffeine content</dt>
        <dd>{name === 'tea' ? '15–70 mg/cup' : '80–185 mg/cup'}</dd>
        <dt>Age</dt>
        <dd>{name === 'tea' ? '4,000+ years' : '1,000+ years'}</dd>
      </dl>
    </section>
  );
}

export default function DrinkList() {
  return (
    <div>
      <Drink name="tea" />
      <Drink name="coffee" />
    </div>
  );
}
```

</Sandpack>

Once you've refactored the code to use `if`, do you have further ideas on how to simplify it?

<Solution>

There are multiple ways you could go about this, but here is one starting point:

<Sandpack>

```js
function Drink({ name }) {
  let part, caffeine, age;
  if (name === 'tea') {
    part = 'leaf';
    caffeine = '15–70 mg/cup';
    age = '4,000+ years';
  } else if (name === 'coffee') {
    part = 'bean';
    caffeine = '80–185 mg/cup';
    age = '1,000+ years';
  }
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{part}</dd>
        <dt>Caffeine content</dt>
        <dd>{caffeine}</dd>
        <dt>Age</dt>
        <dd>{age}</dd>
      </dl>
    </section>
  );
}

export default function DrinkList() {
  return (
    <div>
      <Drink name="tea" />
      <Drink name="coffee" />
    </div>
  );
}
```

</Sandpack>

Here the information about each drink is grouped together instead of being spread across multiple conditions. This makes it easier to add more drinks in the future.

Another solution would be to remove the condition altogether by moving the information into objects:

<Sandpack>

```js
const drinks = {
  tea: {
    part: 'leaf',
    caffeine: '15–70 mg/cup',
    age: '4,000+ years'
  },
  coffee: {
    part: 'bean',
    caffeine: '80–185 mg/cup',
    age: '1,000+ years'
  }
};

function Drink({ name }) {
  const info = drinks[name];
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{info.part}</dd>
        <dt>Caffeine content</dt>
        <dd>{info.caffeine}</dd>
        <dt>Age</dt>
        <dd>{info.age}</dd>
      </dl>
    </section>
  );
}

export default function DrinkList() {
  return (
    <div>
      <Drink name="tea" />
      <Drink name="coffee" />
    </div>
  );
}
```

</Sandpack>

</Solution>

</Challenges>
