# React Performance Optimization

Deep dive into performance patterns. Reference this when optimizing React applications.

## Performance Priorities

### Phase 1: Quick Wins

**1. Enable React Compiler (React 19+)**

The compiler automatically memoizes components and hooks. No manual `useMemo`/`useCallback` needed in most cases.

**2. Route-Based Code Splitting**

```jsx
import { lazy, Suspense } from 'react';

const Dashboard = lazy(() => import('./pages/Dashboard'));
const Settings = lazy(() => import('./pages/Settings'));

function App() {
  return (
    <Suspense fallback={<PageLoader />}>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/settings" element={<Settings />} />
      </Routes>
    </Suspense>
  );
}
```

Reduces initial bundle by 60-80%.

**3. Image Optimization**

```jsx
// Use modern formats + lazy loading
<img
  src="/image.webp"
  srcSet="/image-400.webp 400w, /image-800.webp 800w"
  sizes="(max-width: 600px) 400px, 800px"
  loading="lazy"
  width={800}
  height={600}
  alt="Description"
/>
```

- Convert to WebP/AVIF (30-50% smaller)
- Always set width/height to prevent layout shift
- Lazy load below-the-fold images

**4. Bundle Analysis**

```bash
# Install analyzer
npm install --save-dev webpack-bundle-analyzer

# Add to webpack config or use
npx webpack-bundle-analyzer stats.json
```

Common fixes:
- Replace moment.js with date-fns or dayjs
- Import only what you need: `import { debounce } from 'lodash-es'`
- Remove unused dependencies

### Phase 2: Foundation

**5. Virtualize Large Lists**

```jsx
import { FixedSizeList } from 'react-window';

function VirtualizedList({ items }) {
  return (
    <FixedSizeList
      height={500}
      itemCount={items.length}
      itemSize={50}
      width="100%"
    >
      {({ index, style }) => (
        <div style={style}>
          {items[index].name}
        </div>
      )}
    </FixedSizeList>
  );
}
```

For 50+ items, virtualization maintains 60 FPS vs browser freeze.

**6. State Management Patterns**

Split context by update frequency:

```jsx
// ❌ One context for everything
const AppContext = createContext({ user, theme, cart, notifications });

// ✅ Separate contexts
const UserContext = createContext(null);    // Changes rarely
const ThemeContext = createContext('light'); // Changes rarely
const CartContext = createContext([]);       // Changes often
```

Consider Zustand for selective subscriptions:

```jsx
import { create } from 'zustand';

const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}));

// Only re-renders when count changes
function Counter() {
  const count = useStore((state) => state.count);
  return <span>{count}</span>;
}
```

**7. Performance Profiling**

React DevTools Profiler:
1. Open React DevTools → Profiler tab
2. Click Record, interact with app, stop
3. Look for: long renders, unnecessary re-renders, cascading updates

Chrome DevTools Performance:
1. Open DevTools → Performance tab
2. Record interaction
3. Look for: long tasks (>50ms), forced reflows, layout thrashing

### Phase 3: Advanced

**8. Web Workers for Heavy Computation**

```jsx
// worker.js
self.onmessage = (e) => {
  const result = expensiveCalculation(e.data);
  self.postMessage(result);
};

// Component
function DataProcessor({ data }) {
  const [result, setResult] = useState(null);

  useEffect(() => {
    const worker = new Worker(new URL('./worker.js', import.meta.url));
    worker.postMessage(data);
    worker.onmessage = (e) => setResult(e.data);
    return () => worker.terminate();
  }, [data]);

  return result ? <Results data={result} /> : <Loading />;
}
```

Use for: image processing, large dataset sorting, complex calculations.

**9. CSS Performance**

```css
/* ❌ Triggers layout */
.animate {
  left: 100px;
  width: 200px;
}

/* ✅ GPU-accelerated */
.animate {
  transform: translateX(100px) scale(1.2);
}
```

- Use `transform` and `opacity` for animations
- Apply `will-change` sparingly for known animations
- Use `content-visibility: auto` for off-screen content

**10. Memory Leak Prevention**

```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch('/api/data', { signal: controller.signal })
    .then(res => res.json())
    .then(setData)
    .catch(err => {
      if (err.name !== 'AbortError') throw err;
    });

  return () => controller.abort();
}, []);

useEffect(() => {
  const handler = () => console.log('resize');
  window.addEventListener('resize', handler);
  return () => window.removeEventListener('resize', handler);
}, []);
```

Always clean up:
- Fetch requests (AbortController)
- Event listeners
- Timers/intervals
- WebSocket connections
- Subscriptions

### Phase 4: Architecture

**11. Server-Side Rendering**

| Strategy | Use When | Load Time |
|----------|----------|-----------|
| SSR | Dynamic, personalized content | 0.5-1.5s |
| SSG | Static content (blogs, docs) | 0.2-0.8s |
| ISR | Semi-static (product pages) | 0.3-1.0s |

**12. Service Worker Caching**

```js
// sw.js
const CACHE_NAME = 'app-v1';
const STATIC_ASSETS = ['/', '/index.html', '/main.js', '/styles.css'];

self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME).then((cache) => cache.addAll(STATIC_ASSETS))
  );
});

self.addEventListener('fetch', (event) => {
  event.respondWith(
    caches.match(event.request).then((cached) => cached || fetch(event.request))
  );
});
```

Reduces repeat-visit load times by 80-95%.

## Measurement Checklist

- [ ] Largest Contentful Paint (LCP) < 2.5s
- [ ] First Input Delay (FID) < 100ms
- [ ] Cumulative Layout Shift (CLS) < 0.1
- [ ] Time to Interactive (TTI) < 3.8s
- [ ] Bundle size < 200KB gzipped (initial)

## Tools

- **React DevTools Profiler** — Component render analysis
- **Chrome DevTools Performance** — Runtime profiling
- **Lighthouse** — Automated audits
- **webpack-bundle-analyzer** — Bundle composition
- **why-did-you-render** — Unnecessary re-render detection
