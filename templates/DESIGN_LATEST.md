# Design System — [Project Name]

> Living design reference for this project. Updated during `docs` runs and snapshotted before each `release`.  
> Agents read this alongside `ARCHITECTURE_LATEST.md` to understand UI constraints during implementation.

## Design System

| Property | Value |
|----------|-------|
| Design system | [e.g. Material UI 5, Tailwind CSS 3, custom] |
| Component library | [e.g. shadcn/ui, Ant Design, internal] |
| Figma / Storybook | [link or "not configured"] |
| CSS strategy | [e.g. CSS Modules, Tailwind utility classes, styled-components] |

## Component Conventions

- **Naming:** [e.g. PascalCase for components, kebab-case for files]
- **File co-location:** [e.g. `ComponentName/index.tsx` + `ComponentName.module.css`]
- **Props pattern:** [e.g. destructured props, typed with TypeScript interfaces]
- **State management:** [e.g. React Context, Zustand, Redux — scope and usage rules]
- **Async / loading states:** [e.g. skeleton loaders, suspense boundaries, error states]

## Token Standards

| Token category | Convention | Example |
|---------------|-----------|---------|
| Colors | [e.g. semantic tokens via CSS custom properties] | `--color-primary`, `--color-surface` |
| Typography | [e.g. type scale via design system tokens] | `--font-size-base`, `--font-weight-medium` |
| Spacing | [e.g. 4px base unit multiples] | `--spacing-2` = 8px |
| Border radius | [e.g. design system presets] | `--radius-md` |
| Shadows | [e.g. elevation tokens] | `--shadow-sm`, `--shadow-card` |

**Rules:**
- Do not use hard-coded color hex values or magic numbers for spacing — always use tokens.
- Prefer design system components over custom implementations; justify deviations in an ADR.

## Accessibility Baseline

- **WCAG target:** [e.g. AA (WCAG 2.1)]
- **Minimum color contrast:** [e.g. 4.5:1 for normal text, 3:1 for large text]
- **Focus management:** [e.g. all interactive elements must have visible `:focus-visible` rings]
- **Keyboard navigation:** [e.g. full keyboard operability required for all interactive flows]
- **Screen reader support:** [e.g. ARIA roles and labels required on icons, modals, and custom controls]
- **Motion:** [e.g. respect `prefers-reduced-motion`; no autoplay animations]
- **Touch targets:** [e.g. minimum 44×44px for interactive elements on mobile]

## Responsive Breakpoints

| Breakpoint | Min width | Usage |
|-----------|-----------|-------|
| `sm` | [e.g. 640px] | [e.g. small tablets, large phones landscape] |
| `md` | [e.g. 768px] | [e.g. tablets] |
| `lg` | [e.g. 1024px] | [e.g. desktop baseline] |
| `xl` | [e.g. 1280px] | [e.g. wide desktop] |

## Tools & References

| Tool | Purpose | Link |
|------|---------|------|
| Figma | Design source of truth | [link] |
| Storybook | Component catalog | [link or "not configured"] |
| Chromatic | Visual regression | [link or "not configured"] |
| Lighthouse | Accessibility / performance | [run locally] |

## Change History

| Date | Change | Updated by |
|------|--------|-----------|
| [YYYY-MM-DD] | Initial design system baseline | `speckit.acps.setup` |
