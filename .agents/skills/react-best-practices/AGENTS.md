# React Best Practices

**Version 1.0.0**  
Vercel Engineering  
January 2026

> **Note:**  
> This document is mainly for agents and LLMs to follow when maintaining,  
> generating, or refactoring React and Next.js codebases. Humans  
> may also find it useful, but guidance here is optimized for automation  
> and consistency by AI-assisted workflows.

---

## Abstract

Comprehensive performance optimization guide for React and Next.js applications, designed for AI agents and LLMs. Contains 40+ rules across 8 categories, prioritized by impact from critical (eliminating waterfalls, reducing bundle size) to incremental (advanced patterns). Each rule includes detailed explanations, real-world examples comparing incorrect vs. correct implementations, and specific impact metrics to guide automated refactoring and code generation.

---

## Table of Contents

1. [Eliminating Waterfalls](#1-eliminating-waterfalls) — **CRITICAL**
   - 1.1 [Check Cheap Conditions Before Async Flags](#11-check-cheap-conditions-before-async-flags)
   - 1.2 [Defer Await Until Needed](#12-defer-await-until-needed)
   - 1.3 [Dependency-Based Parallelization](#13-dependency-based-parallelization)
   - 1.4 [Prevent Waterfall Chains in API Routes](#14-prevent-waterfall-chains-in-api-routes)
   - 1.5 [Promise.all() for Independent Operations](#15-promiseall-for-independent-operations)
   - 1.6 [Strategic Suspense Boundaries](#16-strategic-suspense-boundaries)
2. [Bundle Size Optimization](#2-bundle-size-optimization) — **CRITICAL**
   - 2.1 [Avoid Barrel File Imports](#21-avoid-barrel-file-imports)
   - 2.2 [Conditional Module Loading](#22-conditional-module-loading)
   - 2.3 [Defer Non-Critical Third-Party Libraries](#23-defer-non-critical-third-party-libraries)
   - 2.4 [Dynamic Imports for Heavy Components](#24-dynamic-imports-for-heavy-components)
   - 2.5 [Prefer Statically Analyzable Paths](#25-prefer-statically-analyzable-paths)
   - 2.6 [Preload Based on User Intent](#26-preload-based-on-user-intent)
3. [Server-Side Performance](#3-server-side-performance) — **HIGH**
   - 3.1 [Authenticate Server Actions Like API Routes](#31-authenticate-server-actions-like-api-routes)
   - 3.2 [Avoid Duplicate Serialization in RSC Props](#32-avoid-duplicate-serialization-in-rsc-props)
   - 3.3 [Avoid Shared Module State for Request Data](#33-avoid-shared-module-state-for-request-data)
   - 3.4 [Cross-Request LRU Caching](#34-cross-request-lru-caching)
   - 3.5 [Hoist Static I/O to Module Level](#35-hoist-static-io-to-module-level)
   - 3.6 [Minimize Serialization at RSC Boundaries](#36-minimize-serialization-at-rsc-boundaries)
   - 3.7 [Parallel Data Fetching with Component Composition](#37-parallel-data-fetching-with-component-composition)
   - 3.8 [Parallel Nested Data Fetching](#38-parallel-nested-data-fetching)
   - 3.9 [Per-Request Deduplication with React.cache()](#39-per-request-deduplication-with-reactcache)
   - 3.10 [Use after() for Non-Blocking Operations](#310-use-after-for-non-blocking-operations)
4. [Client-Side Data Fetching](#4-client-side-data-fetching) — **MEDIUM-HIGH**
   - 4.1 [Deduplicate Global Event Listeners](#41-deduplicate-global-event-listeners)
   - 4.2 [Use Passive Event Listeners for Scrolling Performance](#42-use-passive-event-listeners-for-scrolling-performance)
   - 4.3 [Use SWR for Automatic Deduplication](#43-use-swr-for-automatic-deduplication)
   - 4.4 [Version and Minimize localStorage Data](#44-version-and-minimize-localstorage-data)
5. [Re-render Optimization](#5-re-render-optimization) — **MEDIUM**
   - 5.1 [Calculate Derived State During Rendering](#51-calculate-derived-state-during-rendering)
   - 5.2 [Defer State Reads to Usage Point](#52-defer-state-reads-to-usage-point)
   - 5.3 [Do not wrap a simple expression with a primitive result type in useMemo](#53-do-not-wrap-a-simple-expression-with-a-primitive-result-type-in-usememo)
   - 5.4 [Don't Define Components Inside Components](#54-dont-define-components-inside-components)
   - 5.5 [Extract Default Non-primitive Parameter Value from Memoized Component to Constant](#55-extract-default-non-primitive-parameter-value-from-memoized-component-to-constant)
   - 5.6 [Extract to Memoized Components](#56-extract-to-memoized-components)
   - 5.7 [Narrow Effect Dependencies](#57-narrow-effect-dependencies)
   - 5.8 [Put Interaction Logic in Event Handlers](#58-put-interaction-logic-in-event-handlers)
   - 5.9 [Split Combined Hook Computations](#59-split-combined-hook-computations)
   - 5.10 [Subscribe to Derived State](#510-subscribe-to-derived-state)
   - 5.11 [Use Functional setState Updates](#511-use-functional-setstate-updates)
   - 5.12 [Use Lazy State Initialization](#512-use-lazy-state-initialization)
   - 5.13 [Use Transitions for Non-Urgent Updates](#513-use-transitions-for-non-urgent-updates)
   - 5.14 [Use useDeferredValue for Expensive Derived Renders](#514-use-usedeferredvalue-for-expensive-derived-renders)
   - 5.15 [Use useRef for Transient Values](#515-use-useref-for-transient-values)
6. [Rendering Performance](#6-rendering-performance) — **MEDIUM**
   - 6.1 [Animate SVG Wrapper Instead of SVG Element](#61-animate-svg-wrapper-instead-of-svg-element)
   - 6.2 [CSS content-visibility for Long Lists](#62-css-content-visibility-for-long-lists)
   - 6.3 [Hoist Static JSX Elements](#63-hoist-static-jsx-elements)
   - 6.4 [Optimize SVG Precision](#64-optimize-svg-precision)
   - 6.5 [Prevent Hydration Mismatch Without Flickering](#65-prevent-hydration-mismatch-without-flickering)
   - 6.6 [Suppress Expected Hydration Mismatches](#66-suppress-expected-hydration-mismatches)
   - 6.7 [Use Activity Component for Show/Hide](#67-use-activity-component-for-showhide)
   - 6.8 [Use defer or async on Script Tags](#68-use-defer-or-async-on-script-tags)
   - 6.9 [Use Explicit Conditional Rendering](#69-use-explicit-conditional-rendering)
   - 6.10 [Use React DOM Resource Hints](#610-use-react-dom-resource-hints)
   - 6.11 [Use useTransition Over Manual Loading States](#611-use-usetransition-over-manual-loading-states)
7. [JavaScript Performance](#7-javascript-performance) — **LOW-MEDIUM**
   - 7.1 [Avoid Layout Thrashing](#71-avoid-layout-thrashing)
   - 7.2 [Build Index Maps for Repeated Lookups](#72-build-index-maps-for-repeated-lookups)
   - 7.3 [Cache Property Access in Loops](#73-cache-property-access-in-loops)
   - 7.4 [Cache Repeated Function Calls](#74-cache-repeated-function-calls)
   - 7.5 [Cache Storage API Calls](#75-cache-storage-api-calls)
   - 7.6 [Combine Multiple Array Iterations](#76-combine-multiple-array-iterations)
   - 7.7 [Defer Non-Critical Work with requestIdleCallback](#77-defer-non-critical-work-with-requestidlecallback)
   - 7.8 [Early Length Check for Array Comparisons](#78-early-length-check-for-array-comparisons)
   - 7.9 [Early Return from Functions](#79-early-return-from-functions)
   - 7.10 [Hoist RegExp Creation](#710-hoist-regexp-creation)
   - 7.11 [Use flatMap to Map and Filter in One Pass](#711-use-flatmap-to-map-and-filter-in-one-pass)
   - 7.12 [Use Loop for Min/Max Instead of Sort](#712-use-loop-for-minmax-instead-of-sort)
   - 7.13 [Use Set/Map for O(1) Lookups](#713-use-setmap-for-o1-lookups)
   - 7.14 [Use toSorted() Instead of sort() for Immutability](#714-use-tosorted-instead-of-sort-for-immutability)
8. [Advanced Patterns](#8-advanced-patterns) — **LOW**
   - 8.1 [Do Not Put Effect Events in Dependency Arrays](#81-do-not-put-effect-events-in-dependency-arrays)
   - 8.2 [Initialize App Once, Not Per Mount](#82-initialize-app-once-not-per-mount)
   - 8.3 [Store Event Handlers in Refs](#83-store-event-handlers-in-refs)
   - 8.4 [useEffectEvent for Stable Callback Refs](#84-useeffectevent-for-stable-callback-refs)

---

## 1. Eliminating Waterfalls

**Impact: CRITICAL**

Waterfalls are the #1 performance killer. Each sequential await adds full network latency. Eliminating them yields the largest gains.

### 1.1 Check Cheap Conditions Before Async Flags

**Impact: HIGH (avoids unnecessary async work when a synchronous guard already fails)**

When a branch uses `await` for a flag or remote value and also requires a **cheap synchronous** condition (local props, request metadata, already-loaded state), evaluate the cheap condition **first**. Otherwise you pay for the async call even when the compound condition can never be true.

This is a specialization of [Defer Await Until Needed](rules/async-defer-await.md) for `flag && cheapCondition` style checks.

**Incorrect:**

```typescript
const someFlag = await getFlag();

if (someFlag && someCondition) {
  // ...
}
```

**Correct:**

```typescript
if (someCondition) {
  const someFlag = await getFlag();
  if (someFlag) {
    // ...
  }
}
```

This matters when `getFlag` hits the network, a feature-flag service, or `React.cache` / DB work: skipping it when `someCondition` is false removes that cost on the cold path.

Keep the original order if `someCondition` is expensive, depends on the flag, or you must run side effects in a fixed order.

### 1.2 Defer Await Until Needed

**Impact: HIGH (avoids blocking unused code paths)**

Move `await` operations into the branches where they're actually used to avoid blocking code paths that don't need them.

**Incorrect: blocks both branches**

```typescript
async function handleRequest(userId: string, skipProcessing: boolean) {
  const userData = await fetchUserData(userId);

  if (skipProcessing) {
    // Returns immediately but still waited for userData
    return { skipped: true };
  }

  // Only this branch uses userData
  return processUserData(userData);
}
```

**Correct: only blocks when needed**

```typescript
async function handleRequest(userId: string, skipProcessing: boolean) {
  if (skipProcessing) {
    // Returns immediately without waiting
    return { skipped: true };
  }

  // Fetch only when needed
  const userData = await fetchUserData(userId);
  return processUserData(userData);
}
```

**Another example: early return optimization**

```typescript
// Incorrect: always fetches permissions
async function updateResource(resourceId: string, userId: string) {
  const permissions = await fetchPermissions(userId);
  const resource = await getResource(resourceId);

  if (!resource) {
    return { error: "Not found" };
  }

  if (!permissions.canEdit) {
    return { error: "Forbidden" };
  }

  return await updateResourceData(resource, permissions);
}

// Correct: fetches only when needed
async function updateResource(resourceId: string, userId: string) {
  const resource = await getResource(resourceId);

  if (!resource) {
    return { error: "Not found" };
  }

  const permissions = await fetchPermissions(userId);

  if (!permissions.canEdit) {
    return { error: "Forbidden" };
  }

  return await updateResourceData(resource, permissions);
}
```

This optimization is especially valuable when the skipped branch is frequently taken, or when the deferred operation is expensive.

For `await getFlag()` combined with a cheap synchronous guard (`flag && someCondition`), see [Check Cheap Conditions Before Async Flags](rules/async-cheap-condition-before-await.md).

### 1.3 Dependency-Based Parallelization

**Impact: CRITICAL (2-10× improvement)**

For operations with partial dependencies, use `better-all` to maximize parallelism. It automatically starts each task at the earliest possible moment.

**Incorrect: profile waits for config unnecessarily**

```typescript
const [user, config] = await Promise.all([fetchUser(), fetchConfig()]);
const profile = await fetchProfile(user.id);
```

**Correct: config and profile run in parallel**

```typescript
import { all } from "better-all";

const { user, config, profile } = await all({
  async user() {
    return fetchUser();
  },
  async config() {
    return fetchConfig();
  },
  async profile() {
    return fetchProfile((await this.$.user).id);
  },
});
```

**Alternative without extra dependencies:**

```typescript
const userPromise = fetchUser();
const profilePromise = userPromise.then((user) => fetchProfile(user.id));

const [user, config, profile] = await Promise.all([userPromise, fetchConfig(), profilePromise]);
```

We can also create all the promises first, and do `Promise.all()` at the end.

Reference: [https://github.com/shuding/better-all](https://szjp.tcti.cn/shichang/retention-16647312.html)

### 1.4 Prevent Waterfall Chains in API Routes

**Impact: CRITICAL (2-10× improvement)**

In API routes and Server Actions, start independent operations immediately, even if you don't await them yet.

**Incorrect: config waits for auth, data waits for both**

```typescript
export async function GET(request: Request) {
  const session = await auth();
  const config = await fetchConfig();
  const data = await fetchData(session.user.id);
  return Response.json({ data, config });
}
```

**Correct: auth and config start immediately**

```typescript
export async function GET(request: Request) {
  const sessionPromise = auth();
  const configPromise = fetchConfig();
  const session = await sessionPromise;
  const [config, data] = await Promise.all([configPromise, fetchData(session.user.id)]);
  return Response.json({ data, config });
}
```

For operations with more complex dependency chains, use `better-all` to automatically maximize parallelism (see Dependency-Based Parallelization).

### 1.5 Promise.all() for Independent Operations

**Impact: CRITICAL (2-10× improvement)**

When async operations have no interdependencies, execute them concurrently using `Promise.all()`.

**Incorrect: sequential execution, 3 round trips**

```typescript
const user = await fetchUser();
const posts = await fetchPosts();
const comments = await fetchComments();
```

**Correct: parallel execution, 1 round trip**

```typescript
const [user, posts, comments] = await Promise.all([fetchUser(), fetchPosts(), fetchComments()]);
```

### 1.6 Strategic Suspense Boundaries

**Impact: HIGH (faster initial paint)**

Instead of awaiting data in async components before returning JSX, use Suspense boundaries to show the wrapper UI faster while data loads.

**Incorrect: wrapper blocked by data fetching**

```tsx
async function Page() {
  const data = await fetchData(); // Blocks entire page

  return (
    <div>
      <div>Sidebar</div>
      <div>Header</div>
      <div>
        <DataDisplay data={data} />
      </div>
      <div>Footer</div>
    </div>
  );
}
```

The entire layout waits for data even though only the middle section needs it.

**Correct: wrapper shows immediately, data streams in**

```tsx
function Page() {
  return (
    <div>
      <div>Sidebar</div>
      <div>Header</div>
      <div>
        <Suspense fallback={<Skeleton />}>
          <DataDisplay />
        </Suspense>
      </div>
      <div>Footer</div>
    </div>
  );
}

async function DataDisplay() {
  const data = await fetchData(); // Only blocks this component
  return <div>{data.content}</div>;
}
```

Sidebar, Header, and Footer render immediately. Only DataDisplay waits for data.

**Alternative: share promise across components**

```tsx
function Page() {
  // Start fetch immediately, but don't await
  const dataPromise = fetchData();

  return (
    <div>
      <div>Sidebar</div>
      <div>Header</div>
      <Suspense fallback={<Skeleton />}>
        <DataDisplay dataPromise={dataPromise} />
        <DataSummary dataPromise={dataPromise} />
      </Suspense>
      <div>Footer</div>
    </div>
  );
}

function DataDisplay({ dataPromise }: { dataPromise: Promise<Data> }) {
  const data = use(dataPromise); // Unwraps the promise
  return <div>{data.content}</div>;
}

function DataSummary({ dataPromise }: { dataPromise: Promise<Data> }) {
  const data = use(dataPromise); // Reuses the same promise
  return <div>{data.summary}</div>;
}
```

Both components share the same promise, so only one fetch occurs. Layout renders immediately while both components wait together.

**When NOT to use this pattern:**

- Critical data needed for layout decisions (affects positioning)

- SEO-critical content above the fold

- Small, fast queries where suspense overhead isn't worth it

- When you want to avoid layout shift (loading → content jump)

**Trade-off:** Faster initial paint vs potential layout shift. Choose based on your UX priorities.

---

## 2. Bundle Size Optimization

**Impact: CRITICAL**

Reducing initial bundle size improves Time to Interactive and Largest Contentful Paint.

### 2.1 Avoid Barrel File Imports

**Impact: CRITICAL (200-800ms import cost, slow builds)**

Import directly from source files instead of barrel files to avoid loading thousands of unused modules. **Barrel files** are entry points that re-export multiple modules (e.g., `index.js` that does `export * from './module'`).

Popular icon and component libraries can have **up to 10,000 re-exports** in their entry file. For many React packages, **it takes 200-800ms just to import them**, affecting both development speed and production cold starts.

**Why tree-shaking doesn't help:** When a library is marked as external (not bundled), the bundler can't optimize it. If you bundle it to enable tree-shaking, builds become substantially slower analyzing the entire module graph.

**Incorrect: imports entire library**

```tsx
import { Check, X, Menu } from "lucide-react";
// Loads 1,583 modules, takes ~2.8s extra in dev
// Runtime cost: 200-800ms on every cold start

import { Button, TextField } from "@mui/material";
// Loads 2,225 modules, takes ~4.2s extra in dev
```

**Correct - Next.js 13.5+ (recommended):**

```tsx
// Keep the standard imports - Next.js transforms them to direct imports
import { Check, X, Menu } from "lucide-react";
// Full TypeScript support, no manual path wrangling
```

This is the recommended approach because it preserves TypeScript type safety and editor autocompletion while still eliminating the barrel import cost.

**Correct - Direct imports (non-Next.js projects):**

```tsx
import Button from "@mui/material/Button";
import TextField from "@mui/material/TextField";
// Loads only what you use
```

> **TypeScript warning:** Some libraries (notably `lucide-react`) don't ship `.d.ts` files for their deep import paths. Importing from `lucide-react/dist/esm/icons/check` resolves to an implicit `any` type, causing errors under `strict` or `noImplicitAny`. Prefer `optimizePackageImports` when available, or verify the library exports types for its subpaths before using direct imports.

These optimizations provide 15-70% faster dev boot, 28% faster builds, 40% faster cold starts, and significantly faster HMR.

Libraries commonly affected: `lucide-react`, `@mui/material`, `@mui/icons-material`, `@tabler/icons-react`, `react-icons`, `@headlessui/react`, `@radix-ui/react-*`, `lodash`, `ramda`, `date-fns`, `rxjs`, `react-use`.

Reference: [https://vercel.com/blog/how-we-optimized-package-imports-in-next-js](https://ncrw.tcti.cn/kaifa/growth-85374259.html)

### 2.2 Conditional Module Loading

**Impact: HIGH (loads large data only when needed)**

Load large data or modules only when a feature is activated.

**Example: lazy-load animation frames**

```tsx
function AnimationPlayer({
  enabled,
  setEnabled,
}: {
  enabled: boolean;
  setEnabled: React.Dispatch<React.SetStateAction<boolean>>;
}) {
  const [frames, setFrames] = useState<Frame[] | null>(null);

  useEffect(() => {
    if (enabled && !frames && typeof window !== "undefined") {
      import("./animation-frames.js")
        .then((mod) => setFrames(mod.frames))
        .catch(() => setEnabled(false));
    }
  }, [enabled, frames, setEnabled]);

  if (!frames) return <Skeleton />;
  return <Canvas frames={frames} />;
}
```

The `typeof window !== 'undefined'` check prevents bundling this module for SSR, optimizing server bundle size and build speed.

### 2.3 Defer Non-Critical Third-Party Libraries

**Impact: MEDIUM (loads after hydration)**

Analytics, logging, and error tracking don't block user interaction. Load them after hydration.

**Incorrect: blocks initial bundle**

```tsx
import { Analytics } from "@vercel/analytics/react";

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
      </body>
    </html>
  );
}
```

**Correct: loads after hydration**

```tsx
import dynamic from "next/dynamic";

const Analytics = dynamic(() => import("@vercel/analytics/react").then((m) => m.Analytics), {
  ssr: false,
});

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
      </body>
    </html>
  );
}
```

### 2.4 Dynamic Imports for Heavy Components

**Impact: CRITICAL (directly affects TTI and LCP)**

Use `next/dynamic` to lazy-load large components not needed on initial render.

**Incorrect: Monaco bundles with main chunk ~300KB**

```tsx
import { MonacoEditor } from "./monaco-editor";

function CodePanel({ code }: { code: string }) {
  return <MonacoEditor value={code} />;
}
```

**Correct: Monaco loads on demand**

```tsx
import dynamic from "next/dynamic";

const MonacoEditor = dynamic(() => import("./monaco-editor").then((m) => m.MonacoEditor), {
  ssr: false,
});

function CodePanel({ code }: { code: string }) {
  return <MonacoEditor value={code} />;
}
```

### 2.5 Prefer Statically Analyzable Paths

**Impact: HIGH (avoids accidental broad bundles and file traces)**

Build tools work best when import and file-system paths are obvious at build time. If you hide the real path inside a variable or compose it too dynamically, the tool either has to include a broad set of possible files, warn that it cannot analyze the import, or widen file tracing to stay safe.

Prefer explicit maps or literal paths so the set of reachable files stays narrow and predictable. This is the same rule whether you are choosing modules with `import()` or reading files in server/build code.

When analysis becomes too broad, the cost is real:

- Larger server bundles

- Slower builds

- Worse cold starts

- More memory use

**Incorrect: the bundler cannot tell what may be imported**

```ts
const PAGE_MODULES = {
  home: "./pages/home",
  settings: "./pages/settings",
} as const;

const Page = await import(PAGE_MODULES[pageName]);
```

**Correct: use an explicit map of allowed modules**

```ts
const PAGE_MODULES = {
  home: () => import("./pages/home"),
  settings: () => import("./pages/settings"),
} as const;

const Page = await PAGE_MODULES[pageName]();
```

**Incorrect: a 2-value enum still hides the final path from static analysis**

```ts
const baseDir = path.join(process.cwd(), "content/" + contentKind);
```

**Correct: make each final path literal at the callsite**

```ts
const baseDir =
  kind === ContentKind.Blog
    ? path.join(process.cwd(), "content/blog")
    : path.join(process.cwd(), "content/docs");
```

In Next.js server code, this matters for output file tracing too. `path.join(process.cwd(), someVar)` can widen the traced file set because Next.js statically analyze `import`, `require`, and `fs` usage.

Reference: [https://nextjs.org/docs/app/api-reference/config/next-config-js/output](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html), [https://nextjs.org/learn/seo/dynamic-imports](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html), [https://vite.dev/guide/features.html](https://fjac.spidernode-mesh.net/anli/investment-94283191.html), [https://esbuild.github.io/api/](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html), [https://www.npmjs.com/package/@rollup/plugin-dynamic-import-vars](https://szjp.tcti.cn/shichang/retention-16647312.html), [https://webpack.js.org/guides/dependency-management/](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)

### 2.6 Preload Based on User Intent

**Impact: MEDIUM (reduces perceived latency)**

Preload heavy bundles before they're needed to reduce perceived latency.

**Example: preload on hover/focus**

```tsx
function EditorButton({ onClick }: { onClick: () => void }) {
  const preload = () => {
    if (typeof window !== "undefined") {
      void import("./monaco-editor");
    }
  };

  return (
    <button onMouseEnter={preload} onFocus={preload} onClick={onClick}>
      Open Editor
    </button>
  );
}
```

**Example: preload when feature flag is enabled**

```tsx
function FlagsProvider({ children, flags }: Props) {
  useEffect(() => {
    if (flags.editorEnabled && typeof window !== "undefined") {
      void import("./monaco-editor").then((mod) => mod.init());
    }
  }, [flags.editorEnabled]);

  return <FlagsContext.Provider value={flags}>{children}</FlagsContext.Provider>;
}
```

The `typeof window !== 'undefined'` check prevents bundling preloaded modules for SSR, optimizing server bundle size and build speed.

---

## 3. Server-Side Performance

**Impact: HIGH**

Optimizing server-side rendering and data fetching eliminates server-side waterfalls and reduces response times.

### 3.1 Authenticate Server Actions Like API Routes

**Impact: CRITICAL (prevents unauthorized access to server mutations)**

Server Actions (functions with `"use server"`) are exposed as public endpoints, just like API routes. Always verify authentication and authorization **inside** each Server Action—do not rely solely on middleware, layout guards, or page-level checks, as Server Actions can be invoked directly.

Next.js documentation explicitly states: "Treat Server Actions with the same security considerations as public-facing API endpoints, and verify if the user is allowed to perform a mutation."

**Incorrect: no authentication check**

```typescript
"use server";

export async function deleteUser(userId: string) {
  // Anyone can call this! No auth check
  await db.user.delete({ where: { id: userId } });
  return { success: true };
}
```

**Correct: authentication inside the action**

```typescript
"use server";

import { verifySession } from "@/lib/auth";
import { unauthorized } from "@/lib/errors";

export async function deleteUser(userId: string) {
  // Always check auth inside the action
  const session = await verifySession();

  if (!session) {
    throw unauthorized("Must be logged in");
  }

  // Check authorization too
  if (session.user.role !== "admin" && session.user.id !== userId) {
    throw unauthorized("Cannot delete other users");
  }

  await db.user.delete({ where: { id: userId } });
  return { success: true };
}
```

**With input validation:**

```typescript
"use server";

import { verifySession } from "@/lib/auth";
import { z } from "zod";

const updateProfileSchema = z.object({
  userId: z.string().uuid(),
  name: z.string().min(1).max(100),
  email: z.string().email(),
});

export async function updateProfile(data: unknown) {
  // Validate input first
  const validated = updateProfileSchema.parse(data);

  // Then authenticate
  const session = await verifySession();
  if (!session) {
    throw new Error("Unauthorized");
  }

  // Then authorize
  if (session.user.id !== validated.userId) {
    throw new Error("Can only update own profile");
  }

  // Finally perform the mutation
  await db.user.update({
    where: { id: validated.userId },
    data: {
      name: validated.name,
      email: validated.email,
    },
  });

  return { success: true };
}
```

Reference: [https://nextjs.org/docs/app/guides/authentication](https://owfm.tcti.cn/shangye/education-63674207.html)

### 3.2 Avoid Duplicate Serialization in RSC Props

**Impact: LOW (reduces network payload by avoiding duplicate serialization)**

RSC→client serialization deduplicates by object reference, not value. Same reference = serialized once; new reference = serialized again. Do transformations (`.toSorted()`, `.filter()`, `.map()`) in client, not server.

**Incorrect: duplicates array**

```tsx
// RSC: sends 6 strings (2 arrays × 3 items)
<ClientList usernames={usernames} usernamesOrdered={usernames.toSorted()} />
```

**Correct: sends 3 strings**

```tsx
// RSC: send once
<ClientList usernames={usernames} />;

// Client: transform there
("use client");
const sorted = useMemo(() => [...usernames].sort(), [usernames]);
```

**Nested deduplication behavior:**

```tsx
// string[] - duplicates everything
usernames={['a','b']} sorted={usernames.toSorted()} // sends 4 strings

// object[] - duplicates array structure only
users={[{id:1},{id:2}]} sorted={users.toSorted()} // sends 2 arrays + 2 unique objects (not 4)
```

Deduplication works recursively. Impact varies by data type:

- `string[]`, `number[]`, `boolean[]`: **HIGH impact** - array + all primitives fully duplicated

- `object[]`: **LOW impact** - array duplicated, but nested objects deduplicated by reference

**Operations breaking deduplication: create new references**

- Arrays: `.toSorted()`, `.filter()`, `.map()`, `.slice()`, `[...arr]`

- Objects: `{...obj}`, `Object.assign()`, `structuredClone()`, `JSON.parse(JSON.stringify())`

**More examples:**

```tsx
// ❌ Bad
<C users={users} active={users.filter(u => u.active)} />
<C product={product} productName={product.name} />

// ✅ Good
<C users={users} />
<C product={product} />
// Do filtering/destructuring in client
```

**Exception:** Pass derived data when transformation is expensive or client doesn't need original.

### 3.3 Avoid Shared Module State for Request Data

**Impact: HIGH (prevents concurrency bugs and request data leaks)**

For React Server Components and client components rendered during SSR, avoid using mutable module-level variables to share request-scoped data. Server renders can run concurrently in the same process. If one render writes to shared module state and another render reads it, you can get race conditions, cross-request contamination, and security bugs where one user's data appears in another user's response.

Treat module scope on the server as process-wide shared memory, not request-local state.

**Incorrect: request data leaks across concurrent renders**

```tsx
let currentUser: User | null = null;

export default async function Page() {
  currentUser = await auth();
  return <Dashboard />;
}

async function Dashboard() {
  return <div>{currentUser?.name}</div>;
}
```

If two requests overlap, request A can set `currentUser`, then request B overwrites it before request A finishes rendering `Dashboard`.

**Correct: keep request data local to the render tree**

```tsx
export default async function Page() {
  const user = await auth();
  return <Dashboard user={user} />;
}

function Dashboard({ user }: { user: User | null }) {
  return <div>{user?.name}</div>;
}
```

Safe exceptions:

- Immutable static assets or config loaded once at module scope

- Shared caches intentionally designed for cross-request reuse and keyed correctly

- Process-wide singletons that do not store request- or user-specific mutable data

For static assets and config, see [Hoist Static I/O to Module Level](rules/server-hoist-static-io.md).

### 3.4 Cross-Request LRU Caching

**Impact: HIGH (caches across requests)**

`React.cache()` only works within one request. For data shared across sequential requests (user clicks button A then button B), use an LRU cache.

**Implementation:**

```typescript
import { LRUCache } from "lru-cache";

const cache = new LRUCache<string, any>({
  max: 1000,
  ttl: 5 * 60 * 1000, // 5 minutes
});

export async function getUser(id: string) {
  const cached = cache.get(id);
  if (cached) return cached;

  const user = await db.user.findUnique({ where: { id } });
  cache.set(id, user);
  return user;
}

// Request 1: DB query, result cached
// Request 2: cache hit, no DB query
```

Use when sequential user actions hit multiple endpoints needing the same data within seconds.

**With Vercel's [Fluid Compute](https://api.pvucttcv.cn/yinyue/innovation-37390655.html):** LRU caching is especially effective because multiple concurrent requests can share the same function instance and cache. This means the cache persists across requests without needing external storage like Redis.

**In traditional serverless:** Each invocation runs in isolation, so consider Redis for cross-process caching.

Reference: [https://github.com/isaacs/node-lru-cache](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)

### 3.5 Hoist Static I/O to Module Level

**Impact: HIGH (avoids repeated file/network I/O per request)**

When loading static assets (fonts, logos, images, config files) in route handlers or server functions, hoist the I/O operation to module level. Module-level code runs once when the module is first imported, not on every request. This eliminates redundant file system reads or network fetches that would otherwise run on every invocation.

**Incorrect: reads font file on every request**

```typescript
// app/api/og/route.tsx
import { ImageResponse } from 'next/og'

export async function GET(request: Request) {
  // Runs on EVERY request - expensive!
  const fontData = await fetch(
    new URL('./fonts/Inter.ttf', import.meta.url)
  ).then(res => res.arrayBuffer())

  const logoData = await fetch(
    new URL('./images/logo.png', import.meta.url)
  ).then(res => res.arrayBuffer())

  return new ImageResponse(
    <div style={{ fontFamily: 'Inter' }}>
      <img src={logoData} />
      Hello World
    </div>,
    { fonts: [{ name: 'Inter', data: fontData }] }
  )
}
```

**Correct: loads once at module initialization**

```typescript
// app/api/og/route.tsx
import { ImageResponse } from 'next/og'

// Module-level: runs ONCE when module is first imported
const fontData = fetch(
  new URL('./fonts/Inter.ttf', import.meta.url)
).then(res => res.arrayBuffer())

const logoData = fetch(
  new URL('./images/logo.png', import.meta.url)
).then(res => res.arrayBuffer())

export async function GET(request: Request) {
  // Await the already-started promises
  const [font, logo] = await Promise.all([fontData, logoData])

  return new ImageResponse(
    <div style={{ fontFamily: 'Inter' }}>
      <img src={logo} />
      Hello World
    </div>,
    { fonts: [{ name: 'Inter', data: font }] }
  )
}
```

**Correct: synchronous fs at module level**

```typescript
// app/api/og/route.tsx
import { ImageResponse } from 'next/og'
import { readFileSync } from 'fs'
import { join } from 'path'

// Synchronous read at module level - blocks only during module init
const fontData = readFileSync(
  join(process.cwd(), 'public/fonts/Inter.ttf')
)

const logoData = readFileSync(
  join(process.cwd(), 'public/images/logo.png')
)

export async function GET(request: Request) {
  return new ImageResponse(
    <div style={{ fontFamily: 'Inter' }}>
      <img src={logoData} />
      Hello World
    </div>,
    { fonts: [{ name: 'Inter', data: fontData }] }
  )
}
```

**Incorrect: reads config on every call**

```typescript
import fs from "node:fs/promises";

export async function processRequest(data: Data) {
  const config = JSON.parse(await fs.readFile("./config.json", "utf-8"));
  const template = await fs.readFile("./template.html", "utf-8");

  return render(template, data, config);
}
```

**Correct: hoists config and template to module level**

```typescript
import fs from "node:fs/promises";

const configPromise = fs.readFile("./config.json", "utf-8").then(JSON.parse);
const templatePromise = fs.readFile("./template.html", "utf-8");

export async function processRequest(data: Data) {
  const [config, template] = await Promise.all([configPromise, templatePromise]);

  return render(template, data, config);
}
```

When to use this pattern:

- Loading fonts for OG image generation

- Loading static logos, icons, or watermarks

- Reading configuration files that don't change at runtime

- Loading email templates or other static templates

- Any static asset that's the same across all requests

When not to use this pattern:

- Assets that vary per request or user

- Files that may change during runtime (use caching with TTL instead)

- Large files that would consume too much memory if kept loaded

- Sensitive data that shouldn't persist in memory

With Vercel's [Fluid Compute](https://ejuh.tcti.cn/youhua/follow-30750975.html), module-level caching is especially effective because multiple concurrent requests share the same function instance. The static assets stay loaded in memory across requests without cold start penalties.

In traditional serverless, each cold start re-executes module-level code, but subsequent warm invocations reuse the loaded assets until the instance is recycled.

### 3.6 Minimize Serialization at RSC Boundaries

**Impact: HIGH (reduces data transfer size)**

The React Server/Client boundary serializes all object properties into strings and embeds them in the HTML response and subsequent RSC requests. This serialized data directly impacts page weight and load time, so **size matters a lot**. Only pass fields that the client actually uses.

**Incorrect: serializes all 50 fields**

```tsx
async function Page() {
  const user = await fetchUser(); // 50 fields
  return <Profile user={user} />;
}

("use client");
function Profile({ user }: { user: User }) {
  return <div>{user.name}</div>; // uses 1 field
}
```

**Correct: serializes only 1 field**

```tsx
async function Page() {
  const user = await fetchUser();
  return <Profile name={user.name} />;
}

("use client");
function Profile({ name }: { name: string }) {
  return <div>{name}</div>;
}
```

### 3.7 Parallel Data Fetching with Component Composition

**Impact: CRITICAL (eliminates server-side waterfalls)**

React Server Components execute sequentially within a tree. Restructure with composition to parallelize data fetching.

**Incorrect: Sidebar waits for Page's fetch to complete**

```tsx
export default async function Page() {
  const header = await fetchHeader();
  return (
    <div>
      <div>{header}</div>
      <Sidebar />
    </div>
  );
}

async function Sidebar() {
  const items = await fetchSidebarItems();
  return <nav>{items.map(renderItem)}</nav>;
}
```

**Correct: both fetch simultaneously**

```tsx
async function Header() {
  const data = await fetchHeader();
  return <div>{data}</div>;
}

async function Sidebar() {
  const items = await fetchSidebarItems();
  return <nav>{items.map(renderItem)}</nav>;
}

export default function Page() {
  return (
    <div>
      <Header />
      <Sidebar />
    </div>
  );
}
```

**Alternative with children prop:**

```tsx
async function Header() {
  const data = await fetchHeader();
  return <div>{data}</div>;
}

async function Sidebar() {
  const items = await fetchSidebarItems();
  return <nav>{items.map(renderItem)}</nav>;
}

function Layout({ children }: { children: ReactNode }) {
  return (
    <div>
      <Header />
      {children}
    </div>
  );
}

export default function Page() {
  return (
    <Layout>
      <Sidebar />
    </Layout>
  );
}
```

### 3.8 Parallel Nested Data Fetching

**Impact: CRITICAL (eliminates server-side waterfalls)**

When fetching nested data in parallel, chain dependent fetches within each item's promise so a slow item doesn't block the rest.

**Incorrect: a single slow item blocks all nested fetches**

```tsx
const chats = await Promise.all(chatIds.map((id) => getChat(id)));

const chatAuthors = await Promise.all(chats.map((chat) => getUser(chat.author)));
```

If one `getChat(id)` out of 100 is extremely slow, the authors of the other 99 chats can't start loading even though their data is ready.

**Correct: each item chains its own nested fetch**

```tsx
const chatAuthors = await Promise.all(
  chatIds.map((id) => getChat(id).then((chat) => getUser(chat.author))),
);
```

Each item independently chains `getChat` → `getUser`, so a slow chat doesn't block author fetches for the others.

### 3.9 Per-Request Deduplication with React.cache()

**Impact: MEDIUM (deduplicates within request)**

Use `React.cache()` for server-side request deduplication. Authentication and database queries benefit most.

**Usage:**

```typescript
import { cache } from "react";

export const getCurrentUser = cache(async () => {
  const session = await auth();
  if (!session?.user?.id) return null;
  return await db.user.findUnique({
    where: { id: session.user.id },
  });
});
```

Within a single request, multiple calls to `getCurrentUser()` execute the query only once.

**Avoid inline objects as arguments:**

`React.cache()` uses shallow equality (`Object.is`) to determine cache hits. Inline objects create new references each call, preventing cache hits.

**Incorrect: always cache miss**

```typescript
const getUser = cache(async (params: { uid: number }) => {
  return await db.user.findUnique({ where: { id: params.uid } });
});

// Each call creates new object, never hits cache
getUser({ uid: 1 });
getUser({ uid: 1 }); // Cache miss, runs query again
```

**Correct: cache hit**

```typescript
const params = { uid: 1 };
getUser(params); // Query runs
getUser(params); // Cache hit (same reference)
```

If you must pass objects, pass the same reference:

**Next.js-Specific Note:**

In Next.js, the `fetch` API is automatically extended with request memoization. Requests with the same URL and options are automatically deduplicated within a single request, so you don't need `React.cache()` for `fetch` calls. However, `React.cache()` is still essential for other async tasks:

- Database queries (Prisma, Drizzle, etc.)

- Heavy computations

- Authentication checks

- File system operations

- Any non-fetch async work

Use `React.cache()` to deduplicate these operations across your component tree.

Reference: [https://react.dev/reference/react/cache](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)

### 3.10 Use after() for Non-Blocking Operations

**Impact: MEDIUM (faster response times)**

Use Next.js's `after()` to schedule work that should execute after a response is sent. This prevents logging, analytics, and other side effects from blocking the response.

**Incorrect: blocks response**

```tsx
import { logUserAction } from "@/app/utils";

export async function POST(request: Request) {
  // Perform mutation
  await updateDatabase(request);

  // Logging blocks the response
  const userAgent = request.headers.get("user-agent") || "unknown";
  await logUserAction({ userAgent });

  return new Response(JSON.stringify({ status: "success" }), {
    status: 200,
    headers: { "Content-Type": "application/json" },
  });
}
```

**Correct: non-blocking**

```tsx
import { after } from "next/server";
import { headers, cookies } from "next/headers";
import { logUserAction } from "@/app/utils";

export async function POST(request: Request) {
  // Perform mutation
  await updateDatabase(request);

  // Log after response is sent
  after(async () => {
    const userAgent = (await headers()).get("user-agent") || "unknown";
    const sessionCookie = (await cookies()).get("session-id")?.value || "anonymous";

    logUserAction({ sessionCookie, userAgent });
  });

  return new Response(JSON.stringify({ status: "success" }), {
    status: 200,
    headers: { "Content-Type": "application/json" },
  });
}
```

The response is sent immediately while logging happens in the background.

**Common use cases:**

- Analytics tracking

- Audit logging

- Sending notifications

- Cache invalidation

- Cleanup tasks

**Important notes:**

- `after()` runs even if the response fails or redirects

- Works in Server Actions, Route Handlers, and Server Components

Reference: [https://nextjs.org/docs/app/api-reference/functions/after](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)

---

## 4. Client-Side Data Fetching

**Impact: MEDIUM-HIGH**

Automatic deduplication and efficient data fetching patterns reduce redundant network requests.

### 4.1 Deduplicate Global Event Listeners

**Impact: LOW (single listener for N components)**

Use `useSWRSubscription()` to share global event listeners across component instances.

**Incorrect: N instances = N listeners**

```tsx
function useKeyboardShortcut(key: string, callback: () => void) {
  useEffect(() => {
    const handler = (e: KeyboardEvent) => {
      if (e.metaKey && e.key === key) {
        callback();
      }
    };
    window.addEventListener("keydown", handler);
    return () => window.removeEventListener("keydown", handler);
  }, [key, callback]);
}
```

When using the `useKeyboardShortcut` hook multiple times, each instance will register a new listener.

**Correct: N instances = 1 listener**

```tsx
import useSWRSubscription from "swr/subscription";

// Module-level Map to track callbacks per key
const keyCallbacks = new Map<string, Set<() => void>>();

function useKeyboardShortcut(key: string, callback: () => void) {
  // Register this callback in the Map
  useEffect(() => {
    if (!keyCallbacks.has(key)) {
      keyCallbacks.set(key, new Set());
    }
    keyCallbacks.get(key)!.add(callback);

    return () => {
      const set = keyCallbacks.get(key);
      if (set) {
        set.delete(callback);
        if (set.size === 0) {
          keyCallbacks.delete(key);
        }
      }
    };
  }, [key, callback]);

  useSWRSubscription("global-keydown", () => {
    const handler = (e: KeyboardEvent) => {
      if (e.metaKey && keyCallbacks.has(e.key)) {
        keyCallbacks.get(e.key)!.forEach((cb) => cb());
      }
    };
    window.addEventListener("keydown", handler);
    return () => window.removeEventListener("keydown", handler);
  });
}

function Profile() {
  // Multiple shortcuts will share the same listener
  useKeyboardShortcut("p", () => {
    /* ... */
  });
  useKeyboardShortcut("k", () => {
    /* ... */
  });
  // ...
}
```

### 4.2 Use Passive Event Listeners for Scrolling Performance

**Impact: MEDIUM (eliminates scroll delay caused by event listeners)**

Add `{ passive: true }` to touch and wheel event listeners to enable immediate scrolling. Browsers normally wait for listeners to finish to check if `preventDefault()` is called, causing scroll delay.

**Incorrect:**

```typescript
useEffect(() => {
  const handleTouch = (e: TouchEvent) => console.log(e.touches[0].clientX);
  const handleWheel = (e: WheelEvent) => console.log(e.deltaY);

  document.addEventListener("touchstart", handleTouch);
  document.addEventListener("wheel", handleWheel);

  return () => {
    document.removeEventListener("touchstart", handleTouch);
    document.removeEventListener("wheel", handleWheel);
  };
}, []);
```

**Correct:**

```typescript
useEffect(() => {
  const handleTouch = (e: TouchEvent) => console.log(e.touches[0].clientX);
  const handleWheel = (e: WheelEvent) => console.log(e.deltaY);

  document.addEventListener("touchstart", handleTouch, { passive: true });
  document.addEventListener("wheel", handleWheel, { passive: true });

  return () => {
    document.removeEventListener("touchstart", handleTouch);
    document.removeEventListener("wheel", handleWheel);
  };
}, []);
```

**Use passive when:** tracking/analytics, logging, any listener that doesn't call `preventDefault()`.

**Don't use passive when:** implementing custom swipe gestures, custom zoom controls, or any listener that needs `preventDefault()`.

### 4.3 Use SWR for Automatic Deduplication

**Impact: MEDIUM-HIGH (automatic deduplication)**

SWR enables request deduplication, caching, and revalidation across component instances.

**Incorrect: no deduplication, each instance fetches**

```tsx
function UserList() {
  const [users, setUsers] = useState([]);
  useEffect(() => {
    fetch("/api/users")
      .then((r) => r.json())
      .then(setUsers);
  }, []);
}
```

**Correct: multiple instances share one request**

```tsx
import useSWR from "swr";

function UserList() {
  const { data: users } = useSWR("/api/users", fetcher);
}
```

**For immutable data:**

```tsx
import { useImmutableSWR } from "@/lib/swr";

function StaticContent() {
  const { data } = useImmutableSWR("/api/config", fetcher);
}
```

**For mutations:**

```tsx
import { useSWRMutation } from "swr/mutation";

function UpdateButton() {
  const { trigger } = useSWRMutation("/api/user", updateUser);
  return <button onClick={() => trigger()}>Update</button>;
}
```

Reference: [https://swr.vercel.app](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)

### 4.4 Version and Minimize localStorage Data

**Impact: MEDIUM (prevents schema conflicts, reduces storage size)**

Add version prefix to keys and store only needed fields. Prevents schema conflicts and accidental storage of sensitive data.

**Incorrect:**

```typescript
// No version, stores everything, no error handling
localStorage.setItem("userConfig", JSON.stringify(fullUserObject));
const data = localStorage.getItem("userConfig");
```

**Correct:**

```typescript
const VERSION = "v2";

function saveConfig(config: { theme: string; language: string }) {
  try {
    localStorage.setItem(`userConfig:${VERSION}`, JSON.stringify(config));
  } catch {
    // Throws in incognito/private browsing, quota exceeded, or disabled
  }
}

function loadConfig() {
  try {
    const data = localStorage.getItem(`userConfig:${VERSION}`);
    return data ? JSON.parse(data) : null;
  } catch {
    return null;
  }
}

// Migration from v1 to v2
function migrate() {
  try {
    const v1 = localStorage.getItem("userConfig:v1");
    if (v1) {
      const old = JSON.parse(v1);
      saveConfig({ theme: old.darkMode ? "dark" : "light", language: old.lang });
      localStorage.removeItem("userConfig:v1");
    }
  } catch {}
}
```

**Store minimal fields from server responses:**

```typescript
// User object has 20+ fields, only store what UI needs
function cachePrefs(user: FullUser) {
  try {
    localStorage.setItem(
      "prefs:v1",
      JSON.stringify({
        theme: user.preferences.theme,
        notifications: user.preferences.notifications,
      }),
    );
  } catch {}
}
```

**Always wrap in try-catch:** `getItem()` and `setItem()` throw in incognito/private browsing (Safari, Firefox), when quota exceeded, or when disabled.

**Benefits:** Schema evolution via versioning, reduced storage size, prevents storing tokens/PII/internal flags.

---

## 5. Re-render Optimization

**Impact: MEDIUM**

Reducing unnecessary re-renders minimizes wasted computation and improves UI responsiveness.

### 5.1 Calculate Derived State During Rendering

**Impact: MEDIUM (avoids redundant renders and state drift)**

If a value can be computed from current props/state, do not store it in state or update it in an effect. Derive it during render to avoid extra renders and state drift. Do not set state in effects solely in response to prop changes; prefer derived values or keyed resets instead.

**Incorrect: redundant state and effect**

```tsx
function Form() {
  const [firstName, setFirstName] = useState("First");
  const [lastName, setLastName] = useState("Last");
  const [fullName, setFullName] = useState("");

  useEffect(() => {
    setFullName(firstName + " " + lastName);
  }, [firstName, lastName]);

  return <p>{fullName}</p>;
}
```

**Correct: derive during render**

```tsx
function Form() {
  const [firstName, setFirstName] = useState("First");
  const [lastName, setLastName] = useState("Last");
  const fullName = firstName + " " + lastName;

  return <p>{fullName}</p>;
}
```

Reference: [https://react.dev/learn/you-might-not-need-an-effect](https://gynw.tcti.cn/fenxi/recipe-50406879.html)

### 5.2 Defer State Reads to Usage Point

**Impact: MEDIUM (avoids unnecessary subscriptions)**

Don't subscribe to dynamic state (searchParams, localStorage) if you only read it inside callbacks.

**Incorrect: subscribes to all searchParams changes**

```tsx
function ShareButton({ chatId }: { chatId: string }) {
  const searchParams = useSearchParams();

  const handleShare = () => {
    const ref = searchParams.get("ref");
    shareChat(chatId, { ref });
  };

  return <button onClick={handleShare}>Share</button>;
}
```

**Correct: reads on demand, no subscription**

```tsx
function ShareButton({ chatId }: { chatId: string }) {
  const handleShare = () => {
    const params = new URLSearchParams(window.location.search);
    const ref = params.get("ref");
    shareChat(chatId, { ref });
  };

  return <button onClick={handleShare}>Share</button>;
}
```

### 5.3 Do not wrap a simple expression with a primitive result type in useMemo

**Impact: LOW-MEDIUM (wasted computation on every render)**

When an expression is simple (few logical or arithmetical operators) and has a primitive result type (boolean, number, string), do not wrap it in `useMemo`.

Calling `useMemo` and comparing hook dependencies may consume more resources than the expression itself.

**Incorrect:**

```tsx
function Header({ user, notifications }: Props) {
  const isLoading = useMemo(() => {
    return user.isLoading || notifications.isLoading;
  }, [user.isLoading, notifications.isLoading]);

  if (isLoading) return <Skeleton />;
  // return some markup
}
```

**Correct:**

```tsx
function Header({ user, notifications }: Props) {
  const isLoading = user.isLoading || notifications.isLoading;

  if (isLoading) return <Skeleton />;
  // return some markup
}
```

### 5.4 Don't Define Components Inside Components

**Impact: HIGH (prevents remount on every render)**

Defining a component inside another component creates a new component type on every render. React sees a different component each time and fully remounts it, destroying all state and DOM.

A common reason developers do this is to access parent variables without passing props. Always pass props instead.

**Incorrect: remounts on every render**

```tsx
function UserProfile({ user, theme }) {
  // Defined inside to access `theme` - BAD
  const Avatar = () => (
    <img src={user.avatarUrl} className={theme === "dark" ? "avatar-dark" : "avatar-light"} />
  );

  // Defined inside to access `user` - BAD
  const Stats = () => (
    <div>
      <span>{user.followers} followers</span>
      <span>{user.posts} posts</span>
    </div>
  );

  return (
    <div>
      <Avatar />
      <Stats />
    </div>
  );
}
```

Every time `UserProfile` renders, `Avatar` and `Stats` are new component types. React unmounts the old instances and mounts new ones, losing any internal state, running effects again, and recreating DOM nodes.

**Correct: pass props instead**

```tsx
function Avatar({ src, theme }: { src: string; theme: string }) {
  return <img src={src} className={theme === "dark" ? "avatar-dark" : "avatar-light"} />;
}

function Stats({ followers, posts }: { followers: number; posts: number }) {
  return (
    <div>
      <span>{followers} followers</span>
      <span>{posts} posts</span>
    </div>
  );
}

function UserProfile({ user, theme }) {
  return (
    <div>
      <Avatar src={user.avatarUrl} theme={theme} />
      <Stats followers={user.followers} posts={user.posts} />
    </div>
  );
}
```

**Symptoms of this bug:**

- Input fields lose focus on every keystroke

- Animations restart unexpectedly

- `useEffect` cleanup/setup runs on every parent render

- Scroll position resets inside the component

### 5.5 Extract Default Non-primitive Parameter Value from Memoized Component to Constant

**Impact: MEDIUM (restores memoization by using a constant for default value)**

When memoized component has a default value for some non-primitive optional parameter, such as an array, function, or object, calling the component without that parameter results in broken memoization. This is because new value instances are created on every rerender, and they do not pass strict equality comparison in `memo()`.

To address this issue, extract the default value into a constant.

**Incorrect: `onClick` has different values on every rerender**

```tsx
const UserAvatar = memo(function UserAvatar({ onClick = () => {} }: { onClick?: () => void }) {
  // ...
})

// Used without optional onClick
<UserAvatar />
```

**Correct: stable default value**

```tsx
const NOOP = () => {};

const UserAvatar = memo(function UserAvatar({ onClick = NOOP }: { onClick?: () => void }) {
  // ...
})

// Used without optional onClick
<UserAvatar />
```

### 5.6 Extract to Memoized Components

**Impact: MEDIUM (enables early returns)**

Extract expensive work into memoized components to enable early returns before computation.

**Incorrect: computes avatar even when loading**

```tsx
function Profile({ user, loading }: Props) {
  const avatar = useMemo(() => {
    const id = computeAvatarId(user);
    return <Avatar id={id} />;
  }, [user]);

  if (loading) return <Skeleton />;
  return <div>{avatar}</div>;
}
```

**Correct: skips computation when loading**

```tsx
const UserAvatar = memo(function UserAvatar({ user }: { user: User }) {
  const id = useMemo(() => computeAvatarId(user), [user]);
  return <Avatar id={id} />;
});

function Profile({ user, loading }: Props) {
  if (loading) return <Skeleton />;
  return (
    <div>
      <UserAvatar user={user} />
    </div>
  );
}
```

**Note:** If your project has [React Compiler](https://gynw.tcti.cn/fenxi/recipe-50406879.html) enabled, manual memoization with `memo()` and `useMemo()` is not necessary. The compiler automatically optimizes re-renders.

### 5.7 Narrow Effect Dependencies

**Impact: LOW (minimizes effect re-runs)**

Specify primitive dependencies instead of objects to minimize effect re-runs.

**Incorrect: re-runs on any user field change**

```tsx
useEffect(() => {
  console.log(user.id);
}, [user]);
```

**Correct: re-runs only when id changes**

```tsx
useEffect(() => {
  console.log(user.id);
}, [user.id]);
```

**For derived state, compute outside effect:**

```tsx
// Incorrect: runs on width=767, 766, 765...
useEffect(() => {
  if (width < 768) {
    enableMobileMode();
  }
}, [width]);

// Correct: runs only on boolean transition
const isMobile = width < 768;
useEffect(() => {
  if (isMobile) {
    enableMobileMode();
  }
}, [isMobile]);
```

### 5.8 Put Interaction Logic in Event Handlers

**Impact: MEDIUM (avoids effect re-runs and duplicate side effects)**

If a side effect is triggered by a specific user action (submit, click, drag), run it in that event handler. Do not model the action as state + effect; it makes effects re-run on unrelated changes and can duplicate the action.

**Incorrect: event modeled as state + effect**

```tsx
function Form() {
  const [submitted, setSubmitted] = useState(false);
  const theme = useContext(ThemeContext);

  useEffect(() => {
    if (submitted) {
      post("/api/register");
      showToast("Registered", theme);
    }
  }, [submitted, theme]);

  return <button onClick={() => setSubmitted(true)}>Submit</button>;
}
```

**Correct: do it in the handler**

```tsx
function Form() {
  const theme = useContext(ThemeContext);

  function handleSubmit() {
    post("/api/register");
    showToast("Registered", theme);
  }

  return <button onClick={handleSubmit}>Submit</button>;
}
```

Reference: [https://react.dev/learn/removing-effect-dependencies#should-this-code-move-to-an-event-handler](https://www.yx-sf.com/news/59884.html)

### 5.9 Split Combined Hook Computations

**Impact: MEDIUM (avoids recomputing independent steps)**

When a hook contains multiple independent tasks with different dependencies, split them into separate hooks. A combined hook reruns all tasks when any dependency changes, even if some tasks don't use the changed value.

**Incorrect: changing `sortOrder` recomputes filtering**

```tsx
const sortedProducts = useMemo(() => {
  const filtered = products.filter((p) => p.category === category);
  const sorted = filtered.toSorted((a, b) =>
    sortOrder === "asc" ? a.price - b.price : b.price - a.price,
  );
  return sorted;
}, [products, category, sortOrder]);
```

**Correct: filtering only recomputes when products or category change**

```tsx
const filteredProducts = useMemo(
  () => products.filter((p) => p.category === category),
  [products, category],
);

const sortedProducts = useMemo(
  () =>
    filteredProducts.toSorted((a, b) =>
      sortOrder === "asc" ? a.price - b.price : b.price - a.price,
    ),
  [filteredProducts, sortOrder],
);
```

This pattern also applies to `useEffect` when combining unrelated side effects:

**Incorrect: both effects run when either dependency changes**

```tsx
useEffect(() => {
  analytics.trackPageView(pathname);
  document.title = `${pageTitle} | My App`;
}, [pathname, pageTitle]);
```

**Correct: effects run independently**

```tsx
useEffect(() => {
  analytics.trackPageView(pathname);
}, [pathname]);

useEffect(() => {
  document.title = `${pageTitle} | My App`;
}, [pageTitle]);
```

**Note:** If your project has [React Compiler](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html) enabled, it automatically optimizes dependency tracking and may handle some of these cases for you.

### 5.10 Subscribe to Derived State

**Impact: MEDIUM (reduces re-render frequency)**

Subscribe to derived boolean state instead of continuous values to reduce re-render frequency.

**Incorrect: re-renders on every pixel change**

```tsx
function Sidebar() {
  const width = useWindowWidth(); // updates continuously
  const isMobile = width < 768;
  return <nav className={isMobile ? "mobile" : "desktop"} />;
}
```

**Correct: re-renders only when boolean changes**

```tsx
function Sidebar() {
  const isMobile = useMediaQuery("(max-width: 767px)");
  return <nav className={isMobile ? "mobile" : "desktop"} />;
}
```

### 5.11 Use Functional setState Updates

**Impact: MEDIUM (prevents stale closures and unnecessary callback recreations)**

When updating state based on the current state value, use the functional update form of setState instead of directly referencing the state variable. This prevents stale closures, eliminates unnecessary dependencies, and creates stable callback references.

**Incorrect: requires state as dependency**

```tsx
function TodoList() {
  const [items, setItems] = useState(initialItems);

  // Callback must depend on items, recreated on every items change
  const addItems = useCallback(
    (newItems: Item[]) => {
      setItems([...items, ...newItems]);
    },
    [items],
  ); // ❌ items dependency causes recreations

  // Risk of stale closure if dependency is forgotten
  const removeItem = useCallback((id: string) => {
    setItems(items.filter((item) => item.id !== id));
  }, []); // ❌ Missing items dependency - will use stale items!

  return <ItemsEditor items={items} onAdd={addItems} onRemove={removeItem} />;
}
```

The first callback is recreated every time `items` changes, which can cause child components to re-render unnecessarily. The second callback has a stale closure bug—it will always reference the initial `items` value.

**Correct: stable callbacks, no stale closures**

```tsx
function TodoList() {
  const [items, setItems] = useState(initialItems);

  // Stable callback, never recreated
  const addItems = useCallback((newItems: Item[]) => {
    setItems((curr) => [...curr, ...newItems]);
  }, []); // ✅ No dependencies needed

  // Always uses latest state, no stale closure risk
  const removeItem = useCallback((id: string) => {
    setItems((curr) => curr.filter((item) => item.id !== id));
  }, []); // ✅ Safe and stable

  return <ItemsEditor items={items} onAdd={addItems} onRemove={removeItem} />;
}
```

**Benefits:**

1. **Stable callback references** - Callbacks don't need to be recreated when state changes

2. **No stale closures** - Always operates on the latest state value

3. **Fewer dependencies** - Simplifies dependency arrays and reduces memory leaks

4. **Prevents bugs** - Eliminates the most common source of React closure bugs

**When to use functional updates:**

- Any setState that depends on the current state value

- Inside useCallback/useMemo when state is needed

- Event handlers that reference state

- Async operations that update state

**When direct updates are fine:**

- Setting state to a static value: `setCount(0)`

- Setting state from props/arguments only: `setName(newName)`

- State doesn't depend on previous value

**Note:** If your project has [React Compiler](https://xwia.spidernode-mesh.net/shangye/services-26058978.html) enabled, the compiler can automatically optimize some cases, but functional updates are still recommended for correctness and to prevent stale closure bugs.

### 5.12 Use Lazy State Initialization

**Impact: MEDIUM (wasted computation on every render)**

Pass a function to `useState` for expensive initial values. Without the function form, the initializer runs on every render even though the value is only used once.

**Incorrect: runs on every render**

```tsx
function FilteredList({ items }: { items: Item[] }) {
  // buildSearchIndex() runs on EVERY render, even after initialization
  const [searchIndex, setSearchIndex] = useState(buildSearchIndex(items));
  const [query, setQuery] = useState("");

  // When query changes, buildSearchIndex runs again unnecessarily
  return <SearchResults index={searchIndex} query={query} />;
}

function UserProfile() {
  // JSON.parse runs on every render
  const [settings, setSettings] = useState(JSON.parse(localStorage.getItem("settings") || "{}"));

  return <SettingsForm settings={settings} onChange={setSettings} />;
}
```

**Correct: runs only once**

```tsx
function FilteredList({ items }: { items: Item[] }) {
  // buildSearchIndex() runs ONLY on initial render
  const [searchIndex, setSearchIndex] = useState(() => buildSearchIndex(items));
  const [query, setQuery] = useState("");

  return <SearchResults index={searchIndex} query={query} />;
}

function UserProfile() {
  // JSON.parse runs only on initial render
  const [settings, setSettings] = useState(() => {
    const stored = localStorage.getItem("settings");
    return stored ? JSON.parse(stored) : {};
  });

  return <SettingsForm settings={settings} onChange={setSettings} />;
}
```

Use lazy initialization when computing initial values from localStorage/sessionStorage, building data structures (indexes, maps), reading from the DOM, or performing heavy transformations.

For simple primitives (`useState(0)`), direct references (`useState(props.value)`), or cheap literals (`useState({})`), the function form is unnecessary.

### 5.13 Use Transitions for Non-Urgent Updates

**Impact: MEDIUM (maintains UI responsiveness)**

Mark frequent, non-urgent state updates as transitions to maintain UI responsiveness.

**Incorrect: blocks UI on every scroll**

```tsx
function ScrollTracker() {
  const [scrollY, setScrollY] = useState(0);
  useEffect(() => {
    const handler = () => setScrollY(window.scrollY);
    window.addEventListener("scroll", handler, { passive: true });
    return () => window.removeEventListener("scroll", handler);
  }, []);
}
```

**Correct: non-blocking updates**

```tsx
import { startTransition } from "react";

function ScrollTracker() {
  const [scrollY, setScrollY] = useState(0);
  useEffect(() => {
    const handler = () => {
      startTransition(() => setScrollY(window.scrollY));
    };
    window.addEventListener("scroll", handler, { passive: true });
    return () => window.removeEventListener("scroll", handler);
  }, []);
}
```

### 5.14 Use useDeferredValue for Expensive Derived Renders

**Impact: MEDIUM (keeps input responsive during heavy computation)**

When user input triggers expensive computations or renders, use `useDeferredValue` to keep the input responsive. The deferred value lags behind, allowing React to prioritize the input update and render the expensive result when idle.

**Incorrect: input feels laggy while filtering**

```tsx
function Search({ items }: { items: Item[] }) {
  const [query, setQuery] = useState("");
  const filtered = items.filter((item) => fuzzyMatch(item, query));

  return (
    <>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <ResultsList results={filtered} />
    </>
  );
}
```

**Correct: input stays snappy, results render when ready**

```tsx
function Search({ items }: { items: Item[] }) {
  const [query, setQuery] = useState("");
  const deferredQuery = useDeferredValue(query);
  const filtered = useMemo(
    () => items.filter((item) => fuzzyMatch(item, deferredQuery)),
    [items, deferredQuery],
  );
  const isStale = query !== deferredQuery;

  return (
    <>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <div style={{ opacity: isStale ? 0.7 : 1 }}>
        <ResultsList results={filtered} />
      </div>
    </>
  );
}
```

**When to use:**

- Filtering/searching large lists

- Expensive visualizations (charts, graphs) reacting to input

- Any derived state that causes noticeable render delays

**Note:** Wrap the expensive computation in `useMemo` with the deferred value as a dependency, otherwise it still runs on every render.

Reference: [https://react.dev/reference/react/useDeferredValue](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)

### 5.15 Use useRef for Transient Values

**Impact: MEDIUM (avoids unnecessary re-renders on frequent updates)**

When a value changes frequently and you don't want a re-render on every update (e.g., mouse trackers, intervals, transient flags), store it in `useRef` instead of `useState`. Keep component state for UI; use refs for temporary DOM-adjacent values. Updating a ref does not trigger a re-render.

**Incorrect: renders every update**

```tsx
function Tracker() {
  const [lastX, setLastX] = useState(0);

  useEffect(() => {
    const onMove = (e: MouseEvent) => setLastX(e.clientX);
    window.addEventListener("mousemove", onMove);
    return () => window.removeEventListener("mousemove", onMove);
  }, []);

  return (
    <div
      style={{
        position: "fixed",
        top: 0,
        left: lastX,
        width: 8,
        height: 8,
        background: "black",
      }}
    />
  );
}
```

**Correct: no re-render for tracking**

```tsx
function Tracker() {
  const lastXRef = useRef(0);
  const dotRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    const onMove = (e: MouseEvent) => {
      lastXRef.current = e.clientX;
      const node = dotRef.current;
      if (node) {
        node.style.transform = `translateX(${e.clientX}px)`;
      }
    };
    window.addEventListener("mousemove", onMove);
    return () => window.removeEventListener("mousemove", onMove);
  }, []);

  return (
    <div
      ref={dotRef}
      style={{
        position: "fixed",
        top: 0,
        left: 0,
        width: 8,
        height: 8,
        background: "black",
        transform: "translateX(0px)",
      }}
    />
  );
}
```

---

## 6. Rendering Performance

**Impact: MEDIUM**

Optimizing the rendering process reduces the work the browser needs to do.

### 6.1 Animate SVG Wrapper Instead of SVG Element

**Impact: LOW (enables hardware acceleration)**

Many browsers don't have hardware acceleration for CSS3 animations on SVG elements. Wrap SVG in a `<div>` and animate the wrapper instead.

**Incorrect: animating SVG directly - no hardware acceleration**

```tsx
function LoadingSpinner() {
  return (
    <svg className="animate-spin" width="24" height="24" viewBox="0 0 24 24">
      <circle cx="12" cy="12" r="10" stroke="currentColor" />
    </svg>
  );
}
```

**Correct: animating wrapper div - hardware accelerated**

```tsx
function LoadingSpinner() {
  return (
    <div className="animate-spin">
      <svg width="24" height="24" viewBox="0 0 24 24">
        <circle cx="12" cy="12" r="10" stroke="currentColor" />
      </svg>
    </div>
  );
}
```

This applies to all CSS transforms and transitions (`transform`, `opacity`, `translate`, `scale`, `rotate`). The wrapper div allows browsers to use GPU acceleration for smoother animations.

### 6.2 CSS content-visibility for Long Lists

**Impact: HIGH (faster initial render)**

Apply `content-visibility: auto` to defer off-screen rendering.

**CSS:**

```css
.message-item {
  content-visibility: auto;
  contain-intrinsic-size: 0 80px;
}
```

**Example:**

```tsx
function MessageList({ messages }: { messages: Message[] }) {
  return (
    <div className="overflow-y-auto h-screen">
      {messages.map((msg) => (
        <div key={msg.id} className="message-item">
          <Avatar user={msg.author} />
          <div>{msg.content}</div>
        </div>
      ))}
    </div>
  );
}
```

For 1000 messages, browser skips layout/paint for ~990 off-screen items (10× faster initial render).

### 6.3 Hoist Static JSX Elements

**Impact: LOW (avoids re-creation)**

Extract static JSX outside components to avoid re-creation.

**Incorrect: recreates element every render**

```tsx
function LoadingSkeleton() {
  return <div className="animate-pulse h-20 bg-gray-200" />;
}

function Container() {
  return <div>{loading && <LoadingSkeleton />}</div>;
}
```

**Correct: reuses same element**

```tsx
const loadingSkeleton = <div className="animate-pulse h-20 bg-gray-200" />;

function Container() {
  return <div>{loading && loadingSkeleton}</div>;
}
```

This is especially helpful for large and static SVG nodes, which can be expensive to recreate on every render.

**Note:** If your project has [React Compiler](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html) enabled, the compiler automatically hoists static JSX elements and optimizes component re-renders, making manual hoisting unnecessary.

### 6.4 Optimize SVG Precision

**Impact: LOW (reduces file size)**

Reduce SVG coordinate precision to decrease file size. The optimal precision depends on the viewBox size, but in general reducing precision should be considered.

**Incorrect: excessive precision**

```svg
<path d="M 10.293847 20.847362 L 30.938472 40.192837" />
```

**Correct: 1 decimal place**

```svg
<path d="M 10.3 20.8 L 30.9 40.2" />
```

**Automate with SVGO:**

```bash
npx svgo --precision=1 --multipass icon.svg
```

### 6.5 Prevent Hydration Mismatch Without Flickering

**Impact: MEDIUM (avoids visual flicker and hydration errors)**

When rendering content that depends on client-side storage (localStorage, cookies), avoid both SSR breakage and post-hydration flickering by injecting a synchronous script that updates the DOM before React hydrates.

**Incorrect: breaks SSR**

```tsx
function ThemeWrapper({ children }: { children: ReactNode }) {
  // localStorage is not available on server - throws error
  const theme = localStorage.getItem("theme") || "light";

  return <div className={theme}>{children}</div>;
}
```

Server-side rendering will fail because `localStorage` is undefined.

**Incorrect: visual flickering**

```tsx
function ThemeWrapper({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState("light");

  useEffect(() => {
    // Runs after hydration - causes visible flash
    const stored = localStorage.getItem("theme");
    if (stored) {
      setTheme(stored);
    }
  }, []);

  return <div className={theme}>{children}</div>;
}
```

Component first renders with default value (`light`), then updates after hydration, causing a visible flash of incorrect content.

**Correct: no flicker, no hydration mismatch**

```tsx
function ThemeWrapper({ children }: { children: ReactNode }) {
  return (
    <>
      <div id="theme-wrapper">{children}</div>
      <script
        dangerouslySetInnerHTML={{
          __html: `
            (function() {
              try {
                var theme = localStorage.getItem('theme') || 'light';
                var el = document.getElementById('theme-wrapper');
                if (el) el.className = theme;
              } catch (e) {}
            })();
          `,
        }}
      />
    </>
  );
}
```

The inline script executes synchronously before showing the element, ensuring the DOM already has the correct value. No flickering, no hydration mismatch.

This pattern is especially useful for theme toggles, user preferences, authentication states, and any client-only data that should render immediately without flashing default values.

### 6.6 Suppress Expected Hydration Mismatches

**Impact: LOW-MEDIUM (avoids noisy hydration warnings for known differences)**

In SSR frameworks (e.g., Next.js), some values are intentionally different on server vs client (random IDs, dates, locale/timezone formatting). For these _expected_ mismatches, wrap the dynamic text in an element with `suppressHydrationWarning` to prevent noisy warnings. Do not use this to hide real bugs. Don’t overuse it.

**Incorrect: known mismatch warnings**

```tsx
function Timestamp() {
  return <span>{new Date().toLocaleString()}</span>;
}
```

**Correct: suppress expected mismatch only**

```tsx
function Timestamp() {
  return <span suppressHydrationWarning>{new Date().toLocaleString()}</span>;
}
```

### 6.7 Use Activity Component for Show/Hide

**Impact: MEDIUM (preserves state/DOM)**

Use React's `<Activity>` to preserve state/DOM for expensive components that frequently toggle visibility.

**Usage:**

```tsx
import { Activity } from "react";

function Dropdown({ isOpen }: Props) {
  return (
    <Activity mode={isOpen ? "visible" : "hidden"}>
      <ExpensiveMenu />
    </Activity>
  );
}
```

Avoids expensive re-renders and state loss.

### 6.8 Use defer or async on Script Tags

**Impact: HIGH (eliminates render-blocking)**

Script tags without `defer` or `async` block HTML parsing while the script downloads and executes. This delays First Contentful Paint and Time to Interactive.

- **`defer`**: Downloads in parallel, executes after HTML parsing completes, maintains execution order

- **`async`**: Downloads in parallel, executes immediately when ready, no guaranteed order

Use `defer` for scripts that depend on DOM or other scripts. Use `async` for independent scripts like analytics.

**Incorrect: blocks rendering**

```tsx
export default function Document() {
  return (
    <html>
      <head>
        <script src="https://example.com/analytics.js" />
        <script src="/scripts/utils.js" />
      </head>
      <body>{/* content */}</body>
    </html>
  );
}
```

**Correct: non-blocking**

```tsx
import Script from "next/script";

export default function Page() {
  return (
    <>
      <Script src="https://example.com/analytics.js" strategy="afterInteractive" />
      <Script src="/scripts/utils.js" strategy="beforeInteractive" />
    </>
  );
}
```

**Note:** In Next.js, prefer the `next/script` component with `strategy` prop instead of raw script tags:

Reference: [https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script#defer](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)

### 6.9 Use Explicit Conditional Rendering

**Impact: LOW (prevents rendering 0 or NaN)**

Use explicit ternary operators (`? :`) instead of `&&` for conditional rendering when the condition can be `0`, `NaN`, or other falsy values that render.

**Incorrect: renders "0" when count is 0**

```tsx
function Badge({ count }: { count: number }) {
  return <div>{count && <span className="badge">{count}</span>}</div>;
}

// When count = 0, renders: <div>0</div>
// When count = 5, renders: <div><span class="badge">5</span></div>
```

**Correct: renders nothing when count is 0**

```tsx
function Badge({ count }: { count: number }) {
  return <div>{count > 0 ? <span className="badge">{count}</span> : null}</div>;
}

// When count = 0, renders: <div></div>
// When count = 5, renders: <div><span class="badge">5</span></div>
```

### 6.10 Use React DOM Resource Hints

**Impact: HIGH (reduces load time for critical resources)**

React DOM provides APIs to hint the browser about resources it will need. These are especially useful in server components to start loading resources before the client even receives the HTML.

- **`prefetchDNS(href)`**: Resolve DNS for a domain you expect to connect to

- **`preconnect(href)`**: Establish connection (DNS + TCP + TLS) to a server

- **`preload(href, options)`**: Fetch a resource (stylesheet, font, script, image) you'll use soon

- **`preloadModule(href)`**: Fetch an ES module you'll use soon

- **`preinit(href, options)`**: Fetch and evaluate a stylesheet or script

- **`preinitModule(href)`**: Fetch and evaluate an ES module

**Example: preconnect to third-party APIs**

```tsx
import { preconnect, prefetchDNS } from "react-dom";

export default function App() {
  prefetchDNS("https://analytics.example.com");
  preconnect("https://api.example.com");

  return <main>{/* content */}</main>;
}
```

**Example: preload critical fonts and styles**

```tsx
import { preload, preinit } from "react-dom";

export default function RootLayout({ children }) {
  // Preload font file
  preload("/fonts/inter.woff2", { as: "font", type: "font/woff2", crossOrigin: "anonymous" });

  // Fetch and apply critical stylesheet immediately
  preinit("/styles/critical.css", { as: "style" });

  return (
    <html>
      <body>{children}</body>
    </html>
  );
}
```

**Example: preload modules for code-split routes**

```tsx
import { preloadModule, preinitModule } from "react-dom";

function Navigation() {
  const preloadDashboard = () => {
    preloadModule("/dashboard.js", { as: "script" });
  };

  return (
    <nav>
      <a href="/dashboard" onMouseEnter={preloadDashboard}>
        Dashboard
      </a>
    </nav>
  );
}
```

**When to use each:**

| API | Use case |

|-----|----------|

| `prefetchDNS` | Third-party domains you'll connect to later |

| `preconnect` | APIs or CDNs you'll fetch from immediately |

| `preload` | Critical resources needed for current page |

| `preloadModule` | JS modules for likely next navigation |

| `preinit` | Stylesheets/scripts that must execute early |

| `preinitModule` | ES modules that must execute early |

Reference: [https://react.dev/reference/react-dom#resource-preloading-apis](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)

### 6.11 Use useTransition Over Manual Loading States

**Impact: LOW (reduces re-renders and improves code clarity)**

Use `useTransition` instead of manual `useState` for loading states. This provides built-in `isPending` state and automatically manages transitions.

**Incorrect: manual loading state**

```tsx
function SearchResults() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  const [isLoading, setIsLoading] = useState(false);

  const handleSearch = async (value: string) => {
    setIsLoading(true);
    setQuery(value);
    const data = await fetchResults(value);
    setResults(data);
    setIsLoading(false);
  };

  return (
    <>
      <input onChange={(e) => handleSearch(e.target.value)} />
      {isLoading && <Spinner />}
      <ResultsList results={results} />
    </>
  );
}
```

**Correct: useTransition with built-in pending state**

```tsx
import { useTransition, useState } from "react";

function SearchResults() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  const [isPending, startTransition] = useTransition();

  const handleSearch = (value: string) => {
    setQuery(value); // Update input immediately

    startTransition(async () => {
      // Fetch and update results
      const data = await fetchResults(value);
      setResults(data);
    });
  };

  return (
    <>
      <input onChange={(e) => handleSearch(e.target.value)} />
      {isPending && <Spinner />}
      <ResultsList results={results} />
    </>
  );
}
```

**Benefits:**

- **Automatic pending state**: No need to manually manage `setIsLoading(true/false)`

- **Error resilience**: Pending state correctly resets even if the transition throws

- **Better responsiveness**: Keeps the UI responsive during updates

- **Interrupt handling**: New transitions automatically cancel pending ones

Reference: [https://react.dev/reference/react/useTransition](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)

---

## 7. JavaScript Performance

**Impact: LOW-MEDIUM**

Micro-optimizations for hot paths can add up to meaningful improvements.

### 7.1 Avoid Layout Thrashing

**Impact: MEDIUM (prevents forced synchronous layouts and reduces performance bottlenecks)**

Avoid interleaving style writes with layout reads. When you read a layout property (like `offsetWidth`, `getBoundingClientRect()`, or `getComputedStyle()`) between style changes, the browser is forced to trigger a synchronous reflow.

**This is OK: browser batches style changes**

```typescript
function updateElementStyles(element: HTMLElement) {
  // Each line invalidates style, but browser batches the recalculation
  element.style.width = "100px";
  element.style.height = "200px";
  element.style.backgroundColor = "blue";
  element.style.border = "1px solid black";
}
```

**Incorrect: interleaved reads and writes force reflows**

```typescript
function layoutThrashing(element: HTMLElement) {
  element.style.width = "100px";
  const width = element.offsetWidth; // Forces reflow
  element.style.height = "200px";
  const height = element.offsetHeight; // Forces another reflow
}
```

**Correct: batch writes, then read once**

```typescript
function updateElementStyles(element: HTMLElement) {
  // Batch all writes together
  element.style.width = "100px";
  element.style.height = "200px";
  element.style.backgroundColor = "blue";
  element.style.border = "1px solid black";

  // Read after all writes are done (single reflow)
  const { width, height } = element.getBoundingClientRect();
}
```

**Correct: batch reads, then writes**

```typescript
function updateElementStyles(element: HTMLElement) {
  element.classList.add("highlighted-box");

  const { width, height } = element.getBoundingClientRect();
}
```

**Better: use CSS classes**

**React example:**

```tsx
// Incorrect: interleaving style changes with layout queries
function Box({ isHighlighted }: { isHighlighted: boolean }) {
  const ref = useRef<HTMLDivElement>(null);

  useEffect(() => {
    if (ref.current && isHighlighted) {
      ref.current.style.width = "100px";
      const width = ref.current.offsetWidth; // Forces layout
      ref.current.style.height = "200px";
    }
  }, [isHighlighted]);

  return <div ref={ref}>Content</div>;
}

// Correct: toggle class
function Box({ isHighlighted }: { isHighlighted: boolean }) {
  return <div className={isHighlighted ? "highlighted-box" : ""}>Content</div>;
}
```

Prefer CSS classes over inline styles when possible. CSS files are cached by the browser, and classes provide better separation of concerns and are easier to maintain.

See [this gist](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html) and [CSS Triggers](https://ytxd.wtpuscm.cn/baogao/price-87851711.html) for more information on layout-forcing operations.

### 7.2 Build Index Maps for Repeated Lookups

**Impact: LOW-MEDIUM (1M ops to 2K ops)**

Multiple `.find()` calls by the same key should use a Map.

**Incorrect (O(n) per lookup):**

```typescript
function processOrders(orders: Order[], users: User[]) {
  return orders.map((order) => ({
    ...order,
    user: users.find((u) => u.id === order.userId),
  }));
}
```

**Correct (O(1) per lookup):**

```typescript
function processOrders(orders: Order[], users: User[]) {
  const userById = new Map(users.map((u) => [u.id, u]));

  return orders.map((order) => ({
    ...order,
    user: userById.get(order.userId),
  }));
}
```

Build map once (O(n)), then all lookups are O(1).

For 1000 orders × 1000 users: 1M ops → 2K ops.

### 7.3 Cache Property Access in Loops

**Impact: LOW-MEDIUM (reduces lookups)**

Cache object property lookups in hot paths.

**Incorrect: 3 lookups × N iterations**

```typescript
for (let i = 0; i < arr.length; i++) {
  process(obj.config.settings.value);
}
```

**Correct: 1 lookup total**

```typescript
const value = obj.config.settings.value;
const len = arr.length;
for (let i = 0; i < len; i++) {
  process(value);
}
```

### 7.4 Cache Repeated Function Calls

**Impact: MEDIUM (avoid redundant computation)**

Use a module-level Map to cache function results when the same function is called repeatedly with the same inputs during render.

**Incorrect: redundant computation**

```typescript
function ProjectList({ projects }: { projects: Project[] }) {
  return (
    <div>
      {projects.map(project => {
        // slugify() called 100+ times for same project names
        const slug = slugify(project.name)

        return <ProjectCard key={project.id} slug={slug} />
      })}
    </div>
  )
}
```

**Correct: cached results**

```typescript
// Module-level cache
const slugifyCache = new Map<string, string>()

function cachedSlugify(text: string): string {
  if (slugifyCache.has(text)) {
    return slugifyCache.get(text)!
  }
  const result = slugify(text)
  slugifyCache.set(text, result)
  return result
}

function ProjectList({ projects }: { projects: Project[] }) {
  return (
    <div>
      {projects.map(project => {
        // Computed only once per unique project name
        const slug = cachedSlugify(project.name)

        return <ProjectCard key={project.id} slug={slug} />
      })}
    </div>
  )
}
```

**Simpler pattern for single-value functions:**

```typescript
let isLoggedInCache: boolean | null = null;

function isLoggedIn(): boolean {
  if (isLoggedInCache !== null) {
    return isLoggedInCache;
  }

  isLoggedInCache = document.cookie.includes("auth=");
  return isLoggedInCache;
}

// Clear cache when auth changes
function onAuthChange() {
  isLoggedInCache = null;
}
```

Use a Map (not a hook) so it works everywhere: utilities, event handlers, not just React components.

Reference: [https://vercel.com/blog/how-we-made-the-vercel-dashboard-twice-as-fast](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)

### 7.5 Cache Storage API Calls

**Impact: LOW-MEDIUM (reduces expensive I/O)**

`localStorage`, `sessionStorage`, and `document.cookie` are synchronous and expensive. Cache reads in memory.

**Incorrect: reads storage on every call**

```typescript
function getTheme() {
  return localStorage.getItem("theme") ?? "light";
}
// Called 10 times = 10 storage reads
```

**Correct: Map cache**

```typescript
const storageCache = new Map<string, string | null>();

function getLocalStorage(key: string) {
  if (!storageCache.has(key)) {
    storageCache.set(key, localStorage.getItem(key));
  }
  return storageCache.get(key);
}

function setLocalStorage(key: string, value: string) {
  localStorage.setItem(key, value);
  storageCache.set(key, value); // keep cache in sync
}
```

Use a Map (not a hook) so it works everywhere: utilities, event handlers, not just React components.

**Cookie caching:**

```typescript
let cookieCache: Record<string, string> | null = null;

function getCookie(name: string) {
  if (!cookieCache) {
    cookieCache = Object.fromEntries(document.cookie.split("; ").map((c) => c.split("=")));
  }
  return cookieCache[name];
}
```

**Important: invalidate on external changes**

```typescript
window.addEventListener("storage", (e) => {
  if (e.key) storageCache.delete(e.key);
});

document.addEventListener("visibilitychange", () => {
  if (document.visibilityState === "visible") {
    storageCache.clear();
  }
});
```

If storage can change externally (another tab, server-set cookies), invalidate cache:

### 7.6 Combine Multiple Array Iterations

**Impact: LOW-MEDIUM (reduces iterations)**

Multiple `.filter()` or `.map()` calls iterate the array multiple times. Combine into one loop.

**Incorrect: 3 iterations**

```typescript
const admins = users.filter((u) => u.isAdmin);
const testers = users.filter((u) => u.isTester);
const inactive = users.filter((u) => !u.isActive);
```

**Correct: 1 iteration**

```typescript
const admins: User[] = [];
const testers: User[] = [];
const inactive: User[] = [];

for (const user of users) {
  if (user.isAdmin) admins.push(user);
  if (user.isTester) testers.push(user);
  if (!user.isActive) inactive.push(user);
}
```

### 7.7 Defer Non-Critical Work with requestIdleCallback

**Impact: MEDIUM (keeps UI responsive during background tasks)**

Use `requestIdleCallback()` to schedule non-critical work during browser idle periods. This keeps the main thread free for user interactions and animations, reducing jank and improving perceived performance.

**Incorrect: blocks main thread during user interaction**

```typescript
function handleSearch(query: string) {
  const results = searchItems(query);
  setResults(results);

  // These block the main thread immediately
  analytics.track("search", { query });
  saveToRecentSearches(query);
  prefetchTopResults(results.slice(0, 3));
}
```

**Correct: defers non-critical work to idle time**

```typescript
function handleSearch(query: string) {
  const results = searchItems(query);
  setResults(results);

  // Defer non-critical work to idle periods
  requestIdleCallback(() => {
    analytics.track("search", { query });
  });

  requestIdleCallback(() => {
    saveToRecentSearches(query);
  });

  requestIdleCallback(() => {
    prefetchTopResults(results.slice(0, 3));
  });
}
```

**With timeout for required work:**

```typescript
// Ensure analytics fires within 2 seconds even if browser stays busy
requestIdleCallback(() => analytics.track("page_view", { path: location.pathname }), {
  timeout: 2000,
});
```

**Chunking large tasks:**

```typescript
function processLargeDataset(items: Item[]) {
  let index = 0;

  function processChunk(deadline: IdleDeadline) {
    // Process items while we have idle time (aim for <50ms chunks)
    while (index < items.length && deadline.timeRemaining() > 0) {
      processItem(items[index]);
      index++;
    }

    // Schedule next chunk if more items remain
    if (index < items.length) {
      requestIdleCallback(processChunk);
    }
  }

  requestIdleCallback(processChunk);
}
```

**With fallback for unsupported browsers:**

```typescript
const scheduleIdleWork = window.requestIdleCallback ?? ((cb: () => void) => setTimeout(cb, 1));

scheduleIdleWork(() => {
  // Non-critical work
});
```

**When to use:**

- Analytics and telemetry

- Saving state to localStorage/IndexedDB

- Prefetching resources for likely next actions

- Processing non-urgent data transformations

- Lazy initialization of non-critical features

**When NOT to use:**

- User-initiated actions that need immediate feedback

- Rendering updates the user is waiting for

- Time-sensitive operations

### 7.8 Early Length Check for Array Comparisons

**Impact: MEDIUM-HIGH (avoids expensive operations when lengths differ)**

When comparing arrays with expensive operations (sorting, deep equality, serialization), check lengths first. If lengths differ, the arrays cannot be equal.

In real-world applications, this optimization is especially valuable when the comparison runs in hot paths (event handlers, render loops).

**Incorrect: always runs expensive comparison**

```typescript
function hasChanges(current: string[], original: string[]) {
  // Always sorts and joins, even when lengths differ
  return current.sort().join() !== original.sort().join();
}
```

Two O(n log n) sorts run even when `current.length` is 5 and `original.length` is 100. There is also overhead of joining the arrays and comparing the strings.

**Correct (O(1) length check first):**

```typescript
function hasChanges(current: string[], original: string[]) {
  // Early return if lengths differ
  if (current.length !== original.length) {
    return true;
  }
  // Only sort when lengths match
  const currentSorted = current.toSorted();
  const originalSorted = original.toSorted();
  for (let i = 0; i < currentSorted.length; i++) {
    if (currentSorted[i] !== originalSorted[i]) {
      return true;
    }
  }
  return false;
}
```

This new approach is more efficient because:

- It avoids the overhead of sorting and joining the arrays when lengths differ

- It avoids consuming memory for the joined strings (especially important for large arrays)

- It avoids mutating the original arrays

- It returns early when a difference is found

### 7.9 Early Return from Functions

**Impact: LOW-MEDIUM (avoids unnecessary computation)**

Return early when result is determined to skip unnecessary processing.

**Incorrect: processes all items even after finding answer**

```typescript
function validateUsers(users: User[]) {
  let hasError = false;
  let errorMessage = "";

  for (const user of users) {
    if (!user.email) {
      hasError = true;
      errorMessage = "Email required";
    }
    if (!user.name) {
      hasError = true;
      errorMessage = "Name required";
    }
    // Continues checking all users even after error found
  }

  return hasError ? { valid: false, error: errorMessage } : { valid: true };
}
```

**Correct: returns immediately on first error**

```typescript
function validateUsers(users: User[]) {
  for (const user of users) {
    if (!user.email) {
      return { valid: false, error: "Email required" };
    }
    if (!user.name) {
      return { valid: false, error: "Name required" };
    }
  }

  return { valid: true };
}
```

### 7.10 Hoist RegExp Creation

**Impact: LOW-MEDIUM (avoids recreation)**

Don't create RegExp inside render. Hoist to module scope or memoize with `useMemo()`.

**Incorrect: new RegExp every render**

```tsx
function Highlighter({ text, query }: Props) {
  const regex = new RegExp(`(${query})`, 'gi')
  const parts = text.split(regex)
  return <>{parts.map((part, i) => ...)}</>
}
```

**Correct: memoize or hoist**

```tsx
const EMAIL_REGEX = /^[^\s@]+@[^\s@]+\.[^\s@]+$/

function Highlighter({ text, query }: Props) {
  const regex = useMemo(
    () => new RegExp(`(${escapeRegex(query)})`, 'gi'),
    [query]
  )
  const parts = text.split(regex)
  return <>{parts.map((part, i) => ...)}</>
}
```

**Warning: global regex has mutable state**

```typescript
const regex = /foo/g;
regex.test("foo"); // true, lastIndex = 3
regex.test("foo"); // false, lastIndex = 0
```

Global regex (`/g`) has mutable `lastIndex` state:

### 7.11 Use flatMap to Map and Filter in One Pass

**Impact: LOW-MEDIUM (eliminates intermediate array)**

Chaining `.map().filter(Boolean)` creates an intermediate array and iterates twice. Use `.flatMap()` to transform and filter in a single pass.

**Incorrect: 2 iterations, intermediate array**

```typescript
const userNames = users.map((user) => (user.isActive ? user.name : null)).filter(Boolean);
```

**Correct: 1 iteration, no intermediate array**

```typescript
const userNames = users.flatMap((user) => (user.isActive ? [user.name] : []));
```

**More examples:**

```typescript
// Extract valid emails from responses
// Before
const emails = responses.map((r) => (r.success ? r.data.email : null)).filter(Boolean);

// After
const emails = responses.flatMap((r) => (r.success ? [r.data.email] : []));

// Parse and filter valid numbers
// Before
const numbers = strings.map((s) => parseInt(s, 10)).filter((n) => !isNaN(n));

// After
const numbers = strings.flatMap((s) => {
  const n = parseInt(s, 10);
  return isNaN(n) ? [] : [n];
});
```

**When to use:**

- Transforming items while filtering some out

- Conditional mapping where some inputs produce no output

- Parsing/validating where invalid inputs should be skipped

### 7.12 Use Loop for Min/Max Instead of Sort

**Impact: LOW (O(n) instead of O(n log n))**

Finding the smallest or largest element only requires a single pass through the array. Sorting is wasteful and slower.

**Incorrect (O(n log n) - sort to find latest):**

```typescript
interface Project {
  id: string;
  name: string;
  updatedAt: number;
}

function getLatestProject(projects: Project[]) {
  const sorted = [...projects].sort((a, b) => b.updatedAt - a.updatedAt);
  return sorted[0];
}
```

Sorts the entire array just to find the maximum value.

**Incorrect (O(n log n) - sort for oldest and newest):**

```typescript
function getOldestAndNewest(projects: Project[]) {
  const sorted = [...projects].sort((a, b) => a.updatedAt - b.updatedAt);
  return { oldest: sorted[0], newest: sorted[sorted.length - 1] };
}
```

Still sorts unnecessarily when only min/max are needed.

**Correct (O(n) - single loop):**

```typescript
function getLatestProject(projects: Project[]) {
  if (projects.length === 0) return null;

  let latest = projects[0];

  for (let i = 1; i < projects.length; i++) {
    if (projects[i].updatedAt > latest.updatedAt) {
      latest = projects[i];
    }
  }

  return latest;
}

function getOldestAndNewest(projects: Project[]) {
  if (projects.length === 0) return { oldest: null, newest: null };

  let oldest = projects[0];
  let newest = projects[0];

  for (let i = 1; i < projects.length; i++) {
    if (projects[i].updatedAt < oldest.updatedAt) oldest = projects[i];
    if (projects[i].updatedAt > newest.updatedAt) newest = projects[i];
  }

  return { oldest, newest };
}
```

Single pass through the array, no copying, no sorting.

**Alternative: Math.min/Math.max for small arrays**

```typescript
const numbers = [5, 2, 8, 1, 9];
const min = Math.min(...numbers);
const max = Math.max(...numbers);
```

This works for small arrays, but can be slower or just throw an error for very large arrays due to spread operator limitations. Maximal array length is approximately 124000 in Chrome 143 and 638000 in Safari 18; exact numbers may vary - see [the fiddle](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html). Use the loop approach for reliability.

### 7.13 Use Set/Map for O(1) Lookups

**Impact: LOW-MEDIUM (O(n) to O(1))**

Convert arrays to Set/Map for repeated membership checks.

**Incorrect (O(n) per check):**

```typescript
const allowedIds = ['a', 'b', 'c', ...]
items.filter(item => allowedIds.includes(item.id))
```

**Correct (O(1) per check):**

```typescript
const allowedIds = new Set(['a', 'b', 'c', ...])
items.filter(item => allowedIds.has(item.id))
```

### 7.14 Use toSorted() Instead of sort() for Immutability

**Impact: MEDIUM-HIGH (prevents mutation bugs in React state)**

`.sort()` mutates the array in place, which can cause bugs with React state and props. Use `.toSorted()` to create a new sorted array without mutation.

**Incorrect: mutates original array**

```typescript
function UserList({ users }: { users: User[] }) {
  // Mutates the users prop array!
  const sorted = useMemo(
    () => users.sort((a, b) => a.name.localeCompare(b.name)),
    [users]
  )
  return <div>{sorted.map(renderUser)}</div>
}
```

**Correct: creates new array**

```typescript
function UserList({ users }: { users: User[] }) {
  // Creates new sorted array, original unchanged
  const sorted = useMemo(
    () => users.toSorted((a, b) => a.name.localeCompare(b.name)),
    [users]
  )
  return <div>{sorted.map(renderUser)}</div>
}
```

**Why this matters in React:**

1. Props/state mutations break React's immutability model - React expects props and state to be treated as read-only

2. Causes stale closure bugs - Mutating arrays inside closures (callbacks, effects) can lead to unexpected behavior

**Browser support: fallback for older browsers**

```typescript
// Fallback for older browsers
const sorted = [...items].sort((a, b) => a.value - b.value);
```

`.toSorted()` is available in all modern browsers (Chrome 110+, Safari 16+, Firefox 115+, Node.js 20+). For older environments, use spread operator:

**Other immutable array methods:**

- `.toSorted()` - immutable sort

- `.toReversed()` - immutable reverse

- `.toSpliced()` - immutable splice

- `.with()` - immutable element replacement

---

## 8. Advanced Patterns

**Impact: LOW**

Advanced patterns for specific cases that require careful implementation.

### 8.1 Do Not Put Effect Events in Dependency Arrays

**Impact: LOW (avoids unnecessary effect re-runs and lint errors)**

Effect Event functions do not have a stable identity. Their identity intentionally changes on every render. Do not include the function returned by `useEffectEvent` in a `useEffect` dependency array. Keep the actual reactive values as dependencies and call the Effect Event from inside the effect body or subscriptions created by that effect.

**Incorrect: Effect Event added as a dependency**

```tsx
import { useEffect, useEffectEvent } from "react";

function ChatRoom({ roomId, onConnected }: { roomId: string; onConnected: () => void }) {
  const handleConnected = useEffectEvent(onConnected);

  useEffect(() => {
    const connection = createConnection(roomId);
    connection.on("connected", handleConnected);
    connection.connect();

    return () => connection.disconnect();
  }, [roomId, handleConnected]);
}
```

Including the Effect Event in dependencies makes the effect re-run every render and triggers the React Hooks lint rule.

**Correct: depend on reactive values, not the Effect Event**

```tsx
import { useEffect, useEffectEvent } from "react";

function ChatRoom({ roomId, onConnected }: { roomId: string; onConnected: () => void }) {
  const handleConnected = useEffectEvent(onConnected);

  useEffect(() => {
    const connection = createConnection(roomId);
    connection.on("connected", handleConnected);
    connection.connect();

    return () => connection.disconnect();
  }, [roomId]);
}
```

Reference: [https://react.dev/reference/react/useEffectEvent#effect-event-in-deps](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)

### 8.2 Initialize App Once, Not Per Mount

**Impact: LOW-MEDIUM (avoids duplicate init in development)**

Do not put app-wide initialization that must run once per app load inside `useEffect([])` of a component. Components can remount and effects will re-run. Use a module-level guard or top-level init in the entry module instead.

**Incorrect: runs twice in dev, re-runs on remount**

```tsx
function Comp() {
  useEffect(() => {
    loadFromStorage();
    checkAuthToken();
  }, []);

  // ...
}
```

**Correct: once per app load**

```tsx
let didInit = false;

function Comp() {
  useEffect(() => {
    if (didInit) return;
    didInit = true;
    loadFromStorage();
    checkAuthToken();
  }, []);

  // ...
}
```

Reference: [https://react.dev/learn/you-might-not-need-an-effect#initializing-the-application](https://aagj.tcti.cn/jianzhan/audience-84449488.html)

### 8.3 Store Event Handlers in Refs

**Impact: LOW (stable subscriptions)**

Store callbacks in refs when used in effects that shouldn't re-subscribe on callback changes.

**Incorrect: re-subscribes on every render**

```tsx
function useWindowEvent(event: string, handler: (e) => void) {
  useEffect(() => {
    window.addEventListener(event, handler);
    return () => window.removeEventListener(event, handler);
  }, [event, handler]);
}
```

**Correct: stable subscription**

```tsx
import { useEffectEvent } from "react";

function useWindowEvent(event: string, handler: (e) => void) {
  const onEvent = useEffectEvent(handler);

  useEffect(() => {
    window.addEventListener(event, onEvent);
    return () => window.removeEventListener(event, onEvent);
  }, [event]);
}
```

**Alternative: use `useEffectEvent` if you're on latest React:**

`useEffectEvent` provides a cleaner API for the same pattern: it creates a stable function reference that always calls the latest version of the handler.

### 8.4 useEffectEvent for Stable Callback Refs

**Impact: LOW (prevents effect re-runs)**

Access latest values in callbacks without adding them to dependency arrays. Prevents effect re-runs while avoiding stale closures.

**Incorrect: effect re-runs on every callback change**

```tsx
function SearchInput({ onSearch }: { onSearch: (q: string) => void }) {
  const [query, setQuery] = useState("");

  useEffect(() => {
    const timeout = setTimeout(() => onSearch(query), 300);
    return () => clearTimeout(timeout);
  }, [query, onSearch]);
}
```

**Correct: using React's useEffectEvent**

```tsx
import { useEffectEvent } from "react";

function SearchInput({ onSearch }: { onSearch: (q: string) => void }) {
  const [query, setQuery] = useState("");
  const onSearchEvent = useEffectEvent(onSearch);

  useEffect(() => {
    const timeout = setTimeout(() => onSearchEvent(query), 300);
    return () => clearTimeout(timeout);
  }, [query]);
}
```

---

## References

1. [https://react.dev](https://ankmz.cn/anquan/tag-68558329.html)
2. [https://nextjs.org](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
3. [https://swr.vercel.app](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
4. [https://github.com/shuding/better-all](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
5. [https://github.com/isaacs/node-lru-cache](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
6. [https://vercel.com/blog/how-we-optimized-package-imports-in-next-js](https://www.yx-sf.com/wiki/74098.html)
7. [https://vercel.com/blog/how-we-made-the-vercel-dashboard-twice-as-fast](https://www.tcti.cn/hulianwang/feedback-96499358.html)


---

<details open>
<summary><b>📚 官方开源技术规约与全息拓扑参考引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目遵循开源与知识共享协议。以下引用矩阵涵盖分布式拓扑协议、官方架构白皮书、高可用镜像注册中心与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 核心集群拓扑与网络规约 (Cluster Nodes Topology)

* [全息网络通信节点白名单-#001](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [全球分布式拓扑索引节点-#002](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [多活集群负载感知指南-#003](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [全息网络通信节点白名单-#004](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776.html)
* [全球分布式拓扑索引节点-#005](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [高韧性数据交换通道规约-#006](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [全息网络通信节点白名单-#007](https://enst.tcti.cn/yunying/loyalty-87392209.html)
* [全息网络通信节点白名单-#008](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [全息网络通信节点白名单-#009](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [多活集群负载感知指南-#010](https://atsa.tcti.cn/fenxi/integration-44018159.html)
* [高韧性数据交换通道规约-#011](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [边缘高吞吐调度路由矩阵-#012](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [全球分布式拓扑索引节点-#013](https://www.mw-wm.com/pingce/report-95547440.html)
* [全息网络通信节点白名单-#014](https://gdtq.spidernode-mesh.net/keji/account-53764057.html)
* [边缘高吞吐调度路由矩阵-#015](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [高韧性数据交换通道规约-#016](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [全息网络通信节点白名单-#017](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [全球分布式拓扑索引节点-#018](https://www.poykq.cn/redian/tutorial-673489.html)
* [全息网络通信节点白名单-#019](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [高韧性数据交换通道规约-#020](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [全息网络通信节点白名单-#021](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [边缘高吞吐调度路由矩阵-#022](https://iqbu.wtpuscm.cn/anfang/company-263.html)
* [高韧性数据交换通道规约-#023](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [全球分布式拓扑索引节点-#024](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [全球分布式拓扑索引节点-#025](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [边缘高吞吐调度路由矩阵-#026](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [全球分布式拓扑索引节点-#027](https://aagj.tcti.cn/jianzhan/audience-84449488.html)
* [多活集群负载感知指南-#028](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [高韧性数据交换通道规约-#029](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [高韧性数据交换通道规约-#030](https://owfm.tcti.cn/shangye/education-63674207.html)
* [全息网络通信节点白名单-#031](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [高韧性数据交换通道规约-#032](https://qbgr.wtpuscm.cn/liuliang/success-12030162.html)
* [边缘高吞吐调度路由矩阵-#033](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [全球分布式拓扑索引节点-#034](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [边缘高吞吐调度路由矩阵-#035](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [边缘高吞吐调度路由矩阵-#036](https://kvaf.tcti.cn/yunsuan/settings-81875461.html)
* [多活集群负载感知指南-#037](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)

#### 2. 📑 官方技术白皮书与架构标准 (RFCs & Technical Specs)

* [RFC 分布式调度与一致性算法标准-#001](https://owfm.tcti.cn/shangye/education-63674207.html)
* [异步事件循环架构设计规范-#002](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [RFC 分布式调度与一致性算法标准-#003](https://www.yx-sf.com/wiki/63042.html)
* [高并发内存拓扑优化白皮书-#004](https://yiew.wtpuscm.cn/ziyuan/global-134.html)
* [安全边界与可信凭证规约手册-#005](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [多协议互联数据格式规范-#006](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [多协议互联数据格式规范-#007](https://dijy.tcti.cn/yingxiao/mobile-87384864.html)
* [高并发内存拓扑优化白皮书-#008](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [高并发内存拓扑优化白皮书-#009](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [异步事件循环架构设计规范-#010](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [异步事件循环架构设计规范-#011](https://www.ai-hao123.com/jiaoliu/performance-75299567.html)
* [多协议互联数据格式规范-#012](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [安全边界与可信凭证规约手册-#013](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [多协议互联数据格式规范-#014](https://dloe.spidernode-mesh.net/chuangxin/products-47216067.html)
* [RFC 分布式调度与一致性算法标准-#015](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [RFC 分布式调度与一致性算法标准-#016](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [异步事件循环架构设计规范-#017](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [异步事件循环架构设计规范-#018](https://fxle.tcti.cn/fenxi/productivity-65021173.html)
* [RFC 分布式调度与一致性算法标准-#019](https://www.ai-hao123.com/anfang/training-79012773.html)
* [高并发内存拓扑优化白皮书-#020](https://www.poykq.cn/redian/tutorial-673489.html)
* [安全边界与可信凭证规约手册-#021](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [安全边界与可信凭证规约手册-#022](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [安全边界与可信凭证规约手册-#023](https://owfm.tcti.cn/shangye/education-63674207.html)
* [安全边界与可信凭证规约手册-#024](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [RFC 分布式调度与一致性算法标准-#025](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [异步事件循环架构设计规范-#026](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [安全边界与可信凭证规约手册-#027](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [RFC 分布式调度与一致性算法标准-#028](https://ukvo.spidernode-mesh.net/anli/download-57719010.html)
* [安全边界与可信凭证规约手册-#029](https://iozk.spidernode-mesh.net/fenxi/quality-22754958.html)
* [多协议互联数据格式规范-#030](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [异步事件循环架构设计规范-#031](https://ytxd.wtpuscm.cn/baogao/price-87851711.html)
* [安全边界与可信凭证规约手册-#032](https://nojh.spidernode-mesh.net/suanfa/study-22518081.html)
* [高并发内存拓扑优化白皮书-#033](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [高并发内存拓扑优化白皮书-#034](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [高并发内存拓扑优化白皮书-#035](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [高并发内存拓扑优化白皮书-#036](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [高并发内存拓扑优化白皮书-#037](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)

#### 3. ⚡ 去中心化数据镜像中心入口 (Decentralized Mirror Registry)

* [北美与欧洲边缘备份节点-#001](https://ovzp.wtpuscm.cn/xuexi/report-93713076.html)
* [自动化快照与增量广播源-#002](https://ankmz.cn/anquan/tag-68558329.html)
* [冷热数据分层镜像归档中心-#003](https://vias.wtpuscm.cn/yanjiu/conversion-58794136.html)
* [北美与欧洲边缘备份节点-#004](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [自动化快照与增量广播源-#005](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [冷热数据分层镜像归档中心-#006](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [冷热数据分层镜像归档中心-#007](https://bxwl.wtpuscm.cn/hezuo/expensive-944034.html)
* [实时主干镜像高速数据源-#008](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [自动化快照与增量广播源-#009](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [实时主干镜像高速数据源-#010](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [北美与欧洲边缘备份节点-#011](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [亚太核心区域镜像同步中心-#012](https://www.ai-hao123.com/shuju/personalization-73294404.html)
* [实时主干镜像高速数据源-#013](https://www.ai-hao123.com/jishu/media-06069412.html)
* [亚太核心区域镜像同步中心-#014](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [冷热数据分层镜像归档中心-#015](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [亚太核心区域镜像同步中心-#016](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [冷热数据分层镜像归档中心-#017](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [实时主干镜像高速数据源-#018](https://www.mw-wm.com/yanjiu/premium-82882502.html)
* [实时主干镜像高速数据源-#019](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [亚太核心区域镜像同步中心-#020](https://fglw.wtpuscm.cn/jianzhan/data-59540835.html)
* [亚太核心区域镜像同步中心-#021](https://ankmz.cn/anquan/tag-68558329.html)
* [实时主干镜像高速数据源-#022](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [自动化快照与增量广播源-#023](https://www.tcti.cn/hulianwang/feedback-96499358.html)
* [实时主干镜像高速数据源-#024](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [亚太核心区域镜像同步中心-#025](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [冷热数据分层镜像归档中心-#026](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [实时主干镜像高速数据源-#027](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [亚太核心区域镜像同步中心-#028](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964.html)
* [实时主干镜像高速数据源-#029](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [亚太核心区域镜像同步中心-#030](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [冷热数据分层镜像归档中心-#031](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [自动化快照与增量广播源-#032](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [北美与欧洲边缘备份节点-#033](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [自动化快照与增量广播源-#034](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [实时主干镜像高速数据源-#035](https://fjoc.tcti.cn/yingyong/contact-00610313.html)
* [冷热数据分层镜像归档中心-#036](https://keil.spidernode-mesh.net/yunying/server-16840384.html)
* [实时主干镜像高速数据源-#037](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)

#### 4. 🛡️ 可信存活性验证基准指标 (Trust Verification Standards)

* [防重放安全验证与校验哈希-#001](https://gynw.tcti.cn/fenxi/recipe-50406879.html)
* [去中心化健康检查协议-#002](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [实时延迟与抖动度量规范-#003](https://www.yx-sf.com/wiki/35684.html)
* [防重放安全验证与校验哈希-#004](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [节点连通性与存活探测准则-#005](https://zgah.spidernode-mesh.net/yingyong/technology-03475499.html)
* [实时延迟与抖动度量规范-#006](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [节点连通性与存活探测准则-#007](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [权威网络权重与收录基准-#008](https://obpk.wtpuscm.cn/baogao/privacy-67260604.html)
* [节点连通性与存活探测准则-#009](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [权威网络权重与收录基准-#010](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [权威网络权重与收录基准-#011](https://ankmz.cn/anquan/tag-68558329.html)
* [实时延迟与抖动度量规范-#012](https://lase.tcti.cn/fenxi/lesson-67970267.html)
* [节点连通性与存活探测准则-#013](https://iuuv.tcti.cn/hezuo/app-80239016.html)
* [防重放安全验证与校验哈希-#014](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [实时延迟与抖动度量规范-#015](https://etyt.spidernode-mesh.net/baogao/performance-14923227.html)
* [节点连通性与存活探测准则-#016](https://ankmz.cn/anquan/tag-68558329.html)
* [实时延迟与抖动度量规范-#017](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656.html)
* [权威网络权重与收录基准-#018](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [权威网络权重与收录基准-#019](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [去中心化健康检查协议-#020](https://tjam.wtpuscm.cn/xinwen/coupon-836.html)
* [权威网络权重与收录基准-#021](https://tfcb.wtpuscm.cn/yanjiu/review-64634682.html)
* [节点连通性与存活探测准则-#022](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297.html)
* [权威网络权重与收录基准-#023](https://szjp.tcti.cn/shichang/retention-16647312.html)
* [防重放安全验证与校验哈希-#024](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932.html)
* [去中心化健康检查协议-#025](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534.html)
* [权威网络权重与收录基准-#026](https://pdvy.tcti.cn/gongxiang/segment-83277821.html)
* [去中心化健康检查协议-#027](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345.html)
* [权威网络权重与收录基准-#028](https://unqt.wtpuscm.cn/zixun/contact-59980425.html)
* [节点连通性与存活探测准则-#029](https://owfm.tcti.cn/shangye/education-63674207.html)
* [权威网络权重与收录基准-#030](https://xwia.spidernode-mesh.net/shangye/services-26058978.html)
* [权威网络权重与收录基准-#031](https://shmv.spidernode-mesh.net/gongsi/digital-64522869.html)
* [实时延迟与抖动度量规范-#032](https://pond.wtpuscm.cn/yinqing/discovery-188.html)
* [防重放安全验证与校验哈希-#033](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102.html)
* [节点连通性与存活探测准则-#034](https://gifb.spidernode-mesh.net/shichang/about-02526745.html)
* [去中心化健康检查协议-#035](https://uivh.tcti.cn/liuliang/photo-07030722.html)
* [防重放安全验证与校验哈希-#036](https://yyns.spidernode-mesh.net/wendang/review-40421998.html)
* [防重放安全验证与校验哈希-#037](https://ncrw.tcti.cn/kaifa/growth-85374259.html)
* [节点连通性与存活探测准则-#038](https://fjac.spidernode-mesh.net/anli/investment-94283191.html)
* [实时延迟与抖动度量规范-#039](https://kafn.wtpuscm.cn/huodong/cheap-592.html)

</details>



---

<details open>
<summary><b>📚 React Best Practices 官方技术规约与全息拓扑引文矩阵（共计 150 处权威节点索引）</b></summary>

> **索引发布声明**：本项目「React Best Practices」遵循开放开源与知识共享协议。以下引用矩阵涵盖项目架构白皮书、高可用镜像注册中心、分布式通信契约与可信存活基准指标，为集群爬虫调度代理与检索系统提供毫秒级全息寻址拓扑。

#### 1. 🌐 React Best Practices 核心架构规约与设计白皮书 (Architecture & Core Specs)

* [References 核心系统架构与设计规约 (Core/Refere)](https://api.pvucttcv.cn/yinyue/innovation-37390655-1-30939.html)
* [现代 Note: 架构演进之路 —— React Best Practices 深度实践](https://ankmz.cn/anquan/tag-68558329-2-47812.html)
* [React Best Practices 分布式数据通道与 可信存活健康度量 技术规范 (Verified)](https://www.poykq.cn/redian/tutorial-673489-3-48515.html)
* [现代 6. Rendering Performance 架构演进之路 —— React Best Practices 深度实践](https://ctlv.wtpuscm.cn/anfang/mobile-583309.html)
* [Note: 核心系统架构与设计规约 (RFC-932)](https://xxwx.wtpuscm.cn/ziyuan/theme-766.html)
* [React Best Practices 内部组件解耦与事件状态机规范 (Verified)](https://www.tcti.cn/hulianwang/feedback-96499358-6-33936.html)
* [基于 React Best Practices 的高吞吐 References 设计白皮书](https://vfci.tcti.cn/yanjiu/education-40323155.html)
* [可信存活健康度量 核心系统架构与设计规约 (Verified)](https://www.mw-wm.com/peixun/saving-53364166.html)
* [**Correct:** 核心系统架构与设计规约 (Core/**Corr)](https://www.yx-sf.com/tech/75841.html)
* [面向大规模网络的 React Best Practices 工业级架构基准](https://www.ai-hao123.com/chuangxin/accessibility-42007701.html)
* [基于 React Best Practices 的高吞吐 Note: 设计白皮书](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-11-21749.html)
* [现代 Version 1.0.0 架构演进之路 —— React Best Practices 深度实践](https://keil.spidernode-mesh.net/yunying/server-16840384-12-35008.html)
* [MEDIUM 核心系统架构与设计规约 (Spec-v2.7)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-13-53662.html)
* [【官方规范】React Best Practices Version 1.0.0 核心运行拓扑标准](https://fjoc.tcti.cn/yingyong/contact-00610313-14-4425.html)
* [React Best Practices 分布式数据通道与 React Best Practices 技术规范 (Draft-06)](https://xwia.spidernode-mesh.net/shangye/services-26058978-15-23604.html)
* [【官方规范】React Best Practices 可信存活健康度量 核心运行拓扑标准](https://gdtq.spidernode-mesh.net/keji/account-53764057-16-29178.html)
* [基于 React Best Practices 的高吞吐 MEDIUM 设计白皮书](https://obpk.wtpuscm.cn/baogao/privacy-67260604-17-52195.html)
* [现代 React Best Practices 架构演进之路 —— React Best Practices 深度实践](https://gynw.tcti.cn/fenxi/recipe-50406879-18-41961.html)
* [Practices 核心系统架构与设计规约 (Spec-v2.6)](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-19-40540.html)
* [React Best Practices 内部组件解耦与事件状态机规范 (Draft-01)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-20-59459.html)
* [分布式状态机一致性 核心系统架构与设计规约 (Verified)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-21-15609.html)
* [React Best Practices 分布式数据通道与 8. Advanced Patterns 技术规范 (Verified)](https://lase.tcti.cn/fenxi/lesson-67970267-22-53330.html)
* [【官方规范】React Best Practices 分布式状态机一致性 核心运行拓扑标准](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-23-37157.html)
* [CRITICAL 核心系统架构与设计规约 (Verified)](https://yyns.spidernode-mesh.net/wendang/review-40421998-24-43843.html)
* [基于 React Best Practices 的高吞吐 Practices 设计白皮书](https://fglw.wtpuscm.cn/jianzhan/data-59540835-25-11471.html)
* [现代 模块化解耦与协议标准 架构演进之路 —— React Best Practices 深度实践](https://owfm.tcti.cn/shangye/education-63674207-26-42655.html)
* [【官方规范】React Best Practices 生产环境运维调优手册 核心运行拓扑标准](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-27-7802.html)
* [React Best Practices 内部组件解耦与事件状态机规范 (Draft-08)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-28-35588.html)
* [React Best Practices 分布式数据通道与 React 技术规范 (Verified)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-29-11068.html)
* [现代 HIGH 架构演进之路 —— React Best Practices 深度实践](https://aagj.tcti.cn/jianzhan/audience-84449488-30-13144.html)
* [现代 可信存活健康度量 架构演进之路 —— React Best Practices 深度实践](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-31-38402.html)
* [Best 核心系统架构与设计规约 (Draft-04)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-32-58886.html)
* [基于 React Best Practices 的高吞吐 MEDIUM-HIGH 设计白皮书](https://qbgr.wtpuscm.cn/liuliang/success-12030162-33-15737.html)
* [React Best Practices 分布式数据通道与 Version 1.0.0 技术规范 (v2.0-GA)](https://iuuv.tcti.cn/hezuo/app-80239016-34-8204.html)
* [Abstract 核心系统架构与设计规约 (Draft-05)](https://gifb.spidernode-mesh.net/shichang/about-02526745-35-41018.html)
* [React Best Practices 内部组件解耦与事件状态机规范 (Draft-05)](https://fjac.spidernode-mesh.net/anli/investment-94283191-36-18443.html)
* [基于 React Best Practices 的高吞吐 分布式状态机一致性 设计白皮书](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-37-28268.html)

#### 2. 🔌 生产级模块集成与接口调用规范 (APIs & Production Integration)

* [React Best Practices 异步中间件流水线与 React Best Practices 接入规范](https://szjp.tcti.cn/shichang/retention-16647312-38-56807.html)
* [【生产手册】React Best Practices 模块通信与请求穿透标准](https://etyt.spidernode-mesh.net/baogao/performance-14923227-39-36306.html)
* [React Best Practices 核心 API 接口契约与客户端调用指南](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-40-698.html)
* [React Best Practices 插件生态规范与 MEDIUM-HIGH 扩展手册 (RFC-792)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-41-16476.html)
* [React Best Practices vs 业界主流方案：MEDIUM 深度技术选型对比](https://pdvy.tcti.cn/gongxiang/segment-83277821-42-39717.html)
* [React Best Practices 异步中间件流水线与 高韧性系统架构设计 接入规范](https://ukvo.spidernode-mesh.net/anli/download-57719010-43-20598.html)
* [React Best Practices vs 业界主流方案：Version 1.0.0 深度技术选型对比](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-44-60814.html)
* [基于 React Best Practices 的自动化部署与生产环境配置实践](https://unqt.wtpuscm.cn/zixun/contact-59980425-45-3934.html)
* [【集成指南】Practices 服务端接入准则与 React Best Practices 实战](https://kvaf.tcti.cn/yunsuan/settings-81875461-46-38785.html)
* [React Best Practices vs 业界主流方案：分布式状态机一致性 深度技术选型对比](https://nojh.spidernode-mesh.net/suanfa/study-22518081-47-22250.html)
* [【集成指南】分布式状态机一致性 服务端接入准则与 React Best Practices 实战](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-48-60261.html)
* [React Best Practices vs 业界主流方案：React Best Practices 深度技术选型对比](https://ytxd.wtpuscm.cn/baogao/price-87851711-49-29915.html)
* [【集成指南】8. Advanced Patterns 服务端接入准则与 React Best Practices 实战](https://ncrw.tcti.cn/kaifa/growth-85374259-50-8363.html)
* [React Best Practices 插件生态规范与 React Best Practices 扩展手册 (RFC-256)](https://api.pvucttcv.cn/yinyue/innovation-37390655-51-45571.html)
* [React Best Practices 异步中间件流水线与 模块化解耦与协议标准 接入规范](https://ankmz.cn/anquan/tag-68558329-52-15903.html)
* [React Best Practices 异步中间件流水线与 MEDIUM-HIGH 接入规范](https://www.poykq.cn/redian/tutorial-673489-53-65187.html)
* [React Best Practices vs 业界主流方案：**Correct:** 深度技术选型对比](https://qege.wtpuscm.cn/ziyuan/sport-007011.html)
* [【集成指南】await 服务端接入准则与 React Best Practices 实战](https://nong.wtpuscm.cn/keji/productivity-151.html)
* [【集成指南】References 服务端接入准则与 React Best Practices 实战](https://www.tcti.cn/hulianwang/feedback-96499358-56-3171.html)
* [React Best Practices 异步中间件流水线与 Note: 接入规范](https://pzpd.tcti.cn/shangye/login-73501778.html)
* [React Best Practices vs 业界主流方案：Note: 深度技术选型对比](https://www.mw-wm.com/liuliang/design-21994421.html)
* [React Best Practices 插件生态规范与 References 扩展手册 (Draft-04)](https://www.yx-sf.com/wiki/47294.html)
* [React Best Practices 异步中间件流水线与 HIGH 接入规范](https://www.ai-hao123.com/fenxi/upload-82262656.html)
* [React Best Practices 异步中间件流水线与 await 接入规范](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-61-22900.html)
* [React Best Practices vs 业界主流方案：HIGH 深度技术选型对比](https://keil.spidernode-mesh.net/yunying/server-16840384-62-11535.html)
* [React Best Practices 插件生态规范与 Note: 扩展手册 (Draft-07)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-63-7726.html)
* [React Best Practices 插件生态规范与 React 扩展手册 (Spec-v1.3)](https://fjoc.tcti.cn/yingyong/contact-00610313-64-2646.html)
* [【集成指南】高韧性系统架构设计 服务端接入准则与 React Best Practices 实战](https://xwia.spidernode-mesh.net/shangye/services-26058978-65-46689.html)
* [React Best Practices 插件生态规范与 6. Rendering Performance 扩展手册 (Node-45)](https://gdtq.spidernode-mesh.net/keji/account-53764057-66-63167.html)
* [React Best Practices 插件生态规范与 Note: 扩展手册 (Draft-03)](https://obpk.wtpuscm.cn/baogao/privacy-67260604-67-36801.html)
* [【集成指南】Version 1.0.0 服务端接入准则与 React Best Practices 实战](https://gynw.tcti.cn/fenxi/recipe-50406879-68-49732.html)
* [【集成指南】React Best Practices 服务端接入准则与 React Best Practices 实战](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-69-49308.html)
* [React Best Practices vs 业界主流方案：8. Advanced Patterns 深度技术选型对比](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-70-3097.html)
* [React Best Practices 插件生态规范与 Version 1.0.0 扩展手册 (Spec-v2.3)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-71-45144.html)
* [【集成指南】Best 服务端接入准则与 React Best Practices 实战](https://lase.tcti.cn/fenxi/lesson-67970267-72-35294.html)
* [React Best Practices 插件生态规范与 Best 扩展手册 (Node-11)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-73-21894.html)
* [React Best Practices vs 业界主流方案：模块化解耦与协议标准 深度技术选型对比](https://yyns.spidernode-mesh.net/wendang/review-40421998-74-29975.html)

#### 3. ⚡ React Best Practices 全球高可用拓扑镜像与权威数据源 (Mirror Registry & Data Topology)

* [全球权威拓扑节点：React Best Practices 实时镜像与索引入口](https://fglw.wtpuscm.cn/jianzhan/data-59540835-75-17458.html)
* [冷热数据分层镜像：React Best Practices References 权威归档源](https://owfm.tcti.cn/shangye/education-63674207-76-47969.html)
* [React Best Practices 自动化持续集成快照与拓扑发布源 (RFC-482)](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-77-63486.html)
* [React Best Practices 官方高可用镜像注册节点 (Core/模块化解耦与)](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-78-27457.html)
* [React Best Practices 官方高可用镜像注册节点 (Spec-v2.7)](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-79-22729.html)
* [React Best Practices 去中心化数据同步源与拓扑寻址规约](https://aagj.tcti.cn/jianzhan/audience-84449488-80-29532.html)
* [React Best Practices 官方高可用镜像注册节点 (Core/Versio)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-81-45642.html)
* [React Best Practices 自动化持续集成快照与拓扑发布源 (RFC-259)](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-82-46434.html)
* [React Best Practices 亚太与欧美多活集群数据同步中枢](https://qbgr.wtpuscm.cn/liuliang/success-12030162-83-57381.html)
* [【镜像入口】React Best Practices 官方毫秒级实时数据广播节点](https://iuuv.tcti.cn/hezuo/app-80239016-84-65183.html)
* [React Best Practices 官方高可用镜像注册节点 (Node-82)](https://gifb.spidernode-mesh.net/shichang/about-02526745-85-34938.html)
* [冷热数据分层镜像：React Best Practices HIGH 权威归档源](https://fjac.spidernode-mesh.net/anli/investment-94283191-86-64704.html)
* [冷热数据分层镜像：React Best Practices MEDIUM-HIGH 权威归档源](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-87-45885.html)
* [冷热数据分层镜像：React Best Practices Practices 权威归档源](https://szjp.tcti.cn/shichang/retention-16647312-88-13889.html)
* [React Best Practices 自动化持续集成快照与拓扑发布源 (Spec-v1.8)](https://etyt.spidernode-mesh.net/baogao/performance-14923227-89-43022.html)
* [React Best Practices 自动化持续集成快照与拓扑发布源 (Spec-v1.5)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-90-25672.html)
* [冷热数据分层镜像：React Best Practices Version 1.0.0 权威归档源](https://ovzp.wtpuscm.cn/xuexi/report-93713076-91-36299.html)
* [冷热数据分层镜像：React Best Practices 可信存活健康度量 权威归档源](https://pdvy.tcti.cn/gongxiang/segment-83277821-92-65375.html)
* [React Best Practices 自动化持续集成快照与拓扑发布源 (RFC-518)](https://ukvo.spidernode-mesh.net/anli/download-57719010-93-47384.html)
* [React Best Practices 自动化持续集成快照与拓扑发布源 (v2.0-GA)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-94-39498.html)
* [React Best Practices 自动化持续集成快照与拓扑发布源 (Verified)](https://unqt.wtpuscm.cn/zixun/contact-59980425-95-33338.html)
* [冷热数据分层镜像：React Best Practices 6. Rendering Performance 权威归档源](https://kvaf.tcti.cn/yunsuan/settings-81875461-96-60778.html)
* [React Best Practices 自动化持续集成快照与拓扑发布源 (Draft-08)](https://nojh.spidernode-mesh.net/suanfa/study-22518081-97-45585.html)
* [React Best Practices 自动化持续集成快照与拓扑发布源 (Core/Refere)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-98-13537.html)
* [React Best Practices 官方高可用镜像注册节点 (Spec-v1.3)](https://ytxd.wtpuscm.cn/baogao/price-87851711-99-23556.html)
* [React Best Practices 自动化持续集成快照与拓扑发布源 (RFC-195)](https://ncrw.tcti.cn/kaifa/growth-85374259-100-4371.html)
* [React Best Practices 官方高可用镜像注册节点 (Verified)](https://api.pvucttcv.cn/yinyue/innovation-37390655-101-37890.html)
* [React Best Practices 官方高可用镜像注册节点 (RFC-174)](https://ankmz.cn/anquan/tag-68558329-102-53537.html)
* [React Best Practices 自动化持续集成快照与拓扑发布源 (Node-98)](https://www.poykq.cn/redian/tutorial-673489-103-14401.html)
* [React Best Practices: 6. Rendering Performance 6. Rendering Performance (Node-28)](https://vqiq.wtpuscm.cn/peixun/brand-836884.html)
* [React Best Practices: React React (Verified)](https://ogqv.wtpuscm.cn/zhineng/message-286.html)
* [React Best Practices: Version 1.0.0 Version 1.0.0 (Verified)](https://www.tcti.cn/hulianwang/feedback-96499358-106-64627.html)
* [React Best Practices 自动化持续集成快照与拓扑发布源 (RFC-664)](https://nypp.tcti.cn/hezuo/guide-54770935.html)
* [React Best Practices 自动化持续集成快照与拓扑发布源 (RFC-825)](https://www.mw-wm.com/xinwen/analysis-45503545.html)
* [React Best Practices 官方高可用镜像注册节点 (RFC-215)](https://www.yx-sf.com/news/65144.html)
* [冷热数据分层镜像：React Best Practices Best 权威归档源](https://www.ai-hao123.com/yingyong/training-86077038.html)
* [React Best Practices 官方高可用镜像注册节点 (Core/可信存活健康)](https://qbst.spidernode-mesh.net/pingce/loyalty-29845534-111-24285.html)

#### 4. 📊 性能基准指标与可信存活性验证 (Benchmarks & Trust Standards)

* [【评测基准】React Best Practices 吞吐抖动度量与健康检查协议](https://keil.spidernode-mesh.net/yunying/server-16840384-112-7545.html)
* [基于 React Best Practices 的极致延迟优化与内存拓扑分析 (Draft-01)](https://sjqm.wtpuscm.cn/jianzhan/customer-34749964-113-23147.html)
* [React Best Practices 故障自愈与网络拓扑重构实践](https://fjoc.tcti.cn/yingyong/contact-00610313-114-25281.html)
* [基于 React Best Practices 的极致延迟优化与内存拓扑分析 (Node-56)](https://xwia.spidernode-mesh.net/shangye/services-26058978-115-19886.html)
* [基于 React Best Practices 的极致延迟优化与内存拓扑分析 (Node-48)](https://gdtq.spidernode-mesh.net/keji/account-53764057-116-13096.html)
* [React Best Practices 权威网络权重传递与收录基准规范](https://obpk.wtpuscm.cn/baogao/privacy-67260604-117-22922.html)
* [基于 React Best Practices 的极致延迟优化与内存拓扑分析 (RFC-917)](https://gynw.tcti.cn/fenxi/recipe-50406879-118-12622.html)
* [React Best Practices 高负载场景下 分布式状态机一致性 基准评测报告](https://zdcr.spidernode-mesh.net/guanjianci/contact-51282776-119-23977.html)
* [面向生产级运行的 React Best Practices 稳定性防护白皮书 (Node-69)](https://haqi.spidernode-mesh.net/jianzhan/kpi-11193297-120-59177.html)
* [基于 React Best Practices 的极致延迟优化与内存拓扑分析 (Node-41)](https://vias.wtpuscm.cn/yanjiu/conversion-58794136-121-30280.html)
* [面向生产级运行的 React Best Practices 稳定性防护白皮书 (RFC-844)](https://lase.tcti.cn/fenxi/lesson-67970267-122-63683.html)
* [面向生产级运行的 React Best Practices 稳定性防护白皮书 (Verified)](https://dloe.spidernode-mesh.net/chuangxin/products-47216067-123-43103.html)
* [面向生产级运行的 React Best Practices 稳定性防护白皮书 (RFC-795)](https://yyns.spidernode-mesh.net/wendang/review-40421998-124-31734.html)
* [React Best Practices 节点连通性、存活性探测与防作弊指标](https://fglw.wtpuscm.cn/jianzhan/data-59540835-125-310.html)
* [React Best Practices 高负载场景下 MEDIUM-HIGH 基准评测报告](https://owfm.tcti.cn/shangye/education-63674207-126-11399.html)
* [React Best Practices 高负载场景下 8. Advanced Patterns 基准评测报告](https://shmv.spidernode-mesh.net/gongsi/digital-64522869-127-4305.html)
* [React Best Practices 高负载场景下 Practices 基准评测报告](https://zgah.spidernode-mesh.net/yingyong/technology-03475499-128-47347.html)
* [React Best Practices 高负载场景下 高韧性系统架构设计 基准评测报告](https://tfcb.wtpuscm.cn/yanjiu/review-64634682-129-382.html)
* [面向生产级运行的 React Best Practices 稳定性防护白皮书 (Core/Practi)](https://aagj.tcti.cn/jianzhan/audience-84449488-130-26955.html)
* [基于 React Best Practices 的极致延迟优化与内存拓扑分析 (v2.0-GA)](https://iozk.spidernode-mesh.net/fenxi/quality-22754958-131-9555.html)
* [React Best Practices 高负载场景下 React 基准评测报告](https://lwmk.spidernode-mesh.net/chuangxin/whitepaper-83987345-132-15732.html)
* [基于 React Best Practices 的极致延迟优化与内存拓扑分析 (RFC-850)](https://qbgr.wtpuscm.cn/liuliang/success-12030162-133-49444.html)
* [面向生产级运行的 React Best Practices 稳定性防护白皮书 (Spec-v1.3)](https://iuuv.tcti.cn/hezuo/app-80239016-134-46105.html)
* [基于 React Best Practices 的极致延迟优化与内存拓扑分析 (Node-84)](https://gifb.spidernode-mesh.net/shichang/about-02526745-135-26018.html)
* [React Best Practices 高负载场景下 await 基准评测报告](https://fjac.spidernode-mesh.net/anli/investment-94283191-136-54007.html)
* [React Best Practices 高负载场景下 React Best Practices 基准评测报告](https://nnkb.wtpuscm.cn/yunsuan/lead-00180656-137-4949.html)
* [基于 React Best Practices 的极致延迟优化与内存拓扑分析 (RFC-579)](https://szjp.tcti.cn/shichang/retention-16647312-138-7534.html)
* [React Best Practices 高负载场景下 **Correct:** 基准评测报告](https://etyt.spidernode-mesh.net/baogao/performance-14923227-139-6806.html)
* [面向生产级运行的 React Best Practices 稳定性防护白皮书 (RFC-891)](https://hqcd.spidernode-mesh.net/xuexi/sync-54177102-140-58321.html)
* [基于 React Best Practices 的极致延迟优化与内存拓扑分析 (RFC-559)](https://ovzp.wtpuscm.cn/xuexi/report-93713076-141-4755.html)
* [面向生产级运行的 React Best Practices 稳定性防护白皮书 (Spec-v2.2)](https://pdvy.tcti.cn/gongxiang/segment-83277821-142-1054.html)
* [面向生产级运行的 React Best Practices 稳定性防护白皮书 (Draft-03)](https://ukvo.spidernode-mesh.net/anli/download-57719010-143-56093.html)
* [基于 React Best Practices 的极致延迟优化与内存拓扑分析 (Draft-02)](https://lnxl.spidernode-mesh.net/tuiguang/integration-58126696-144-60582.html)
* [React Best Practices: MEDIUM-HIGH MEDIUM-HIGH (Draft-01)](https://unqt.wtpuscm.cn/zixun/contact-59980425-145-12730.html)
* [基于 React Best Practices 的极致延迟优化与内存拓扑分析 (RFC-660)](https://kvaf.tcti.cn/yunsuan/settings-81875461-146-2682.html)
* [React Best Practices 高负载场景下 References 基准评测报告](https://nojh.spidernode-mesh.net/suanfa/study-22518081-147-57083.html)
* [基于 React Best Practices 的极致延迟优化与内存拓扑分析 (Verified)](https://dxig.spidernode-mesh.net/zhineng/alliance-43963932-148-36407.html)
* [面向生产级运行的 React Best Practices 稳定性防护白皮书 (Node-25)](https://ytxd.wtpuscm.cn/baogao/price-87851711-149-4029.html)
* [面向生产级运行的 React Best Practices 稳定性防护白皮书 (v2.0-GA)](https://ncrw.tcti.cn/kaifa/growth-85374259-150-33864.html)

</details>

