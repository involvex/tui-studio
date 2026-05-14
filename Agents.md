# Agents.md

This file provides comprehensive guidance for AI agents working with code in this repository.

## Project Overview

TUIStudio is a Figma-like visual editor for Terminal User Interfaces (TUIs). It enables developers to design terminal UIs visually and export them as code for multiple frameworks (Ink, BubbleTea, Blessed, Textual, OpenTUI, Tview).

**Status**: Alpha - Active development

## Useful Commands

### Development Commands

```bash
npm run dev          # Start Vite dev server with hot reload (http://localhost:5173)
npm run build        # TypeScript compile + Vite production build (tsc -b && vite build)
npm run lint         # Run ESLint
npm run preview      # Preview production build locally
npm run format       # Format code with Prettier
npm run format:check # Check code formatting with Prettier
```

### Package Management

```bash
npm install          # Install dependencies
npm install <pkg>    # Add a new dependency
npm install -D <pkg> # Add a new dev dependency
npm update           # Update dependencies
```

### Git Commands

```bash
git status           # Check repository status
git add .            # Stage all changes
git commit -m "msg"  # Commit changes
git push             # Push to remote
git pull             # Pull from remote
```

## Technologies

### Core Technologies

- **React 19** - UI framework with latest features
- **TypeScript 5.8** - Type-safe JavaScript with strict mode enabled
- **Vite 7** - Fast build tool and dev server
- **Zustand 5** - Lightweight state management

### UI & Styling

- **Tailwind CSS 3** - Utility-first CSS framework for editor UI
- **Lucide React** - Icon library
- **class-variance-authority** - Component variant management
- **clsx** - Conditional className utility
- **tailwind-merge** - Merge Tailwind classes intelligently

### Development Tools

- **ESLint 9** - Code linting with TypeScript support
- **Prettier 3** - Code formatting
- **Playwright 1.58** - Browser testing framework
- **PostCSS** - CSS processing
- **Autoprefixer** - CSS vendor prefixing

### Routing

- **react-router-dom 7** - Client-side routing

## Project Structure

```
src/
├── components/          # React components
│   ├── debug/         # Debug utilities
│   ├── editor/        # Main editor components (Canvas, PropertyPanel, etc.)
│   ├── export/        # Export-related components
│   ├── palette/       # Component palette
│   └── properties/    # Property editing components
├── constants/         # Constants and component library definitions
├── data/             # Static data (themes, etc.)
├── hooks/            # Custom React hooks
├── stores/           # Zustand state management
├── types/            # TypeScript type definitions
└── utils/            # Utility functions
    ├── export/       # Code generation utilities
    ├── layout/       # Layout engine (flexbox, grid, absolute)
    └── rendering/    # ANSI rendering system
```

## Architecture

### State Management (Zustand Stores)

Located in `src/stores/`:

- **componentStore.ts** - Core store managing component tree, history/undo-redo, and tree mutations (add, remove, update, move, duplicate)
- **canvasStore.ts** - Viewport state: zoom, pan, grid settings, canvas dimensions
- **selectionStore.ts** - Multi-selection (selected, hovered, focused component IDs)
- **themeStore.ts** - TUI color theme for rendering

### Data Flow

1. User actions mutate `componentStore` (the source of truth)
2. `LayoutEngine` (`src/utils/layout/engine.ts`) computes positions/sizes from the component tree + canvas dimensions
3. `Canvas.tsx` renders the computed layout using the ANSI rendering system
4. `PropertyPanel.tsx` reads/writes the selected component's props via `componentStore`

### Layout Engine (`src/utils/layout/`)

- `engine.ts` - Orchestrates layout calculation; dispatches to flexbox, grid, or absolute sub-engines
- `flexbox.ts` - Flexbox layout algorithm implementation
- `grid.ts` - Grid layout algorithm implementation
- `absolute.ts` - Absolute positioning layout implementation

### Rendering System (`src/utils/rendering/`)

Converts the component tree into a visual TUI representation using ANSI escape codes and Unicode box-drawing characters:

- `canvas.ts` - Canvas rendering logic
- `components.ts` - Component-specific rendering
- `ansi.ts` - ANSI escape code utilities
- `borders.ts` - Border rendering with Unicode characters

### Component Library (`src/constants/components.ts`)

Defines `COMPONENT_LIBRARY`: 20+ TUI component types (Screen, Box, Button, TextInput, List, Table, Tree, Menu, Tabs, Modal, etc.), each with default props, layout config, style, and event handlers.

### Export / Code Generation (`src/utils/export/`)

- `codeExporter.ts` - Multi-framework code generation (Ink, BubbleTea, Blessed, Textual, OpenTUI, Tview)
- `textExporter.ts` - Plain text/ASCII export

### Key Types (`src/types/`)

