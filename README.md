# typescript-eslint rule-tester typed testing reproduction


## Issue

This repository reproduces an issue I'm having with RuleTester not being able to parse fixture files unless they are nested under _two_ fixture folders, like `fixture/fixture/files.ts`.

I am trying to follow the docs here: https://typescript-eslint.io/packages/rule-tester/#type-aware-testing

The rule tester [is declared](./src/test/rules/rule-tester.ts) like in the docs:
```ts
export const ruleTester = new RuleTester({
  parser: '@typescript-eslint/parser',
  parserOptions: {
    tsconfigRootDir: './fixture',
    project: './tsconfig.json',
  },
});
```

The [fixture tsconfig](./fixture/tsconfig.json) is also setup like in the docs:

```json
{
  "compilerOptions": {
    "strict": true
  },
  "include": ["file.ts", "react.tsx"]
}
```

And a file layout of:
```
fixture
├── file.ts
├── react.tsx
└── tsconfig.json
```

The error when running the tests is:

```
  AssertionError [ERR_ASSERTION]: A fatal parsing error occurred: Parsing error: ESLint was configured to run on `<tsconfigRootDir>/fixture/file.ts` using `parserOptions.project`: <tsconfigRootDir>/tsconfig.json
  However, that TSConfig does not include this file. Either:
  - Change ESLint's list of included files to not include this file
  - Change that TSConfig to include this file
  - Create a new TSConfig that includes this file and include it in your parserOptions.project
  See the typescript-eslint docs for more info: https://typescript-eslint.io/linting/troubleshooting#i-get-errors-telling-me-eslint-was-configured-to-run--however-that-tsconfig-does-not--none-of-those-tsconfigs-include-this-file
      at RuleTester.runRuleForItem (/Users/justinfagnani/Projects/Lit/tmp/ts-eslint-starter/node_modules/@typescript-eslint/rule-tester/src/RuleTester.ts:590:11)
      at RuleTester._RuleTester_testInvalidTemplate (/Users/justinfagnani/Projects/Lit/tmp/ts-eslint-starter/node_modules/@typescript-eslint/rule-tester/src/RuleTester.ts:706:25)
      at TestContext.<anonymous> (/Users/justinfagnani/Projects/Lit/tmp/ts-eslint-starter/node_modules/@typescript-eslint/rule-tester/src/RuleTester.ts:426:40)
      at Test.runInAsyncScope (node:async_hooks:206:9)
      at Test.run (node:internal/test_runner/test:631:25)
      at Test.start (node:internal/test_runner/test:542:17)
      at node:internal/test_runner/test:947:71
      at node:internal/per_context/primordials:488:82
      at new Promise (<anonymous>)
      at new SafePromise (node:internal/per_context/primordials:456:29) {
```

## Workaround

The parsing error can be solved by layout the filesystem like:

```
fixture
├── fixture
│   ├── file.ts
│   └── react.tsx
└── tsconfig.json
```

(and adjusting the `fixture/tsconfig.json`` include paths)

_However_, then the empty fixture files are not populated with the test fixtures by the RuleTester. I can't tell if this is a related problem or not.
