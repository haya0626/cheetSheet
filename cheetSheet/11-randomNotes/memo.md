# 半年学習ロードマップ

## 目標

半年で、React / Next.js / TypeScript を主軸にしつつ、

- API
- HTTP
- Java / Spring Boot
- SQL / DB
- テスト
- Git
- CI/CD
- セキュリティ
- 設計

まで実務で最低限説明・実装できる状態を目指す。

---

# 優先度 S

最優先で学習する。

## JavaScript

- [ ] let / const
- [ ] primitive 型 / reference 型
- [ ] null / undefined
- [ ] == / ===
- [ ] object
- [ ] array
- [ ] function
- [ ] arrow function
- [ ] scope
- [ ] closure
- [ ] destructuring
- [ ] spread syntax
- [ ] rest parameters
- [ ] optional chaining
- [ ] nullish coalescing
- [ ] map
- [ ] filter
- [ ] find
- [ ] some
- [ ] every
- [ ] reduce
- [ ] sort
- [ ] shallow copy
- [ ] deep copy
- [ ] Promise
- [ ] async / await
- [ ] Promise.all
- [ ] Promise.allSettled
- [ ] Event Loop
- [ ] Call Stack
- [ ] Microtask Queue
- [ ] import / export

---

## TypeScript

- [ ] 基本的な型
- [ ] 型推論
- [ ] type
- [ ] interface
- [ ] type と interface の違い
- [ ] Union Type
- [ ] Intersection Type
- [ ] Literal Type
- [ ] optional property
- [ ] function type
- [ ] Generics
- [ ] keyof
- [ ] typeof
- [ ] indexed access type
- [ ] Utility Types
- [ ] Partial
- [ ] Required
- [ ] Pick
- [ ] Omit
- [ ] Record
- [ ] ReturnType
- [ ] unknown
- [ ] never
- [ ] any を避ける理由
- [ ] Type Guard
- [ ] narrowing
- [ ] discriminated union
- [ ] satisfies
- [ ] readonly

---

## React

- [ ] Component
- [ ] JSX
- [ ] Props
- [ ] State
- [ ] Event
- [ ] conditional rendering
- [ ] list rendering
- [ ] key
- [ ] controlled component
- [ ] uncontrolled component
- [ ] lifting state up
- [ ] useState
- [ ] useEffect
- [ ] useRef
- [ ] useMemo
- [ ] useCallback
- [ ] useContext
- [ ] useReducer
- [ ] custom hooks
- [ ] dependency array
- [ ] effect cleanup
- [ ] stale closure
- [ ] rendering
- [ ] re-render
- [ ] mount
- [ ] unmount
- [ ] reconciliation
- [ ] React.memo
- [ ] Error Boundary
- [ ] Suspense

---

## Next.js

- [ ] App Router
- [ ] Server Component
- [ ] Client Component
- [ ] use client
- [ ] SSR
- [ ] CSR
- [ ] SSG
- [ ] ISR
- [ ] Hydration
- [ ] Route Handler
- [ ] Dynamic Route
- [ ] Path Params
- [ ] Search Params
- [ ] Layout
- [ ] loading.tsx
- [ ] error.tsx
- [ ] not-found.tsx
- [ ] Middleware
- [ ] Server Actions
- [ ] Next.js Cache
- [ ] Request Memoization
- [ ] Data Cache
- [ ] Router Cache
- [ ] Revalidation

---

## HTTP / Web

- [ ] URL / URI
- [ ] Domain
- [ ] DNS
- [ ] IP
- [ ] TCP
- [ ] HTTPS
- [ ] TLS
- [ ] HTTP Request
- [ ] HTTP Response
- [ ] HTTP Method
- [ ] HTTP Status Code
- [ ] HTTP Header
- [ ] Content-Type
- [ ] Content-Disposition
- [ ] Authorization
- [ ] Cookie
- [ ] Session
- [ ] LocalStorage
- [ ] SessionStorage
- [ ] Same-Origin Policy
- [ ] CORS
- [ ] Preflight Request
- [ ] Cache-Control
- [ ] ETag

---

## API

- [ ] REST
- [ ] Resource
- [ ] URI 設計
- [ ] Request
- [ ] Response
- [ ] JSON
- [ ] serialization
- [ ] deserialization
- [ ] Axios
- [ ] fetch
- [ ] OpenAPI
- [ ] Swagger
- [ ] Orval
- [ ] Request DTO
- [ ] Response DTO
- [ ] Error Response
- [ ] Pagination
- [ ] Filtering
- [ ] Sorting
- [ ] File Upload
- [ ] File Download
- [ ] Blob
- [ ] multipart/form-data

---

# 優先度 A

実務力を伸ばすために必須。

## React 設計

- [ ] Component 設計
- [ ] Props 設計
- [ ] State 設計
- [ ] Custom Hooks 設計
- [ ] Container / View
- [ ] shared component
- [ ] business component
- [ ] Composition
- [ ] Headless UI
- [ ] Design System
- [ ] Design Token
- [ ] Storybook
- [ ] Feature Based Architecture

---

## Form

