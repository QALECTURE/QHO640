# QHO640 Contemporary Web Applications

# Week 5 Seminar: Build CampusBites with Next.js

**Student lab guide | 2 hours | Build from scratch | JavaScript + Next.js App Router**

> **Your mission:** The university wants a small food-ordering website for students between lectures. You have been asked to build the first prototype, **CampusBites**. Students must be able to browse the menu, search for meals, add food to a basket, and move between pages. Once the basic site works, you will improve it **independently** with new features.
>
> This is a **fictional learning project**. It does not accept real orders, take payments, contact restaurants, or store customer information.

## 1. What will you build?

By the end of the seminar, your browser should show:

- **Home** (`/`): a welcome page and a link to the menu.
- **Menu** (`/menu`): food cards with names, prices, a search box, and an interactive basket.
- **About** (`/about`): a short description of the student project.
- **API** (`/api/menu`): a simple endpoint that returns demonstration JSON.
- **Your own features**: four independent activities, including an **order progress tracker**.

### How this connects to the lectures

| Previously learned | Where you will use it today |
|---|---|
| JavaScript objects and arrays | Store meals and their prices |
| `filter()`, `map()`, `find()`, `reduce()` | Search meals, display cards, calculate totals |
| React components | `MealCard` and `MenuClient` |
| Props | Pass a meal into a reusable card |
| React `useState` Hook | Remember the search, basket, and your new features |
| React `useEffect` Hook | Optional/independent order tracker browser-title update |
| Next.js App Router | Home, Menu, About, and API routes |
| Server and Client Components | Server prepares meal data; client handles clicks |

**Think about it:** A student clicks **Add to basket**. Which part of the application changes? The meal data, the React state, or the URL?

<details><summary>Reveal the answer</summary>

The **React basket state** changes. The component renders again to show the new count and total. The URL does not need to change, and the original menu data stays the same.

</details>

---

## 2. Seminar schedule

| Time | Activity | Mode |
|---|---|---|
| 0–10 min | Discuss the scenario, check Node.js and npm | Together |
| 10–20 min | Create and run a new Next.js application | Together |
| 20–35 min | Build shared layout, Home, and About | Together |
| 35–50 min | Add menu data and a reusable meal card | Together |
| 50–70 min | Add search, basket state, and styling | Together |
| 70–80 min | Explore a simple API and test the app | Together |
| 80–90 min | **Activity 1:** Add meals and sold-out behaviour | **Independent** |
| 90–100 min | **Activity 2:** Sort the menu by price | **Independent** |
| 100–110 min | **Activity 3:** Build a favourites feature | **Independent** |
| 110–120 min | **Activity 4:** Build an order tracker (start in class; finish if needed) | **Independent** |

> If typing or installation takes longer, finish Activity 4 as an extension after class. **Activities 1–3 are the minimum required independent work**, and Activity 4 is the additional component challenge.

---

# PART A — Guided build: Follow your lecturer

## Step 1. Open VS Code and check your setup

1. Open **Visual Studio Code**.
2. Select **Terminal → New Terminal**.
3. Type these commands **one at a time**:

```bash
node --version
npm --version
```

You should see version numbers. If either command is not recognised, install a **supported Node.js LTS** version from <https://nodejs.org/> and reopen VS Code. Next.js may reject older Node.js versions; follow the version requirement shown by the installer.

**Ask yourself:** What is the difference between Node.js and npm?

<details><summary>Answer</summary>

Node.js runs JavaScript outside the browser. npm installs packages and runs project scripts.

</details>

## Step 2. Create the Next.js project

In the VS Code terminal, run:

```bash
npx create-next-app@latest campusbites
```

When the setup asks questions, select these options (the exact wording may vary):

| Setup option | Select |
|---|---|
| JavaScript or TypeScript | **JavaScript** |
| ESLint | **Yes** |
| Tailwind CSS | **No** |
| `src/` directory | **No** |
| App Router | **Yes** |
| React Compiler | **No**, if asked |
| Import alias | Keep default |

