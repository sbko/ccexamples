# React Project Configuration

## Purpose
Comprehensive configuration for React/Next.js projects with TypeScript, testing, and modern best practices.

## Dependencies
<!-- These modules should be imported first -->
- modules/global/flox-environment.md
- modules/global/code-quality.md
- modules/global/git-workflow.md

## React-Specific Standards

### Component Structure
```
src/
├── components/
│   ├── common/          # Shared components
│   ├── features/        # Feature-specific components
│   └── layouts/         # Layout components
├── hooks/               # Custom React hooks
├── contexts/            # React contexts
├── pages/               # Page components (Next.js) or routes
├── services/            # API and external services
├── utils/               # Utility functions
└── types/               # TypeScript type definitions
```

### Component Best Practices

#### Functional Components Only
```typescript
// Always use functional components with TypeScript
interface ButtonProps {
  children: React.ReactNode;
  onClick?: () => void;
  variant?: 'primary' | 'secondary';
  disabled?: boolean;
}

export const Button: React.FC<ButtonProps> = ({
  children,
  onClick,
  variant = 'primary',
  disabled = false
}) => {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
};
```

#### Custom Hooks Pattern
```typescript
// Extract logic into custom hooks
export const useUser = () => {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    fetchUser()
      .then(setUser)
      .catch(setError)
      .finally(() => setLoading(false));
  }, []);

  return { user, loading, error };
};
```

### State Management

#### Context for Global State
```typescript
// Create typed context
interface AppContextType {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

const AppContext = createContext<AppContextType | undefined>(undefined);

// Custom hook for context
export const useApp = () => {
  const context = useContext(AppContext);
  if (!context) {
    throw new Error('useApp must be used within AppProvider');
  }
  return context;
};
```

#### Local State First
- Use local state for component-specific data
- Lift state only when necessary
- Consider context before external state management
- Use external state (Redux/Zustand) for complex apps

### Performance Optimization

#### Memoization Strategy
```typescript
// Memoize expensive computations
const expensiveResult = useMemo(
  () => computeExpensiveValue(data),
  [data]
);

// Memoize callbacks to prevent re-renders
const handleClick = useCallback(
  (id: string) => {
    dispatch({ type: 'SELECT', payload: id });
  },
  [dispatch]
);

// Memoize components when needed
export const ExpensiveComponent = memo(({ data }) => {
  return <div>{/* Complex rendering */}</div>;
}, (prevProps, nextProps) => {
  return prevProps.data.id === nextProps.data.id;
});
```

#### Code Splitting
```typescript
// Route-based splitting
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Settings = lazy(() => import('./pages/Settings'));

// Component-based splitting
const HeavyChart = lazy(() => import('./components/HeavyChart'));

// With loading state
<Suspense fallback={<LoadingSpinner />}>
  <HeavyChart data={chartData} />
</Suspense>
```

### Testing React Components

#### Component Testing
```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

describe('Button', () => {
  it('should call onClick when clicked', async () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    const button = screen.getByRole('button', { name: /click me/i });
    await userEvent.click(button);
    
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

#### Hook Testing
```typescript
import { renderHook, act } from '@testing-library/react';

describe('useCounter', () => {
  it('should increment counter', () => {
    const { result } = renderHook(() => useCounter());
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });
});
```

### Styling Best Practices

#### CSS Modules
```typescript
// Button.module.css
.button {
  padding: 8px 16px;
  border-radius: 4px;
}

.primary {
  background: blue;
  color: white;
}

// Button.tsx
import styles from './Button.module.css';

export const Button = ({ variant }) => (
  <button className={`${styles.button} ${styles[variant]}`}>
    Click me
  </button>
);
```

#### Styled Components / Emotion
```typescript
import styled from '@emotion/styled';

const StyledButton = styled.button<{ variant: 'primary' | 'secondary' }>`
  padding: 8px 16px;
  border-radius: 4px;
  background: ${props => props.variant === 'primary' ? 'blue' : 'gray'};
  color: white;
`;
```

### Form Handling

#### React Hook Form
```typescript
import { useForm } from 'react-hook-form';
import { z } from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';