- [ ] React Hook Form
- [ ] useForm
- [ ] Controller
- [ ] useWatch
- [ ] reset
- [ ] setValue
- [ ] getValues
- [ ] formState
- [ ] dirty
- [ ] touched
- [ ] Yup
- [ ] Zod
- [ ] Schema Validation
- [ ] Cross Field Validation
- [ ] error message 設計

---

## TanStack Query

- [ ] Server State
- [ ] Query
- [ ] Mutation
- [ ] QueryKey
- [ ] cache
- [ ] staleTime
- [ ] gcTime
- [ ] enabled
- [ ] select
- [ ] placeholderData
- [ ] refetch
- [ ] invalidateQueries
- [ ] retry
- [ ] optimistic update
- [ ] dependent queries
- [ ] parallel queries
- [ ] prefetch

---

## Web Security

- [ ] Authentication
- [ ] Authorization
- [ ] Session Authentication
- [ ] Token Authentication
- [ ] JWT
- [ ] OAuth 2.0
- [ ] OpenID Connect
- [ ] CSRF
- [ ] XSS
- [ ] SQL Injection
- [ ] CORS
- [ ] CSP
- [ ] Clickjacking
- [ ] HttpOnly
- [ ] Secure Cookie
- [ ] SameSite
- [ ] API Key
- [ ] Secrets
- [ ] OWASP Top 10

---

## Testing

- [ ] Unit Test
- [ ] Integration Test
- [ ] E2E Test
- [ ] Test Pyramid
- [ ] Mock
- [ ] Stub
- [ ] Spy
- [ ] Jest
- [ ] Vitest
- [ ] React Testing Library
- [ ] userEvent
- [ ] MSW
- [ ] Playwright
- [ ] Coverage
- [ ] Branch Coverage
- [ ] TDD
- [ ] Boundary Value
- [ ] Equivalence Partitioning

---

## Git

- [ ] Working Tree
- [ ] Staging Area
- [ ] Repository
- [ ] HEAD
- [ ] Branch
- [ ] Remote
- [ ] commit
- [ ] fetch
- [ ] pull
- [ ] push
- [ ] merge
- [ ] rebase
- [ ] conflict
- [ ] cherry-pick
- [ ] revert
- [ ] reset
- [ ] reflog
- [ ] stash
- [ ] squash
- [ ] fixup
- [ ] interactive rebase
- [ ] upstream
- [ ] Pull Request
- [ ] Code Review

---

# 優先度 B

フロントエンジニアとして差をつける。

## Accessibility

- [ ] semantic HTML
- [ ] WCAG
- [ ] keyboard 操作
- [ ] focus
- [ ] focus management
- [ ] screen reader
- [ ] alt
- [ ] label
- [ ] ARIA
- [ ] contrast
- [ ] form accessibility
- [ ] modal accessibility

---

## Performance

- [ ] Core Web Vitals
- [ ] LCP
- [ ] INP
- [ ] CLS
- [ ] Lighthouse
- [ ] bundle size
- [ ] code splitting
- [ ] lazy loading
- [ ] dynamic import
- [ ] image optimization
- [ ] font optimization
- [ ] caching
- [ ] React Profiler
- [ ] Browser DevTools Performance
- [ ] unnecessary re-render

---

## Debugging

- [ ] Chrome DevTools
- [ ] Elements
- [ ] Console
- [ ] Sources
- [ ] Network
- [ ] Application
- [ ] Performance
- [ ] React DevTools
- [ ] Breakpoint
- [ ] Stack Trace
- [ ] HTTP Request / Response 調査
- [ ] Front / Backend 切り分け
- [ ] SQL 切り分け
- [ ] 環境差異
- [ ] 再現条件
- [ ] Root Cause Analysis

---

## CI/CD

- [ ] CI
- [ ] CD
- [ ] Pipeline
- [ ] GitHub Actions
- [ ] Workflow
- [ ] Job
- [ ] Step
- [ ] Artifact
- [ ] Cache
- [ ] Secret
- [ ] lint
- [ ] type check
- [ ] test
- [ ] build
- [ ] deploy
- [ ] rollback
- [ ] SonarQube
- [ ] Static Analysis

---

## Docker

- [ ] Container
- [ ] Docker
- [ ] Docker Image
- [ ] Dockerfile
- [ ] Volume
- [ ] Network
- [ ] Docker Compose
- [ ] Container Registry

---

# 優先度 C

フロントからバックエンドまで追えるようにする。

## Java 基礎

- [ ] 変数
- [ ] primitive 型
- [ ] reference 型
- [ ] String
- [ ] Wrapper
- [ ] null
- [ ] if
- [ ] switch
- [ ] for
- [ ] 拡張 for
- [ ] while
- [ ] method
- [ ] return
- [ ] class
- [ ] field
- [ ] constructor
- [ ] this
- [ ] new
- [ ] public
- [ ] private
- [ ] protected
- [ ] static
- [ ] final
- [ ] package
- [ ] import

---

## Java OOP

- [ ] Object
- [ ] Class
- [ ] Instance
- [ ] Encapsulation
- [ ] Inheritance
- [ ] Polymorphism
- [ ] Interface
- [ ] abstract class
- [ ] extends
- [ ] implements
- [ ] override
- [ ] overload
- [ ] Composition