If you are offered a recommended-defaults shortcut, choose the option to **customise** so you can use the settings above.

Then run:

```bash
cd campusbites
code .
npm run dev
```

If `code .` is not recognised, use **File → Open Folder** in VS Code and select `campusbites`. Keep the terminal running.

Open the local URL printed in the terminal, usually:

<http://localhost:3000>

**Checkpoint:** You can see the default Next.js page. If port 3000 is occupied, use the alternative URL printed in the terminal.

### Important folder rule

This lab uses the **App Router**, where `app/menu/page.js` creates `/menu`. Some older tutorials use `pages/`; **do not mix the two routing styles in this lab**.

## Step 3. Create the project structure

In the VS Code Explorer, right-click a folder and select **New Folder** or **New File** as required. You will create these files:

```text
campusbites/
├── package.json
├── public/
└── app/
    ├── layout.js
    ├── page.js
    ├── globals.css
    ├── about/
    │   └── page.js
    ├── menu/
    │   └── page.js
    ├── components/
    │   ├── MealCard.js
    │   └── MenuClient.js
    └── api/
        └── menu/
            └── route.js
```

The `app`, `layout.js`, `page.js`, and `globals.css` files may already exist. **Replace their contents** when instructed below.

**Mini question:** What is the difference between `page.js` and `layout.js`?

<details><summary>Answer</summary>

A `page.js` defines a page for a URL. A `layout.js` provides shared structure around pages, such as navigation and a footer.

</details>

## Step 4. Build the shared website layout

Open **`app/layout.js`**, remove its current content, and type:

```jsx
import Link from "next/link";
import "./globals.css";

export const metadata = {
  title: "CampusBites",
  description: "A student-built Next.js food menu",
};

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <header className="header">
          <Link href="/" className="logo">CampusBites</Link>
          <nav aria-label="Main navigation">
            <Link href="/">Home</Link>
            <Link href="/menu">Menu</Link>
            <Link href="/about">About</Link>
          </nav>
        </header>

        <main className="container">{children}</main>

        <footer className="footer">
          Built by QHO640 students | Demo project
        </footer>
      </body>
    </html>
  );
}
```

**Understand these lines:**

- `Link` is the Next.js component for internal navigation.
- `href="/menu"` means **open the Menu route**.
- `{children}` is the current page inside the shared layout.
- `metadata` controls basic page title/description information.

**Checkpoint:** Save the file. You may see temporary errors until you complete the next files.

## Step 5. Create the Home page

Open **`app/page.js`**, replace its contents, and type:

```jsx
import Link from "next/link";

export default function HomePage() {
  return (
    <section className="hero">
      <p className="eyebrow">STUDENT FOOD PROJECT</p>
      <h1>Hungry between lectures?</h1>
      <p>Explore our campus menu and build your own basket.</p>
      <Link href="/menu" className="button">View today's menu</Link>
    </section>
  );
}
```

**Test:** Visit `/` and look for **Hungry between lectures?**. Click **View today's menu** after completing the Menu page.

## Step 6. Create the About page

Create **`app/about/page.js`** and type:

```jsx
export default function AboutPage() {
  return (
    <section>
      <h1>About CampusBites</h1>
      <p>We are learning Next.js by building a campus food website.</p>
      <p>This is a demonstration. No real food orders are placed.</p>
    </section>
  );
}
```

**Test:** Open <http://localhost:3000/about>. The page should show the same header and footer as Home.

**Ask:** Did we write the header a second time inside the About page?

<details><summary>Answer</summary>

No. The header is shared through `app/layout.js`.

</details>

## Step 7. Add the meal data and create the Menu route

Create **`app/menu/page.js`** and type:

```jsx
import MenuClient from "../components/MenuClient";

const meals = [
  { id: 1, name: "Chicken Wrap", price: 5.5, emoji: "🥪" },
  { id: 2, name: "Rice Bowl", price: 6.0, emoji: "🍚" },
  { id: 3, name: "Fruit Cup", price: 3.0, emoji: "🍓" },
];

export default function MenuPage() {
  return (
    <section>
      <h1>Today's menu</h1>
      <p>Choose your lunch and watch the basket update.</p>
      <MenuClient meals={meals} />
    </section>
  );
}
```

