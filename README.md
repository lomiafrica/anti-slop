# @lomi./anti-slop

Oxlint rules that reject low-evidence TypeScript and JavaScript patterns.

**Repository:** [github.com/lomiafrica/anti-slop](https://github.com/lomiafrica/anti-slop) — standalone lomi. tool. In the lomi. monorepo it is checked out as a submodule at `apps/tools/anti-slop`. Upstream: [dmmulroy/anti-slop](https://github.com/dmmulroy/anti-slop).

Oxlint loads this plugin as TypeScript source. Point `jsPlugins` at `index.ts` in this repo. Do not treat it as a compiled npm package.

## Monorepo checkout

```bash
git submodule update --init apps/tools/anti-slop
```

Local umbrella `oxlint.config.ts` (gitignored in the monorepo) should keep:

```ts
jsPlugins: [
  { name: "anti-slop", specifier: "./apps/tools/anti-slop/index.ts" },
],
```

Then run `pnpm anti-slop` from the umbrella after a local root `pnpm install`. Node 22.18+ is required because the plugin is TypeScript.

## Standalone use

```bash
git clone https://github.com/lomiafrica/anti-slop.git
cd anti-slop
pnpm install
pnpm typecheck
```

In the consuming repo, install matching `oxlint` and `@oxlint/plugins`, then:

```ts
import { defineConfig } from "oxlint";

export default defineConfig({
  ignorePatterns: ["anti-slop/**"],
  jsPlugins: [
    { name: "anti-slop", specifier: "./anti-slop/index.ts" },
  ],
  rules: {
    "anti-slop/no-chained-type-assertions": "error",
    "anti-slop/no-conditional-empty-object-spread": "error",
    "anti-slop/no-known-value-widening": "error",
    "anti-slop/no-module-mocking": "error",
    "anti-slop/no-object-parameters": "error",
    "anti-slop/no-reflect-apply": "error",
    "anti-slop/no-reflect-get": "error",
    "anti-slop/no-runtime-typeof": ["error", { allowInTypeGuards: true }],
    "anti-slop/no-shape-in-symbol-names": "error",
    "anti-slop/no-unknown-parameters": "error",
    "anti-slop/no-unknown-returns": "error",
    "anti-slop/no-unknown-type-aliases": "error",
    "anti-slop/no-unsafe-dictionary-type": "error",
    "anti-slop/no-widen-then-assert": "error",
    "anti-slop/require-safety-comment-for-type-assertion": "error",
  },
});
```

## Rules

- `no-unknown-parameters` — do not type params as `unknown` except `cause`
- `no-unknown-returns` — return a named type, not `unknown`
- `require-safety-comment-for-type-assertion` — avoid `as T` unless a `// SAFETY:` comment states the invariant
- `no-chained-type-assertions` — never `as unknown as T`
- `no-runtime-typeof` — `typeof` only inside a type predicate when `allowInTypeGuards` is on
- `no-known-value-widening` — do not annotate a known value as a loose dictionary
- `no-unsafe-dictionary-type` — no `Record<string, unknown>`
- `no-conditional-empty-object-spread` — no `{ ...(cond ? { k: v } : {}) }`
- `no-object-parameters` — no `param: object`
- `no-module-mocking` — no `jest.mock` / `vi.mock`
- `no-unknown-type-aliases` — no `type X = unknown`
- `no-widen-then-assert` — do not widen and then assert back
- `no-reflect-apply` / `no-reflect-get` — no `Reflect.apply` / `Reflect.get`
- `no-shape-in-symbol-names` — no forbidden terms in symbol names

## License

MIT. Rule source is from [dmmulroy/anti-slop](https://github.com/dmmulroy/anti-slop).