---

## Java 実務

- [ ] List
- [ ] ArrayList
- [ ] Set
- [ ] HashSet
- [ ] Map
- [ ] HashMap
- [ ] Generics
- [ ] Optional
- [ ] Exception
- [ ] RuntimeException
- [ ] try / catch
- [ ] throw
- [ ] throws
- [ ] enum
- [ ] record
- [ ] Lambda
- [ ] Stream API
- [ ] filter
- [ ] map
- [ ] flatMap
- [ ] toList
- [ ] forEach
- [ ] findFirst
- [ ] anyMatch
- [ ] Method Reference
- [ ] LocalDate
- [ ] LocalDateTime
- [ ] BigDecimal
- [ ] Objects
- [ ] equals
- [ ] hashCode
- [ ] immutable
- [ ] pass by value
- [ ] Stack / Heap
- [ ] Garbage Collection

---

## Spring Boot

- [ ] Spring Boot
- [ ] IoC
- [ ] DI
- [ ] Bean
- [ ] Component Scan
- [ ] @Component
- [ ] @Service
- [ ] @Repository
- [ ] @RestController
- [ ] @RequestMapping
- [ ] @GetMapping
- [ ] @PostMapping
- [ ] @PutMapping
- [ ] @DeleteMapping
- [ ] @RequestBody
- [ ] @PathVariable
- [ ] @RequestParam
- [ ] Controller
- [ ] Service
- [ ] Repository
- [ ] DTO
- [ ] Entity
- [ ] Mapper
- [ ] Validation
- [ ] ExceptionHandler
- [ ] Transaction
- [ ] @Transactional
- [ ] MyBatis

---

## SQL

- [ ] SELECT
- [ ] INSERT
- [ ] UPDATE
- [ ] DELETE
- [ ] WHERE
- [ ] ORDER BY
- [ ] GROUP BY
- [ ] HAVING
- [ ] aggregate function
- [ ] DISTINCT
- [ ] CASE
- [ ] NULL
- [ ] COALESCE
- [ ] INNER JOIN
- [ ] LEFT JOIN
- [ ] RIGHT JOIN
- [ ] 1 対 1
- [ ] 1 対多
- [ ] 多対多
- [ ] JOIN による重複
- [ ] EXISTS
- [ ] NOT EXISTS
- [ ] IN
- [ ] Subquery
- [ ] CTE
- [ ] UNION
- [ ] UNION ALL

---

## Database

- [ ] Primary Key
- [ ] Foreign Key
- [ ] Unique
- [ ] Constraint
- [ ] Index
- [ ] Composite Index
- [ ] Execution Plan
- [ ] Full Scan
- [ ] Transaction
- [ ] ACID
- [ ] Commit
- [ ] Rollback
- [ ] Isolation Level
- [ ] Lock
- [ ] Optimistic Lock
- [ ] Pessimistic Lock
- [ ] Deadlock
- [ ] Sequence
- [ ] ER 図
- [ ] Cardinality
- [ ] 正規化

---

# 優先度 D

余裕があれば半年以内に触れる。

## Architecture

- [ ] MVC
- [ ] Layered Architecture
- [ ] Clean Architecture
- [ ] Hexagonal Architecture
- [ ] Dependency Inversion
- [ ] Separation of Concerns
- [ ] High Cohesion
- [ ] Low Coupling
- [ ] SOLID
- [ ] DRY
- [ ] KISS
- [ ] YAGNI
- [ ] Repository Pattern
- [ ] Adapter Pattern
- [ ] Strategy Pattern
- [ ] Factory Pattern

---

## AWS / Infrastructure

- [ ] IAM
- [ ] VPC
- [ ] EC2
- [ ] S3
- [ ] CloudFront
- [ ] Route 53
- [ ] ALB
- [ ] ECS
- [ ] EKS
- [ ] Lambda
- [ ] API Gateway
- [ ] CloudWatch
- [ ] Kubernetes 概要
- [ ] Pod
- [ ] Deployment
- [ ] Service

---

# 半年の進め方

## 1 ヶ月目

- JavaScript
- TypeScript
- React 基礎
- HTTP 基礎

## 2 ヶ月目

- React 応用
- Next.js
- API
- React Hook Form

## 3 ヶ月目

- React 設計
- TanStack Query
- Security
- Testing

## 4 ヶ月目

- Git
- CI/CD
- Performance
- Accessibility
- Debugging

## 5 ヶ月目

- Java
- Spring Boot
- SQL
- Database

## 6 ヶ月目

- Architecture
- Docker
- AWS
- 総復習
- ポートフォリオ・実務コードへの適用

---

# 学習完了の基準

各項目について最低限、

- [ ] 何なのか説明できる
- [ ] なぜ使うのか説明できる
- [ ] 簡単なコードを書ける
- [ ] 実務でどこに使われるか説明できる
- [ ] よくある問題・注意点を 1 つ以上説明できる

状態にする。

暗記ではなく、

**「なぜ → どう使う → 何が問題になる → どう直す」**

まで理解する。