**Stop and explain:**

1. `meals` is a JavaScript **array**.
2. Each meal is an **object** with properties such as `name` and `price`.
3. `<MenuClient meals={meals} />` passes the array as a **prop**.
4. This page is a **Server Component by default**. We will use a Client Component for interactive behaviour.

**Console recap from earlier weeks:**

```javascript
const meals = [
  { id: 1, name: "Wrap", price: 5.5 },
  { id: 2, name: "Rice Bowl", price: 6.0 },
];

console.log(meals[0].name); // Wrap
```

**Checkpoint:** The Menu route may show a module-not-found error until you create `MenuClient.js`. That is expected at this stage.

## Step 8. Create a reusable MealCard component

Create **`app/components/MealCard.js`** and type:

```jsx
export default function MealCard({ meal, onAdd }) {
  return (
    <article className="meal-card">
      <div className="meal-emoji" aria-hidden="true">
        {meal.emoji}
      </div>
      <h2>{meal.name}</h2>
      <p className="price">£{meal.price.toFixed(2)}</p>
      <button type="button" onClick={() => onAdd(meal.id)}>
        Add to basket
      </button>
    </article>
  );
}
```

**Explain while typing:**

- `MealCard` is a reusable React component.
- `meal` is a prop containing the selected meal's data.
- `onAdd` is a function passed as a prop.
- `.toFixed(2)` displays a price with two decimal places.
- Clicking the button calls `onAdd(meal.id)`.

**Real-world connection:** A restaurant uses the same menu-card design for many different dishes. Only the information changes.

## Step 9. Add interactivity with `useState`

Create **`app/components/MenuClient.js`** and type the entire code below:

```jsx
"use client";

import { useState } from "react";
import MealCard from "./MealCard";

export default function MenuClient({ meals }) {
  const [search, setSearch] = useState("");
  const [basket, setBasket] = useState([]);

  // Week 3 recap: filter the array based on what the user types.
  const visibleMeals = meals.filter((meal) =>
    meal.name.toLowerCase().includes(search.toLowerCase())
  );

  // Store meal IDs in the basket. Repeated IDs mean multiple quantities.
  function addToBasket(mealId) {
    setBasket((previous) => [...previous, mealId]);
  }

  // Week 3 recap: find each meal and add its price to the total.
  const total = basket.reduce((sum, mealId) => {
    const meal = meals.find((item) => item.id === mealId);
    return sum + (meal?.price ?? 0);
  }, 0);

  return (
    <div>
      <div className="toolbar">
        <label htmlFor="meal-search">Search meals</label>
        <input
          id="meal-search"
          type="search"
          placeholder="Try wrap or rice"
          value={search}
          onChange={(event) => setSearch(event.target.value)}
        />
      </div>

      <div className="meal-grid">
        {visibleMeals.map((meal) => (
          <MealCard
            key={meal.id}
            meal={meal}
            onAdd={addToBasket}
          />
        ))}
      </div>

      {visibleMeals.length === 0 && (
        <p>No meals found. Try another search.</p>
      )}

      <section className="basket" aria-label="Your basket">
        <h2>Your basket</h2>
        <p>Items: <strong>{basket.length}</strong></p>
        <p>Total: <strong>£{total.toFixed(2)}</strong></p>
        <button type="button" onClick={() => setBasket([])}>
          Clear basket
        </button>
      </section>
    </div>
  );
}
```

### What just happened?

| Code | Meaning | Real-world example |
|---|---|---|
| `"use client"` | Makes this module a Client Component boundary | The browser handles the interactive menu |
| `useState("")` | Remembers the search text | Typing `wrap` |
| `useState([])` | Remembers basket meal IDs | Adding two wraps |
| `filter()` | Keeps matching meals | Show only wraps |
| `map()` | Creates a card for each visible meal | Display menu cards |
| `onClick` | Responds to a click | Add to basket |
| `reduce()` | Calculates a total | £5.50 + £5.50 = £11.00 |
| `key={meal.id}` | Gives each card a stable identity | Meal ID 1 is always the wrap |

