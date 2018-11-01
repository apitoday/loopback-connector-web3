# loopback-connector-web3

This module is an [Ethereum Web3](https://github.com/ethereum/web3.js/tree/1.0) connector for [LoopBack](https://loopback.io).

## Configuration

The connector now supports two modes:

### Refer to an existing solidity project

The datasource configures the `solidityProject` property to point to an existing solidity project. The connector automatically builds models from smart contracts discovered in the solidity project.

**server/datasources.json**:

```json
{
  "web3": {
    "url": "http://localhost:8545",
    "name": "web3",
    "connector": "loopback-connector-web3",
    "solidityProject": "../channel-contracts"
  },
  ...
}
```

### Define models with `ethereum` extensions

We can use `lb model` to create models representing Ethereum contracts.

**common/models/global-click.json**:

```json
{
  "name": "GlobalClick",
  "base": "Model",
  "idInjection": true,
  "options": {
    "validateUpsert": true,
    "ethereum": {
      "contract": {}
    }
  },
  "properties": {},
  "validations": [],
  "relations": {},
  "acls": [],
  "methods": {}
}
```

**common/models/global-click.js**:

```js
'use strict';

module.exports = function(GlobalClick) {
  // Programmatically configures the ethereum contract JSON interface
  const CONTRACT = require('channel-contracts').contracts.Demo1;
  GlobalClick.settings.ethereum.contract = CONTRACT;
};
```

**server/model-config.json**

```json
{
  "GlobalClick": {
    "dataSource": "web3",
    "public": true
  },
  ...
}
```

## Run migration of smart contracts

The Web3 connector is able to migrate corresponding smart contracts if the datasource
refers to a solidity project. For example,

**server/boot/migrate.js**

```js
'use strict';

module.exports = function(app, cb) {
  const ds = app.dataSources.web3;
  ds.on('connected', () => ds.automigrate(cb));
};
```

## Example

For an example to get you up and running, checkout the
[statechannels demo](https://github.com/LunchBadger/statechannels/tree/master/packages/sc-demo-app).
