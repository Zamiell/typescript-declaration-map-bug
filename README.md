# typescript-declaration-map-bug

Steps to reproduce:

- `git clone git@github.com:Zamiell/typescript-declaration-map-bug.git`
- `cd typescript-declaration-map-bug`
- `npm ci`
- `cat tsconfig.json`

Observe that we have a standard tsconfig. Since this is intended to be a TypeScript library published to NPM, we turn on "declaration", "declarationMap" and "sourceMap". We also turn on "sourceMapRoot", because when we actually upload it to NPM, the directories will have shifted around at that point to be a little different than they are now.

Note that the "sourceMapRoot" setting applies to **both** the `.d.ts.map` files and the `.js.map` files. (There is no corresponding "declarationMapRoot" tsconfig key.)

- `cat src/foo.index.ts`

Observe that this is just exporting a basic function.

- `cat src/someDirectory/bar.index.ts`

Observe that this is just exporting a basic function.

- `npx tsc`
- `cat dist/index.d.ts.map`

Observe that when we combine the "sourceRoot" and the "sources", the combined path is:

```sh
/project/dist/../src/index.ts
```

Which resolves to:

```sh
/project/src/index.ts
```

Which is correct. 👍

We can confirm this by consuming this library on NPM and using the "Go to Definition" feature of VSCode, which will work properly in warping to the actual ".ts" file. 👍

- `cat dist/someDirectory/bar.d.ts.map`

Observe that when we combine the "sourceRoot" and the "sources", the combined path is:

```sh
/project/dist/someDirectory/../src/index.ts
```

Which resolves to:

```sh
/project/dist/src/index.ts
```

Which is not correct. 👎

We can confirm this by consuming this library on NPM and using the "Go to Definition" feature of VSCode, which will not work properly in that it will warp to the ".d.ts" file instead of the ".ts" file. 👎
