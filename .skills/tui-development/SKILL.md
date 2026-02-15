---
name: tui-development
description: Build interactive Text User Interfaces (TUI) with rich terminal experiences using modern libraries like Ink, Blessed, and React Terminal.
triggers:
  - keywords: ["tui", "terminal ui", "blessed", "ink", "react-terminal", "blessed-react", "terminal interface", "interactive terminal"]
  - file_patterns: ["**/tui/**", "**/terminal/**", "**/*.tui.*"]
  - code_patterns: ["import.*ink", "require.*ink", "import.*blessed", "render.*Ink"]
---

# TUI Development

Build rich, interactive Text User Interfaces that provide GUI-like experiences in the terminal.

## Overview

TUIs offer the best of both worlds: the accessibility of CLIs and the rich interactivity of GUIs. Modern TUIs use React patterns for component-based development.

## Key Principles

1. **Keyboard-first navigation** - All interactions via keyboard shortcuts
2. **Responsive layouts** - Handle terminal resizing gracefully
3. **Accessible** - Screen reader compatible where possible
4. **Fast refresh** - Efficient rendering, minimal re-renders

## Architecture Patterns

### React with Ink (Modern Approach)

```tsx
// tui/src/App.tsx
import { Box, Text, useInput, useApp } from 'ink';
import { useState } from 'react';
import { Menu } from './components/Menu';
import { StatusBar } from './components/StatusBar';
import { MainContent } from './components/MainContent';

export function App() {
  const [activeTab, setActiveTab] = useState(0);
  const [loading, setLoading] = useState(false);
  const { exit } = useApp();
  
  useInput((input, key) => {
    if (input === 'q') {
      exit();
    }
    if (key.leftArrow) {
      setActiveTab((prev) => Math.max(0, prev - 1));
    }
    if (key.rightArrow) {
      setActiveTab((prev) => Math.min(2, prev + 1));
    }
  });
  
  return (
    <Box flexDirection="column" height="100%">
      <Menu activeIndex={activeTab} />
      <Box flexGrow={1} padding={1}>
        <MainContent tab={activeTab} loading={loading} />
      </Box>
      <StatusBar activeTab={activeTab} />
    </Box>
  );
}
```

### Component-based Structure

```tsx
// tui/src/components/Menu.tsx
import { Box, Text } from 'ink';

interface MenuProps {
  activeIndex: number;
}

const tabs = ['Dashboard', 'Projects', 'Settings'];

export function Menu({ activeIndex }: MenuProps) {
  return (
    <Box borderStyle="round" paddingX={1}>
      {tabs.map((tab, index) => (
        <Box key={tab} paddingX={1}>
          <Text 
            color={index === activeIndex ? 'green' : 'gray'}
            bold={index === activeIndex}
          >
            {index === activeIndex ? '> ' : '  '}
            {tab}
          </Text>
        </Box>
      ))}
    </Box>
  );
}
```

### List with Selection

```tsx
// tui/src/components/SelectableList.tsx
import { Box, Text, useInput } from 'ink';
import { useState } from 'react';

interface SelectableListProps<T> {
  items: T[];
  onSelect: (item: T, index: number) => void;
  renderItem: (item: T, index: number, isSelected: boolean) => React.ReactNode;
}

export function SelectableList<T>({ 
  items, 
  onSelect, 
  renderItem 
}: SelectableListProps<T>) {
  const [selectedIndex, setSelectedIndex] = useState(0);
  
  useInput((input, key) => {
    if (key.upArrow) {
      setSelectedIndex((prev) => Math.max(0, prev - 1));
    }
    if (key.downArrow) {
      setSelectedIndex((prev) => Math.min(items.length - 1, prev + 1));
    }
    if (key.return) {
      onSelect(items[selectedIndex], selectedIndex);
    }
  });
  
  return (
    <Box flexDirection="column">
      {items.map((item, index) => (
        <Box key={index}>
          {renderItem(item, index, index === selectedIndex)}
        </Box>
      ))}
    </Box>
  );
}

// Usage
<SelectableList
  items={projects}
  onSelect={(project) => console.log('Selected:', project.name)}
  renderItem={(project, index, isSelected) => (
    <Text color={isSelected ? 'green' : 'white'}>
      {isSelected ? '>' : ' '} {project.name}
    </Text>
  )}
/>
```