- `ComponentNode` - Core tree node with `id`, `type`, `props`, `children`, `layout`, `style`
- `ComponentType` - Union of all supported TUI component type strings
- `LayoutProps`, `StyleProps`, `EventHandlers` - Prop category types

### Tree Utilities (`src/utils/treeUtils.ts`)

`findNodeById`, `findParentNode`, `flattenTree`, `cloneNode` - used throughout for tree traversal and manipulation.

## Best Practices and Guidelines

### Code Style

Follow the project's Prettier configuration (`.prettierrc.json`):

- **Semicolons**: Required
- **Quotes**: Single quotes
- **Trailing commas**: ES5 style
- **Print width**: 100 characters
- **Tab width**: 2 spaces
- **Indentation**: 2 spaces

### TypeScript Configuration

The project uses strict TypeScript mode with these key settings:

- **Target**: ES2020
- **Module**: ESNext
- **Strict mode**: Enabled
- **No unused locals**: Enabled
- **No unused parameters**: Enabled
- **No fallthrough cases in switch**: Enabled
- **Path aliases**: `@/*` maps to `./src/*`

### Linting Rules

ESLint is configured with:

- TypeScript ESLint recommended rules
- React Hooks recommended rules
- React Refresh for HMR optimization
- Prettier integration (no conflicts)
- **Note**: `@typescript-eslint/no-explicit-any` is disabled

### React Best Practices

1. **Component Structure**
   - Use functional components with hooks
   - Keep components small and focused
   - Extract reusable logic into custom hooks

2. **State Management**
   - Use Zustand stores for global state
   - Use React state for component-local state
   - Keep state as close to where it's used as possible

3. **Performance**
   - Use `React.memo` for expensive components
   - Use `useCallback` and `useMemo` for expensive computations
   - Avoid unnecessary re-renders

4. **Type Safety**
   - Always define prop types with TypeScript interfaces
   - Use proper typing for Zustand stores
   - Avoid `any` types (though ESLint allows them)

### File Naming Conventions

- **Components**: PascalCase (e.g., `Canvas.tsx`, `PropertyPanel.tsx`)
- **Utilities**: camelCase (e.g., `treeUtils.ts`, `layoutEngine.ts`)
- **Types**: PascalCase (e.g., `ComponentNode.ts`, `LayoutProps.ts`)
- **Constants**: camelCase (e.g., `components.ts`, `themes.ts`)
- **Hooks**: camelCase with `use` prefix (e.g., `useComponentTree.ts`)

### Import Organization

1. React and third-party imports
2. Internal imports (using `@/*` path aliases)
3. Type imports (if separate)

Example:

```typescript
import React, { useState, useEffect } from 'react';
import { useComponentStore } from '@/stores/componentStore';
import type { ComponentNode } from '@/types';
```

### Component Development Guidelines

1. **Component Props**
   - Define interfaces for props
   - Use proper TypeScript types
   - Document complex props with JSDoc comments

2. **Styling**
   - Use Tailwind CSS classes
   - Use `clsx` and `tailwind-merge` for conditional classes
   - Follow the existing design system

3. **Event Handling**
   - Use proper event types
   - Prevent default behavior when needed
   - Clean up event listeners in useEffect

### State Management Guidelines

1. **Zustand Stores**
   - Keep stores focused on specific domains
   - Use actions for state mutations
   - Implement proper TypeScript typing
   - Consider middleware for logging, persistence, etc.

2. **Component State**
   - Use for UI-specific state
   - Keep it minimal
   - Lift state up when shared

### Testing Guidelines

- **No test runner is currently configured**
- Playwright is available for browser testing
- Consider adding unit tests with Vitest or Jest
- Consider adding component tests with React Testing Library

### Git Workflow

1. **Commit Messages**
   - Use clear, descriptive messages
   - Follow conventional commit format if desired
   - Keep commits focused and atomic

2. **Branching**
   - Use feature branches for new features
   - Keep main branch stable
   - Pull requests for code review

### Performance Considerations

1. **Large Components**
   - `Canvas.tsx` is a large file (~44KB) - consider splitting if working on it
   - Use code splitting for routes
   - Lazy load heavy components

2. **Rendering**
   - The ANSI rendering system can be expensive
   - Consider memoization for expensive calculations
   - Optimize the layout engine for large component trees

3. **State Updates**
   - Batch state updates when possible
   - Use Zustand's shallow comparison for arrays/objects
   - Avoid unnecessary re-renders

### Security Considerations

1. **File Operations**
   - Validate file paths before operations
   - Use proper error handling for file I/O
   - Sanitize user input for file names

2. **Code Generation**
   - Validate generated code before export
   - Escape user content in generated code
   - Be careful with eval-like operations

### Accessibility