**Important:** `MealCard.js` does not need its own `"use client"` line here because it is imported by the Client Component `MenuClient.js`.

### Checkpoint: Test the behaviour

1. Visit <http://localhost:3000/menu>.
2. Type `wrap` in the search box. Only Chicken Wrap should appear.
3. Clear the search. All three meals should return.
4. Click **Add to basket** on Chicken Wrap twice. The basket should show **2 items, £11.00**.
5. Click **Clear basket**. The basket should show **0 items, £0.00**.
6. Refresh the browser. The basket resets because it is not saved to a database or local storage.

## Step 10. Style the website

Open **`app/globals.css`**, remove its old content, and type:

```css
* { box-sizing: border-box; }
body { margin: 0; font-family: Arial, sans-serif; color: #192b3c; background: #f5f8fc; }
a { color: inherit; }
.header { display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 12px; padding: 18px 7%; color: white; background: #102c40; }
.logo { font-size: 23px; font-weight: bold; text-decoration: none; }
nav { display: flex; gap: 20px; }
nav a { text-decoration: none; }
nav a:hover { text-decoration: underline; }
.container { width: min(1000px, 90%); min-height: 72vh; margin: 32px auto; }
.hero { padding: 60px 35px; border-radius: 16px; background: #e2f1ff; }
.hero h1 { font-size: clamp(28px, 5vw, 45px); }
.eyebrow { font-weight: bold; color: #1566a5; letter-spacing: 2px; font-size: 12px; }
.button, button { display: inline-block; border: 0; border-radius: 8px; padding: 11px 15px; background: #1474bd; color: white; text-decoration: none; cursor: pointer; font-size: 15px; }
button:hover, .button:hover { background: #0e588e; }
button:disabled { background: #94a3b8; cursor: not-allowed; }
.toolbar { display: flex; flex-wrap: wrap; align-items: center; gap: 12px; margin: 20px 0; padding: 15px; background: white; border-radius: 12px; border: 1px solid #d9e5ee; }
.toolbar input, .toolbar select { padding: 10px; border: 1px solid #aebfcf; border-radius: 8px; font-size: 15px; }
.toolbar input { flex: 1; min-width: 180px; }
.meal-grid { display: grid; grid-template-columns: repeat(3, minmax(0, 1fr)); gap: 16px; }
.meal-card { padding: 18px; background: white; border: 1px solid #d9e5ee; border-radius: 14px; }
.meal-card h2 { font-size: 20px; }
.meal-emoji { font-size: 52px; padding: 14px; text-align: center; border-radius: 10px; background: #eef7ff; }
.price { font-weight: bold; color: #11609c; }
.basket { margin-top: 22px; padding: 18px; background: #eaf5ec; border-radius: 12px; }
.footer { padding: 18px 7%; background: #e5edf4; color: #475569; font-size: 13px; }
@media (max-width: 700px) { .meal-grid { grid-template-columns: 1fr; } .hero { padding: 35px 20px; } }
```

**Test:** Reduce your browser width. The meal cards should become a single column on narrow screens.

## Step 11. Create a simple Next.js API endpoint

Create **`app/api/menu/route.js`** and type:

```javascript
export function GET() {
  return Response.json({
    message: "Welcome to the CampusBites API",
    special: "Chicken Wrap",
    price: 5.5,
  });
}
```

Visit <http://localhost:3000/api/menu>.

You should see a JSON response. This demonstrates a **Next.js Route Handler**.

**Important:** The API currently returns fixed demonstration data. The Menu page does **not** fetch from this endpoint, and there is **no database**. We are keeping the first Next.js project simple.

### Guided build checklist

