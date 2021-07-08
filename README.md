# webpack-ts-storybook

This is a repository that reproduces the problem webpack (and Vite!) have
resolving imports with extensions in TypeScript
(i.e. using `import * from './text.js'` in TypeScript)

## Installing

To install, run:

```shell
npm install
```

To see the problem in action, run webpack using:

```shell
npx webpack
```

This will generate an error.

To "resolve" this problem, edit `index.ts` and remove the extension from the import of `test.js`,
and run `npm run build` again.

To see the bundle run after a succesful pack, run

```shell
node dist/bundle.js
```

Note: this "resolution" (of removing the extension) should not be perceived as a solution,
because in an ESM TypeScript project, all extensions in a relative import
must be specified and must be `.js` (<https://www.typescriptlang.org/docs/handbook/2/modules.html#es-module-syntax>).

## Description of the problem

The following two files, which work when transpiling using TSC, WebPack errors when bundling:

```js
// src/index.ts
import {text} from './text.js'

const t: string = text()

console.log(t)
```

```js
// src/text.ts
export function text() {
  return 'hello! ts'
}
```

The above two files work when I do `npx tsc`, and then run `node lib/index.js`. This
weird behavior of TypeScript, whereby you need to specify a `.js` extension even if the source
is `.ts`, is documented here: <https://www.typescriptlang.org/docs/handbook/2/modules.html#es-module-syntax>.

Yet, when running webpack (using `npm run build`), we get the following error:

```log
Error: Can't resolve './text.js' in '/.../webpack-ts-storybook/src'
resolve './text.js'
```

It can't resolve the `test.js` to `test.ts` (using regular `ts-loader` options).
But this is strange, because `ts-loader` uses TypeScript, so it _should_ have resolved it.

