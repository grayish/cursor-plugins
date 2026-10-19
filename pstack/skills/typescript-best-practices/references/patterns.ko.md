# TypeScript 패턴

> 이 문서는 [patterns.md](./patterns.md)를 한국어로 번역한 문서입니다. 원본과 내용이 다르면 원본을 기준으로 합니다.

`SKILL.md`에 있는 각 규칙의 코드 예시입니다. 바탕이 되는 원칙은 특정 언어에 묶이지 않습니다. **type-system-discipline** 및 **boundary-discipline** 원칙 스킬을 참고합니다.

## 브랜드 타입(Branded types)

원시 타입에 브랜드를 붙여 서로 섞이지 않게 합니다. 경계에서 한 번만 검증합니다. 그 이후의 코드는 타입을 신뢰합니다.

```ts
type AgentId = string & { readonly __brand: "AgentId" };

function isAgentId(input: string): input is AgentId {
  return isUUID(input);
}

function parseAgentId(input: string): AgentId {
  if (!isAgentId(input)) throw new Error(`Invalid agent id: ${input}`);
  return input;
}

function focusAgent(id: AgentId): void {
  /* input is trusted */
}
```

`readonly __brand: 'X'` 형태를 따릅니다. 새로운 관례를 만들지 않습니다.

## 판별 유니언(Discriminated unions)

리터럴 판별자로 변형을 모델링합니다. 모든 변형이 같은 필드 이름을 공유하고 변형마다 값이 고유하므로, 불가능한 조합을 표현할 수 없습니다.

```ts
// Don't. Boolean + optionals lets contradictory states exist.
type DiffState = { loading: boolean; diff?: GitDiff; error?: string };

// Do. Only valid states exist.
type DiffState =
  | { kind: "loading" }
  | { kind: "ready"; diff: GitDiff }
  | { kind: "error"; error: string };
```

판별자 이름(`kind`, `type`, `tag`)을 하나 정하고 일관되게 사용합니다.

## 구성적 모델링(Constructive modeling)

느슨한 타입을 런타임 검사로 제한하는 대신, 모두 유효한 부분들로 타입을 구성합니다.

가변 길이 튜플로 비어 있지 않은 배열을 표현합니다.

```ts
type NonEmpty<T> = [T, ...T[]];

// Don't: T[] plus a length check every caller must repeat
function pickWinner(entries: string[]): string {
  if (entries.length === 0) throw new Error("no entries");
  return entries[Math.floor(Math.random() * entries.length)];
}

// Do: an empty value of the type can't exist
function pickWinner(entries: NonEmpty<string>): string {
  return entries[Math.floor(Math.random() * entries.length)];
}
```

일반 `T[]`가 들어오는 곳에서는 가드로 한 번만 좁힙니다. 그러면 그 사실이 타입에 담겨 전달됩니다.

```ts
const isNonEmpty = <T>(arr: T[]): arr is NonEmpty<T> => arr.length > 0;
```

짝수 길이는 쌍의 배열로 표현합니다.

```ts
type Pairs<T> = [T, T][];
```

시간 범위는 시작 시각과 지속 시간으로 표현합니다.

```ts
// Don't: a comment holds the invariant
type TimeRange = { start: Date; end: Date }; // start <= end

// Do: a negative range can't be written; derive end when needed
type TimeRange = { start: Date; durationMs: number };
```

`durationMs`는 일반 숫자로 둡니다. 지속 시간이 와야 할 자리에 가공되지 않은 숫자가 전달될 수 있는 경우에만 (브랜드 타입 규칙에 따라) 브랜드를 붙이고, 습관적으로 붙이지 않습니다. 잘못된 상태를 만들 수 없게 하는 표현을 고른 다음, 필요한 읽기 방식을 그 위에 제공합니다(`pairs.flat()`, `rangeEnd()` 헬퍼 등).

## 가장 단순한 전체 타입(Simplest total type)

모든 것을 강화하지 않습니다. 모든 연산이 전체 함수(total)라면 `T[]`를 그대로 둡니다.

```ts
const sum = (xs: number[]) => xs.reduce((a, b) => a + b, 0); // [] is 0, fine
```