- [ ] I created the project myself using the terminal.
- [ ] Home (`/`) opens.
- [ ] About (`/about`) opens.
- [ ] Menu (`/menu`) displays three cards.
- [ ] Search filters the meal cards.
- [ ] Add to basket changes the count and total.
- [ ] Clear basket works.
- [ ] `/api/menu` returns JSON.
- [ ] I can explain why `MenuClient.js` needs `"use client"`.

---

# PART B — Independent student activities

**Stop following the lecturer's typing.** You are now the developer. Complete the tasks yourself, test each change, and be prepared to explain the code you wrote.

> **Rule:** Do not delete the existing search or basket functionality. Your new features must work alongside them.

## Activity 1 — Menu Manager: Add a meal and handle sold-out food

**Estimated time:** 10 minutes  
**Concepts:** Arrays, objects, props, conditional rendering, button attributes

**Scenario:** The campus café has added a **Pasta Box**, but the **Fruit Cup** is temporarily sold out. The website must reflect this.

### Your tasks

1. Open `app/menu/page.js`.
2. Add a fourth meal: **Pasta Box**, priced **£4.75**, with a new unique `id` and a food emoji.
3. Add an `available` property to **every** meal. Set Fruit Cup to `false`; the others should be `true`.
4. Open `app/components/MealCard.js`.
5. Display **Sold out** when `meal.available` is `false`.
6. Disable the Add to basket button for sold-out meals.
7. Confirm that the Pasta Box can still be searched and added to the basket.

**Hints:**

- A Boolean property can be `available: true` or `available: false`.
- Conditional text can be written as `{meal.available ? "Available" : "Sold out"}`.
- A button can use `disabled={!meal.available}`.

**Acceptance test:**

- [ ] Four meals appear.
- [ ] Searching `pasta` finds Pasta Box.
- [ ] Fruit Cup says **Sold out**.
- [ ] Fruit Cup cannot be added to the basket.
- [ ] Other meals still work.

**Think:** Why is disabling a button in the browser not enough to protect a real ordering system?

<details><summary>Concept answer</summary>

A real server must independently validate orders, prices, and availability. Browser controls alone can be bypassed. This exercise is a front-end simulation.

</details>

## Activity 2 — Price Sorter: Cheapest first / most expensive first

**Estimated time:** 10 minutes  
**Concepts:** `useState`, `sort()`, spread syntax, controlled inputs

**Scenario:** Students on a tight budget want to find the cheapest meal quickly.

### Your tasks

1. Open `app/components/MenuClient.js`.
2. Create a new state variable called `sortOrder` with a default value of `"default"`.
3. Add a `<select>` control near the search input with these options:
   - Default order
   - Price: low to high
   - Price: high to low
4. Create a **copy** of `visibleMeals` and sort that copy according to the selected option.
5. Render the sorted list instead of the original filtered list.
6. Check that searching and sorting work together.

**Hints:**

```javascript
const [sortOrder, setSortOrder] = useState("default");

// Make a copy before sorting. sort() changes the array it receives.
const sortedMeals = [...visibleMeals];

if (sortOrder === "low") {
  sortedMeals.sort((a, b) => a.price - b.price);
}
```

Add a `<select>` with `value={sortOrder}` and `onChange={(event) => setSortOrder(event.target.value)}`. Then use `sortedMeals.map(...)`.

**Acceptance test:**

- [ ] Default order shows the original order.
- [ ] Low to high places the cheapest meal first.
- [ ] High to low places the most expensive meal first.
- [ ] Search and sorting work at the same time.

**Think:** Why do we write `[...visibleMeals]` instead of directly sorting `visibleMeals`?

## Activity 3 — Favourites: Add a heart to each meal card

**Estimated time:** 10 minutes  
**Concepts:** `useState`, props, event handlers, `includes()`, arrays

**Scenario:** A student regularly orders the same lunch. They want to mark meals as favourites.

### Your tasks

1. In `MenuClient.js`, create a new state variable `favourites` starting as an empty array.
2. Write a function `toggleFavourite(mealId)` that:
   - removes the ID if it is already in the favourites array;
   - otherwise adds the ID.