## State Management

### Context Pattern

```tsx
// tui/src/context/AppContext.tsx
import { createContext, useContext, useState, ReactNode } from 'react';

interface Project {
  id: string;
  name: string;
  status: 'active' | 'inactive';
}

interface AppState {
  projects: Project[];
  selectedProject: Project | null;
  loading: boolean;
  error: string | null;
}

const AppContext = createContext<{
  state: AppState;
  setState: React.Dispatch<React.SetStateAction<AppState>>;
}> | null>(null);

export function AppProvider({ children }: { children: ReactNode }) {
  const [state, setState] = useState<AppState>({
    projects: [],
    selectedProject: null,
    loading: false,
    error: null
  });
  
  return (
    <AppContext.Provider value={{ state, setState }}>
      {children}
    </AppContext.Provider>
  );
}

export const useAppState = () => {
  const context = useContext(AppContext);
  if (!context) throw new Error('useAppState must be used within AppProvider');
  return context;
};
```

## Advanced Patterns

### Split View Layout

```tsx
// tui/src/components/SplitView.tsx
import { Box } from 'ink';

interface SplitViewProps {
  left: React.ReactNode;
  right: React.ReactNode;
  leftWidth?: number;
}

export function SplitView({ left, right, leftWidth = 30 }: SplitViewProps) {
  return (
    <Box width="100%">
      <Box width={leftWidth} borderStyle="single">
        {left}
      </Box>
      <Box flexGrow={1} borderStyle="single">
        {right}
      </Box>
    </Box>
  );
}
```

### Modal/Dialog

```tsx
// tui/src/components/ConfirmDialog.tsx
import { Box, Text, useInput } from 'ink';

interface ConfirmDialogProps {
  message: string;
  onConfirm: () => void;
  onCancel: () => void;
}

export function ConfirmDialog({ message, onConfirm, onCancel }: ConfirmDialogProps) {
  const [selected, setSelected] = useState(0);
  
  useInput((_, key) => {
    if (key.leftArrow || key.rightArrow) {
      setSelected((prev) => (prev === 0 ? 1 : 0));
    }
    if (key.return) {
      selected === 0 ? onConfirm() : onCancel();
    }
    if (key.escape) {
      onCancel();
    }
  });
  
  return (
    <Box 
      borderStyle="double" 
      paddingX={2} 
      paddingY={1}
      borderColor="yellow"
    >
      <Text>{message}</Text>
      <Box paddingY={1}>
        <Text color={selected === 0 ? 'green' : 'gray'}>
          {selected === 0 ? '>' : ' '} Yes
        </Text>
        <Text>  </Text>
        <Text color={selected === 1 ? 'red' : 'gray'}>
          {selected === 1 ? '>' : ' '} No
        </Text>
      </Box>
    </Box>
  );
}
```

### Loading States

```tsx
// tui/src/components/Spinner.tsx
import { Text } from 'ink';
import { useEffect, useState } from 'react';

const spinnerFrames = ['⠋', '⠙', '⠹', '⠸', '⠼', '⠴', '⠦', '⠧', '⠇', '⠏'];

interface SpinnerProps {
  message?: string;
}

export function Spinner({ message = 'Loading...' }: SpinnerProps) {
  const [frame, setFrame] = useState(0);
  
  useEffect(() => {
    const timer = setInterval(() => {
      setFrame((prev) => (prev + 1) % spinnerFrames.length);
    }, 80);
    
    return () => clearInterval(timer);
  }, []);
  
  return <Text color="green">{spinnerFrames[frame]} {message}</Text>;
}
```

## Data Visualization in Terminal

### Progress Bar