느슨한 타입 때문에 사용하는 곳에서 거짓 주장을 해야 할 때 강화합니다. 그 신호는 `!`, `arr[0] as T`, 그리고 "절대 일어나지 않아야 하는" throw입니다.

```ts
// Don't: partiality smuggled past the compiler
function newestSession(sessions: Session[]): Session {
  return sessions.at(0)!;
}

// Do: strengthen the input; the assertion disappears
function newestSession(sessions: NonEmpty<Session>): Session {
  return sessions[0];
}
```

결과 타입을 `Session | undefined`로 약화하는 것이 또 다른 전체 함수 시그니처입니다.

## `any`보다 `unknown`

외부 데이터는 항상 `unknown`입니다. 사용하기 전에 좁힙니다.

```ts
// Don't
function handle(input: any) {
  return input.foo.bar;
}

// Do
function handle(input: unknown) {
  if (typeof input === "object" && input !== null && "foo" in input) {
    // narrowed; compiler verifies access
  }
}
```

외부 소스에는 RPC 페이로드, `JSON.parse`, `postMessage`, IPC, 파일 내용, 환경 변수, 데이터베이스 결과가 포함됩니다.

## 직접 작성한 가드보다 스키마 먼저

외부 데이터에 대해 속성을 하나씩 확인하는 타입 가드를 작성하기 전에, 저장소의 런타임 스키마 라이브러리와 기존 스키마를 찾아봅니다. 하나의 스키마가 검증을 책임지게 하고 TypeScript 타입은 그 스키마에서 파생합니다. 서로 어긋날 수 있는 스키마, 중복 인터페이스, 가드를 따로 유지하지 않습니다.

```ts
import { z } from "zod";

const UserSchema = z.object({
  id: z.string().uuid(),
  role: z.enum(["admin", "member"]),
});

type User = z.infer<typeof UserSchema>;

function parseUser(input: unknown): User {
  return UserSchema.parse(input);
}
```

실패가 예상되는 분기라면 `safeParse`를 사용합니다. 저장소가 다른 스키마 라이브러리를 사용한다면 그에 해당하는 추론 헬퍼를 사용합니다. 가드 하나를 위해 새로운 스키마 의존성을 추가하지 않습니다. 이 규칙은 코드베이스가 이미 신뢰하는 스키마 시스템을 우선합니다.

## `as` 캐스트 금지

모든 `as`는 잠재적인 런타임 크래시입니다. 타입 시스템이 주장을 검증한 뒤에만 캐스트합니다.

```ts
import { z } from "zod";

// Don't
const user = data as User;

// Don't
function isUser(data: unknown): data is User {
  return typeof data === "object" && data !== null && "id" in data;
}

// Do
const userSchema = z.object({ id: z.string(), name: z.string() });
type User = z.infer<typeof userSchema>;

function parseUser(data: unknown): User {
  return userSchema.parse(data);
}
```

타입을 먼저 정의했다면, 검증기에 그것이 증명하는 타입을 표기합니다. 그러면 컴파일러는 타입보다 적게 증명하는 검증기를 거부합니다. 아래 객체에서 `name`을 제거하면 할당이 컴파일되지 않습니다.

```ts
type User = { id: string; name: string };

const userSchema: z.ZodType<User> = z.object({ id: z.string(), name: z.string() });
```

기존 코드에서 `as`를 제거하도록 리팩터링할 때는 TypeScript가 추론하지 못하는 이유를 파악합니다.

- 판별자가 없는 경우: 판별자를 추가하고 판별 유니언으로 전환합니다.
- 소스 타입이 지나치게 넓은 경우(예: `Record<string, unknown>`): 타입을 좁힙니다.
- 타입이 없는 경계인 경우: 그 형태를 책임지는 스키마로 파싱합니다. 스키마가 없는 곳에서만 새로 추가합니다.
- 정말로 표현할 수 없는 경우: 브랜드 타입이나 `satisfies`를 사용합니다.

## 좁히기(narrowing) 우선순위

가장 좋은 방법부터 최후의 수단까지 순서대로 나열합니다.

