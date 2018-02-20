# jest-hoisting-bug-demo

React Native Lerna Jest Hoist Bug

Found: React Native cannot resolve dependencies when attempting to run Jest tests in a hoisted monorepo

| Dependency | Version |
| -----------| --------|
| Node | v8.9.1 |
| NPM | 5.5.1 |
| Lerna | 2.4.0 |
| react-native-cli | 2.0.1 |
| react-native | 0.53.3 |


## Create repo

```
mkdir test
cd test
lerna init
cd packages
react-native init Foo
react-native init Bar
```



## Verify that tests run unhoisted
at this point the init Foo/Bar has done an install without lerna and deps are installed in each package

```
cd Foo
ls -l node_modules
(observe that Foo has local dependencies all installed with no hoisting)
npm run test
(Foo tests pass)
cd ../Bar
ls -l node_modules
(observe that Bar has local dependencies all installed with no hoisting)
npm run test
(Bar tests pass)
```


## Verify that things are being hoisted

```
(from package root)
rm -rf packages/Foo/node_modules
rm -rf packages/Bar/node_modules
(add   "hoist": true, to your lerna.json)
lerna bootstrap
ls -l node_modules
ls -l packages/Foo/node_modules
ls -l packages/Bar/node_modules
(observe that everything has been hoisted as expected)
```


## Observe that tests are now broken

```
cd packages/Foo
npm run test
(observe the following error)
```

```
> Foo@0.0.1 test /Volumes/code/jest-hoisting-bug-demo/packages/Foo
> jest

● Validation Error:

  Module <rootDir>/node_modules/react-native/jest/assetFileTransformer.js in the transform option was not found.

  Configuration Documentation:
  https://facebook.github.io/jest/docs/configuration.html

npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! Foo@0.0.1 test: `jest`
npm ERR! Exit status 1
npm ERR!
npm ERR! Failed at the Foo@0.0.1 test script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.
```


full npm-debug.log

```
0 info it worked if it ends with ok
1 verbose cli [ '/usr/local/bin/iojs', '/usr/local/bin/npm', 'run', 'test' ]
2 info using npm@5.5.1
3 info using node@v8.9.1
4 verbose run-script [ 'pretest', 'test', 'posttest' ]
5 info lifecycle Foo@0.0.1~pretest: Foo@0.0.1
6 info lifecycle Foo@0.0.1~test: Foo@0.0.1
7 verbose lifecycle Foo@0.0.1~test: unsafe-perm in lifecycle true
8 verbose lifecycle Foo@0.0.1~test: PATH: ***
9 verbose lifecycle Foo@0.0.1~test: CWD: /Volumes/code/jest-hoisting-bug-demo/packages/Foo
10 silly lifecycle Foo@0.0.1~test: Args: [ '-c', 'jest' ]
11 silly lifecycle Foo@0.0.1~test: Returned: code: 1  signal: null
12 info lifecycle Foo@0.0.1~test: Failed to exec test script
13 verbose stack Error: Foo@0.0.1 test: `jest`
13 verbose stack Exit status 1
13 verbose stack     at EventEmitter.<anonymous> (/usr/local/lib/node_modules/npm/node_modules/npm-lifecycle/index.js:280:16)
13 verbose stack     at emitTwo (events.js:126:13)
13 verbose stack     at EventEmitter.emit (events.js:214:7)
13 verbose stack     at ChildProcess.<anonymous> (/usr/local/lib/node_modules/npm/node_modules/npm-lifecycle/lib/spawn.js:55:14)
13 verbose stack     at emitTwo (events.js:126:13)
13 verbose stack     at ChildProcess.emit (events.js:214:7)
13 verbose stack     at maybeClose (internal/child_process.js:925:16)
13 verbose stack     at Process.ChildProcess._handle.onexit (internal/child_process.js:209:5)
14 verbose pkgid Foo@0.0.1
15 verbose cwd /Volumes/code/jest-hoisting-bug-demo/packages/Foo
16 verbose Darwin 17.4.0
17 verbose argv "/usr/local/bin/iojs" "/usr/local/bin/npm" "run" "test"
18 verbose node v8.9.1
19 verbose npm  v5.5.1
20 error code ELIFECYCLE
21 error errno 1
22 error Foo@0.0.1 test: `jest`
22 error Exit status 1
23 error Failed at the Foo@0.0.1 test script.
23 error This is probably not a problem with npm. There is likely additional logging output above.
24 verbose exit [ 1, true ]
```

