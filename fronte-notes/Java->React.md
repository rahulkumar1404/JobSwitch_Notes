# 📘 React.js Notes — 3 Years Experience | Java Full Stack Developer

> Tailored for Java Full Stack developers with 3 YOE. Focuses on intermediate-advanced concepts, Spring Boot + React integration patterns, real-world scenarios, and interview questions expected at your experience level.

---

## 📑 Table of Contents

1. [React Architecture & How It Maps to Java/Spring](#react-architecture--java-spring-mapping)
2. [Component Design Patterns](#component-design-patterns)
3. [Hooks Deep Dive](#hooks-deep-dive)
4. [State Management Strategy](#state-management-strategy)
5. [API Integration with Spring Boot](#api-integration-with-spring-boot)
6. [Authentication & Authorization (JWT + Spring Security)](#authentication--authorization)
7. [React Router — Advanced Patterns](#react-router-advanced)
8. [Performance Optimization](#performance-optimization)
9. [Error Handling & Boundaries](#error-handling--boundaries)
10. [Testing](#testing)
11. [TypeScript with React](#typescript-with-react)
12. [Deployment & Build](#deployment--build)
13. [Real-World Scenario Questions](#real-world-scenario-questions)
14. [Interview Questions for 3 YOE](#interview-questions-for-3-yoe)

---

## React Architecture & Java Spring Mapping

**Hindi Explanation:**
Java Full Stack developer ho toh React ko Spring Boot ke concepts se map karna easy hai. Components = Controllers (UI layer), Hooks = Service layer logic, Context/Redux = Shared State (like Spring ApplicationContext), Custom Hooks = Utility/Service classes.

| Java/Spring Concept | React Equivalent |
|---|---|
| `@Controller` / REST endpoint | Page Component / Route |
| `@Service` class | Custom Hook (`useXyz`) |
| `@Repository` / DAO | API service layer (`api/userService.js`) |
| Spring `ApplicationContext` | React Context / Redux Store |
| DTO / Model | TypeScript Interface / Type |
| `@Component` (DI) | Reusable UI Component |
| AOP / Interceptors | Axios Interceptors / HOC |
| `@Valid` / Bean Validation | Form validation (Yup/Zod + React Hook Form) |
| `@Cacheable` | `useMemo`, `React.memo`, React Query cache |
| Event-driven (`@EventListener`) | useEffect, Event Emitters, Pub-Sub |

### Project Structure (Production Grade)

```
src/
├── api/                    # API service layer (like @Repository)
│   ├── axiosInstance.js    # Configured Axios (interceptors, baseURL)
│   ├── userApi.js
│   └── productApi.js
├── components/             # Reusable UI (like shared @Components)
│   ├── common/
│   │   ├── Button.tsx
│   │   ├── Modal.tsx
│   │   └── Table.tsx
│   └── layout/
│       ├── Header.tsx
│       ├── Sidebar.tsx
│       └── Footer.tsx
├── features/               # Feature modules (like bounded contexts)
│   ├── auth/
│   │   ├── LoginPage.tsx
│   │   ├── useAuth.ts
│   │   └── authSlice.ts
│   ├── users/
│   │   ├── UserList.tsx
│   │   ├── UserForm.tsx
│   │   ├── useUsers.ts
│   │   └── userApi.ts
│   └── dashboard/
├── hooks/                  # Custom hooks (like @Service)
├── context/                # React Context providers
├── store/                  # Redux store config
├── types/                  # TypeScript interfaces (like DTOs)
├── utils/                  # Utility functions (like @Utility)
├── routes/                 # Route configuration
└── App.tsx
```

---

## Component Design Patterns

**Hindi Explanation:**
3 saal ka experience hai toh interviewer expect karta hai ki tum component design patterns jano — kab kaunsa use karna hai, trade-offs kya hain.

### 1. Container/Presentational Pattern

```tsx
// Container (logic) — like @Service
function UserListContainer() {
  const { data: users, isLoading, error } = useUsers();
  const [filter, setFilter] = useState('');

  const filteredUsers = useMemo(
    () => users?.filter(u => u.name.toLowerCase().includes(filter.toLowerCase())),
    [users, filter]
  );

  if (isLoading) return <Skeleton count={5} />;
  if (error) return <ErrorMessage error={error} />;

  return <UserListView users={filteredUsers} filter={filter} onFilterChange={setFilter} />;
}

// Presentational (pure UI) — like a Thymeleaf template
interface UserListViewProps {
  users: User[];
  filter: string;
  onFilterChange: (val: string) => void;
}

const UserListView: React.FC<UserListViewProps> = React.memo(({ users, filter, onFilterChange }) => (
  <div>
    <input value={filter} onChange={(e) => onFilterChange(e.target.value)} placeholder="Filter..." />
    <table>
      <thead><tr><th>Name</th><th>Email</th><th>Role</th></tr></thead>
      <tbody>
        {users.map(user => (
          <tr key={user.id}>
            <td>{user.name}</td>
            <td>{user.email}</td>
            <td><RoleBadge role={user.role} /></td>
          </tr>
        ))}
      </tbody>
    </table>
  </div>
));
```

### 2. Compound Components (like Builder Pattern in Java)

```tsx
// Similar to how Java Builder pattern gives fluent API
// Compound components give flexible, declarative composition

const DataTable = ({ children, data }) => {
  const [sortConfig, setSortConfig] = useState({ key: null, dir: 'asc' });
  const [selected, setSelected] = useState(new Set());

  const sortedData = useMemo(() => {
    if (!sortConfig.key) return data;
    return [...data].sort((a, b) => {
      const val = a[sortConfig.key] > b[sortConfig.key] ? 1 : -1;
      return sortConfig.dir === 'asc' ? val : -val;
    });
  }, [data, sortConfig]);

  return (
    <TableContext.Provider value={{ sortedData, sortConfig, setSortConfig, selected, setSelected }}>
      <table>{children}</table>
    </TableContext.Provider>
  );
};

DataTable.Header = ({ columns }) => { /* ... */ };
DataTable.Body = ({ renderRow }) => { /* ... */ };
DataTable.Pagination = ({ pageSize }) => { /* ... */ };

// Usage — clean, declarative API
<DataTable data={users}>
  <DataTable.Header columns={['Name', 'Email', 'Created']} />
  <DataTable.Body renderRow={(user) => <UserRow user={user} />} />
  <DataTable.Pagination pageSize={20} />
</DataTable>
```

### 3. Custom Hook Extraction (like extracting @Service from @Controller)

```tsx
// ❌ Fat component (like putting business logic in Controller)
function OrderPage() {
  const [orders, setOrders] = useState([]);
  const [loading, setLoading] = useState(true);
  const [page, setPage] = useState(1);
  const [sort, setSort] = useState('date');
  const [filter, setFilter] = useState({});

  useEffect(() => { /* fetch logic */ }, [page, sort, filter]);

  const calculateTotal = () => { /* ... */ };
  const handleExport = () => { /* ... */ };
  // ... 200 lines of mixed logic and UI
}

// ✅ Clean separation (like Spring layered architecture)
function OrderPage() {
  const { orders, loading, pagination, sorting, filtering } = useOrders();
  const { total, tax } = useOrderCalculations(orders);
  const { exportToCSV } = useOrderExport(orders);

  if (loading) return <PageSkeleton />;

  return (
    <PageLayout title="Orders">
      <OrderFilters {...filtering} />
      <OrderTable orders={orders} {...sorting} />
      <OrderSummary total={total} tax={tax} />
      <Pagination {...pagination} />
      <Button onClick={exportToCSV}>Export CSV</Button>
    </PageLayout>
  );
}
```

---

## Hooks Deep Dive

### useEffect — Common Pitfalls at 3 YOE Level

**Hindi Explanation:**
3 saal experience pe interviewer advanced useEffect scenarios puchega — race conditions, cleanup, stale closures, infinite loops.

```tsx
// 1. Race Condition (jab fast typing se puraani API response nayi se baad mein aaye)
function useSearch(query: string) {
  const [results, setResults] = useState([]);

  useEffect(() => {
    const controller = new AbortController(); // Like Java's Future.cancel()

    const search = async () => {
      try {
        const res = await fetch(`/api/search?q=${query}`, {
          signal: controller.signal
        });
        const data = await res.json();
        setResults(data);
      } catch (err) {
        if (err.name !== 'AbortError') console.error(err);
      }
    };

    if (query.length > 2) search();
    return () => controller.abort(); // Cancel previous request
  }, [query]);

  return results;
}

// 2. Avoiding Infinite Loop — Object/Array in dependency
// ❌ Creates new object every render → infinite loop
useEffect(() => {
  fetchData(filters);
}, [{ status: 'active', role: 'admin' }]); // New reference every time!

// ✅ Serialize or use individual values
useEffect(() => {
  fetchData(filters);
}, [filters.status, filters.role]);

// 3. When NOT to use useEffect
// ❌ Derived state (like computing in getter method in Java)
useEffect(() => {
  setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);

// ✅ Just compute it (it's a derived value, not a side effect)
const fullName = `${firstName} ${lastName}`;

// ❌ Transforming data for display
useEffect(() => {
  setFilteredList(items.filter(i => i.active));
}, [items]);

// ✅ useMemo for expensive transforms
const filteredList = useMemo(() => items.filter(i => i.active), [items]);
```

### useReducer — For Complex Business Logic

```tsx
// Like a State Machine — similar to how you'd model Order states in Java
type OrderStatus = 'idle' | 'loading' | 'success' | 'error' | 'retrying';

interface OrderState {
  status: OrderStatus;
  data: Order[] | null;
  error: string | null;
  retryCount: number;
}

type OrderAction =
  | { type: 'FETCH_START' }
  | { type: 'FETCH_SUCCESS'; payload: Order[] }
  | { type: 'FETCH_ERROR'; payload: string }
  | { type: 'RETRY' }
  | { type: 'RESET' };

function orderReducer(state: OrderState, action: OrderAction): OrderState {
  switch (action.type) {
    case 'FETCH_START':
      return { ...state, status: 'loading', error: null };
    case 'FETCH_SUCCESS':
      return { status: 'success', data: action.payload, error: null, retryCount: 0 };
    case 'FETCH_ERROR':
      return { ...state, status: 'error', error: action.payload };
    case 'RETRY':
      if (state.retryCount >= 3) return state; // Max retries (like @Retryable)
      return { ...state, status: 'retrying', retryCount: state.retryCount + 1 };
    case 'RESET':
      return { status: 'idle', data: null, error: null, retryCount: 0 };
    default:
      return state;
  }
}
```

---

## State Management Strategy

**Hindi Explanation:**
3 YOE pe ye question zaroor aayega: "Kaunsa state management kab use karoge?" Iska answer depend karta hai state ki nature pe.

### Decision Matrix

| State Type | Where to Store | Example |
|---|---|---|
| UI state (local) | `useState` | Modal open/close, form inputs |
| Complex local logic | `useReducer` | Multi-step form, filters |
| Shared across few components | Context + `useReducer` | Theme, locale |
| Server/remote state | React Query / SWR | API data, caching |
| Global client state | Redux Toolkit / Zustand | Auth, cart, notifications |
| URL state | React Router | Page, search params, filters |
| Persistent state | localStorage + hook | User preferences |

### React Query (TanStack Query) — The Game Changer

**Hindi Explanation:**
React Query server state management solve karta hai. Ye caching, background refresh, retry, pagination sab handle karta hai. Spring Boot ke saath kaam karte ho toh ye must-have hai. Redux mein API state rakhne ki zaroorat nahi.

```tsx
// api/userApi.ts — like @Repository in Java
import axios from './axiosInstance';

export const userApi = {
  getAll: (params: UserFilters) =>
    axios.get<PaginatedResponse<User>>('/api/users', { params }),
  getById: (id: string) =>
    axios.get<User>(`/api/users/${id}`),
  create: (data: CreateUserDTO) =>
    axios.post<User>('/api/users', data),
  update: (id: string, data: UpdateUserDTO) =>
    axios.put<User>(`/api/users/${id}`, data),
  delete: (id: string) =>
    axios.delete(`/api/users/${id}`),
};

// hooks/useUsers.ts — like @Service in Java
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

export function useUsers(filters: UserFilters) {
  return useQuery({
    queryKey: ['users', filters],  // Cache key (like @Cacheable key)
    queryFn: () => userApi.getAll(filters),
    staleTime: 5 * 60 * 1000, // 5 min (like cache TTL)
    placeholderData: keepPreviousData, // Show old data while fetching new
  });
}

export function useUser(id: string) {
  return useQuery({
    queryKey: ['users', id],
    queryFn: () => userApi.getById(id),
    enabled: !!id, // Don't fetch if no id
  });
}

export function useCreateUser() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: userApi.create,
    onSuccess: () => {
      // Invalidate cache (like @CacheEvict)
      queryClient.invalidateQueries({ queryKey: ['users'] });
    },
    onError: (error: AxiosError<ApiError>) => {
      toast.error(error.response?.data?.message || 'Failed to create user');
    },
  });
}

// Optimistic Update (update UI before server confirms — like eventual consistency)
export function useUpdateUser() {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: ({ id, data }: { id: string; data: UpdateUserDTO }) =>
      userApi.update(id, data),
    onMutate: async ({ id, data }) => {
      await queryClient.cancelQueries({ queryKey: ['users', id] });
      const previous = queryClient.getQueryData(['users', id]);
      queryClient.setQueryData(['users', id], (old: User) => ({ ...old, ...data }));
      return { previous };
    },
    onError: (_err, { id }, context) => {
      queryClient.setQueryData(['users', id], context?.previous); // Rollback
    },
    onSettled: (_, __, { id }) => {
      queryClient.invalidateQueries({ queryKey: ['users', id] });
    },
  });
}

// Component usage — clean and simple
function UserManagement() {
  const [filters, setFilters] = useState<UserFilters>({ page: 1, size: 10 });
  const { data, isLoading, isError } = useUsers(filters);
  const createUser = useCreateUser();

  const handleCreate = (formData: CreateUserDTO) => {
    createUser.mutate(formData);
  };

  return (
    <div>
      {createUser.isPending && <Spinner />}
      <UserForm onSubmit={handleCreate} isSubmitting={createUser.isPending} />
      <UserTable
        users={data?.content || []}
        loading={isLoading}
        totalPages={data?.totalPages}
        currentPage={filters.page}
        onPageChange={(page) => setFilters(f => ({ ...f, page }))}
      />
    </div>
  );
}
```

---

## API Integration with Spring Boot

### Axios Instance with Interceptors (like Spring's RestTemplate + Interceptor)

```tsx
// api/axiosInstance.ts
import axios, { AxiosError, InternalAxiosRequestConfig } from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL || 'http://localhost:8080',
  timeout: 30000,
  headers: { 'Content-Type': 'application/json' },
});

// Request Interceptor — attach JWT (like Spring's ClientHttpRequestInterceptor)
api.interceptors.request.use(
  (config: InternalAxiosRequestConfig) => {
    const token = localStorage.getItem('accessToken');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// Response Interceptor — handle 401, refresh token
let isRefreshing = false;
let failedQueue: Array<{ resolve: Function; reject: Function }> = [];

const processQueue = (error: any, token: string | null) => {
  failedQueue.forEach(({ resolve, reject }) => {
    error ? reject(error) : resolve(token);
  });
  failedQueue = [];
};

api.interceptors.response.use(
  (response) => response.data, // Unwrap data
  async (error: AxiosError) => {
    const originalRequest = error.config as any;

    // Token expired — refresh (like Spring Security filter chain)
    if (error.response?.status === 401 && !originalRequest._retry) {
      if (isRefreshing) {
        return new Promise((resolve, reject) => {
          failedQueue.push({ resolve, reject });
        }).then(token => {
          originalRequest.headers.Authorization = `Bearer ${token}`;
          return api(originalRequest);
        });
      }

      originalRequest._retry = true;
      isRefreshing = true;

      try {
        const refreshToken = localStorage.getItem('refreshToken');
        const { accessToken } = await axios.post('/api/auth/refresh', { refreshToken });
        localStorage.setItem('accessToken', accessToken);
        processQueue(null, accessToken);
        originalRequest.headers.Authorization = `Bearer ${accessToken}`;
        return api(originalRequest);
      } catch (refreshError) {
        processQueue(refreshError, null);
        localStorage.clear();
        window.location.href = '/login';
        return Promise.reject(refreshError);
      } finally {
        isRefreshing = false;
      }
    }

    return Promise.reject(error);
  }
);

export default api;
```

### Handling Spring Boot Validation Errors

```tsx
// Spring Boot returns: { "fieldErrors": { "email": "must be valid", "name": "required" } }
// Map to form errors

interface SpringValidationError {
  status: number;
  message: string;
  fieldErrors?: Record<string, string>;
  timestamp?: string;
}

function useFormWithSpringValidation<T extends Record<string, any>>(
  initialValues: T,
  onSubmit: (values: T) => Promise<void>
) {
  const [values, setValues] = useState<T>(initialValues);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});
  const [submitting, setSubmitting] = useState(false);

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    setErrors({});
    setSubmitting(true);

    try {
      await onSubmit(values);
    } catch (err: any) {
      if (err.response?.status === 400) {
        const validationError: SpringValidationError = err.response.data;
        if (validationError.fieldErrors) {
          setErrors(validationError.fieldErrors as any);
        }
      }
    } finally {
      setSubmitting(false);
    }
  };

  const setField = (field: keyof T, value: any) => {
    setValues(prev => ({ ...prev, [field]: value }));
    setErrors(prev => ({ ...prev, [field]: undefined })); // Clear on type
  };

  return { values, errors, submitting, handleSubmit, setField };
}
```

---

## Authentication & Authorization

### Full JWT Flow (Spring Security + React)

**Hindi Explanation:**
Backend pe Spring Security + JWT hai. React pe login karne pe accessToken + refreshToken milta hai. Protected routes pe token check hota hai. Token expire hone pe refresh token se naya accessToken lete hain.

```tsx
// types/auth.ts
interface LoginRequest { email: string; password: string; }
interface AuthResponse { accessToken: string; refreshToken: string; user: User; }
interface User { id: string; name: string; email: string; roles: string[]; }

// context/AuthContext.tsx
interface AuthContextType {
  user: User | null;
  loading: boolean;
  login: (creds: LoginRequest) => Promise<void>;
  logout: () => void;
  hasRole: (role: string) => boolean;
  hasAnyRole: (roles: string[]) => boolean;
}

const AuthContext = createContext<AuthContextType | null>(null);

export function AuthProvider({ children }: { children: React.ReactNode }) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const navigate = useNavigate();

  // Check token on mount (like Spring's SecurityContextHolder)
  useEffect(() => {
    const token = localStorage.getItem('accessToken');
    if (token) {
      api.get<User>('/api/auth/me')
        .then(setUser)
        .catch(() => localStorage.clear())
        .finally(() => setLoading(false));
    } else {
      setLoading(false);
    }
  }, []);

  const login = async (creds: LoginRequest) => {
    const { accessToken, refreshToken, user } = await api.post<AuthResponse>(
      '/api/auth/login', creds
    );
    localStorage.setItem('accessToken', accessToken);
    localStorage.setItem('refreshToken', refreshToken);
    setUser(user);
    navigate('/dashboard');
  };

  const logout = () => {
    api.post('/api/auth/logout').catch(() => {});
    localStorage.clear();
    setUser(null);
    navigate('/login');
  };

  const hasRole = (role: string) => user?.roles.includes(role) ?? false;
  const hasAnyRole = (roles: string[]) => roles.some(r => user?.roles.includes(r));

  return (
    <AuthContext.Provider value={{ user, loading, login, logout, hasRole, hasAnyRole }}>
      {children}
    </AuthContext.Provider>
  );
}

export const useAuth = () => {
  const ctx = useContext(AuthContext);
  if (!ctx) throw new Error('useAuth must be inside AuthProvider');
  return ctx;
};

// Route Guards (like Spring Security @PreAuthorize)
function ProtectedRoute({ roles, children }: { roles?: string[]; children: React.ReactNode }) {
  const { user, loading, hasAnyRole } = useAuth();

  if (loading) return <FullPageSpinner />;
  if (!user) return <Navigate to="/login" replace />;
  if (roles && !hasAnyRole(roles)) return <Navigate to="/unauthorized" replace />;

  return <>{children}</>;
}

// Usage in routes
<Route path="/admin/*" element={
  <ProtectedRoute roles={['ROLE_ADMIN']}>
    <AdminLayout />
  </ProtectedRoute>
} />

// Role-based UI rendering (like sec:authorize in Thymeleaf)
function Navbar() {
  const { user, hasRole, logout } = useAuth();

  return (
    <nav>
      <Link to="/dashboard">Dashboard</Link>
      {hasRole('ROLE_ADMIN') && <Link to="/admin">Admin Panel</Link>}
      {hasRole('ROLE_MANAGER') && <Link to="/reports">Reports</Link>}
      <button onClick={logout}>Logout</button>
    </nav>
  );
}
```

---

## React Router Advanced

```tsx
// Lazy loading + route config (like Spring MVC URL mappings)
const routes = [
  {
    path: '/',
    element: <MainLayout />,
    errorElement: <ErrorPage />,
    children: [
      { index: true, element: <Dashboard /> },
      { path: 'users', element: <ProtectedRoute roles={['ADMIN']}><UserList /></ProtectedRoute> },
      { path: 'users/:id', element: <UserDetail />, loader: userLoader },
      { path: 'orders', element: <Outlet />,
        children: [
          { index: true, element: <OrderList /> },
          { path: ':id', element: <OrderDetail /> },
          { path: 'new', element: <OrderForm /> },
        ]
      },
    ],
  },
  { path: '/login', element: <Login /> },
  { path: '*', element: <NotFound /> },
];

// Search params as state (instead of useState for filters)
import { useSearchParams } from 'react-router-dom';

function OrderList() {
  const [searchParams, setSearchParams] = useSearchParams();

  const filters = {
    status: searchParams.get('status') || 'all',
    page: Number(searchParams.get('page')) || 1,
    sort: searchParams.get('sort') || 'createdAt',
  };

  const updateFilter = (key: string, value: string) => {
    setSearchParams(prev => {
      prev.set(key, value);
      if (key !== 'page') prev.set('page', '1'); // Reset page on filter change
      return prev;
    });
  };

  const { data } = useOrders(filters); // URL is the source of truth

  return (/* ... */);
}
```

---

## Performance Optimization

**Hindi Explanation:**
3 YOE pe interviewer expect karta hai ki tum real performance issues solve kar sako — unnecessary re-renders, large lists, bundle size, API waterfall.

### Re-render Prevention Cheat Sheet

```tsx
// 1. State colocation — move state down
// ❌ Parent re-renders everything on hover state change
function Page() {
  const [hovered, setHovered] = useState(false); // Only Tooltip needs this!
  return (
    <div>
      <ExpensiveTree /> {/* Re-renders unnecessarily */}
      <Tooltip visible={hovered} />
    </div>
  );
}

// ✅ Move state to where it's needed
function Page() {
  return (
    <div>
      <ExpensiveTree />
      <TooltipWrapper /> {/* Manages its own hover state */}
    </div>
  );
}

// 2. Children as props pattern (composition over memo)
// ❌ ExpensiveTree re-renders when count changes
function Parent() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>{count}</button>
      <ExpensiveTree />
    </div>
  );
}

// ✅ Pass ExpensiveTree as children — it won't re-render
function Counter({ children }) {
  const [count, setCount] = useState(0);
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>{count}</button>
      {children}
    </div>
  );
}

<Counter><ExpensiveTree /></Counter>

// 3. Virtualization for large lists (like pagination in DB but for UI)
import { useVirtualizer } from '@tanstack/react-virtual';

function VirtualList({ items }: { items: Item[] }) {
  const parentRef = useRef<HTMLDivElement>(null);

  const virtualizer = useVirtualizer({
    count: items.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 50,
    overscan: 5,
  });

  return (
    <div ref={parentRef} style={{ height: '500px', overflow: 'auto' }}>
      <div style={{ height: `${virtualizer.getTotalSize()}px`, position: 'relative' }}>
        {virtualizer.getVirtualItems().map(virtualRow => (
          <div
            key={virtualRow.key}
            style={{
              position: 'absolute',
              top: 0,
              transform: `translateY(${virtualRow.start}px)`,
              height: `${virtualRow.size}px`,
            }}
          >
            {items[virtualRow.index].name}
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

## Error Handling & Boundaries

```tsx
// Global Error Boundary with retry + error reporting
class GlobalErrorBoundary extends React.Component<
  { children: React.ReactNode },
  { hasError: boolean; error: Error | null }
> {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error: Error) {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    // Send to monitoring (like Spring's @ControllerAdvice logging)
    errorReportingService.captureException(error, { extra: errorInfo });
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="error-page">
          <h1>Something went wrong</h1>
          <p>{this.state.error?.message}</p>
          <button onClick={() => this.setState({ hasError: false })}>Try Again</button>
          <button onClick={() => window.location.href = '/'}>Go Home</button>
        </div>
      );
    }
    return this.props.children;
  }
}

// API error handling hook
function useApiError() {
  const navigate = useNavigate();

  const handleError = useCallback((error: AxiosError<ApiError>) => {
    const status = error.response?.status;
    const message = error.response?.data?.message || 'Something went wrong';

    switch (status) {
      case 401: navigate('/login'); break;
      case 403: toast.error('Access denied'); navigate('/unauthorized'); break;
      case 404: toast.error('Resource not found'); break;
      case 422: return error.response?.data?.fieldErrors; // Return for form display
      case 429: toast.error('Too many requests. Please wait.'); break;
      case 500: toast.error('Server error. Try again later.'); break;
      default: toast.error(message);
    }
  }, [navigate]);

  return { handleError };
}
```

---

## Testing

**Hindi Explanation:**
Testing mein interviewer expect karta hai ki tum unit tests (components), integration tests (hooks + API), aur basic e2e scenarios likh sako. Jest + React Testing Library standard hai.

```tsx
// Component Testing
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { rest } from 'msw';
import { setupServer } from 'msw/node';

// Mock server (like WireMock for Spring Boot tests)
const server = setupServer(
  rest.get('/api/users', (req, res, ctx) => {
    return res(ctx.json({ content: [{ id: '1', name: 'John' }], totalPages: 1 }));
  }),
  rest.post('/api/users', (req, res, ctx) => {
    return res(ctx.status(201), ctx.json({ id: '2', ...req.body }));
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

// Test wrapper with providers
function renderWithProviders(ui: React.ReactElement) {
  const queryClient = new QueryClient({
    defaultOptions: { queries: { retry: false } },
  });
  return render(
    <QueryClientProvider client={queryClient}>
      <MemoryRouter>{ui}</MemoryRouter>
    </QueryClientProvider>
  );
}

// Test cases
describe('UserList', () => {
  it('renders users from API', async () => {
    renderWithProviders(<UserList />);

    expect(screen.getByText(/loading/i)).toBeInTheDocument();
    await waitFor(() => {
      expect(screen.getByText('John')).toBeInTheDocument();
    });
  });

  it('handles API error gracefully', async () => {
    server.use(
      rest.get('/api/users', (req, res, ctx) => res(ctx.status(500)))
    );

    renderWithProviders(<UserList />);
    await waitFor(() => {
      expect(screen.getByText(/error/i)).toBeInTheDocument();
    });
  });

  it('creates user and refreshes list', async () => {
    const user = userEvent.setup();
    renderWithProviders(<UserManagement />);

    await user.type(screen.getByLabelText(/name/i), 'Jane');
    await user.type(screen.getByLabelText(/email/i), 'jane@test.com');
    await user.click(screen.getByRole('button', { name: /create/i }));

    await waitFor(() => {
      expect(screen.getByText('Jane')).toBeInTheDocument();
    });
  });
});

// Custom Hook Testing
import { renderHook, waitFor } from '@testing-library/react';

describe('useDebounce', () => {
  it('debounces value updates', async () => {
    const { result, rerender } = renderHook(
      ({ value }) => useDebounce(value, 300),
      { initialProps: { value: '' } }
    );

    rerender({ value: 'hello' });
    expect(result.current).toBe(''); // Not updated yet

    await waitFor(() => {
      expect(result.current).toBe('hello');
    }, { timeout: 400 });
  });
});
```

---

## TypeScript with React

**Hindi Explanation:**
Java developer ho toh TypeScript natural lagega — types, interfaces, generics sab similar hai. Production React projects mein TypeScript must hai.

```tsx
// Types (like Java DTOs / Entities)
interface User {
  id: string;
  name: string;
  email: string;
  roles: Role[];
  createdAt: string;
  department?: Department; // Optional (like Java's Optional<Department>)
}

type Role = 'ADMIN' | 'USER' | 'MANAGER'; // Like Java Enum
type OrderStatus = 'PENDING' | 'CONFIRMED' | 'SHIPPED' | 'DELIVERED';

// Generic API response (like Java's ResponseEntity<T> or Page<T>)
interface PaginatedResponse<T> {
  content: T[];
  totalElements: number;
  totalPages: number;
  page: number;
  size: number;
}

interface ApiError {
  status: number;
  message: string;
  timestamp: string;
  fieldErrors?: Record<string, string>;
}

// Generic reusable hook (like Java generics)
function useCrud<T extends { id: string }>(baseUrl: string) {
  const queryClient = useQueryClient();
  const queryKey = [baseUrl];

  const list = useQuery<PaginatedResponse<T>>({
    queryKey,
    queryFn: () => api.get(baseUrl),
  });

  const create = useMutation<T, AxiosError<ApiError>, Omit<T, 'id'>>({
    mutationFn: (data) => api.post(baseUrl, data),
    onSuccess: () => queryClient.invalidateQueries({ queryKey }),
  });

  const update = useMutation<T, AxiosError<ApiError>, { id: string; data: Partial<T> }>({
    mutationFn: ({ id, data }) => api.put(`${baseUrl}/${id}`, data),
    onSuccess: () => queryClient.invalidateQueries({ queryKey }),
  });

  const remove = useMutation<void, AxiosError<ApiError>, string>({
    mutationFn: (id) => api.delete(`${baseUrl}/${id}`),
    onSuccess: () => queryClient.invalidateQueries({ queryKey }),
  });

  return { list, create, update, remove };
}

// Usage — just like calling a Java generic service
const { list, create, remove } = useCrud<User>('/api/users');
const { list: orderList } = useCrud<Order>('/api/orders');

// Component Props with TypeScript
interface DataTableProps<T> {
  data: T[];
  columns: Column<T>[];
  onRowClick?: (item: T) => void;
  loading?: boolean;
  emptyMessage?: string;
}

interface Column<T> {
  key: keyof T;
  header: string;
  render?: (value: T[keyof T], item: T) => React.ReactNode;
  sortable?: boolean;
}

function DataTable<T extends { id: string }>({
  data, columns, onRowClick, loading, emptyMessage = 'No data'
}: DataTableProps<T>) {
  if (loading) return <TableSkeleton columns={columns.length} />;
  if (!data.length) return <EmptyState message={emptyMessage} />;

  return (
    <table>
      <thead>
        <tr>{columns.map(col => <th key={String(col.key)}>{col.header}</th>)}</tr>
      </thead>
      <tbody>
        {data.map(item => (
          <tr key={item.id} onClick={() => onRowClick?.(item)}>
            {columns.map(col => (
              <td key={String(col.key)}>
                {col.render ? col.render(item[col.key], item) : String(item[col.key])}
              </td>
            ))}
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```

---

## Deployment & Build

### Spring Boot + React Deployment Options

```
Option 1: Monolith (Single JAR)
├── React build → src/main/resources/static/
├── Spring Boot serves React SPA
└── Single deployment unit

Option 2: Separate (Recommended for 3 YOE projects)
├── React → Vercel / Netlify / S3+CloudFront / Nginx
├── Spring Boot → AWS EC2 / ECS / Kubernetes
├── CORS configured on backend
└── Environment-based API URL

Option 3: Docker Compose
├── docker-compose.yml
│   ├── frontend (Nginx + React build)
│   ├── backend (Spring Boot JAR)
│   └── db (PostgreSQL)
```

```dockerfile
# Dockerfile for React (multi-stage build)
FROM node:18-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

---

## Real-World Scenario Questions

### Scenario 1: User complains the page is slow

**Hindi:** Interviewer bolega "Application slow hai, kaise debug karoge?"

**Answer Framework:**
1. **Identify** — React DevTools Profiler → which component re-renders most
2. **Network** — Check API waterfall in Network tab, large payloads
3. **Bundle** — Analyze with `npx vite-bundle-visualizer`, code-split heavy libs
4. **Rendering** — Check for missing memo, inline objects in props, expensive maps without virtualization
5. **State** — Context with many consumers? Split contexts. Redux selector not specific enough?

### Scenario 2: Two tabs show stale data after update in one

**Answer:** Use React Query's `refetchOnWindowFocus: true` (default) or broadcast channel API to sync state across tabs.

### Scenario 3: Form with 50+ fields — how to architect?

**Answer:**
- Use React Hook Form (uncontrolled internally = better performance)
- Split into wizard/stepper with sub-forms
- Schema validation with Zod/Yup
- Dynamic fields with `useFieldArray`

```tsx
import { useForm, useFieldArray } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const schema = z.object({
  name: z.string().min(1, 'Required'),
  email: z.string().email(),
  addresses: z.array(z.object({
    street: z.string().min(1),
    city: z.string().min(1),
    pincode: z.string().regex(/^\d{6}$/),
  })).min(1, 'At least one address'),
});

function ComplexForm() {
  const { register, control, handleSubmit, formState: { errors } } = useForm({
    resolver: zodResolver(schema),
    defaultValues: { name: '', email: '', addresses: [{ street: '', city: '', pincode: '' }] }
  });

  const { fields, append, remove } = useFieldArray({ control, name: 'addresses' });

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('name')} />
      {errors.name && <span>{errors.name.message}</span>}

      {fields.map((field, index) => (
        <div key={field.id}>
          <input {...register(`addresses.${index}.street`)} />
          <input {...register(`addresses.${index}.city`)} />
          <input {...register(`addresses.${index}.pincode`)} />
          <button type="button" onClick={() => remove(index)}>Remove</button>
        </div>
      ))}
      <button type="button" onClick={() => append({ street: '', city: '', pincode: '' })}>
        Add Address
      </button>
      <button type="submit">Submit</button>
    </form>
  );
}
```

### Scenario 4: Real-time notifications from Spring Boot

**Answer:** Use WebSocket (STOMP over SockJS) or Server-Sent Events.

```tsx
// WebSocket with STOMP (matches Spring's @EnableWebSocketMessageBroker)
import { Client } from '@stomp/stompjs';

function useNotifications() {
  const [notifications, setNotifications] = useState<Notification[]>([]);

  useEffect(() => {
    const client = new Client({
      brokerURL: 'ws://localhost:8080/ws',
      onConnect: () => {
        client.subscribe('/user/queue/notifications', (message) => {
          const notification = JSON.parse(message.body);
          setNotifications(prev => [notification, ...prev]);
          toast.info(notification.message);
        });
      },
      reconnectDelay: 5000,
    });

    client.activate();
    return () => { client.deactivate(); };
  }, []);

  return notifications;
}
```

### Scenario 5: Implement Role-Based Access at Component Level

```tsx
// Like Spring Security's @PreAuthorize but for UI
function usePermission() {
  const { user } = useAuth();

  const can = useCallback((action: string, resource: string): boolean => {
    const permissions = ROLE_PERMISSIONS[user?.role] || [];
    return permissions.includes(`${action}:${resource}`);
  }, [user]);

  return { can };
}

// Permission component
function Can({ action, resource, children, fallback = null }) {
  const { can } = usePermission();
  return can(action, resource) ? <>{children}</> : <>{fallback}</>;
}

// Usage
<Can action="delete" resource="user" fallback={<span>No permission</span>}>
  <button onClick={handleDelete}>Delete User</button>
</Can>
```

---

## Interview Questions for 3 YOE

### Q1: How would you structure a React project that integrates with multiple Spring Boot microservices?

**Answer:**
- Create a shared API layer with service-specific modules (`userApi.ts`, `orderApi.ts`, `paymentApi.ts`)
- Each microservice gets its own Axios instance (different base URLs)
- Use React Query with query key prefixes per service
- BFF (Backend for Frontend) pattern if needed — one API gateway
- Feature-based folder structure mapping to domain boundaries
- Shared TypeScript types generated from OpenAPI specs (`openapi-typescript-codegen`)

---

### Q2: Explain the React rendering cycle and how batching works.

**Answer:**
React's rendering has 3 phases:
1. **Trigger** — setState called → component marked as "dirty"
2. **Render** — React calls your component function, creates new Virtual DOM
3. **Commit** — Diff old vs new VDOM, apply minimal changes to real DOM

**Batching (React 18):** All state updates within the same event loop tick are batched into a single re-render — whether in event handlers, setTimeout, promises, or native events. Use `flushSync` to force synchronous update.

---

### Q3: Context API vs Redux — when would you choose one over the other?

**Answer:**
**Context:**
- Theme, auth, locale (low-frequency updates)
- Small-medium apps
- When you want zero dependencies

**Redux (Toolkit):**
- Complex client state with many actions
- State shared across many unrelated components
- Time-travel debugging needed
- Middleware requirements (logging, side effects)

**Neither for server state** — use React Query/SWR instead.

---

### Q4: How do you handle memory leaks in React?

**Answer:**
Common sources and solutions:
1. **Unmounted state updates** — AbortController for fetch, cleanup in useEffect
2. **Event listeners** — Remove in cleanup function
3. **Subscriptions (WebSocket, intervals)** — Unsubscribe/clear in cleanup
4. **Closures holding references** — Ensure proper dependency arrays
5. **Large objects in state** — Clean up when component unmounts

```tsx
useEffect(() => {
  const controller = new AbortController();
  const interval = setInterval(poll, 5000);
  window.addEventListener('resize', handleResize);

  return () => {
    controller.abort();
    clearInterval(interval);
    window.removeEventListener('resize', handleResize);
  };
}, []);
```

---

### Q5: What's the difference between `key` remounting and state preservation?

**Answer:**
Same key + same position = state preserved. Different key = component unmounts and remounts (fresh state). This is useful for:
- Resetting form state when switching between edit targets
- Forcing re-initialization of a component

```tsx
// Reset form when userId changes (instead of useEffect to reset)
<UserForm key={userId} userId={userId} />
```

---

### Q6: How does React Query differ from Redux for data fetching?

**Answer:**
| Aspect | Redux + Thunk/Saga | React Query |
|--------|---|---|
| Boilerplate | High (actions, reducers, selectors) | Minimal |
| Caching | Manual | Automatic (stale-while-revalidate) |
| Background refresh | Manual | Built-in |
| Retry | Manual | Built-in with exponential backoff |
| Pagination/Infinite | Manual | `useInfiniteQuery` |
| Optimistic updates | Manual | Built-in pattern |
| DevTools | Redux DevTools | React Query DevTools |
| Cache invalidation | Manual dispatch | `queryClient.invalidateQueries` |

---

### Q7: Explain Concurrent Features in React 18.

**Answer:**
- **useTransition** — Mark state updates as non-urgent (e.g., filtering large lists). UI stays responsive.
- **useDeferredValue** — Defer re-rendering of expensive components with stale value.
- **Suspense for data** — Components can "suspend" while loading, showing fallback.
- **Automatic Batching** — All updates batched regardless of context.
- **Streaming SSR** — `renderToPipeableStream` for progressive HTML streaming.

---

### Q8: You're asked to migrate a jQuery/legacy app to React. How do you approach it?

**Answer:**
1. **Strangler Fig Pattern** — Don't rewrite all at once
2. Mount React components inside existing pages (`ReactDOM.createRoot(existingDiv)`)
3. Start with new features in React
4. Gradually replace page sections
5. Share state via custom events or a micro-frontend approach
6. Ensure build tool (Webpack/Vite) coexists with legacy bundler

---

### Q9: How do you prevent unnecessary re-renders in a complex form?

**Answer:**
1. Use React Hook Form (uncontrolled by default — doesn't re-render on every keystroke)
2. Split form into sub-components with `React.memo`
3. Use `useFormContext` for nested form access without prop drilling
4. Isolate expensive components with `Controller` from RHF
5. Avoid storing derived state — compute inline or with `useMemo`

---

### Q10: Explain the difference between SSR, SSG, ISR, and CSR.

**Answer:**
| Strategy | When HTML Generated | Use Case |
|----------|---|---|
| **CSR** | Browser (runtime) | Dashboards, admin panels |
| **SSR** | Server (every request) | Dynamic pages needing SEO |
| **SSG** | Build time | Blogs, docs, marketing pages |
| **ISR** | Build time + revalidate on interval | E-commerce product pages |

In Next.js: `getServerSideProps` (SSR), `getStaticProps` (SSG), `revalidate` option (ISR).

---

### Q11: How would you implement an undo/redo feature?

**Answer:**
Use a history stack pattern:

```tsx
function useUndoRedo<T>(initialState: T) {
  const [history, setHistory] = useState<T[]>([initialState]);
  const [pointer, setPointer] = useState(0);

  const current = history[pointer];

  const set = (newState: T) => {
    const newHistory = history.slice(0, pointer + 1);
    newHistory.push(newState);
    setHistory(newHistory);
    setPointer(newHistory.length - 1);
  };

  const undo = () => setPointer(Math.max(0, pointer - 1));
  const redo = () => setPointer(Math.min(history.length - 1, pointer + 1));

  const canUndo = pointer > 0;
  const canRedo = pointer < history.length - 1;

  return { current, set, undo, redo, canUndo, canRedo };
}
```

---

### Q12: How do you handle environment-specific configuration in React?

**Answer:**
```bash
# .env.development
VITE_API_URL=http://localhost:8080
VITE_FEATURE_FLAG_CHAT=true

# .env.production
VITE_API_URL=https://api.myapp.com
VITE_FEATURE_FLAG_CHAT=false
```

```tsx
// config.ts — centralized, typed configuration
const config = {
  apiUrl: import.meta.env.VITE_API_URL,
  features: {
    chat: import.meta.env.VITE_FEATURE_FLAG_CHAT === 'true',
  },
} as const;

export default config;
```

---

## 🏁 Final Tips for 3 YOE Interview

1. **Always relate React concepts to Java** — interviewers love cross-domain knowledge
2. **Know trade-offs** — don't just say "use Redux". Explain when and why NOT to.
3. **Talk about production issues you've solved** — stale data, memory leaks, performance
4. **Be ready for system design** — "Design a dashboard with real-time updates, role-based access, and 10k rows"
5. **Know the ecosystem** — React Query, React Hook Form, Zustand, Next.js, Vite
6. **Testing is expected** — at least know RTL basics and MSW for mocking APIs

---

> **💡 Java Full Stack Advantage:** Your Spring Boot knowledge (DI, layered architecture, validation, security) maps directly to React patterns. Use this in interviews — show you think in systems, not just components.
