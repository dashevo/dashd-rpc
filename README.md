bitcoind-rpc-saga.js
===============

[![NPM Package](https://img.shields.io/npm/v/bitcoind-rpc-saga.svg?style=flat-square)](https://www.npmjs.org/package/bitcoind-rpc-saga)
[![Build Status](https://img.shields.io/travis/sagacrypto/bitcoind-rpc-saga.svg?branch=master&style=flat-square)](https://travis-ci.org/sagacrypto/bitcoind-rpc-saga)
[![Coverage Status](https://img.shields.io/coveralls/sagacrypto/bitcoind-rpc-saga.svg?style=flat-square)](https://coveralls.io/r/sagacrypto/bitcoind-rpc-saga?branch=master)

A client library to connect to SagaCoin Core RPC in JavaScript.

## Get Started

bitcoind-rpc-saga.js runs on [node](http://nodejs.org/), and can be installed via [npm](https://npmjs.org/):

```bash
npm install bitcoind-rpc-saga
```

## RpcClient

Config parameters : 

	- protocol : (string - optional) - (default: 'https') - Set the protocol to be used. Either `http` or `https`.
	- user : (string - optional) - (default: 'user') - Set the user credential.
	- pass : (string - optional) - (default: 'pass') - Set the password credential.
	- host : (string - optional) - (default: '127.0.0.1') - The host you want to connect with.
	- port : (integer - optional) - (default: 9998) - Set the port on which perform the RPC command.

Promise vs callback based

  - `require('bitcoind-rpc-saga/promise')` to have promises returned
  - `require('bitcoind-rpc-saga')` to have callback functions returned
	
## Examples

Config:
```javascript
var config = {
    protocol: 'http',
    user: 'saga',
    pass: 'local321',
    host: '127.0.0.1',
    port: 48844
};
```

Promise based:
```javascript
var RpcClient = require('bitcoind-rpc-saga/promise');
var rpc = new RpcClient(config);

rpc.getRawMemPool()
    .then(ret => {
        return Promise.all(ret.result.map(r => rpc.getRawTransaction(r)))
    })
    .then(rawTxs => {
        rawTxs.forEach(rawTx => {
            console.log(`RawTX: ${rawTx.result}`);
        })
    })
    .catch(err => {
        console.log(err)
    })

```

Callback based (legacy):
```javascript
var run = function() {
  var bitcore = require('bitcore');
  var RpcClient = require('bitcoind-rpc-saga');
  var rpc = new RpcClient(config);

  var txids = [];

  function showNewTransactions() {
    rpc.getRawMemPool(function (err, ret) {
      if (err) {
        console.error(err);
        return setTimeout(showNewTransactions, 10000);
      }

      function batchCall() {
        ret.result.forEach(function (txid) {
          if (txids.indexOf(txid) === -1) {
            rpc.getRawTransaction(txid);
          }
        });
      }

      rpc.batch(batchCall, function(err, rawtxs) {
        if (err) {
          console.error(err);
          return setTimeout(showNewTransactions, 10000);
        }

        rawtxs.map(function (rawtx) {
          var tx = new bitcore.Transaction(rawtx.result);
          console.log('\n\n\n' + tx.id + ':', tx.toObject());
        });

        txids = ret.result;
        setTimeout(showNewTransactions, 2500);
      });
    });
  }

  showNewTransactions();
};
```

## Help 

You can dynamically access to the help of each method by doing
```
const RpcClient = require('bitcoind-rpc-saga');
var client = new RPCclient({
    protocol:'http',
    user: 'saga',
    pass: 'local321', 
    host: '127.0.0.1', 
    port: 48844
});

var cb = function (err, data) {
    console.log(data)
};
client.help(cb); //Get full help
client.help('getinfo',cb); //Get help of specific method
```
## License

**Code released under [the MIT license](https://github.com/bitpay/bitcore/blob/master/LICENSE).**

Copyright 2013-2014 BitPay, Inc.
Copyright 2018 Saga Development Team.
