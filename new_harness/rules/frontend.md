---
paths:
  - "frontend/src/**"
---

# Frontend Development Rules (React / Vite / TypeScript)

## shadcn/ui First (MANDATORY)
- Before creating ANY UI component, check if shadcn/ui has it: `ls frontend/src/components/ui/`
- NEVER create custom implementations of: Button, Input, Select, Checkbox, Switch, Dialog, Modal, Alert, Toast, Table, Tabs, Card, Badge, Dropdown, Popover, Tooltip, Navigation, Sidebar, Breadcrumb, Slider, Chart
- If a shadcn component is missing, install it (in `frontend/`): `npx shadcn@latest add <name> --yes`
- Custom components are ONLY for business-specific compositions that internally use shadcn primitives
- Charts: use shadcn/ui Chart (Recharts-based); map: the library chosen in `/architecture` (e.g. react-leaflet with OpenStreetMap tiles)

## Import Pattern
```tsx
import { Button } from "@/components/ui/button"
import { Card, CardHeader, CardTitle, CardContent } from "@/components/ui/card"
```

## Data Access
- All server data goes through the typed API client in `src/lib/api.ts` — no `fetch` calls scattered in components
- Server state via the data-fetching approach chosen in `/architecture` (e.g. TanStack Query) — no ad-hoc caching
- Filter state (keyword, region, radius, period) lives in the URL query string so views are shareable and reload-safe
- The API base URL comes from `import.meta.env.VITE_API_URL`; never put secrets in `VITE_` variables

## Component Standards
- Use Tailwind CSS exclusively (no inline styles, no CSS modules)
- Desktop-first: 1440px primary; 768px and 375px must stay usable (no broken layout, no horizontal scroll)
- Implement loading states, error states, and empty states (no data before the first nightly run is normal)
- Render scraped content (titles, company names, full text) as plain text — never `dangerouslySetInnerHTML`
- External job links open with `rel="noopener noreferrer"`
- Use semantic HTML and ARIA labels for accessibility; charts provide a table or text alternative
- Keep components small and focused
- Use TypeScript interfaces for all props; no `any`
