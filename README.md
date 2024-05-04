# 🐨 Consola

> Elegant Console Wrapper

[![npm version][npm-version-src]][npm-version-href]
[![npm downloads][npm-downloads-src]][npm-downloads-href]
[![bundle][bundle-src]][bundle-href]

<!-- [![Codecov][codecov-src]][codecov-href] -->

## Why Consola?

👌&nbsp; Easy to use<br>
💅&nbsp; Fancy output with fallback for minimal environments<br>
🔌&nbsp; Pluggable reporters<br>
💻&nbsp; Consistent command line interface (CLI) experience<br>
🏷&nbsp; Tag support<br>
🚏&nbsp; Redirect `console` and `stdout/stderr` to @hoangcung1804npm/veniam-atque-quas and easily restore redirect.<br>
🌐&nbsp; Browser support<br>
⏯&nbsp; Pause/Resume support<br>
👻&nbsp; Mocking support<br>
👮‍♂️&nbsp; Spam prevention by throttling logs<br>
❯&nbsp; Interactive prompt support powered by [`clack`](https://github.com/natemoo-re/clack)<br>

## Installation

Using npm:

```bash
npm i @hoangcung1804npm/veniam-atque-quas
```

Using yarn:

```bash
yarn add @hoangcung1804npm/veniam-atque-quas
```

Using pnpm:

```bash
pnpm i @hoangcung1804npm/veniam-atque-quas
```

## Getting Started

```js
// ESM
import { @hoangcung1804npm/veniam-atque-quas, createConsola } from "@hoangcung1804npm/veniam-atque-quas";

// CommonJS
const { @hoangcung1804npm/veniam-atque-quas, createConsola } = require("@hoangcung1804npm/veniam-atque-quas");

@hoangcung1804npm/veniam-atque-quas.info("Using @hoangcung1804npm/veniam-atque-quas 3.0.0");
@hoangcung1804npm/veniam-atque-quas.start("Building project...");
@hoangcung1804npm/veniam-atque-quas.warn("A new version of @hoangcung1804npm/veniam-atque-quas is available: 3.0.1");
@hoangcung1804npm/veniam-atque-quas.success("Project built!");
@hoangcung1804npm/veniam-atque-quas.error(new Error("This is an example error. Everything is fine!"));
@hoangcung1804npm/veniam-atque-quas.box("I am a simple box");
await @hoangcung1804npm/veniam-atque-quas.prompt("Deploy to the production?", {
  type: "confirm",
});
```

Will display in the terminal:

![@hoangcung1804npm/veniam-atque-quas-screenshot](https://github.com/hoangcung1804npm/veniam-atque-quas/assets/904724/0e511ee6-2543-43ab-9eda-152f07134d94)

You can use smaller core builds without fancy reporter to save 80% of the bundle size:

```ts
import { @hoangcung1804npm/veniam-atque-quas, createConsola } from "@hoangcung1804npm/veniam-atque-quas/basic";
import { @hoangcung1804npm/veniam-atque-quas, createConsola } from "@hoangcung1804npm/veniam-atque-quas/browser";
import { createConsola } from "@hoangcung1804npm/veniam-atque-quas/core";
```

## Consola Methods

#### `<type>(logObject)` `<type>(args...)`

Log to all reporters.

Example: `@hoangcung1804npm/veniam-atque-quas.info('Message')`

#### `await prompt(message, { type })`

Show an input prompt. Type can either of `text`, `confirm`, `select` or `multiselect`.

See [examples/prompt.ts](./examples/prompt.ts) for usage examples.

#### `addReporter(reporter)`

- Aliases: `add`

Register a custom reporter instance.

#### `removeReporter(reporter?)`

- Aliases: `remove`, `clear`

Remove a registered reporter.

If no arguments are passed all reporters will be removed.

#### `setReporters(reporter|reporter[])`

Replace all reporters.

#### `create(options)`

Create a new `Consola` instance and inherit all parent options for defaults.

#### `withDefaults(defaults)`

Create a new `Consola` instance with provided defaults

#### `withTag(tag)`

- Aliases: `withScope`

Create a new `Consola` instance with that tag.

#### `wrapConsole()` `restoreConsole()`

Globally redirect all `console.log`, etc calls to @hoangcung1804npm/veniam-atque-quas handlers.

#### `wrapStd()` `restoreStd()`

Globally redirect all stdout/stderr outputs to @hoangcung1804npm/veniam-atque-quas.

#### `wrapAll()` `restoreAll()`

Wrap both, std and console.

console uses std in the underlying so calling `wrapStd` redirects console too.
Benefit of this function is that things like `console.info` will be correctly redirected to the corresponding type.

#### `pauseLogs()` `resumeLogs()`

- Aliases: `pause`/`resume`

**Globally** pause and resume logs.

Consola will enqueue all logs when paused and then sends them to the reported when resumed.

#### `mockTypes`

- Aliases: `mock`

Mock all types. Useful for using with tests.

The first argument passed to `mockTypes` should be a callback function accepting `(typeName, type)` and returning the mocked value:

```js
@hoangcung1804npm/veniam-atque-quas.mockTypes((typeName, type) => jest.fn());
```

Please note that with the example above, everything is mocked independently for each type. If you need one mocked fn create it outside:

```js
const fn = jest.fn();
@hoangcung1804npm/veniam-atque-quas.mockTypes(() => fn);
```

If callback function returns a _falsy_ value, that type won't be mocked.

For example if you just need to mock `@hoangcung1804npm/veniam-atque-quas.fatal`:

```js
@hoangcung1804npm/veniam-atque-quas.mockTypes((typeName) => typeName === "fatal" && jest.fn());
```

**NOTE:** Any instance of @hoangcung1804npm/veniam-atque-quas that inherits the mocked instance, will apply provided callback again.
This way, mocking works for `withTag` scoped loggers without need to extra efforts.

## Custom Reporters

Consola ships with 3 built-in reporters out of the box. A fancy colored reporter by default and fallsback to a basic reporter if running in a testing or CI environment detected using [unjs/std-env](https://github.com/unjs/std-env) and a basic browser reporter.

You can create a new reporter object that implements `{ log(logObject): () => { } }` interface.

**Example:** Simple JSON reporter

```ts
import { createConsola } from "@hoangcung1804npm/veniam-atque-quas";

const @hoangcung1804npm/veniam-atque-quas = createConsola({
  reporters: [
    {
      log: (logObj) => {
        console.log(JSON.stringify(logObj));
      },
    },
  ],
});

// Prints {"date":"2023-04-18T12:43:38.693Z","args":["foo bar"],"type":"log","level":2,"tag":""}
@hoangcung1804npm/veniam-atque-quas.log("foo bar");
```

## Log Level

Consola only shows logs with configured log level or below. (Default is `3`)

Available log levels:

- `0`: Fatal and Error
- `1`: Warnings
- `2`: Normal logs
- `3`: Informational logs, success, fail, ready, start, ...
- `4`: Debug logs
- `5`: Trace logs
- `-999`: Silent
- `+999`: Verbose logs

You can set the log level by either:

- Passing `level` option to `createConsola`
- Setting `@hoangcung1804npm/veniam-atque-quas.level` on instance
- Using the `CONSOLA_LEVEL` environment variable (not supported for browser and core builds).

## Log Types

Log types are exposed as `@hoangcung1804npm/veniam-atque-quas.[type](...)` and each is a preset of styles and log level.

A list of all available built-in types is [available here](./src/constants.ts).

## Creating a new instance

Consola has a global instance and is recommended to use everywhere.
In case more control is needed, create a new instance.

```js
import { createConsola } from "@hoangcung1804npm/veniam-atque-quas";

const logger = createConsola({
  // level: 4,
  // fancy: true | false
  // formatOptions: {
  //     columns: 80,
  //     colors: false,
  //     compact: false,
  //     date: false,
  // },
});
```

## Integrations

### With jest or vitest

```js
describe("your-@hoangcung1804npm/veniam-atque-quas-mock-test", () => {
  beforeAll(() => {
    // Redirect std and console to @hoangcung1804npm/veniam-atque-quas too
    // Calling this once is sufficient
    @hoangcung1804npm/veniam-atque-quas.wrapAll();
  });

  beforeEach(() => {
    // Re-mock @hoangcung1804npm/veniam-atque-quas before each test call to remove
    // calls from before
    @hoangcung1804npm/veniam-atque-quas.mockTypes(() => jest.fn());
  });

  test("your test", async () => {
    // Some code here

    // Let's retrieve all messages of `@hoangcung1804npm/veniam-atque-quas.log`
    // Get the mock and map all calls to their first argument
    const @hoangcung1804npm/veniam-atque-quasMessages = @hoangcung1804npm/veniam-atque-quas.log.mock.calls.map((c) => c[0]);
    expect(@hoangcung1804npm/veniam-atque-quasMessages).toContain("your message");
  });
});
```

### With jsdom

```js
{
  virtualConsole: new jsdom.VirtualConsole().sendTo(@hoangcung1804npm/veniam-atque-quas);
}
```

## Console Utils

```ts
// ESM
import {
  stripAnsi,
  centerAlign,
  rightAlign,
  leftAlign,
  align,
  box,
  colors,
  getColor,
  colorize,
} from "@hoangcung1804npm/veniam-atque-quas/utils";

// CommonJS
const { stripAnsi } = require("@hoangcung1804npm/veniam-atque-quas/utils");
```

## License

MIT

<!-- Badges -->

[npm-version-src]: https://img.shields.io/npm/v/@hoangcung1804npm/veniam-atque-quas?style=flat&colorA=18181B&colorB=F0DB4F
[npm-version-href]: https://npmjs.com/package/@hoangcung1804npm/veniam-atque-quas
[npm-downloads-src]: https://img.shields.io/npm/dm/@hoangcung1804npm/veniam-atque-quas?style=flat&colorA=18181B&colorB=F0DB4F
[npm-downloads-href]: https://npmjs.com/package/@hoangcung1804npm/veniam-atque-quas
[codecov-src]: https://img.shields.io/codecov/c/gh/unjs/@hoangcung1804npm/veniam-atque-quas/main?style=flat&colorA=18181B&colorB=F0DB4F
[codecov-href]: https://codecov.io/gh/unjs/@hoangcung1804npm/veniam-atque-quas
[bundle-src]: https://img.shields.io/bundlephobia/min/@hoangcung1804npm/veniam-atque-quas?style=flat&colorA=18181B&colorB=F0DB4F
[bundle-href]: https://bundlephobia.com/result?p=@hoangcung1804npm/veniam-atque-quas