3. Pass two new props into each `MealCard`:
   - `isFavourite`: whether the meal ID is in the favourites array;
   - `onFavourite`: the function to toggle it.
4. In `MealCard.js`, add a button that shows **♡ Favourite** or **♥ Favourited**.
5. Display the number of favourites somewhere above the menu.

**Hints:**

```javascript
const [favourites, setFavourites] = useState([]);

function toggleFavourite(mealId) {
  setFavourites((previous) =>
    previous.includes(mealId)
      ? previous.filter((id) => id !== mealId)
      : [...previous, mealId]
  );
}
```

**Acceptance test:**

- [ ] Each meal has a favourite button.
- [ ] Clicking once marks the meal as a favourite.
- [ ] Clicking again removes the favourite.
- [ ] The favourites count changes correctly.
- [ ] The basket and search still work.

**Think:** Why should we update the array with `setFavourites()` instead of using `favourites.push()`?

## Activity 4 — Extra component challenge: Order Progress Tracker

**Estimated time:** 10–15 minutes in class, complete afterwards if necessary  
**Concepts:** Components, `useState`, conditional rendering, `useEffect`

**Scenario:** After placing a demonstration order, a student wants to see its progress:

```text
Order placed  →  Preparing  →  Ready for collection
```

### Your tasks

1. Create a new file: **`app/components/OrderTracker.js`**.
2. Make a React component named `OrderTracker`.
3. Inside it, create an array:

```javascript
const steps = ["Order placed", "Preparing", "Ready for collection"];
```

4. Use `useState(0)` to remember the current step index.
5. Display the current step in a heading and all three stages in a list or progress bar.
6. Add a **Next stage** button that moves to the next step, but never beyond the last step.
7. In `MenuClient.js`, create an `orderPlaced` state variable starting at `false`.
8. Add a **Place demo order** button that becomes enabled only when the basket has at least one item. Clicking it should show `<OrderTracker />`.
9. **Extra Hook challenge:** Use `useEffect()` inside `OrderTracker` to update `document.title` when the order stage changes.

**Hints:**

```jsx
"use client";

import { useEffect, useState } from "react";

export default function OrderTracker() {
  const steps = ["Order placed", "Preparing", "Ready for collection"];
  const [step, setStep] = useState(0);

  // Your task: show steps[step], create the button,
  // and stop advancing at steps.length - 1.

  useEffect(() => {
    document.title = `CampusBites | ${steps[step]}`;
    return () => {
      document.title = "CampusBites";
    };
  }, [step]);

  return <section>{/* Build the tracker here */}</section>;
}
```

**Acceptance test:**

- [ ] The order button is disabled when the basket is empty.
- [ ] After adding food, **Place demo order** shows the tracker.
- [ ] The tracker starts at **Order placed**.
- [ ] The button advances to **Preparing**, then **Ready for collection**.
- [ ] It never moves beyond the final stage.
- [ ] If you implement `useEffect`, the browser tab title reflects the current stage.

**Real-world note:** This is a manually advanced simulation, **not live order tracking**. A real app would obtain status updates from a server.

### Optional visual enhancement

Add CSS classes such as `.step-active` to highlight the current stage. Use a CSS transition to animate the highlight when the stage changes. Avoid adding animation until the logic works.

---

# PART C — Debugging and evidence

## 12. Common errors and how to fix them

