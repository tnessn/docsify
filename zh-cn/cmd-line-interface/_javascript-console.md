PlatON console are based on Ethereum javascript console.

## Interactive use: the JSRE REPL  Console

The `PlatON CLI` executable `platon` has a JavaScript console (a **Read, Evaluate & Print Loop** = REPL exposing the JSRE), which can be started with the `console` or `attach` subcommand. The `console` subcommands starts the platon node and then opens the console. The `attach` subcommand will not start the platon node but instead tries to open the console on a running platon instance.

    $ platon console
    $ platon attach

The attach node accepts an endpoint in case the platon node is running with a non default ipc endpoint or you would like to connect over the rpc interface.

    $ platon attach ipc:/some/custom/path
    $ platon attach http://191.168.1.1:6789
    $ platon attach ws://191.168.1.1:6790

Note that by default the platon node doesn't start the http and weboscket service and not all functionality is provided over these interfaces due to security reasons. These defaults can be overridden when the `--rpcapi` and `--wsapi` arguments when the platon node is started, or with [admin.startRPC](admin_startRPC) and [admin.startWS](admin_startWS).

If you need log information, start with:

    $ platon --verbosity 5 console 2>> /tmp/eth.log

Otherwise mute your logs, so that it does not pollute your console:

    $ platon console 2>> /dev/null

or 

    $ platon --verbosity 0 console

PlatON has support to load custom JavaScript files into the console through the `--preload` argument. This can be used to load often used functions, setup web3 contract objects, or ...
```
platon --preload "/my/scripts/folder/utils.js,/my/scripts/folder/contracts.js" console
```


## Non-interactive use: JSRE script mode

It's also possible to execute files to the JavaScript interpreter. The `console` and `attach` subcommand accept the `--exec` argument which is a javascript statement. 

    $ platon --exec "eth.blockNumber" attach

This prints the current block number of a running platon instance.

Or execute a local script with more complex statements on a remote node over http:

    $ platon --exec 'loadScript("/tmp/checkbalances.js")' attach http://123.123.123.123:6789
    $ platon --jspath "/tmp" --exec 'loadScript("checkbalances.js")' attach http://123.123.123.123:6789

Use the `--jspath <path/to/my/js/root>` to set a libdir for your js scripts. Parameters to `loadScript()` with no absolute path will be understood relative to this directory.

You can exit the console cleanly by typing `exit` or simply with `CTRL-C`.

## Caveat 

The platon JSRE uses the [Otto JS VM](https://github.com/robertkrimen/otto) which has some limitations:

* "use strict" will parse, but does nothing.
* The regular expression engine (re2/regexp) is not fully compatible with the ECMA5 specification.

Note that the other known limitation of Otto (namely the lack of timers) is taken care of. PlatON JSRE implements both `setTimeout` and `setInterval`. In addition to this, the console provides `admin.sleep(seconds)` as well as a "blocktime sleep" method `admin.sleepBlocks(number)`. 

Since `web3.js` uses the [`bignumber.js`](https://github.com/MikeMcl/bignumber.js) library (MIT Expat Licence), it is also autoloded.

## Timers

In addition to the full functionality of JS (as per ECMA5), the platon JSRE is augmented with various timers. It implements `setInterval`, `clearInterval`, `setTimeout`, `clearTimeout` you may be used to using in browser windows. It also provides implementation for `admin.sleep(seconds)` and a block based timer, `admin.sleepBlocks(n)` which sleeps till the number of new blocks added is equal to or greater than `n`, think "wait for n confirmations". 

# Management APIs

Beside the official interface the platon node has support for additional management API's. These API's are offered using [JSON-RPC](http://www.jsonrpc.org/specification) and follow the same conventions as used in the DApp API. The platon package comes with a console client which has support for all additional API's.

[For more information please visit the Ethereum's management API wiki page](https://github.com/ethereum/go-ethereum/wiki/Management-APIs).