const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8)
});

type FormData = z.infer<typeof schema>;

export const LoginForm = () => {
  const { register, handleSubmit, formState: { errors } } = useForm<FormData>({
    resolver: zodResolver(schema)
  });

  const onSubmit = async (data: FormData) => {
    await login(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} />
      {errors.email && <span>{errors.email.message}</span>}
      
      <input {...register('password')} type="password" />
      {errors.password && <span>{errors.password.message}</span>}
      
      <button type="submit">Login</button>
    </form>
  );
};
```

### Data Fetching

#### SWR / React Query
```typescript
// Using SWR
import useSWR from 'swr';

export const useUser = (id: string) => {
  const { data, error, isLoading } = useSWR(
    `/api/user/${id}`,
    fetcher,
    {
      revalidateOnFocus: false,
      revalidateOnReconnect: false
    }
  );

  return {
    user: data,
    isLoading,
    isError: error
  };
};

// Using React Query
import { useQuery } from '@tanstack/react-query';

export const useUser = (id: string) => {
  return useQuery({
    queryKey: ['user', id],
    queryFn: () => fetchUser(id),
    staleTime: 5 * 60 * 1000, // 5 minutes
  });
};
```

### Environment Configuration

#### Environment Variables
```bash
# .env.local
NEXT_PUBLIC_API_URL=http://localhost:3000
NEXT_PUBLIC_APP_NAME=My React App
API_SECRET=server-only-secret

# Access in code
const apiUrl = process.env.NEXT_PUBLIC_API_URL;
```

### Build Configuration

#### Next.js Config
```javascript
// next.config.js
module.exports = {
  reactStrictMode: true,
  images: {
    domains: ['example.com'],
  },
  async redirects() {
    return [
      {
        source: '/old-route',
        destination: '/new-route',
        permanent: true,
      },
    ];
  },
};
```

#### Webpack Customization
```javascript
// For Create React App - craco.config.js
module.exports = {
  webpack: {
    alias: {
      '@components': path.resolve(__dirname, 'src/components'),
      '@hooks': path.resolve(__dirname, 'src/hooks'),
      '@utils': path.resolve(__dirname, 'src/utils'),
    },
  },
};
```

### Accessibility Standards

#### ARIA and Semantic HTML
```typescript
// Use semantic HTML and ARIA when needed
export const Modal = ({ isOpen, onClose, title, children }) => {
  return (
    <dialog
      open={isOpen}
      aria-labelledby="modal-title"
      aria-describedby="modal-description"
    >
      <h2 id="modal-title">{title}</h2>
      <div id="modal-description">{children}</div>
      <button onClick={onClose} aria-label="Close modal">
        ×
      </button>
    </dialog>
  );
};
```

### Error Boundaries
```typescript
class ErrorBoundary extends Component<Props, State> {
  state = { hasError: false };

  static getDerivedStateFromError(error: Error) {
    return { hasError: true };
  }

  componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    logErrorToService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <ErrorFallback />;
    }

    return this.props.children;
  }
}
```

## React-Specific Commands

### Development Workflow
```bash
# Start development server
npm run dev

# Run tests in watch mode
npm test -- --watch

# Build for production
npm run build

# Analyze bundle size
npm run analyze

# Type check
npm run type-check

# Lint and format
npm run lint:fix
```

### Pre-commit Checks
```json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.{ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{css,scss}": [
      "stylelint --fix",
      "prettier --write"
    ]
  }
}
```

## Integration with Claude Code

### React-Aware Operations
1. **Component generation**: Create components with proper TypeScript interfaces
2. **Hook extraction**: Identify and extract logic into custom hooks
3. **Performance analysis**: Detect unnecessary re-renders
4. **Accessibility checks**: Ensure ARIA compliance
5. **Test generation**: Create RTL tests for components

### Smart Refactoring
- Convert class components to functional
- Extract repeated JSX into components
- Optimize render performance
- Add proper TypeScript types
- Implement error boundaries

This template ensures React projects follow modern best practices with emphasis on performance, type safety, and maintainability.