1. **판별 유니언 switch / if.** 컴파일러가 자동으로 좁힙니다.
2. **`in` 연산자.** `"key" in obj`는 해당 키를 가진 변형으로 좁힙니다.
3. **`typeof` / `instanceof`.** 원시 타입과 클래스 인스턴스에 사용합니다.
4. **사용자 정의 타입 가드.** 위 방법으로 충분하지 않을 때 사용합니다.
5. **`as` 캐스트.** 검증한 뒤에만 사용합니다.

```ts
function area(s: Shape): number {
  if ("radius" in s) return Math.PI * s.radius ** 2; // narrowed to circle
  return s.width * s.height; // narrowed to rect
}
```

## 타입 가드

가드는 주장하는 내용을 실제로 검증해야 합니다. 거짓말하는 가드는 `as`보다 나쁩니다.

```ts
function isCircle(s: Shape): s is Shape & { kind: "circle" } {
  return s.kind === "circle";
}
```

가능하면 판별자 좁히기를 우선합니다.

## 완전성 검사(Exhaustiveness)

default 분기에서 판별 대상을 `never` 타입 지역 변수에 할당합니다.

```ts
// Value-returning switch
function area(s: Shape): number {
  switch (s.kind) {
    case "circle":
      return Math.PI * s.radius ** 2;
    case "rect":
      return s.width * s.height;
    default: {
      const _exhaustive: never = s;
      return _exhaustive;
    }
  }
}

// Void switch
function handle(s: Shape): void {
  switch (s.kind) {
    case "circle":
      drawCircle(s);
      break;
    case "rect":
      drawRect(s);
      break;
    default: {
      const _exhaustive: never = s;
      void _exhaustive;
    }
  }
}
```

값을 반환하는 switch에서는 return 방식을, 문장형 switch에서는 void 방식을 사용합니다.

## `as`보다 `satisfies`

`satisfies`는 리터럴 타입을 넓히지 않고 검증합니다.

```ts
// Don't. Widens, loses literal types.
const config = { theme: "dark", cols: 3 } as Config;

// Do. Validates AND preserves literal types.
const config = { theme: "dark", cols: 3 } satisfies Config;
// config.theme is "dark" (literal), not string
```

## 경계 검증

데이터가 들어오는 지점에서 한 번만 검증합니다. 내부에서는 타입을 신뢰합니다. **boundary-discipline** 원칙 스킬을 참고합니다.

- **와이어 포맷**(proto, JSON-RPC): 순방향 호환 변경이 기존 클라이언트를 깨뜨리지 않도록 `ignoreUnknownFields`를 사용해 파싱합니다.
- **영속 저장된 JSON:** 버전이 붙은 데이터로 저장하고, 파싱을 try/catch로 감쌉니다.
- 호출 체인 깊은 곳에서 **다시 검증하지 않습니다**.

## 스키마에서 파생한 타입

`.proto`, OpenAPI 명세, GraphQL 스키마, 데이터베이스 마이그레이션이 이미 형태를 정의하고 있다면, 그것을 중복 정의하지 말고 생성된 타입에서 파생합니다.

```ts
// Don't. Duplicate shape, drifts when the schema changes.
type CheckSummary = {
  totalCount: number;
  checks: { name: string; status: string }[];
};
function renderChecks(s: CheckSummary) {
  /* ... */
}

// Do. Derive from the generated schema type.
import type { ChecksMessage } from "<generated module>";
function renderChecks(s: Pick<ChecksMessage, "totalCount" | "checks">) {
  /* ... */
}
```

새 인터페이스를 작성하기 전에 `Pick`, `Omit`, `Parameters`, `ReturnType`, `Awaited`, `typeof`를 먼저 활용합니다.

## 객체 인자

```ts
// Don't. Swap two args, still compiles.
openFile(uri, {
  startLineNumber: 10,
  startColumn: 1,
  endLineNumber: 10,
  endColumn: 1,
});

// Do. Order-independent, self-documenting.
openFile({
  uri,
  selection: {
    startLineNumber: 10,
    startColumn: 1,
    endLineNumber: 10,
    endColumn: 1,
  },
});
```

핫 패스에서는 적용하지 않습니다. 프레임마다 실행되는 렌더링, 토크나이저, 파서처럼 할당 비용이 중요한 촘촘한 루프 안의 코드가 여기에 해당합니다.