```tsx
// tui/src/components/ProgressBar.tsx
import { Box, Text } from 'ink';

interface ProgressBarProps {
  progress: number; // 0-100
  width?: number;
  label?: string;
}

export function ProgressBar({ progress, width = 40, label }: ProgressBarProps) {
  const filled = Math.floor((progress / 100) * width);
  const empty = width - filled;
  
  return (
    <Box flexDirection="column">
      {label && <Text>{label}</Text>}
      <Box>
        <Text color="green">{'█'.repeat(filled)}</Text>
        <Text color="gray">{'░'.repeat(empty)}</Text>
        <Text>  {progress.toFixed(1)}%</Text>
      </Box>
    </Box>
  );
}
```

### Simple Chart

```tsx
// tui/src/components/BarChart.tsx
import { Box, Text } from 'ink';

interface DataPoint {
  label: string;
  value: number;
  color?: string;
}

interface BarChartProps {
  data: DataPoint[];
  maxWidth?: number;
}

export function BarChart({ data, maxWidth = 40 }: BarChartProps) {
  const maxValue = Math.max(...data.map(d => d.value));
  
  return (
    <Box flexDirection="column">
      {data.map((item, index) => {
        const barLength = Math.floor((item.value / maxValue) * maxWidth);
        return (
          <Box key={index}>
            <Box width={15}>
              <Text>{item.label.padEnd(12)}</Text>
            </Box>
            <Box>
              <Text color={item.color || 'green'}>
                {'█'.repeat(barLength)}
              </Text>
              <Text dimColor> {item.value}</Text>
            </Box>
          </Box>
        );
      })}
    </Box>
  );
}
```

## Testing TUIs

```tsx
// tui/src/__tests__/App.test.tsx
import { render } from 'ink-testing-library';
import { describe, it, expect, vi } from 'vitest';
import { App } from '../App';

describe('App', () => {
  it('should render initial state', () => {
    const { lastFrame } = render(<App />);
    
    expect(lastFrame()).toContain('Dashboard');
    expect(lastFrame()).toContain('Projects');
  });
  
  it('should handle keyboard input', () => {
    const { stdin, lastFrame } = render(<App />);
    
    // Simulate right arrow
    stdin.write('\u001b[C');
    
    // Should change active tab
    expect(lastFrame()).toContain('> Projects');
  });
});
```

## Alternative: Blessed (Legacy but Stable)

```typescript
// For complex layouts requiring absolute positioning
import blessed from 'blessed';

const screen = blessed.screen({
  smartCSR: true,
  title: 'My TUI App'
});

const box = blessed.box({
  top: 'center',
  left: 'center',
  width: '50%',
  height: '50%',
  content: 'Hello {green-fg}World{/green-fg}!',
  tags: true,
  border: {
    type: 'line'
  },
  style: {
    fg: 'white',
    bg: 'black',
    border: {
      fg: '#f0f0f0'
    }
  }
});

screen.append(box);
screen.key(['escape', 'q', 'C-c'], () => process.exit(0));
screen.render();
```

## Recommended Libraries

| Purpose | Libraries |
|---------|-----------|
| React-based TUI | `ink` (recommended) |
| Legacy TUI | `blessed`, `neo-blessed` |
| Components | `ink-spinner`, `ink-select-input`, `ink-text-input` |
| Tables | `ink-table`, `ink-tab` |
| Charts | `cliui` (external) |

## Performance Tips

1. **Memoize expensive renders**
   ```tsx
   const memoizedData = useMemo(() => processData(items), [items]);
   ```

2. **Throttle rapid updates**
   ```tsx
   const throttledUpdate = useThrottle(update, 100);
   ```

3. **Limit list rendering**
   ```tsx
   const visibleItems = items.slice(scrollOffset, scrollOffset + viewportHeight);
   ```

## Key Takeaways

1. Use Ink for modern React-based TUIs
2. Implement keyboard navigation patterns
3. Handle terminal resize events
4. Provide visual feedback for all actions
5. Keep state management simple
6. Test with ink-testing-library
7. Consider accessibility in color choices