1. **Keyboard Navigation**
   - Support keyboard shortcuts
   - Provide visible focus indicators
   - Ensure all interactive elements are keyboard accessible

2. **Screen Readers**
   - Use proper ARIA labels
   - Provide alternative text for icons
   - Ensure semantic HTML structure

## Keyboard Shortcuts

### Global Shortcuts

| Action          | Shortcut                          |
| --------------- | --------------------------------- |
| Command Palette | `Cmd/Ctrl+P`                      |
| Save            | `Cmd/Ctrl+S`                      |
| Open            | `Cmd/Ctrl+O`                      |
| Export          | `Cmd/Ctrl+E`                      |
| Copy            | `Cmd/Ctrl+C`                      |
| Paste           | `Cmd/Ctrl+V`                      |
| Delete          | `Backspace` / `Delete`            |
| Undo            | `Cmd/Ctrl+Z`                      |
| Redo            | `Cmd/Ctrl+Shift+Z` / `Cmd/Ctrl+Y` |

### Component Hotkeys (when not typing)

| Key | Component | Key | Component   |
| --- | --------- | --- | ----------- |
| `b` | Button    | `t` | Tabs        |
| `r` | Box       | `l` | List        |
| `k` | Checkbox  | `e` | Tree        |
| `a` | Radio     | `m` | Menu        |
| `s` | Select    | `i` | TextInput   |
| `o` | Toggle    | `p` | ProgressBar |
| `n` | Spinner   | `y` | Text        |
| `j` | Spacer    |     |             |

## File Format

Projects are saved as `.tui` files (JSON):

```json
{
  "version": "1",
  "meta": {
    "name": "My Screen",
    "theme": "dracula",
    "savedAt": "..."
  },
  "tree": { ... }
}
```

## Export Frameworks

| Framework                                               | Language           |
| ------------------------------------------------------- | ------------------ |
| [Ink](https://github.com/vadimdemedes/ink)              | TypeScript / React |
| [BubbleTea](https://github.com/charmbracelet/bubbletea) | Go                 |
| [Blessed](https://github.com/chjj/blessed)              | JavaScript         |
| [Textual](https://github.com/Textualize/textual)        | Python             |
| [OpenTUI](https://opentui.js.org/)                      | TypeScript         |
| [Tview](https://github.com/rivo/tview)                  | Go                 |

## Common Patterns

### Creating a New Component

1. Define the component in `src/constants/components.ts`
2. Add default props, layout config, style, and event handlers
3. Implement rendering logic in `src/utils/rendering/components.ts`
4. Add export logic in `src/utils/export/codeExporter.ts`
5. Update the palette UI if needed

### Adding a New Theme

1. Add theme definition to `src/data/themes.ts`
2. Update theme selector in the UI
3. Test with different components

### Modifying the Layout Engine

1. Understand the current layout algorithm in `src/utils/layout/`
2. Make changes to the specific layout engine (flexbox, grid, or absolute)
3. Test with various component configurations
4. Update documentation if needed

### Adding Export Support for a New Framework

1. Create a new exporter function in `src/utils/export/codeExporter.ts`
2. Implement the framework-specific code generation
3. Add the framework to the export UI
4. Test the generated code

## Troubleshooting

### Common Issues

1. **Build Errors**
   - Check TypeScript compilation: `tsc -b`
   - Verify all imports are correct
   - Check for circular dependencies

2. **Linting Errors**
   - Run `npm run lint` to see all errors
   - Fix ESLint issues first
   - Then run `npm run format` to fix formatting

3. **Runtime Errors**
   - Check browser console for errors
   - Verify state management is correct
   - Check for missing or incorrect props

4. **Performance Issues**
   - Use React DevTools Profiler
   - Check for unnecessary re-renders
   - Optimize expensive computations

## Development Tips

1. **Hot Module Replacement (HMR)**
   - Vite provides fast HMR
   - React Refresh is configured for components
   - State is preserved during HMR

2. **Debugging**
   - Use browser DevTools
   - React DevTools for component inspection
   - Zustand DevTools for state inspection (if added)

3. **Code Organization**
   - Keep related files together
   - Use barrel exports for cleaner imports
   - Document complex logic with comments

## Resources

- **React Documentation**: https://react.dev/
- **TypeScript Documentation**: https://www.typescriptlang.org/docs/
- **Vite Documentation**: https://vitejs.dev/
- **Zustand Documentation**: https://zustand-demo.pmnd.rs/
- **Tailwind CSS Documentation**: https://tailwindcss.com/docs

## Contributing

When contributing to TUIStudio:

1. Follow the code style guidelines
2. Write clear, descriptive commit messages
3. Test your changes thoroughly
4. Update documentation as needed
5. Ensure all linting passes before submitting

## License

For {root} sake I'm a designer. Mostly all the code has been written by Claude and ad latere.
