pouchdb-adapter-react-native-sqlcipher
======

PouchDB adapter using ReactNative SQLite with SQLCipher implementation by [sreejithkrishnanr](https://github.com/sreejithkrishnanr/react-native-sqlcipher-2)

Read [this blogpost](https://dev.to/craftzdog/hacking-pouchdb-to-use-on-react-native-1gjh) for the complete description.

### Install deps

Install PouchDB core packages:

```bash
npm i pouchdb-adapter-http pouchdb-mapreduce
```

And install hacked packages for React Native:

```bash
npm i @craftzdog/pouchdb-core-react-native @craftzdog/pouchdb-replication-react-native 
```

Next, install SQLite3 engine modules:

```bash
npm i pouchdb-adapter-react-native-sqlite react-native-sqlcipher-2
react-native link react-native-sqlcipher-2
```

Then, install some packages to polyfill functions that PouchDB needs:

```bash
npm i base-64 events
```

### Create polyfills

Make a js file to polyfill some functions that PouchDB needs:

```js
import {decode, encode} from 'base-64'

if (!global.btoa) {
    global.btoa = encode;
}

if (!global.atob) {
    global.atob = decode;
}

// Avoid using node dependent modules
process.browser = true
```

Import it at the first line of your `index.js`.

### Load PouchDB

Make `pouchdb.js` like so:

```js
import PouchDB from '@craftzdog/pouchdb-core-react-native'
import HttpPouch from 'pouchdb-adapter-http'
import replication from '@craftzdog/pouchdb-replication-react-native'
import mapreduce from 'pouchdb-mapreduce'

import SQLite from 'react-native-sqlcipher-2'
import SQLiteAdapterFactory from 'pouchdb-adapter-react-native-sqlite'

const SQLiteAdapter = SQLiteAdapterFactory(SQLite)

export default PouchDB
  .plugin(HttpPouch)
  .plugin(replication)
  .plugin(mapreduce)
  .plugin(SQLiteAdapter)
```

If you need other plugins like `pouchdb-find`, just add them to it.

### Use PouchDB

Then, use it as usual:

```js
import PouchDB from './pouchdb'

function loadDB () {
  return new PouchDB('mydb.db', { adapter: 'react-native-sqlite', auth: {password: 'super secure password' })
}
```
### Use RxDB
```js
import RxDB from 'rxdb';

RxDB.create({
  name: 'mislestore',
  adapter: 'react-native-sqlite',
  multiInstance: false,
  pouchSettings: {
    auth: { password }
});
```
## Changelog
- 1.0.0
  + Initial release