| Problem | Likely cause | Fix |
|---|---|---|
| `npm` is not recognised | Node.js/npm not installed | Install supported Node.js LTS; restart terminal |
| `useState` cannot be used in a Server Component | Missing Client Component boundary | Put `"use client"` at top of `MenuClient.js` or `OrderTracker.js` |
| `/menu` shows 404 | Wrong folder or filename | Check `app/menu/page.js` |
| Cannot resolve `./MealCard` | Wrong import or file location | Check `app/components/MealCard.js` |
| `Link` is not defined | Missing import | Add `import Link from "next/link";` |
| Search returns no items | Search value or filter condition wrong | Check `.toLowerCase()` and `includes()` |
| Sort does not update | State is not connected to select | Check `value`, `onChange`, and the rendered array |
| React warns about keys | Missing stable key | Use `key={meal.id}` |
| Clicking a button does nothing | Missing handler or wrong prop | Check `onClick={() => ...}` |
| OrderTracker not found | File/import mismatch | Check filename and import path |
| Browser shows port 3001 | Port 3000 is already occupied | Use the URL printed by `npm run dev` |

## 13. Final testing checklist

Before showing your work to the lecturer, test each feature:

- [ ] The project was created with Next.js, not Vite or Create React App.
- [ ] `/`, `/menu`, and `/about` work.
- [ ] Navigation is shared across pages.
- [ ] The menu is displayed using a reusable `MealCard` component.
- [ ] Searching `wrap` finds Chicken Wrap.
- [ ] The basket count and total change correctly.
- [ ] `/api/menu` returns JSON.
- [ ] **Activity 1:** new meal and sold-out behaviour work.
- [ ] **Activity 2:** price sorting works.
- [ ] **Activity 3:** favourites can be added and removed.
- [ ] **Activity 4:** order progress tracker advances correctly (or is marked for completion).
- [ ] No unresolved errors appear in the terminal or browser console.

## 14. Build and save your work

When you are finished, stop the development server with **Ctrl+C** and run:

```bash
npm run build
```

If the build reports an error, read the **first error message** and fix it before continuing.

Save your work in Git:

```bash
git status
git add .
git commit -m "Build CampusBites Next.js Week 5 seminar"
```

If you have already created and connected a GitHub repository, push your branch using the command appropriate for your setup (for example, `git push -u origin main`). Do not run that command until the remote repository and branch are configured.

### Evidence to show the lecturer

1. Home, Menu and About pages.
2. A working search and basket with a correct total.
3. Your completed independent activities.
4. The API JSON response.
5. A successful `npm run build` output, if your environment supports the build.

---

# PART D — Quick recap and extension

## 15. Five questions to discuss

1. Why does `app/menu/page.js` automatically create the `/menu` URL?
2. What does `"use client"` allow us to do?
3. What is the difference between **props** and **state** in CampusBites?
4. Why do we use `filter()` and `map()` together?
5. Does the `/api/menu` endpoint automatically connect to our menu cards?

<details><summary>Reveal the answers</summary>

1. Next.js App Router uses file-based routing.
2. It creates a Client Component boundary for interactive code using Hooks and event handlers.
3. Props pass meal data into components; state remembers changing values such as search, basket, and favourites.
4. `filter()` selects which meals should appear; `map()` renders a card for each selected meal.
5. No. It is a separate demonstration endpoint. Fetching data from it would require additional code.

</details>

## 16. Optional further challenges

If you finish early, try one of these without using a solution:

- **Budget filter:** Add a maximum-price input and display only meals that cost less than or equal to the selected budget.
- **Basket quantities:** Show how many of each meal were added, instead of displaying only the total number of items.
- **Theme toggle:** Create a dark-mode switch using `useState` and a CSS class.
- **Shared state discussion:** Explain how `useContext` might help if the basket count needed to appear in the header across multiple pages. You do not need to implement Context in this first lab.

## 17. Official learning resources

- [Next.js: Getting Started](https://nextjs.org/docs/app/getting-started)
- [Next.js: Layouts and Pages](https://nextjs.org/docs/app/getting-started/layouts-and-pages)
- [Next.js: Route Handlers](https://nextjs.org/docs/app/getting-started/route-handlers)
- [React: useState](https://react.dev/reference/react/useState)
- [React: useEffect](https://react.dev/reference/react/useEffect)
- [React: Rendering Lists](https://react.dev/learn/rendering-lists)

---

**End of Week 5 seminar.** You have built a small Next.js application from scratch and extended it using the React and JavaScript concepts from previous weeks.
