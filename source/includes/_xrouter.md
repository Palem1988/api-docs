# XRouter API

The following set of calls are used to communicate and interact with blockchains remotely over the Blocknet network via XRouter. 

See [XRouter Setup](#xrouter-setup) for instructions on setting up your environment for use with XRouter.


Call                                              | Description
--------------------------------------------------|---------------
[xrGetNetworkServices](#xrgetnetworkservices)     | Returns supported XRouter services
[xrConnect](#xrconnect)                           | Pre-connect to XRouter nodes
[xrConnectedNodes](#xrconnectednodes)             | Returns connected node services and fees
[xrGetBlockCount](#xrgetblockcount)               | Returns a blockchain's block height
[xrGetBlockHash](#xrgetblockHash)                 | Returns a block number's hash
[xrGetBlock](#xrgetblock)                         | Returns a block hash's block number
[xrGetBlocks](#xrgetblocks)                       | Returns block hashes for multiple block numbers
[xrDecodeRawTransaction](#xrdecoderawtransaction) | Returns decoded transaction HEX
[xrGetTransaction](#xrgettransaction)             | Returns transaction data for transaction ID
[xrGetTransactions](#xrgettransactions)           | Returns transaction data for multiple transaction IDs
[xrSendTransaction](#xrsendtransaction)           | Submit a signed transaction to the network
[xrService](#xrservice)                           | Use to interact with XCloud services
[xrServiceConsensus](#xrServiceconsensus)         | Use to interact with XCloud services with consensus
[xrGetReply](#xrgetreply)                         | Returns prior response associated with UUID
[xrShowConfigs](#xrshowconfigs)                   | Returns all node configs received as raw text
[xrUpdateConfigs](#xrupdateconfigs)               | Requests latest configs from nodes
[xrReloadConfigs](#xrreloadconfigs)               | Applies changes made to your configs
[xrStatus](#xrstatus)                             | Returns your XRouter configurations










## xrGetNetworkServices

This call is used to view the XRouter services currently supported on the network, along with the number of nodes supporting each service. XRouter SPV calls use the `xr::` namespace. XCloud services use the `xrs::` namespace and can be called using [xrService](#xrservice) and [xrServiceConsensus](#xrserviceconsensus).


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrGetNetworkServices
```
<code class="api-call">xrGetNetworkServices</code>

This call does not take parameters.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : {
    "spvwallets" : [ "xr::BLOCK", "xr::BTC", "xr::LTC", "xr::MNP", "xr::SYS", "xr::TBLOCK" ],
    "services" : [ "xrs::BTCgetbestblockhash", "xrs::BTCgetblockhash", "xrs::BTCgettransaction", "xrs::SYSgetbestblockhash", "xrs::SYSgetblock", "xrs::SYSgetgovernanceinfo", "xrs::SYSgetmempool", "xrs::SYSlistoffers", "xrs::SYSofferinfo", "xrs::twilio" ],
    "nodecounts" : {
      "xr::BLOCK" : 27,
      "xr::BTC" : 13,
      "xr::LTC" : 21,
      "xr::MNP" : 1,
      "xr::SYS" : 9,
      "xrs::BTCgetbestblockhash" : 12,
      "xrs::BTCgetblockhash" : 12,
      "xrs::BTCgettransaction" : 5,
      "xrs::SYSgetbestblockhash" : 7,
      "xrs::SYSgetblock" : 6,
      "xrs::SYSgetgovernanceinfo" : 4,
      "xrs::SYSgetmempool" : 4,
      "xrs::SYSlistoffers" : 4,
      "xrs::SYSofferinfo" : 4,
      "xrs::twilio" : 1
    }
  },
  "uuid" : "cd408df7-0ff8-4e29-b5cf-0148af83f93a"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | object  | An object containing information on supported services.
spvwallets      | array   | An array of supported SPV wallets, represented by the asset's ticker.
services        | array   | An array of supported XCloud services.
nodecounts      | object  | An object of supported SPV wallets and XCloud services with how many nodes support each.
-- key          | string  | The SPV wallet or XCloud service with it's namespace.
-- value        | int     | The amount of nodes supporting each respective service.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).










## xrConnect

> Sample Data

```cli
{
  "service": "SYS"
}
```
This call is used to connect to XRouter nodes with the specified service and download their configs. This command is useful to determine how much nodes are charging for services. It's also useful to "warm up" connections. By connecting to nodes immediately before making a large request it can speed up the reponse time (since those connections will be open). However, XRouter nodes close inactive connections after 15 seconds, so keep that in mind. After connecting, call [xrConnectedNodes](#xrconnectednodes) to display information about these XRouter nodes.


### Request Parameters

> Sample Requests

```cli
// Connect to one XRouter node supporting SYS
blocknetdx-cli xrConnect xr::SYS

// Connect to two XRouter nodes supporting SYS
blocknetdx-cli xrConnect xr::SYS 2

// Connect to one XRouter node supporting XCloud service twilio
blocknetdx-cli xrConnect xrs::twilio
```
<code class="api-call">xrConnect [service] [node_count]\(optional)</code>

Parameter       | Type    | Description
----------------|---------|-------------
service         | string  | Service name including the namespace (*xr::[spv_call]* or *xrs::[xcloud_service]*).
node_count      | int     | (Optional Parameter)<br>Defaults to `1` if no `consensus=` setting in `xrouter.conf`.<br>The number of nodes to pre-connect to that will be used to route calls and use the majority response from for consensus.  


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
// Sample response for xrConnect SYS 2
{
  "reply" : [
    {
      "nodepubkey" : "02c6c79a75846fd9bb064788b03145e347fa5464558fa9030ebb009df2833369f0",
      "score" : 0,
      "banned" : false,
      "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
      "spvwallets" : [ "MNP", "SYS" ],
      "spvconfigs" : [
        {
          "spvwallet" : "MNP",
          "commands" : [
            {
              "command" : "xrGetConfig",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockCount",
              "fee" : 0.000000,
              "requestlimit" : 10,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockHash",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlock",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrSendTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlocks",
              "fee" : 0.100000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetTransactions",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrDecodeRawTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            }
          ]
        },
        {
          "spvwallet" : "SYS",
          "commands" : [
            {
              "command" : "xrGetConfig",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockCount",
              "fee" : 0.100000,
              "requestlimit" : 10,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockHash",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlock",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrSendTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlocks",
              "fee" : 0.100000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetTransactions",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrDecodeRawTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            }
          ]
        }
      ],
      "feedefault" : 0.000000,
      "fees" : {
        "xrGetBlockCount" : 0.000000,
        "xrGetBlocks" : 0.100000
      },
      "services" : {
        "SYSgetbestblockhash" : {
          "parameters" : "",
          "fee" : 0.100000,
          "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
          "requestlimit" : -1,
          "fetchlimit" : 50,
          "timeout" : 30,
          "disabled" : false
        },
        "SYSgetgovernanceinfo" : {
          "parameters" : "",
          "fee" : 0.100000,
          "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
          "requestlimit" : -1,
          "fetchlimit" : 50,
          "timeout" : 30,
          "disabled" : false
        }
      }
    },
    {
      "nodepubkey" : "0370874cad6252bb94afa9a253c90122760ce2862e623b515e57bfe0697f3fc515",
      "score" : 0,
      "banned" : false,
      "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
      "spvwallets" : [ "BLOCK", "BTC", "LTC", "SYS" ],
      "spvconfigs" : [
        {
          "spvwallet" : "BLOCK",
          "commands" : [
            {
              "command" : "xrGetConfig",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockCount",
              "fee" : 0.000000,
              "requestlimit" : -1,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockHash",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlock",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrSendTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlocks",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransactions",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrDecodeRawTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            }
          ]
        },
        {
          "spvwallet" : "BTC",
          "commands" : [
            {
              "command" : "xrGetConfig",
              "fee" : 0.200000,
              "requestlimit" : 100,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockCount",
              "fee" : 0.200000,
              "requestlimit" : 100,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockHash",
              "fee" : 0.200000,
              "requestlimit" : 100,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlock",
              "fee" : 0.200000,
              "requestlimit" : 100,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransaction",
              "fee" : 0.200000,
              "requestlimit" : 100,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrSendTransaction",
              "fee" : 0.200000,
              "requestlimit" : 100,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlocks",
              "fee" : 0.200000,
              "requestlimit" : 100,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransactions",
              "fee" : 0.200000,
              "requestlimit" : 100,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrDecodeRawTransaction",
              "fee" : 0.200000,
              "requestlimit" : 100,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            }
          ]
        },
        {
          "spvwallet" : "LTC",
          "commands" : [
            {
              "command" : "xrGetConfig",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockCount",
              "fee" : 0.000000,
              "requestlimit" : -1,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockHash",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlock",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrSendTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlocks",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransactions",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrDecodeRawTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            }
          ]
        },
        {
          "spvwallet" : "SYS",
          "commands" : [
            {
              "command" : "xrGetConfig",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockCount",
              "fee" : 0.000000,
              "requestlimit" : -1,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockHash",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlock",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrSendTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlocks",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransactions",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrDecodeRawTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            }
          ]
        }
      ],
      "feedefault" : 0.000000,
      "fees" : {
        "BTC" : 0.200000,
        "xrGetBlockCount" : 0.000000
      },
      "services" : {
        "BTCgetbestblockhash" : {
          "parameters" : "",
          "fee" : 0.000000,
          "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
          "requestlimit" : 50,
          "fetchlimit" : 50,
          "timeout" : 30,
          "disabled" : false
        },
        "BTCgetblockhash" : {
          "parameters" : "int",
          "fee" : 0.000000,
          "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
          "requestlimit" : 50,
          "fetchlimit" : 50,
          "timeout" : 30,
          "disabled" : false
        },
        "BTCgettransaction" : {
          "parameters" : "string",
          "fee" : 0.000000,
          "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
          "requestlimit" : 50,
          "fetchlimit" : 50,
          "timeout" : 30,
          "disabled" : false
        },
        "SYSgetbestblockhash" : {
          "parameters" : "",
          "fee" : 0.000000,
          "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
          "requestlimit" : 10,
          "fetchlimit" : 50,
          "timeout" : 30,
          "disabled" : false
        }
      }
    }
  ],
  "uuid" : "88A16FDB-464D-470B-94CD-D827E63285CC"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | array   | An array of nodes providing the specified service, along with their configs.
nodepubkey      | string  | The node ID.
score           | int     | The node's score based on quality of service. A score of `-200` will ban the node for a 24hr period. You can change the ban threshold with the `xrouterbanscore` setting in `blocknetdx.conf`. See [node scoring](#xrouter-node-scoring) for more details.
banned          | bool    | Signifies if the node is currently banned.<br>`true`: Node is banned.<br>`false`: Node is not banned. See [node scoring](#xrouter-node-scoring) for more details.
paymentaddress  | string  | The node's payment address.
spvwallets      | array   | An array of supported SPV wallets, represented by the asset's ticker.
spvconfigs      | array   | An array of each SPV wallets and command configurations.
spvwallet       | string  | The SPV wallet that the configurations under `commands` pertains to.
commands        | array   | An array of each SPV wallet command and respective configurations.
command         | string  | The SPV command.
fee             | float   | The command fee, overrides the `feedefault` and `fees` values. This priority has already been accounted for in this value.
requestlimit    | int     | The minimum time allowed between calls in milliseconds. A value of `-1` means there is no limit. If you exceed this value you will be penalized and eventually banned by this specific node.
paymentaddress  | string  | The node's payment address for this specific command.
disabled        | bool    | Signifies if the node has disabled this command.<br>`true`: Call is disabled and not supported.<br>`false`: Call is enabled and supported.
feedefault      | float   | The node's default service fee. This fee is overridden by the values specified in `fees`, SPV command configuration `fee`, and XCloud service command configuration `fee`.
fees            | object  | Object of SPV commands and respective fees. These values are overridden by the SPV wallet-specific configuration `fee`.
services        | object  | Object of the node's XCloud service calls with respective properties.
parameters      | string  | Information on the parameters the command takes.
fee             | float   | The service command fee. This overrides the `feedefault` value.
paymentaddress  | string  | The node's payment address for this specific command.
requestlimit    | int     | The minimum time allowed between calls in milliseconds. A value of `-1` means there is no limit. If you exceed this value you will be penalized and eventually banned by this specific node.
fetchlimit      | int     | The maximum number of records returned. This pertains to calls such as [xrGetBlocks](#xrgetblocks) and [xrGetTransactions](#xrgettransactions) where multiple records are returned. A value of `-1` means there is no limit. A value of `0` means no blocks will be processed.
timeout         | int     | The value for `timeout` you set in `xrouter.conf` for this call. Defines how long (in seconds) your client waits for a response from a Service Node. The default value is `30`.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).



 






## xrConnectedNodes

This call is used to lists all the data about current and previously connected nodes. This information includes supported SPV wallets, SPV calls, services, fees, and other service settings.


### Request Parameters

> Sample Requests

```cli
blocknetdx-cli xrConnectedNodes
```
<code class="api-call">xrConnectedNodes</code>

This call does not take parameters.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : [
    {
      "nodepubkey" : "02c6c79a75846fd9bb064788b03145e347fa5464558fa9030ebb009df2833369f0",
      "score" : 0,
      "banned" : false,
      "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
      "spvwallets" : [ "MNP", "SYS" ],
      "spvconfigs" : [
        {
          "spvwallet" : "MNP",
          "commands" : [
            {
              "command" : "xrGetConfig",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockCount",
              "fee" : 0.000000,
              "requestlimit" : 10,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockHash",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlock",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrSendTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlocks",
              "fee" : 0.100000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetTransactions",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrDecodeRawTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            }
          ]
        },
        {
          "spvwallet" : "SYS",
          "commands" : [
            {
              "command" : "xrGetConfig",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockCount",
              "fee" : 0.100000,
              "requestlimit" : 10,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockHash",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlock",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrSendTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetBlocks",
              "fee" : 0.100000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrGetTransactions",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            },
            {
              "command" : "xrDecodeRawTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
              "disabled" : false
            }
          ]
        }
      ],
      "feedefault" : 0.000000,
      "fees" : {
        "xrGetBlockCount" : 0.000000,
        "xrGetBlocks" : 0.100000
      },
      "services" : {
        "SYSgetbestblockhash" : {
          "parameters" : "",
          "fee" : 0.100000,
          "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
          "requestlimit" : -1,
          "fetchlimit" : 50,
          "timeout" : 30,
          "disabled" : false
        },
        "SYSgetgovernanceinfo" : {
          "parameters" : "",
          "fee" : 0.100000,
          "paymentaddress" : "B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS",
          "requestlimit" : -1,
          "fetchlimit" : 50,
          "timeout" : 30,
          "disabled" : false
        }
      }
    },
    {
      "nodepubkey" : "0370874cad6252bb94afa9a253c90122760ce2862e623b515e57bfe0697f3fc515",
      "score" : 0,
      "banned" : false,
      "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
      "spvwallets" : [ "BLOCK", "BTC", "LTC", "SYS" ],
      "spvconfigs" : [
        {
          "spvwallet" : "BLOCK",
          "commands" : [
            {
              "command" : "xrGetConfig",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockCount",
              "fee" : 0.000000,
              "requestlimit" : -1,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockHash",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlock",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrSendTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlocks",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransactions",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrDecodeRawTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            }
          ]
        },
        {
          "spvwallet" : "BTC",
            "commands" : [
              {
                "command" : "xrGetConfig",
                "fee" : 0.200000,
                "requestlimit" : 100,
                "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
                "disabled" : false
              },
              {
                "command" : "xrGetBlockCount",
                "fee" : 0.200000,
                "requestlimit" : 100,
                "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
                "disabled" : false
              },
              {
                "command" : "xrGetBlockHash",
                "fee" : 0.200000,
                "requestlimit" : 100,
                "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
                "disabled" : false
              },
              {
                "command" : "xrGetBlock",
                "fee" : 0.200000,
                "requestlimit" : 100,
                "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
                "disabled" : false
              },
              {
                "command" : "xrGetTransaction",
                "fee" : 0.200000,
                "requestlimit" : 100,
                "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
                "disabled" : false
              },
              {
                "command" : "xrSendTransaction",
                "fee" : 0.200000,
                "requestlimit" : 100,
                "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
                "disabled" : false
              },
              {
                "command" : "xrGetBlocks",
                "fee" : 0.200000,
                "requestlimit" : 100,
                "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
                "disabled" : false
              },
              {
                "command" : "xrGetTransactions",
                "fee" : 0.200000,
                "requestlimit" : 100,
                "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
                "disabled" : false
              },
              {
                "command" : "xrDecodeRawTransaction",
                "fee" : 0.200000,
                "requestlimit" : 100,
                "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
                "disabled" : false
              }
            ]
        },
        {
          "spvwallet" : "LTC",
          "commands" : [
            {
              "command" : "xrGetConfig",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockCount",
              "fee" : 0.000000,
              "requestlimit" : -1,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockHash",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlock",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrSendTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlocks",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransactions",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrDecodeRawTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            }
          ]
        },
        {
          "spvwallet" : "SYS",
          "commands" : [
            {
              "command" : "xrGetConfig",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockCount",
              "fee" : 0.000000,
              "requestlimit" : -1,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlockHash",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlock",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrSendTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetBlocks",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrGetTransactions",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            },
            {
              "command" : "xrDecodeRawTransaction",
              "fee" : 0.000000,
              "requestlimit" : 50,
              "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
              "disabled" : false
            }
          ]
        }
      ],
      "feedefault" : 0.000000,
      "fees" : {
        "BTC" : 0.200000,
        "xrGetBlockCount" : 0.000000
      },
      "services" : {
        "BTCgetbestblockhash" : {
          "parameters" : "",
          "fee" : 0.000000,
          "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
          "requestlimit" : 50,
          "fetchlimit" : 50,
          "timeout" : 30,
          "disabled" : false
        },
        "BTCgetblockhash" : {
          "parameters" : "int",
          "fee" : 0.000000,
          "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
          "requestlimit" : 50,
          "fetchlimit" : 50,
          "timeout" : 30,
          "disabled" : false
        },
        "BTCgettransaction" : {
          "parameters" : "string",
          "fee" : 0.000000,
          "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
          "requestlimit" : 50,
          "fetchlimit" : 50,
          "timeout" : 30,
          "disabled" : false
        },
        "SYSgetbestblockhash" : {
          "parameters" : "",
          "fee" : 0.000000,
          "paymentaddress" : "BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh",
          "requestlimit" : 10,
          "fetchlimit" : 50,
          "timeout" : 30,
          "disabled" : false
        }
      }
    }
  ],
  "uuid" : "88A16FDB-464D-470B-94CD-D827E63285CC"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | array   | An array of nodes providing the specified service.
nodepubkey      | string  | The node ID.
score           | int     | The node's score based on quality of service. A score of `-200` will ban the node for a 24hr period. You can change the ban threshold with the `xrouterbanscore` setting in `blocknetdx.conf`. See [node scoring](#xrouter-node-scoring) for more details.
banned          | bool    | Signifies if the node is currently banned.<br>`true`: Node is banned.<br>`false`: Node is not banned. See [node scoring](#xrouter-node-scoring) for more details.
paymentaddress  | string  | The node's payment address.
spvwallets      | array   | An array of supported SPV wallets, represented by the asset's ticker.
spvconfigs      | array   | An array of each SPV wallets and command configurations.
spvwallet       | string  | The SPV wallet that the configurations under `commands` pertains to.
commands        | array   | An array of each SPV wallet command and respective configurations.
command         | string  | The SPV command.
fee             | float   | The SPV command fee. This overrides the `feedefault` and `fees` values.
requestlimit    | int     | The minimum time allowed between calls in milliseconds. A value of `-1` means there is no limit. If you exceed this value you will be penalized and eventually banned by this specific node.
paymentaddress  | string  | The node's payment address for this specific command.
disabled        | bool    | Signifies if the node has disabled this command.<br>`true`: Call is disabled and not supported.<br>`false`: Call is enabled and supported.
feedefault      | float   | The node's default service fee. This fee is overridden by the values specified in `fees`, SPV command configuration `fee`, and XCloud service command configuration `fee`.
fees            | object  | Object of SPV commands and respective fees. These values are overridden by the SPV wallet-specific configuration `fee`.
services        | object  | Object of the node's XCloud service calls with respective properties.
parameters      | string  | Information on the parameters the command takes.
fee             | float   | The service command fee. This overrides the `feedefault` value.
paymentaddress  | string  | The node's payment address for this specific command.
requestlimit    | int     | The minimum time allowed between calls in milliseconds. A value of `-1` means there is no limit. If you exceed this value you will be penalized and eventually banned by this specific node.
fetchlimit      | int     | The maximum number of records returned. This pertains to calls such as [xrGetBlocks](#xrgetblocks) and [xrGetTransactions](#xrgettransactions) where multiple records are returned. A value of `-1` means there is no limit. A value of `0` means no blocks will be processed.
timeout         | int     | The value for `timeout` you set in `xrouter.conf` for this call. Defines how long (in seconds) your client waits for a response from a Service Node. The default value is `30`.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).










## xrGetBlockCount

> Sample Data

```cli
{
  "blockchain": "SYS"
}
```
This call is used to retrieve the current block height of the longest chain for the specified blockchain. It requires the blockchain to be SPV wallet supported.


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrGetBlockCount SYS 2
```
<code class="api-call">xrGetBlockCount [blockchain] [node_count]\(optional)</code>

Parameter       | Type    | Description
----------------|---------|-------------
blockchain      | string  | The blockchain, represented by the asset's ticker (BTC, LTC, SYS, etc.).
node_count      | int     | (Optional Parameter)<br>Defaults to `1` if no `consensus=` setting in `xrouter.conf`.<br>The number of nodes that will be used to route calls and use the majority response from for consensus.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : 482107,
  "allreplies" : [
    {
      "nodepubkey" : "02c6c79a75846fd9bb064788b03145e347fa5464558fa9030ebb009df2833369f0",
      "score" : 48,
      "reply" : 482107
    },
    {
      "nodepubkey" : "0370874cad6252bb94afa9a253c90122760ce2862e623b515e57bfe0697f3fc515",
      "score" : 52,
      "reply" : 482107
    }
  ],
  "uuid" : "34d0998e-a950-4fd8-b1d6-7571c83abb50"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | int     | The latest block number of the specified blockchain. If using a `node_count` greater than `1`, this returns the most common reply within `allreplies`. If there is a tie then one is chosen, or if one is an error then the non-error is chosen.
allreplies*     | array   | An array of objects with responses from each node. This can be useful if you wanted to do your own analysis/filtering of the responses.
nodepubkey*     | string  | The node ID.
score*          | int     | The respective node's score based on quality of service. A score of `-200` will ban the node for a 24hr period. You can change the ban threshold with the `xrouterbanscore` setting in `blocknetdx.conf`. See [node scoring](#xrouter-node-scoring) for more details.
reply*          | int     | The latest block number of the specified blockchain from the respective node.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).

\* This is only returned if using a `node_count` greater than `1`.








## xrGetBlockHash

> Sample Data

```cli
{
  "blockchain": "SYS",
  "block_number": 91510
}
```
This call is used to retrieve the block hash of the specified block and blockchain.


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrGetBlockHash SYS 482107 2
```
<code class="api-call">xrGetBlockHash [blockchain] [block_number] [node_count]\(optional)</code>

Parameter       | Type    | Description
----------------|---------|-------------
blockchain      | string  | The blockchain, represented by the asset's ticker (BTC, LTC, SYS, etc.).
block_number    | string  | The block number or hex for the block hash of interest.
node_count      | int     | (Optional Parameter)<br>Defaults to `1` if no `consensus=` setting in `xrouter.conf`.<br>The number of nodes that will be used to route calls and use the majority response from for consensus. 


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : "52583afcd857e45234e7c8981804b36d13a7b3025c17f3535c3e724542590a79",
  "allreplies" : [
    {
      "nodepubkey" : "02c6c79a75846fd9bb064788b03145e347fa5464558fa9030ebb009df2833369f0",
      "score" : 238,
      "reply" : "52583afcd857e45234e7c8981804b36d13a7b3025c17f3535c3e724542590a79"
    },
    {
      "nodepubkey" : "0370874cad6252bb94afa9a253c90122760ce2862e623b515e57bfe0697f3fc515",
      "score" : 288,
      "reply" : "52583afcd857e45234e7c8981804b36d13a7b3025c17f3535c3e724542590a79"
    }
  ],
  "uuid" : "3c84d025-8a03-4b64-848f-99892fe481ff"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | string  | The block hash of the specified block and blockchain. If using a `node_count` greater than `1`, this returns the most common reply within `allreplies`. If there is a tie then one is chosen, or if one is an error then the non-error is chosen.
allreplies*     | array   | An array of objects with responses from each node. This can be useful if you wanted to do your own analysis/filtering of the responses.
nodepubkey*     | string  | The node ID. 
score*          | int     | The respective node's score based on quality of service. A score of `-200` will ban the node for a 24hr period. You can change the ban threshold with the `xrouterbanscore` setting in `blocknetdx.conf`. See [node scoring](#xrouter-node-scoring) for more details.
reply*          | string  | The block hash of the specified block and blockchain from the respective node.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).

\* This is only returned if using a `node_count` greater than `1`.








## xrGetBlock

> Sample Data

```cli
{
  "blockchain": "SYS",
  "block_hash": "0cf18712db68be85793dc06cd0a4fbc8edb166157e6847bb3c9f55d462b02837"
}
```
This call is used to retrieve the block data for the specified block hash and blockchain.


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrGetBlock SYS 0cf18712db68be85793dc06cd0a4fbc8edb166157e6847bb3c9f55d462b02837 2
```
<code class="api-call">xrGetBlock [blockchain] [block_hash] [node_count]\(optional)</code>

Parameter       | Type    | Description
----------------|---------|-------------
blockchain      | string  | The blockchain, represented by the asset's ticker (BTC, LTC, SYS, etc.).
block_hash      | string  | The block hash for the block of interest.
node_count      | int     | (Optional Parameter)<br>Defaults to `1` if no `consensus=` setting in `xrouter.conf`.<br>The number of nodes that will be used to route calls and use the majority response from for consensus. 


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : [
    {
      "hash" : "0cf18712db68be85793dc06cd0a4fbc8edb166157e6847bb3c9f55d462b02837",
      "confirmations" : 390700,
      "size" : 734,
      "height" : 91510,
      "version" : 805306624,
      "versionHex" : "30000100",
      "merkleroot" : "55554df0ea7f66552beffe433d2c2f620bada79063a343ffc23c60c67cdf028f",
      "tx" : [ "55554df0ea7f66552beffe433d2c2f620bada79063a343ffc23c60c67cdf028f" ],
      "time" : 1530894673,
      "mediantime" : 1530894336,
      "nonce" : 0,
      "bits" : "180761db",
      "difficulty" : 148937676133.261200,
      "chainwork" : "00000000000000000000000000000000000000000028ad56864c458781e21970",
      "auxpow" : {
        "tx" : {
          "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff5703dd3408142f5669614254432f626d67706f6f6c2e636f6d2f2cfabe6d6d75654ab7a6b163e7a04e56e43f8e7ca0087f11e8bd6c3c72c69732e849532878040000000000000010d51dbb0b9301409ff96ef9f588c50200ffffffff01c051824a000000001976a914f1c075a01882ae0972f95d3a4177c86c852b7d9188ac00000000",
          "txid" : "c8fc123672fffb3c6a45b94bdd30afc114f282742338809380bc84fe8666d489",
          "size" : 172,
          "version" : 1,
          "locktime" : 0,
          "vin" : [
            {
              "coinbase" : "03dd3408142f5669614254432f626d67706f6f6c2e636f6d2f2cfabe6d6d75654ab7a6b163e7a04e56e43f8e7ca0087f11e8bd6c3c72c69732e849532878040000000000000010d51dbb0b9301409ff96ef9f588c50200",
              "sequence" : 4294967295
            }
          ],
          "vout" : [
            {
              "value" : 12.500546,
              "valueSat" : 1250054592,
              "n" : 0,
              "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 f1c075a01882ae0972f95d3a4177c86c852b7d91 OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "76a914f1c075a01882ae0972f95d3a4177c86c852b7d9188ac",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [ "SjLGSPfmF3kVPA6A213hShYc4aRtiWZ8Wu" ]
              }
            }
          ],
          "blockhash" : "000000000000000004bdb8e3fdf28ffc528f4e6b1871a6177579cbd4dabde955"
        },
        "index" : 0,
        "chainindex" : 2,
        "merklebranch" : [ "a67d749dfa7006b9d66d2284b20a64ebcb8c2e5aae18e355c790b90a060a30c1", "9ab5c057ecf43aa0552c21be4f86b1c9b248fe2dd0821bee38e5a25a22c49c25", "ec11fd79e02fe98bf9f4b624e5460a3221dbae27d24f36c9988dcc5beaf3c592", "dbc42b7360a7165b277f73094a366e951d3ac6d6a1e9493d838d35372e5e846b", "c7260dbfb6b196bd2e4e4dbde1799592627a3e6fa384e6692be8387a9251e5e2" ],
        "chainmerklebranch" : [ "89af0bc973bc0308f14c5c50c3d75f68783eab40a4688064cd56cfaed977d138", "5360cbf0991b4b0e09d589631c232d1f229104faad9e65375695230c47bc2493" ],
        "parentblock" : "0000002024653d98f3eace4b66b6269fa9205e08b647e23cf7880e0000000000000000003feae4c877b2f8b73f6f92a589ed8870fe98abb3551f1539373597f5b44514ada9993f5bb1830118d5793688"
      },
      "previousblockhash" : "ba53d715abf76ac8adc025120ffb48b2331f3a1b9b7d01167ffd7dcd23934390",
      "nextblockhash" : "cd3a236542491089bffd8ba49cbe1d11a20dd4c4ad188307027e60c710732409"
    }
  ],
  "allreplies" : [
    {
      "nodepubkey" : "02c6c79a75846fd9bb064788b03145e347fa5464558fa9030ebb009df2833369f0",
      "score" : 242,
      "reply" : [
        {
          "hash" : "0cf18712db68be85793dc06cd0a4fbc8edb166157e6847bb3c9f55d462b02837",
          "confirmations" : 390700,
          "size" : 734,
          "height" : 91510,
          "version" : 805306624,
          "versionHex" : "30000100",
          "merkleroot" : "55554df0ea7f66552beffe433d2c2f620bada79063a343ffc23c60c67cdf028f",
          "tx" : [ "55554df0ea7f66552beffe433d2c2f620bada79063a343ffc23c60c67cdf028f" ],
          "time" : 1530894673,
          "mediantime" : 1530894336,
          "nonce" : 0,
          "bits" : "180761db",
          "difficulty" : 148937676133.261200,
          "chainwork" : "00000000000000000000000000000000000000000028ad56864c458781e21970",
          "auxpow" : {
            "tx" : {
              "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff5703dd3408142f5669614254432f626d67706f6f6c2e636f6d2f2cfabe6d6d75654ab7a6b163e7a04e56e43f8e7ca0087f11e8bd6c3c72c69732e849532878040000000000000010d51dbb0b9301409ff96ef9f588c50200ffffffff01c051824a000000001976a914f1c075a01882ae0972f95d3a4177c86c852b7d9188ac00000000",
              "txid" : "c8fc123672fffb3c6a45b94bdd30afc114f282742338809380bc84fe8666d489",
              "size" : 172,
              "version" : 1,
              "locktime" : 0,
              "vin" : [
                {
                  "coinbase" : "03dd3408142f5669614254432f626d67706f6f6c2e636f6d2f2cfabe6d6d75654ab7a6b163e7a04e56e43f8e7ca0087f11e8bd6c3c72c69732e849532878040000000000000010d51dbb0b9301409ff96ef9f588c50200",
                  "sequence" : 4294967295
                }
              ],
              "vout" : [
                {
                  "value" : 12.500546,
                  "valueSat" : 1250054592,
                  "n" : 0,
                  "scriptPubKey" : {
                    "asm" : "OP_DUP OP_HASH160 f1c075a01882ae0972f95d3a4177c86c852b7d91 OP_EQUALVERIFY OP_CHECKSIG",
                    "hex" : "76a914f1c075a01882ae0972f95d3a4177c86c852b7d9188ac",
                    "reqSigs" : 1,
                    "type" : "pubkeyhash",
                    "addresses" : [ "SjLGSPfmF3kVPA6A213hShYc4aRtiWZ8Wu" ]
                  }
                }
              ],
                "blockhash" : "000000000000000004bdb8e3fdf28ffc528f4e6b1871a6177579cbd4dabde955"
            },
            "index" : 0,
            "chainindex" : 2,
            "merklebranch" : [ "a67d749dfa7006b9d66d2284b20a64ebcb8c2e5aae18e355c790b90a060a30c1", "9ab5c057ecf43aa0552c21be4f86b1c9b248fe2dd0821bee38e5a25a22c49c25", "ec11fd79e02fe98bf9f4b624e5460a3221dbae27d24f36c9988dcc5beaf3c592", "dbc42b7360a7165b277f73094a366e951d3ac6d6a1e9493d838d35372e5e846b", "c7260dbfb6b196bd2e4e4dbde1799592627a3e6fa384e6692be8387a9251e5e2" ],
            "chainmerklebranch" : [ "89af0bc973bc0308f14c5c50c3d75f68783eab40a4688064cd56cfaed977d138", "5360cbf0991b4b0e09d589631c232d1f229104faad9e65375695230c47bc2493" ],
            "parentblock" : "0000002024653d98f3eace4b66b6269fa9205e08b647e23cf7880e0000000000000000003feae4c877b2f8b73f6f92a589ed8870fe98abb3551f1539373597f5b44514ada9993f5bb1830118d5793688"
          },
          "previousblockhash" : "ba53d715abf76ac8adc025120ffb48b2331f3a1b9b7d01167ffd7dcd23934390",
          "nextblockhash" : "cd3a236542491089bffd8ba49cbe1d11a20dd4c4ad188307027e60c710732409"
        }
      ]
    },
    {
      "nodepubkey" : "0370874cad6252bb94afa9a253c90122760ce2862e623b515e57bfe0697f3fc515",
      "score" : 292,
      "reply" : [
        {
          "hash" : "0cf18712db68be85793dc06cd0a4fbc8edb166157e6847bb3c9f55d462b02837",
          "confirmations" : 390700,
          "size" : 734,
          "height" : 91510,
          "version" : 805306624,
          "versionHex" : "30000100",
          "merkleroot" : "55554df0ea7f66552beffe433d2c2f620bada79063a343ffc23c60c67cdf028f",
          "tx" : [ "55554df0ea7f66552beffe433d2c2f620bada79063a343ffc23c60c67cdf028f" ],
          "time" : 1530894673,
          "mediantime" : 1530894336,
          "nonce" : 0,
          "bits" : "180761db",
          "difficulty" : 148937676133.261200,
          "chainwork" : "00000000000000000000000000000000000000000028ad56864c458781e21970",
          "auxpow" : {
            "tx" : {
              "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff5703dd3408142f5669614254432f626d67706f6f6c2e636f6d2f2cfabe6d6d75654ab7a6b163e7a04e56e43f8e7ca0087f11e8bd6c3c72c69732e849532878040000000000000010d51dbb0b9301409ff96ef9f588c50200ffffffff01c051824a000000001976a914f1c075a01882ae0972f95d3a4177c86c852b7d9188ac00000000",
              "txid" : "c8fc123672fffb3c6a45b94bdd30afc114f282742338809380bc84fe8666d489",
              "size" : 172,
              "version" : 1,
              "locktime" : 0,
              "vin" : [
                {
                  "coinbase" : "03dd3408142f5669614254432f626d67706f6f6c2e636f6d2f2cfabe6d6d75654ab7a6b163e7a04e56e43f8e7ca0087f11e8bd6c3c72c69732e849532878040000000000000010d51dbb0b9301409ff96ef9f588c50200",
                  "sequence" : 4294967295
                }
              ],
              "vout" : [
                {
                  "value" : 12.500546,
                  "valueSat" : 1250054592,
                  "n" : 0,
                  "scriptPubKey" : {
                    "asm" : "OP_DUP OP_HASH160 f1c075a01882ae0972f95d3a4177c86c852b7d91 OP_EQUALVERIFY OP_CHECKSIG",
                    "hex" : "76a914f1c075a01882ae0972f95d3a4177c86c852b7d9188ac",
                    "reqSigs" : 1,
                    "type" : "pubkeyhash",
                    "addresses" : [ "SjLGSPfmF3kVPA6A213hShYc4aRtiWZ8Wu" ]
                  }
                }
              ],
              "blockhash" : "000000000000000004bdb8e3fdf28ffc528f4e6b1871a6177579cbd4dabde955"
            },
            "index" : 0,
            "chainindex" : 2,
            "merklebranch" : [ "a67d749dfa7006b9d66d2284b20a64ebcb8c2e5aae18e355c790b90a060a30c1", "9ab5c057ecf43aa0552c21be4f86b1c9b248fe2dd0821bee38e5a25a22c49c25", "ec11fd79e02fe98bf9f4b624e5460a3221dbae27d24f36c9988dcc5beaf3c592", "dbc42b7360a7165b277f73094a366e951d3ac6d6a1e9493d838d35372e5e846b", "c7260dbfb6b196bd2e4e4dbde1799592627a3e6fa384e6692be8387a9251e5e2" ],
            "chainmerklebranch" : [ "89af0bc973bc0308f14c5c50c3d75f68783eab40a4688064cd56cfaed977d138", "5360cbf0991b4b0e09d589631c232d1f229104faad9e65375695230c47bc2493" ],
            "parentblock" : "0000002024653d98f3eace4b66b6269fa9205e08b647e23cf7880e0000000000000000003feae4c877b2f8b73f6f92a589ed8870fe98abb3551f1539373597f5b44514ada9993f5bb1830118d5793688"
          },
          "previousblockhash" : "ba53d715abf76ac8adc025120ffb48b2331f3a1b9b7d01167ffd7dcd23934390",
          "nextblockhash" : "cd3a236542491089bffd8ba49cbe1d11a20dd4c4ad188307027e60c710732409"
        }
      ]
    }
  ],
  "uuid" : "5404cc31-2b01-48eb-8599-e6adcd8f9216"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | object  | An object containing the block data for the specified block hash and blockchain. If using a `node_count` greater than `1`, this returns the most common reply within `allreplies`. If there is a tie then one is chosen, or if one is an error then the non-error is chosen.
allreplies*     | array   | An array of objects with responses from each node. This can be useful if you wanted to do your own analysis/filtering of the responses.
nodepubkey*     | string  | The node ID.
score*          | int     | The respective node's score based on quality of service. A score of `-200` will ban the node for a 24hr period. You can change the ban threshold with the `xrouterbanscore` setting in `blocknetdx.conf`. See [node scoring](#xrouter-node-scoring) for more details.
reply*          | object  | An object containing the block data for the specified block hash and blockchain from the respective node.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).

\* This is only returned if using a `node_count` greater than `1`.










## xrGetBlocks

> Sample Data

```cli
{
  "blockchain": "SYS",
  "block_hash1": "0cf18712db68be85793dc06cd0a4fbc8edb166157e6847bb3c9f55d462b02837",
  "block_hash2": "52583afcd857e45234e7c8981804b36d13a7b3025c17f3535c3e724542590a79"
}
```
This call is used to retrieve block data for multiple block hashes on the specified blockchain. Currently the maximum request is 50 blocks, although a node may set this limit to less.


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrGetBlocks SYS 0cf18712db68be85793dc06cd0a4fbc8edb166157e6847bb3c9f55d462b02837,52583afcd857e45234e7c8981804b36d13a7b3025c17f3535c3e724542590a79 2
```
<code class="api-call">xrGetBlocks [blockchain] [block_hash1,block_hash2,block_hashN] [node_count]\(optional)</code>

Parameter       | Type    | Description
----------------|---------|-------------
blockchain      | string  | The blockchain, represented by the asset's ticker (BTC, LTC, SYS, etc.).
block_hashN     | string  | The block hashes for the blocks of interest. The hashes must be separated by a comma with no spaces.
node_count      | int     | (Optional Parameter)<br>Defaults to `1` if no `consensus=` setting in `xrouter.conf`.<br>The number of nodes that will be used to route calls and use the majority response from for consensus. 


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : [
    {
      "hash" : "0cf18712db68be85793dc06cd0a4fbc8edb166157e6847bb3c9f55d462b02837",
      "confirmations" : 390700,
      "size" : 734,
      "height" : 91510,
      "version" : 805306624,
      "versionHex" : "30000100",
      "merkleroot" : "55554df0ea7f66552beffe433d2c2f620bada79063a343ffc23c60c67cdf028f",
      "tx" : [ "55554df0ea7f66552beffe433d2c2f620bada79063a343ffc23c60c67cdf028f" ],
      "time" : 1530894673,
      "mediantime" : 1530894336,
      "nonce" : 0,
      "bits" : "180761db",
      "difficulty" : 148937676133.261200,
      "chainwork" : "00000000000000000000000000000000000000000028ad56864c458781e21970",
      "auxpow" : {
        "tx" : {
          "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff5703dd3408142f5669614254432f626d67706f6f6c2e636f6d2f2cfabe6d6d75654ab7a6b163e7a04e56e43f8e7ca0087f11e8bd6c3c72c69732e849532878040000000000000010d51dbb0b9301409ff96ef9f588c50200ffffffff01c051824a000000001976a914f1c075a01882ae0972f95d3a4177c86c852b7d9188ac00000000",
          "txid" : "c8fc123672fffb3c6a45b94bdd30afc114f282742338809380bc84fe8666d489",
          "size" : 172,
          "version" : 1,
          "locktime" : 0,
          "vin" : [
            {
              "coinbase" : "03dd3408142f5669614254432f626d67706f6f6c2e636f6d2f2cfabe6d6d75654ab7a6b163e7a04e56e43f8e7ca0087f11e8bd6c3c72c69732e849532878040000000000000010d51dbb0b9301409ff96ef9f588c50200",
              "sequence" : 4294967295
            }
          ],
          "vout" : [
            {
              "value" : 12.500546,
              "valueSat" : 1250054592,
              "n" : 0,
              "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 f1c075a01882ae0972f95d3a4177c86c852b7d91 OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "76a914f1c075a01882ae0972f95d3a4177c86c852b7d9188ac",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [ "SjLGSPfmF3kVPA6A213hShYc4aRtiWZ8Wu" ]
              }
            }
          ],
          "blockhash" : "000000000000000004bdb8e3fdf28ffc528f4e6b1871a6177579cbd4dabde955"
        },
        "index" : 0,
        "chainindex" : 2,
        "merklebranch" : [ "a67d749dfa7006b9d66d2284b20a64ebcb8c2e5aae18e355c790b90a060a30c1", "9ab5c057ecf43aa0552c21be4f86b1c9b248fe2dd0821bee38e5a25a22c49c25", "ec11fd79e02fe98bf9f4b624e5460a3221dbae27d24f36c9988dcc5beaf3c592", "dbc42b7360a7165b277f73094a366e951d3ac6d6a1e9493d838d35372e5e846b", "c7260dbfb6b196bd2e4e4dbde1799592627a3e6fa384e6692be8387a9251e5e2" ],
        "chainmerklebranch" : [ "89af0bc973bc0308f14c5c50c3d75f68783eab40a4688064cd56cfaed977d138", "5360cbf0991b4b0e09d589631c232d1f229104faad9e65375695230c47bc2493" ],
        "parentblock" : "0000002024653d98f3eace4b66b6269fa9205e08b647e23cf7880e0000000000000000003feae4c877b2f8b73f6f92a589ed8870fe98abb3551f1539373597f5b44514ada9993f5bb1830118d5793688"
      },
      "previousblockhash" : "ba53d715abf76ac8adc025120ffb48b2331f3a1b9b7d01167ffd7dcd23934390",
      "nextblockhash" : "cd3a236542491089bffd8ba49cbe1d11a20dd4c4ad188307027e60c710732409"
    },
    {
      "hash" : "52583afcd857e45234e7c8981804b36d13a7b3025c17f3535c3e724542590a79",
      "confirmations" : 115,
      "size" : 1073,
      "height" : 482107,
      "version" : 805306624,
      "versionHex" : "30000100",
      "merkleroot" : "d1de796d10826dcc97af3d9683711da0f8e8aad996277034bd9b11631ce0d1f2",
      "tx" : [ "d1de796d10826dcc97af3d9683711da0f8e8aad996277034bd9b11631ce0d1f2" ],
      "time" : 1555482589,
      "mediantime" : 1555482282,
      "nonce" : 0,
      "bits" : "1805ec8b",
      "difficulty" : 185600202264.865814,
      "chainwork" : "000000000000000000000000000000000000000000f8bf3057c91ffc80efc4fc",
      "auxpow" : {
        "tx" : {
          "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff5b0355ba08182f5669614254432f4d696e65642062792067656e6469722f2cfabe6d6daa2651dfd7b34e3cf5b87fd5ab3b70a78d23194d2269c502280de7193d73790f10000000000000001056691e0c261e0b871f9d92be0000259bffffffff0206baf84b000000001976a914536ffa992491508dca0354e52f32a3a7a679a53a88ac0000000000000000266a24aa21a9ed6e46f58e0097a34b65b4d332ebde4a0cedb300fab27549b3ee1c09fd2d7875d300000000",
          "txid" : "94ce9c796176df4cbe67695c0bfcd63587b1b664e23b716233980dc07d2874d7",
          "size" : 223,
          "version" : 1,
          "locktime" : 0,
          "vin" : [
            {
              "coinbase" : "0355ba08182f5669614254432f4d696e65642062792067656e6469722f2cfabe6d6daa2651dfd7b34e3cf5b87fd5ab3b70a78d23194d2269c502280de7193d73790f10000000000000001056691e0c261e0b871f9d92be0000259b",
              "sequence" : 4294967295
            }
          ],
          "vout" : [
            {
              "value" : 12.745918,
              "valueSat" : 1274591750,
              "n" : 0,
              "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 536ffa992491508dca0354e52f32a3a7a679a53a OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "76a914536ffa992491508dca0354e52f32a3a7a679a53a88ac",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [ "SUuBGCD7Ff3C2ozR6osYguPeXNho98S5qR" ]
              }
            },
            {
              "value" : 0.000000,
              "valueSat" : 0,
              "n" : 1,
              "scriptPubKey" : {
                "asm" : "OP_RETURN aa21a9ed6e46f58e0097a34b65b4d332ebde4a0cedb300fab27549b3ee1c09fd2d7875d3",
                "hex" : "6a24aa21a9ed6e46f58e0097a34b65b4d332ebde4a0cedb300fab27549b3ee1c09fd2d7875d3",
                "type" : "nulldata"
              }
            }
          ],
          "blockhash" : "000000000000000000bcf15d287a324f073b1e0239452a6d4d513c27dc73150a"
        },
        "index" : 0,
        "chainindex" : 14,
        "merklebranch" : [ "b468e6225dbecfbbf9f86cc8414afb70ae1fade27008e5f8080f74341dbc103c", "c5ec7ac7fb9405564c2afffcd0279c8237a883f0e414011cfe4439572fd8f476", "72731cb13f6ef60cc4647c5c98a48a67ae83a8b2db8e6fcc3ffc2e5cb33e45e3", "3975f42ce7c1be1dfd2118fd7bce4bc2b67993a5f03f6389089e86446e391533", "1fa4527c16a1fd05735914c568d0265ec92e9608890d22ece6f1edcb989fad1a", "018223df5ec9acd3a1ce30e445477d8ca368a6a1dcba8616f5178c9e41287f42", "86dff2db9b12c6afd1cdcd60415e5d19356eaffe5d938fbc2b6147c2430363f5", "77d0cb1a7e0d3cebc57380bd3546a45923107aff5b8bb969d0272d2fd6709280", "04a2a80fdd3a2ccf24642fec547bbb7415922ee6348a0c5b8efbe25611120258", "822ee423a7c096e070117f7573824922c2d612027278d8f330977abf75657ddc", "b7bb4ddf8d79219e9e0e8bb599c23e943317e3c4d8f0e31bd712a08edaace138", "47828c64e2141a2417f271a22dc80fa716604ce044fc0db1827a9a3818c0ca22" ],
        "chainmerklebranch" : [ "0000000000000000000000000000000000000000000000000000000000000000", "f98c4e9736d8eb8bb46299798906695c755369a3df99a93ffdded1713f1cf6e2", "36e70e1ae90b14efb14d594dff72647f862e97c4f704ad0d609041bb7d73aaa5", "63e9f83bff026dcfa4d967e727ceb35dbdf7e02225d88f2ce2f3842f10c16481" ],
        "parentblock" : "000000208100da1ca9afee2f159a537c64aa7fcfce77dbdce07009000000000000000000d63acfaac8ae4bc195f8ba076f306c45ec06a8a740a182263e96732d842984cee6c7b65c1d072c174919714b"
      },
      "previousblockhash" : "abe70792116fd7806b211b6d67cc6bfa1490aa0dea71587d0bbbe9ee435c9c2a",
      "nextblockhash" : "2d7adca3fe7a51018b909aa1c63a7669a75a6729a2da66a08de19d4f13e66032"
    }
  ],
  "allreplies" : [
    {
      "nodepubkey" : "02c6c79a75846fd9bb064788b03145e347fa5464558fa9030ebb009df2833369f0",
      "score" : 242,
      "reply" : [
        {
          "hash" : "0cf18712db68be85793dc06cd0a4fbc8edb166157e6847bb3c9f55d462b02837",
          "confirmations" : 390700,
          "size" : 734,
          "height" : 91510,
          "version" : 805306624,
          "versionHex" : "30000100",
          "merkleroot" : "55554df0ea7f66552beffe433d2c2f620bada79063a343ffc23c60c67cdf028f",
          "tx" : [ "55554df0ea7f66552beffe433d2c2f620bada79063a343ffc23c60c67cdf028f" ],
          "time" : 1530894673,
          "mediantime" : 1530894336,
          "nonce" : 0,
          "bits" : "180761db",
          "difficulty" : 148937676133.261200,
          "chainwork" : "00000000000000000000000000000000000000000028ad56864c458781e21970",
          "auxpow" : {
            "tx" : {
              "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff5703dd3408142f5669614254432f626d67706f6f6c2e636f6d2f2cfabe6d6d75654ab7a6b163e7a04e56e43f8e7ca0087f11e8bd6c3c72c69732e849532878040000000000000010d51dbb0b9301409ff96ef9f588c50200ffffffff01c051824a000000001976a914f1c075a01882ae0972f95d3a4177c86c852b7d9188ac00000000",
              "txid" : "c8fc123672fffb3c6a45b94bdd30afc114f282742338809380bc84fe8666d489",
              "size" : 172,
              "version" : 1,
              "locktime" : 0,
              "vin" : [
                {
                  "coinbase" : "03dd3408142f5669614254432f626d67706f6f6c2e636f6d2f2cfabe6d6d75654ab7a6b163e7a04e56e43f8e7ca0087f11e8bd6c3c72c69732e849532878040000000000000010d51dbb0b9301409ff96ef9f588c50200",
                  "sequence" : 4294967295
                }
              ],
              "vout" : [
                {
                  "value" : 12.500546,
                  "valueSat" : 1250054592,
                  "n" : 0,
                  "scriptPubKey" : {
                    "asm" : "OP_DUP OP_HASH160 f1c075a01882ae0972f95d3a4177c86c852b7d91 OP_EQUALVERIFY OP_CHECKSIG",
                    "hex" : "76a914f1c075a01882ae0972f95d3a4177c86c852b7d9188ac",
                    "reqSigs" : 1,
                    "type" : "pubkeyhash",
                    "addresses" : [ "SjLGSPfmF3kVPA6A213hShYc4aRtiWZ8Wu" ]
                  }
                }
              ],
                "blockhash" : "000000000000000004bdb8e3fdf28ffc528f4e6b1871a6177579cbd4dabde955"
            },
            "index" : 0,
            "chainindex" : 2,
            "merklebranch" : [ "a67d749dfa7006b9d66d2284b20a64ebcb8c2e5aae18e355c790b90a060a30c1", "9ab5c057ecf43aa0552c21be4f86b1c9b248fe2dd0821bee38e5a25a22c49c25", "ec11fd79e02fe98bf9f4b624e5460a3221dbae27d24f36c9988dcc5beaf3c592", "dbc42b7360a7165b277f73094a366e951d3ac6d6a1e9493d838d35372e5e846b", "c7260dbfb6b196bd2e4e4dbde1799592627a3e6fa384e6692be8387a9251e5e2" ],
            "chainmerklebranch" : [ "89af0bc973bc0308f14c5c50c3d75f68783eab40a4688064cd56cfaed977d138", "5360cbf0991b4b0e09d589631c232d1f229104faad9e65375695230c47bc2493" ],
            "parentblock" : "0000002024653d98f3eace4b66b6269fa9205e08b647e23cf7880e0000000000000000003feae4c877b2f8b73f6f92a589ed8870fe98abb3551f1539373597f5b44514ada9993f5bb1830118d5793688"
          },
          "previousblockhash" : "ba53d715abf76ac8adc025120ffb48b2331f3a1b9b7d01167ffd7dcd23934390",
          "nextblockhash" : "cd3a236542491089bffd8ba49cbe1d11a20dd4c4ad188307027e60c710732409"
        },
        {
          "hash" : "52583afcd857e45234e7c8981804b36d13a7b3025c17f3535c3e724542590a79",
          "confirmations" : 115,
          "size" : 1073,
          "height" : 482107,
          "version" : 805306624,
          "versionHex" : "30000100",
          "merkleroot" : "d1de796d10826dcc97af3d9683711da0f8e8aad996277034bd9b11631ce0d1f2",
          "tx" : [ "d1de796d10826dcc97af3d9683711da0f8e8aad996277034bd9b11631ce0d1f2" ],
          "time" : 1555482589,
          "mediantime" : 1555482282,
          "nonce" : 0,
          "bits" : "1805ec8b",
          "difficulty" : 185600202264.865814,
          "chainwork" : "000000000000000000000000000000000000000000f8bf3057c91ffc80efc4fc",
          "auxpow" : {
            "tx" : {
              "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff5b0355ba08182f5669614254432f4d696e65642062792067656e6469722f2cfabe6d6daa2651dfd7b34e3cf5b87fd5ab3b70a78d23194d2269c502280de7193d73790f10000000000000001056691e0c261e0b871f9d92be0000259bffffffff0206baf84b000000001976a914536ffa992491508dca0354e52f32a3a7a679a53a88ac0000000000000000266a24aa21a9ed6e46f58e0097a34b65b4d332ebde4a0cedb300fab27549b3ee1c09fd2d7875d300000000",
              "txid" : "94ce9c796176df4cbe67695c0bfcd63587b1b664e23b716233980dc07d2874d7",
              "size" : 223,
              "version" : 1,
              "locktime" : 0,
              "vin" : [
                {
                  "coinbase" : "0355ba08182f5669614254432f4d696e65642062792067656e6469722f2cfabe6d6daa2651dfd7b34e3cf5b87fd5ab3b70a78d23194d2269c502280de7193d73790f10000000000000001056691e0c261e0b871f9d92be0000259b",
                  "sequence" : 4294967295
                }
              ],
              "vout" : [
                {
                  "value" : 12.745918,
                  "valueSat" : 1274591750,
                  "n" : 0,
                  "scriptPubKey" : {
                    "asm" : "OP_DUP OP_HASH160 536ffa992491508dca0354e52f32a3a7a679a53a OP_EQUALVERIFY OP_CHECKSIG",
                    "hex" : "76a914536ffa992491508dca0354e52f32a3a7a679a53a88ac",
                    "reqSigs" : 1,
                    "type" : "pubkeyhash",
                    "addresses" : [ "SUuBGCD7Ff3C2ozR6osYguPeXNho98S5qR" ]
                  }
                },
                {
                  "value" : 0.000000,
                  "valueSat" : 0,
                  "n" : 1,
                  "scriptPubKey" : {
                    "asm" : "OP_RETURN aa21a9ed6e46f58e0097a34b65b4d332ebde4a0cedb300fab27549b3ee1c09fd2d7875d3",
                    "hex" : "6a24aa21a9ed6e46f58e0097a34b65b4d332ebde4a0cedb300fab27549b3ee1c09fd2d7875d3",
                    "type" : "nulldata"
                  }
                }
              ],
              "blockhash" : "000000000000000000bcf15d287a324f073b1e0239452a6d4d513c27dc73150a"
            },
            "index" : 0,
            "chainindex" : 14,
            "merklebranch" : [ "b468e6225dbecfbbf9f86cc8414afb70ae1fade27008e5f8080f74341dbc103c", "c5ec7ac7fb9405564c2afffcd0279c8237a883f0e414011cfe4439572fd8f476", "72731cb13f6ef60cc4647c5c98a48a67ae83a8b2db8e6fcc3ffc2e5cb33e45e3", "3975f42ce7c1be1dfd2118fd7bce4bc2b67993a5f03f6389089e86446e391533", "1fa4527c16a1fd05735914c568d0265ec92e9608890d22ece6f1edcb989fad1a", "018223df5ec9acd3a1ce30e445477d8ca368a6a1dcba8616f5178c9e41287f42", "86dff2db9b12c6afd1cdcd60415e5d19356eaffe5d938fbc2b6147c2430363f5", "77d0cb1a7e0d3cebc57380bd3546a45923107aff5b8bb969d0272d2fd6709280", "04a2a80fdd3a2ccf24642fec547bbb7415922ee6348a0c5b8efbe25611120258", "822ee423a7c096e070117f7573824922c2d612027278d8f330977abf75657ddc", "b7bb4ddf8d79219e9e0e8bb599c23e943317e3c4d8f0e31bd712a08edaace138", "47828c64e2141a2417f271a22dc80fa716604ce044fc0db1827a9a3818c0ca22" ],
            "chainmerklebranch" : [ "0000000000000000000000000000000000000000000000000000000000000000", "f98c4e9736d8eb8bb46299798906695c755369a3df99a93ffdded1713f1cf6e2", "36e70e1ae90b14efb14d594dff72647f862e97c4f704ad0d609041bb7d73aaa5", "63e9f83bff026dcfa4d967e727ceb35dbdf7e02225d88f2ce2f3842f10c16481" ],
            "parentblock" : "000000208100da1ca9afee2f159a537c64aa7fcfce77dbdce07009000000000000000000d63acfaac8ae4bc195f8ba076f306c45ec06a8a740a182263e96732d842984cee6c7b65c1d072c174919714b"
          },
          "previousblockhash" : "abe70792116fd7806b211b6d67cc6bfa1490aa0dea71587d0bbbe9ee435c9c2a",
          "nextblockhash" : "2d7adca3fe7a51018b909aa1c63a7669a75a6729a2da66a08de19d4f13e66032"
        }
      ]
    },
    {
      "nodepubkey" : "0370874cad6252bb94afa9a253c90122760ce2862e623b515e57bfe0697f3fc515",
      "score" : 292,
      "reply" : [
        {
          "hash" : "0cf18712db68be85793dc06cd0a4fbc8edb166157e6847bb3c9f55d462b02837",
          "confirmations" : 390700,
          "size" : 734,
          "height" : 91510,
          "version" : 805306624,
          "versionHex" : "30000100",
          "merkleroot" : "55554df0ea7f66552beffe433d2c2f620bada79063a343ffc23c60c67cdf028f",
          "tx" : [ "55554df0ea7f66552beffe433d2c2f620bada79063a343ffc23c60c67cdf028f" ],
          "time" : 1530894673,
          "mediantime" : 1530894336,
          "nonce" : 0,
          "bits" : "180761db",
          "difficulty" : 148937676133.261200,
          "chainwork" : "00000000000000000000000000000000000000000028ad56864c458781e21970",
          "auxpow" : {
            "tx" : {
              "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff5703dd3408142f5669614254432f626d67706f6f6c2e636f6d2f2cfabe6d6d75654ab7a6b163e7a04e56e43f8e7ca0087f11e8bd6c3c72c69732e849532878040000000000000010d51dbb0b9301409ff96ef9f588c50200ffffffff01c051824a000000001976a914f1c075a01882ae0972f95d3a4177c86c852b7d9188ac00000000",
              "txid" : "c8fc123672fffb3c6a45b94bdd30afc114f282742338809380bc84fe8666d489",
              "size" : 172,
              "version" : 1,
              "locktime" : 0,
              "vin" : [
                {
                  "coinbase" : "03dd3408142f5669614254432f626d67706f6f6c2e636f6d2f2cfabe6d6d75654ab7a6b163e7a04e56e43f8e7ca0087f11e8bd6c3c72c69732e849532878040000000000000010d51dbb0b9301409ff96ef9f588c50200",
                  "sequence" : 4294967295
                }
              ],
              "vout" : [
                {
                  "value" : 12.500546,
                  "valueSat" : 1250054592,
                  "n" : 0,
                  "scriptPubKey" : {
                    "asm" : "OP_DUP OP_HASH160 f1c075a01882ae0972f95d3a4177c86c852b7d91 OP_EQUALVERIFY OP_CHECKSIG",
                    "hex" : "76a914f1c075a01882ae0972f95d3a4177c86c852b7d9188ac",
                    "reqSigs" : 1,
                    "type" : "pubkeyhash",
                    "addresses" : [ "SjLGSPfmF3kVPA6A213hShYc4aRtiWZ8Wu" ]
                  }
                }
              ],
              "blockhash" : "000000000000000004bdb8e3fdf28ffc528f4e6b1871a6177579cbd4dabde955"
            },
            "index" : 0,
            "chainindex" : 2,
            "merklebranch" : [ "a67d749dfa7006b9d66d2284b20a64ebcb8c2e5aae18e355c790b90a060a30c1", "9ab5c057ecf43aa0552c21be4f86b1c9b248fe2dd0821bee38e5a25a22c49c25", "ec11fd79e02fe98bf9f4b624e5460a3221dbae27d24f36c9988dcc5beaf3c592", "dbc42b7360a7165b277f73094a366e951d3ac6d6a1e9493d838d35372e5e846b", "c7260dbfb6b196bd2e4e4dbde1799592627a3e6fa384e6692be8387a9251e5e2" ],
            "chainmerklebranch" : [ "89af0bc973bc0308f14c5c50c3d75f68783eab40a4688064cd56cfaed977d138", "5360cbf0991b4b0e09d589631c232d1f229104faad9e65375695230c47bc2493" ],
            "parentblock" : "0000002024653d98f3eace4b66b6269fa9205e08b647e23cf7880e0000000000000000003feae4c877b2f8b73f6f92a589ed8870fe98abb3551f1539373597f5b44514ada9993f5bb1830118d5793688"
          },
          "previousblockhash" : "ba53d715abf76ac8adc025120ffb48b2331f3a1b9b7d01167ffd7dcd23934390",
          "nextblockhash" : "cd3a236542491089bffd8ba49cbe1d11a20dd4c4ad188307027e60c710732409"
        },
        {
          "hash" : "52583afcd857e45234e7c8981804b36d13a7b3025c17f3535c3e724542590a79",
          "confirmations" : 115,
          "size" : 1073,
          "height" : 482107,
          "version" : 805306624,
          "versionHex" : "30000100",
          "merkleroot" : "d1de796d10826dcc97af3d9683711da0f8e8aad996277034bd9b11631ce0d1f2",
          "tx" : [ "d1de796d10826dcc97af3d9683711da0f8e8aad996277034bd9b11631ce0d1f2" ],
          "time" : 1555482589,
          "mediantime" : 1555482282,
          "nonce" : 0,
          "bits" : "1805ec8b",
          "difficulty" : 185600202264.865814,
          "chainwork" : "000000000000000000000000000000000000000000f8bf3057c91ffc80efc4fc",
          "auxpow" : {
            "tx" : {
              "hex" : "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff5b0355ba08182f5669614254432f4d696e65642062792067656e6469722f2cfabe6d6daa2651dfd7b34e3cf5b87fd5ab3b70a78d23194d2269c502280de7193d73790f10000000000000001056691e0c261e0b871f9d92be0000259bffffffff0206baf84b000000001976a914536ffa992491508dca0354e52f32a3a7a679a53a88ac0000000000000000266a24aa21a9ed6e46f58e0097a34b65b4d332ebde4a0cedb300fab27549b3ee1c09fd2d7875d300000000",
              "txid" : "94ce9c796176df4cbe67695c0bfcd63587b1b664e23b716233980dc07d2874d7",
              "size" : 223,
              "version" : 1,
              "locktime" : 0,
              "vin" : [
                {
                  "coinbase" : "0355ba08182f5669614254432f4d696e65642062792067656e6469722f2cfabe6d6daa2651dfd7b34e3cf5b87fd5ab3b70a78d23194d2269c502280de7193d73790f10000000000000001056691e0c261e0b871f9d92be0000259b",
                  "sequence" : 4294967295
                }
              ],
              "vout" : [
                {
                  "value" : 12.745918,
                  "valueSat" : 1274591750,
                  "n" : 0,
                  "scriptPubKey" : {
                    "asm" : "OP_DUP OP_HASH160 536ffa992491508dca0354e52f32a3a7a679a53a OP_EQUALVERIFY OP_CHECKSIG",
                    "hex" : "76a914536ffa992491508dca0354e52f32a3a7a679a53a88ac",
                    "reqSigs" : 1,
                    "type" : "pubkeyhash",
                    "addresses" : [ "SUuBGCD7Ff3C2ozR6osYguPeXNho98S5qR" ]
                  }
                },
                {
                  "value" : 0.000000,
                  "valueSat" : 0,
                  "n" : 1,
                  "scriptPubKey" : {
                    "asm" : "OP_RETURN aa21a9ed6e46f58e0097a34b65b4d332ebde4a0cedb300fab27549b3ee1c09fd2d7875d3",
                    "hex" : "6a24aa21a9ed6e46f58e0097a34b65b4d332ebde4a0cedb300fab27549b3ee1c09fd2d7875d3",
                    "type" : "nulldata"
                  }
                }
              ],
              "blockhash" : "000000000000000000bcf15d287a324f073b1e0239452a6d4d513c27dc73150a"
            },
            "index" : 0,
            "chainindex" : 14,
            "merklebranch" : [ "b468e6225dbecfbbf9f86cc8414afb70ae1fade27008e5f8080f74341dbc103c", "c5ec7ac7fb9405564c2afffcd0279c8237a883f0e414011cfe4439572fd8f476", "72731cb13f6ef60cc4647c5c98a48a67ae83a8b2db8e6fcc3ffc2e5cb33e45e3", "3975f42ce7c1be1dfd2118fd7bce4bc2b67993a5f03f6389089e86446e391533", "1fa4527c16a1fd05735914c568d0265ec92e9608890d22ece6f1edcb989fad1a", "018223df5ec9acd3a1ce30e445477d8ca368a6a1dcba8616f5178c9e41287f42", "86dff2db9b12c6afd1cdcd60415e5d19356eaffe5d938fbc2b6147c2430363f5", "77d0cb1a7e0d3cebc57380bd3546a45923107aff5b8bb969d0272d2fd6709280", "04a2a80fdd3a2ccf24642fec547bbb7415922ee6348a0c5b8efbe25611120258", "822ee423a7c096e070117f7573824922c2d612027278d8f330977abf75657ddc", "b7bb4ddf8d79219e9e0e8bb599c23e943317e3c4d8f0e31bd712a08edaace138", "47828c64e2141a2417f271a22dc80fa716604ce044fc0db1827a9a3818c0ca22" ],
            "chainmerklebranch" : [ "0000000000000000000000000000000000000000000000000000000000000000", "f98c4e9736d8eb8bb46299798906695c755369a3df99a93ffdded1713f1cf6e2", "36e70e1ae90b14efb14d594dff72647f862e97c4f704ad0d609041bb7d73aaa5", "63e9f83bff026dcfa4d967e727ceb35dbdf7e02225d88f2ce2f3842f10c16481" ],
            "parentblock" : "000000208100da1ca9afee2f159a537c64aa7fcfce77dbdce07009000000000000000000d63acfaac8ae4bc195f8ba076f306c45ec06a8a740a182263e96732d842984cee6c7b65c1d072c174919714b"
          },
          "previousblockhash" : "abe70792116fd7806b211b6d67cc6bfa1490aa0dea71587d0bbbe9ee435c9c2a",
          "nextblockhash" : "2d7adca3fe7a51018b909aa1c63a7669a75a6729a2da66a08de19d4f13e66032"
        }
      ]
    }
  ],
  "uuid" : "5404cc31-2b01-48eb-8599-e6adcd8f9216"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | array   | An array containing objects with the block data for each the request block on the specified blockchain. If using a `node_count` greater than `1`, this returns the most common reply within `allreplies`. If there is a tie then one is chosen, or if one is an error then the non-error is chosen.
allreplies*     | array   | An array of objects with responses from each node. This can be useful if you wanted to do your own analysis/filtering of the responses.
nodepubkey*     | string  | The node ID.
score*          | int     | The respective node's score based on quality of service. A score of `-200` will ban the node for a 24hr period. You can change the ban threshold with the `xrouterbanscore` setting in `blocknetdx.conf`. See [node scoring](#xrouter-node-scoring) for more details.
reply*          | array   | An array containing objects with the block data for each the request block on the specified blockchain from the respective node.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).

\* This is only returned if using a `node_count` greater than `1`.










## xrDecodeRawTransaction

> Sample Data

```cli
{
  "spvwallet": "SYS",
  "tx_hex": "01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff640363b1082cfabe6d6df75a401bb4e29d77b2162d476557c4bc879380dad926e44c1b2a7c49893a3cc108000000f09f909f000f4d696e6564206279206c616e636163000000000000000000000000000000000000000000000000000000000500c5a00000000000000330952d4b000000001976a914c825a1ecf2a6830c4401620c3a16f1995057c2ab88ac00000000000000002f6a24aa21a9ed2ac560369b71e58f0618a79d2f03bcf7283945e20eef7813de376a6f503448c108000000000000000000000000000000002c6a4c2952534b424c4f434b3a81c66dc456024e97e518ac2ab3ec51bf85b294b9a140c6e9376a040607edc39ddc962c3b"
}
```
This call is used to decode a specified bockchain transaction HEX. It requires the blockchain to be SPV wallet supported.

### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrDecodeRawTransaction SYS 01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff640363b1082cfabe6d6df75a401bb4e29d77b2162d476557c4bc879380dad926e44c1b2a7c49893a3cc108000000f09f909f000f4d696e6564206279206c616e636163000000000000000000000000000000000000000000000000000000000500c5a00000000000000330952d4b000000001976a914c825a1ecf2a6830c4401620c3a16f1995057c2ab88ac00000000000000002f6a24aa21a9ed2ac560369b71e58f0618a79d2f03bcf7283945e20eef7813de376a6f503448c108000000000000000000000000000000002c6a4c2952534b424c4f434b3a81c66dc456024e97e518ac2ab3ec51bf85b294b9a140c6e9376a040607edc39ddc962c3b 2
```
<code class="api-call">xrDecodeRawTransaction [blockchain] [tx_hex] [node_count]\(optional)</code>

Parameter       | Type    | Description
----------------|---------|-------------
blockchain      | string  | The blockchain, represented by the asset's ticker (BTC, LTC, SYS, etc.).
tx_hex          | string  | The raw transaction HEX to decode.
node_count      | int     | (Optional Parameter)<br>Defaults to `1` if no `consensus=` setting in `xrouter.conf`.<br>The number of nodes that will be used to route calls and use the majority response from for consensus.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : {
    "txid" : "aa1838dee180e5c4ad5041ded25aa5d88c41bedb1c339b42f54dbad0e7563c5e",
    "size" : 294,
    "version" : 1,
    "locktime" : 992777948,
    "vin" : [
      {
        "coinbase" : "0363b1082cfabe6d6df75a401bb4e29d77b2162d476557c4bc879380dad926e44c1b2a7c49893a3cc108000000f09f909f000f4d696e6564206279206c616e636163000000000000000000000000000000000000000000000000000000000500c5a00000",
        "sequence" : 0
      }
    ],
    "vout" : [
      {
        "value" : 12.612785,
        "valueSat" : 1261278512,
        "n" : 0,
        "scriptPubKey" : {
          "asm" : "OP_DUP OP_HASH160 c825a1ecf2a6830c4401620c3a16f1995057c2ab OP_EQUALVERIFY OP_CHECKSIG",
          "hex" : "76a914c825a1ecf2a6830c4401620c3a16f1995057c2ab88ac",
          "reqSigs" : 1,
            "type" : "pubkeyhash",
              "addresses" : [ "SfYHFxiGv4mRtUfQVHxfMWknEt53Bjj286" ]
            }
        },
      {
        "value" : 0.000000,
        "valueSat" : 0,
        "n" : 1,
        "scriptPubKey" : {
          "asm" : "OP_RETURN aa21a9ed2ac560369b71e58f0618a79d2f03bcf7283945e20eef7813de376a6f503448c1 0000000000000000",
          "hex" : "6a24aa21a9ed2ac560369b71e58f0618a79d2f03bcf7283945e20eef7813de376a6f503448c1080000000000000000",
          "type" : "nulldata"
        }
      },
      {
        "value" : 0.000000,
        "valueSat" : 0,
        "n" : 2,
        "scriptPubKey" : {
          "asm" : "OP_RETURN 52534b424c4f434b3a81c66dc456024e97e518ac2ab3ec51bf85b294b9a140c6e9376a040607edc39d",
          "hex" : "6a4c2952534b424c4f434b3a81c66dc456024e97e518ac2ab3ec51bf85b294b9a140c6e9376a040607edc39d",
          "type" : "nulldata"
        }
      }
    ]
  },
  "allreplies" : [
    {
      "nodepubkey" : "02c6c79a75846fd9bb064788b03145e347fa5464558fa9030ebb009df2833369f0",
      "score" : 250,
      "reply" : {
        "txid" : "aa1838dee180e5c4ad5041ded25aa5d88c41bedb1c339b42f54dbad0e7563c5e",
        "size" : 294,
        "version" : 1,
        "locktime" : 992777948,
        "vin" : [
          {
            "coinbase" : "0363b1082cfabe6d6df75a401bb4e29d77b2162d476557c4bc879380dad926e44c1b2a7c49893a3cc108000000f09f909f000f4d696e6564206279206c616e636163000000000000000000000000000000000000000000000000000000000500c5a00000",
            "sequence" : 0
          }
        ],
        "vout" : [
          {
            "value" : 12.612785,
            "valueSat" : 1261278512,
            "n" : 0,
            "scriptPubKey" : {
              "asm" : "OP_DUP OP_HASH160 c825a1ecf2a6830c4401620c3a16f1995057c2ab OP_EQUALVERIFY OP_CHECKSIG",
              "hex" : "76a914c825a1ecf2a6830c4401620c3a16f1995057c2ab88ac",
              "reqSigs" : 1,
                "type" : "pubkeyhash",
                  "addresses" : [ "SfYHFxiGv4mRtUfQVHxfMWknEt53Bjj286" ]
                }
            },
          {
            "value" : 0.000000,
            "valueSat" : 0,
            "n" : 1,
            "scriptPubKey" : {
              "asm" : "OP_RETURN aa21a9ed2ac560369b71e58f0618a79d2f03bcf7283945e20eef7813de376a6f503448c1 0000000000000000",
              "hex" : "6a24aa21a9ed2ac560369b71e58f0618a79d2f03bcf7283945e20eef7813de376a6f503448c1080000000000000000",
              "type" : "nulldata"
            }
          },
          {
            "value" : 0.000000,
            "valueSat" : 0,
            "n" : 2,
            "scriptPubKey" : {
              "asm" : "OP_RETURN 52534b424c4f434b3a81c66dc456024e97e518ac2ab3ec51bf85b294b9a140c6e9376a040607edc39d",
              "hex" : "6a4c2952534b424c4f434b3a81c66dc456024e97e518ac2ab3ec51bf85b294b9a140c6e9376a040607edc39d",
              "type" : "nulldata"
            }
          }
        ]
      }
    },
    {
      "nodepubkey" : "0370874cad6252bb94afa9a253c90122760ce2862e623b515e57bfe0697f3fc515",
      "score" : 300,
      "reply" : {
        "txid" : "aa1838dee180e5c4ad5041ded25aa5d88c41bedb1c339b42f54dbad0e7563c5e",
        "size" : 294,
        "version" : 1,
        "locktime" : 992777948,
        "vin" : [
          {
            "coinbase" : "0363b1082cfabe6d6df75a401bb4e29d77b2162d476557c4bc879380dad926e44c1b2a7c49893a3cc108000000f09f909f000f4d696e6564206279206c616e636163000000000000000000000000000000000000000000000000000000000500c5a00000",
            "sequence" : 0
          }
        ],
        "vout" : [
          {
            "value" : 12.612785,
            "valueSat" : 1261278512,
            "n" : 0,
            "scriptPubKey" : {
              "asm" : "OP_DUP OP_HASH160 c825a1ecf2a6830c4401620c3a16f1995057c2ab OP_EQUALVERIFY OP_CHECKSIG",
              "hex" : "76a914c825a1ecf2a6830c4401620c3a16f1995057c2ab88ac",
              "reqSigs" : 1,
                "type" : "pubkeyhash",
                  "addresses" : [ "SfYHFxiGv4mRtUfQVHxfMWknEt53Bjj286" ]
                }
            },
          {
            "value" : 0.000000,
            "valueSat" : 0,
            "n" : 1,
            "scriptPubKey" : {
              "asm" : "OP_RETURN aa21a9ed2ac560369b71e58f0618a79d2f03bcf7283945e20eef7813de376a6f503448c1 0000000000000000",
              "hex" : "6a24aa21a9ed2ac560369b71e58f0618a79d2f03bcf7283945e20eef7813de376a6f503448c1080000000000000000",
              "type" : "nulldata"
            }
          },
          {
            "value" : 0.000000,
            "valueSat" : 0,
            "n" : 2,
            "scriptPubKey" : {
              "asm" : "OP_RETURN 52534b424c4f434b3a81c66dc456024e97e518ac2ab3ec51bf85b294b9a140c6e9376a040607edc39d",
              "hex" : "6a4c2952534b424c4f434b3a81c66dc456024e97e518ac2ab3ec51bf85b294b9a140c6e9376a040607edc39d",
              "type" : "nulldata"
            }
          }
        ]
      }
    }
  ],
  "uuid" : "95e612f1-3505-493d-8097-452052223417"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | object  | An object containing the decoded transaction data. If using a `node_count` greater than `1`, this returns the most common reply within `allreplies`. If there is a tie then one is chosen, or if one is an error then the non-error is chosen.
allreplies*     | array   | An array of objects with responses from each node. This can be useful if you wanted to do your own analysis/filtering of the responses.
nodepubkey*     | string  | The node ID.
score*          | int     | The respective node's score based on quality of service. A score of `-200` will ban the node for a 24hr period. You can change the ban threshold with the `xrouterbanscore` setting in `blocknetdx.conf`. See [node scoring](#xrouter-node-scoring) for more details.
reply*          | object  | An object containing the decoded transaction data from the respective node.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).

\* This is only returned if using a `node_count` greater than `1`.










## xrGetTransaction

> Sample Data

```cli
{
  "blockchain": "SYS",
  "txid": "9e5db236f75babe4e28c17f0ed1eddbcfdb5bde8a69750e1a4952d110c620e51"
}
```
This call is used to retrieve the transaction data for the specified transaction ID (hash) and blockchain.


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrGetTransaction SYS 9e5db236f75babe4e28c17f0ed1eddbcfdb5bde8a69750e1a4952d110c620e51 2
```
<code class="api-call">xrGetTransaction [blockchain] [txid] [node_count]\(optional)</code>

Parameter       | Type    | Description
----------------|---------|-------------
blockchain      | string  | The blockchain, represented by the asset's ticker (BTC, LTC, SYS, etc.).
txid            | string  | The transaction ID(hash) for the transaction of interest.
node_count      | int     | (Optional Parameter)<br>Defaults to `1` if no `consensus=` setting in `xrouter.conf`.<br>The number of nodes that will be used to route calls and use the majority response from for consensus. 


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : {
    "txid" : "9e5db236f75babe4e28c17f0ed1eddbcfdb5bde8a69750e1a4952d110c620e51",
    "size" : 192,
    "version" : 2,
    "locktime" : 0,
    "vin" : [
      {
        "txid" : "10f28315098fff366e2dfba23afacc10ddb5ac30a403d6396cc2d35739f06a79",
        "vout" : 1,
        "scriptSig" : {
          "asm" : "30450221008d807ad2585d4775e57c105d16f12f5eb7925a3f8f66b077f7944ec74e421ed9022067105602af2705d4456ccd68db8b4eb327cdb5e55e7de98d7ba9f1213a75a1ad[ALL] 02e927a2a1d4a613ed3fa73bf1169beaf4e95a6348afac6613e283d80b9bf2abba",
          "hex" : "4830450221008d807ad2585d4775e57c105d16f12f5eb7925a3f8f66b077f7944ec74e421ed9022067105602af2705d4456ccd68db8b4eb327cdb5e55e7de98d7ba9f1213a75a1ad012102e927a2a1d4a613ed3fa73bf1169beaf4e95a6348afac6613e283d80b9bf2abba"
        },
        "sequence" : 4294967295
      }
    ],
    "vout" : [
      {
        "value" : 1.430000,
        "valueSat" : 143000000,
        "n" : 0,
        "scriptPubKey" : {
          "asm" : "OP_DUP OP_HASH160 7be755600f2fd9f3518be6ededb38e2498f51ff5 OP_EQUALVERIFY OP_CHECKSIG",
          "hex" : "76a9147be755600f2fd9f3518be6ededb38e2498f51ff588ac",
          "reqSigs" : 1,
          "type" : "pubkeyhash",
          "addresses" : [ "SYb9Gmcwj1aXUV86cKpnCD8SR7hvZgbKTP" ]
        }
      }
    ]
  },
  "allreplies" : [
    {
      "nodepubkey" : "02c6c79a75846fd9bb064788b03145e347fa5464558fa9030ebb009df2833369f0",
      "score" : 250,
      "reply" : {
        "txid" : "9e5db236f75babe4e28c17f0ed1eddbcfdb5bde8a69750e1a4952d110c620e51",
        "size" : 192,
        "version" : 2,
        "locktime" : 0,
        "vin" : [
          {
            "txid" : "10f28315098fff366e2dfba23afacc10ddb5ac30a403d6396cc2d35739f06a79",
            "vout" : 1,
            "scriptSig" : {
              "asm" : "30450221008d807ad2585d4775e57c105d16f12f5eb7925a3f8f66b077f7944ec74e421ed9022067105602af2705d4456ccd68db8b4eb327cdb5e55e7de98d7ba9f1213a75a1ad[ALL] 02e927a2a1d4a613ed3fa73bf1169beaf4e95a6348afac6613e283d80b9bf2abba",
              "hex" : "4830450221008d807ad2585d4775e57c105d16f12f5eb7925a3f8f66b077f7944ec74e421ed9022067105602af2705d4456ccd68db8b4eb327cdb5e55e7de98d7ba9f1213a75a1ad012102e927a2a1d4a613ed3fa73bf1169beaf4e95a6348afac6613e283d80b9bf2abba"
            },
            "sequence" : 4294967295
          }
        ],
        "vout" : [
          {
            "value" : 1.430000,
            "valueSat" : 143000000,
            "n" : 0,
            "scriptPubKey" : {
              "asm" : "OP_DUP OP_HASH160 7be755600f2fd9f3518be6ededb38e2498f51ff5 OP_EQUALVERIFY OP_CHECKSIG",
              "hex" : "76a9147be755600f2fd9f3518be6ededb38e2498f51ff588ac",
              "reqSigs" : 1,
              "type" : "pubkeyhash",
              "addresses" : [ "SYb9Gmcwj1aXUV86cKpnCD8SR7hvZgbKTP" ]
            }
          }
        ]
      }
    },
    {
      "nodepubkey" : "0370874cad6252bb94afa9a253c90122760ce2862e623b515e57bfe0697f3fc515",
      "score" : 300,
      "reply" : {
        "txid" : "9e5db236f75babe4e28c17f0ed1eddbcfdb5bde8a69750e1a4952d110c620e51",
        "size" : 192,
        "version" : 2,
        "locktime" : 0,
        "vin" : [
          {
            "txid" : "10f28315098fff366e2dfba23afacc10ddb5ac30a403d6396cc2d35739f06a79",
            "vout" : 1,
            "scriptSig" : {
              "asm" : "30450221008d807ad2585d4775e57c105d16f12f5eb7925a3f8f66b077f7944ec74e421ed9022067105602af2705d4456ccd68db8b4eb327cdb5e55e7de98d7ba9f1213a75a1ad[ALL] 02e927a2a1d4a613ed3fa73bf1169beaf4e95a6348afac6613e283d80b9bf2abba",
              "hex" : "4830450221008d807ad2585d4775e57c105d16f12f5eb7925a3f8f66b077f7944ec74e421ed9022067105602af2705d4456ccd68db8b4eb327cdb5e55e7de98d7ba9f1213a75a1ad012102e927a2a1d4a613ed3fa73bf1169beaf4e95a6348afac6613e283d80b9bf2abba"
            },
            "sequence" : 4294967295
          }
        ],
        "vout" : [
          {
            "value" : 1.430000,
            "valueSat" : 143000000,
            "n" : 0,
            "scriptPubKey" : {
              "asm" : "OP_DUP OP_HASH160 7be755600f2fd9f3518be6ededb38e2498f51ff5 OP_EQUALVERIFY OP_CHECKSIG",
              "hex" : "76a9147be755600f2fd9f3518be6ededb38e2498f51ff588ac",
              "reqSigs" : 1,
              "type" : "pubkeyhash",
              "addresses" : [ "SYb9Gmcwj1aXUV86cKpnCD8SR7hvZgbKTP" ]
            }
          }
        ]
      }
    }
  ],
  "uuid" : "cd7c9b1a-8fd5-46a7-84af-98c0f8850148"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | object  | An object containing the transaction data for the specified transaction ID and blockchain. If using a `node_count` greater than `1`, this returns the most common reply within `allreplies`. If there is a tie then one is chosen, or if one is an error then the non-error is chosen.
allreplies*     | array   | An array of objects with responses from each node. This can be useful if you wanted to do your own analysis/filtering of the responses.
nodepubkey*     | string  | The node ID.
score*          | int     | The respective node's score based on quality of service. A score of `-200` will ban the node for a 24hr period. You can change the ban threshold with the `xrouterbanscore` setting in `blocknetdx.conf`. See [node scoring](#xrouter-node-scoring) for more details.
reply*          | object  | An object containing the transaction data for the specified transaction ID and blockchain from the respective node.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).

\* This is only returned if using a `node_count` greater than `1`.










## xrGetTransactions

> Sample Data

```cli
{
  "blockchain": "SYS",
  "txid1": "f7efcb33c817153dbea86b827380ffce108c6c8e4707356e874d98d0426339bd",
  "txid2": "f63543bb90800a601065d6f6d8380d8a98ac1a9f208921febde4eb0168e6fd8e",
}
```
This call is used to retrieve transaction data for multiple transaction IDs (hashes) on the specified blockchain. Currently the maximum request is 50 transactions, although a node may set this limit to less.


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrGetTransactions SYS f7efcb33c817153dbea86b827380ffce108c6c8e4707356e874d98d0426339bd,f63543bb90800a601065d6f6d8380d8a98ac1a9f208921febde4eb0168e6fd8e 2
```
<code class="api-call">xrGetTransactions [blockchain] [txid1,txid2,txidN] [node_count]\(optional)</code>

Parameter       | Type    | Description
----------------|---------|-------------
blockchain      | string  | The blockchain, represented by the asset's ticker (BTC, LTC, SYS, etc.).
txidN           | string  | The transaction ID (hash) for the transactions of interest. The hashes must be separated by a comma with no spaces.
node_count      | int     | (Optional Parameter)<br>Defaults to `1` if no `consensus=` setting in `xrouter.conf`.<br>The number of nodes that will be used to route calls and use the majority response from for consensus. 


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : [
    {
      "txid" : "f7efcb33c817153dbea86b827380ffce108c6c8e4707356e874d98d0426339bd",
      "size" : 135,
      "version" : 2,
      "locktime" : 0,
      "vin" : [
        {
          "coinbase" : "03dc1a070101",
          "sequence" : 4294967295
        }
      ],
      "vout" : [
        {
          "value" : 8.662500,
          "valueSat" : 866250000,
          "n" : 0,
          "scriptPubKey" : {
            "asm" : "02bcff8305d8bb9e536c5bc31c4ad74cbd15ac9d138d5d756f12099c96d541f14c OP_CHECKSIG",
            "hex" : "2102bcff8305d8bb9e536c5bc31c4ad74cbd15ac9d138d5d756f12099c96d541f14cac",
            "reqSigs" : 1,
            "type" : "pubkey",
            "addresses" : [ "SiibszzNJSGAFTAKqWZKyEkkf4Wq4pYH7c" ]
          }
        },
        {
          "value" : 25.987500,
          "valueSat" : 2598750000,
          "n" : 1,
          "scriptPubKey" : {
            "asm" : "OP_DUP OP_HASH160 a47209c6649cf99e6db2e1bde99b44ce10c76e52 OP_EQUALVERIFY OP_CHECKSIG",
            "hex" : "76a914a47209c6649cf99e6db2e1bde99b44ce10c76e5288ac",
            "reqSigs" : 1,
            "type" : "pubkeyhash",
            "addresses" : [ "ScHWTpAFNo93Jv1RAfMBFp9VZCjpLq82Rf" ]
          }
        }
      ]
    },
    {
      "txid" : "f63543bb90800a601065d6f6d8380d8a98ac1a9f208921febde4eb0168e6fd8e",
      "size" : 135,
      "version" : 2,
      "locktime" : 0,
      "vin" : [
        {
          "coinbase" : "03dd1a070101",
          "sequence" : 4294967295
        }
      ],
      "vout" : [
        {
          "value" : 8.662500,
          "valueSat" : 866250000,
          "n" : 0,
          "scriptPubKey" : {
            "asm" : "0248d791d1b048e8fc79da7ba9f6c0a44fe14641d13a772ff25dc60103580d1a3e OP_CHECKSIG",
            "hex" : "210248d791d1b048e8fc79da7ba9f6c0a44fe14641d13a772ff25dc60103580d1a3eac",
            "reqSigs" : 1,
            "type" : "pubkey",
            "addresses" : [ "ScdSb9g4b77DsA9vLipDYP8V1zEP1ru4d8" ]
          }
        },
        {
          "value" : 27.546750,
          "valueSat" : 2754675000,
          "n" : 1,
          "scriptPubKey" : {
            "asm" : "OP_DUP OP_HASH160 3ed09824728101512565c187fdd061df0cc1cac5 OP_EQUALVERIFY OP_CHECKSIG",
            "hex" : "76a9143ed09824728101512565c187fdd061df0cc1cac588ac",
            "reqSigs" : 1,
            "type" : "pubkeyhash",
            "addresses" : [ "ST28p81wqsSL6of7Em9mZGvVonVBfM36wX" ]
          }
        }
      ]
    }
  ],
  "allreplies" : [
    {
      "nodepubkey" : "02c6c79a75846fd9bb064788b03145e347fa5464558fa9030ebb009df2833369f0",
      "score" : 250,
      "reply" : [
        {
          "txid" : "f7efcb33c817153dbea86b827380ffce108c6c8e4707356e874d98d0426339bd",
          "size" : 135,
          "version" : 2,
          "locktime" : 0,
          "vin" : [
            {
              "coinbase" : "03dc1a070101",
              "sequence" : 4294967295
            }
          ],
          "vout" : [
            {
              "value" : 8.662500,
              "valueSat" : 866250000,
              "n" : 0,
              "scriptPubKey" : {
                "asm" : "02bcff8305d8bb9e536c5bc31c4ad74cbd15ac9d138d5d756f12099c96d541f14c OP_CHECKSIG",
                "hex" : "2102bcff8305d8bb9e536c5bc31c4ad74cbd15ac9d138d5d756f12099c96d541f14cac",
                "reqSigs" : 1,
                "type" : "pubkey",
                "addresses" : [ "SiibszzNJSGAFTAKqWZKyEkkf4Wq4pYH7c" ]
              }
            },
            {
              "value" : 25.987500,
              "valueSat" : 2598750000,
              "n" : 1,
              "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 a47209c6649cf99e6db2e1bde99b44ce10c76e52 OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "76a914a47209c6649cf99e6db2e1bde99b44ce10c76e5288ac",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [ "ScHWTpAFNo93Jv1RAfMBFp9VZCjpLq82Rf" ]
              }
            }
          ]
        },
        {
          "txid" : "f63543bb90800a601065d6f6d8380d8a98ac1a9f208921febde4eb0168e6fd8e",
          "size" : 135,
          "version" : 2,
          "locktime" : 0,
          "vin" : [
            {
              "coinbase" : "03dd1a070101",
              "sequence" : 4294967295
            }
          ],
          "vout" : [
            {
              "value" : 8.662500,
              "valueSat" : 866250000,
              "n" : 0,
              "scriptPubKey" : {
                "asm" : "0248d791d1b048e8fc79da7ba9f6c0a44fe14641d13a772ff25dc60103580d1a3e OP_CHECKSIG",
                "hex" : "210248d791d1b048e8fc79da7ba9f6c0a44fe14641d13a772ff25dc60103580d1a3eac",
                "reqSigs" : 1,
                "type" : "pubkey",
                "addresses" : [ "ScdSb9g4b77DsA9vLipDYP8V1zEP1ru4d8" ]
              }
            },
            {
              "value" : 27.546750,
              "valueSat" : 2754675000,
              "n" : 1,
              "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 3ed09824728101512565c187fdd061df0cc1cac5 OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "76a9143ed09824728101512565c187fdd061df0cc1cac588ac",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [ "ST28p81wqsSL6of7Em9mZGvVonVBfM36wX" ]
              }
            }
          ]
        }
      ]
    },
    {
      "nodepubkey" : "0370874cad6252bb94afa9a253c90122760ce2862e623b515e57bfe0697f3fc515",
      "score" : 300,
      "reply" : [
        {
          "txid" : "f7efcb33c817153dbea86b827380ffce108c6c8e4707356e874d98d0426339bd",
          "size" : 135,
          "version" : 2,
          "locktime" : 0,
          "vin" : [
            {
              "coinbase" : "03dc1a070101",
              "sequence" : 4294967295
            }
          ],
          "vout" : [
            {
              "value" : 8.662500,
              "valueSat" : 866250000,
              "n" : 0,
              "scriptPubKey" : {
                "asm" : "02bcff8305d8bb9e536c5bc31c4ad74cbd15ac9d138d5d756f12099c96d541f14c OP_CHECKSIG",
                "hex" : "2102bcff8305d8bb9e536c5bc31c4ad74cbd15ac9d138d5d756f12099c96d541f14cac",
                "reqSigs" : 1,
                "type" : "pubkey",
                "addresses" : [ "SiibszzNJSGAFTAKqWZKyEkkf4Wq4pYH7c" ]
              }
            },
            {
              "value" : 25.987500,
              "valueSat" : 2598750000,
              "n" : 1,
              "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 a47209c6649cf99e6db2e1bde99b44ce10c76e52 OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "76a914a47209c6649cf99e6db2e1bde99b44ce10c76e5288ac",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [ "ScHWTpAFNo93Jv1RAfMBFp9VZCjpLq82Rf" ]
              }
            }
          ]
        },
        {
          "txid" : "f63543bb90800a601065d6f6d8380d8a98ac1a9f208921febde4eb0168e6fd8e",
          "size" : 135,
          "version" : 2,
          "locktime" : 0,
          "vin" : [
            {
              "coinbase" : "03dd1a070101",
              "sequence" : 4294967295
            }
          ],
          "vout" : [
            {
              "value" : 8.662500,
              "valueSat" : 866250000,
              "n" : 0,
              "scriptPubKey" : {
                "asm" : "0248d791d1b048e8fc79da7ba9f6c0a44fe14641d13a772ff25dc60103580d1a3e OP_CHECKSIG",
                "hex" : "210248d791d1b048e8fc79da7ba9f6c0a44fe14641d13a772ff25dc60103580d1a3eac",
                "reqSigs" : 1,
                "type" : "pubkey",
                "addresses" : [ "ScdSb9g4b77DsA9vLipDYP8V1zEP1ru4d8" ]
              }
            },
            {
              "value" : 27.546750,
              "valueSat" : 2754675000,
              "n" : 1,
              "scriptPubKey" : {
                "asm" : "OP_DUP OP_HASH160 3ed09824728101512565c187fdd061df0cc1cac5 OP_EQUALVERIFY OP_CHECKSIG",
                "hex" : "76a9143ed09824728101512565c187fdd061df0cc1cac588ac",
                "reqSigs" : 1,
                "type" : "pubkeyhash",
                "addresses" : [ "ST28p81wqsSL6of7Em9mZGvVonVBfM36wX" ]
              }
            }
          ]
        }
      ]
    }
  ],
  "uuid" : "c778c124-af28-48b6-b3c4-22f2c5518b8b"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | array   | An array containing objects with the transaction data for each requested transaction on the specified blockchain. If using a `node_count` greater than `1`, this returns the most common reply within `allreplies`. If there is a tie then one is chosen, or if one is an error then the non-error is chosen.
allreplies*     | array   | An array of objects with responses from each node. This can be useful if you wanted to do your own analysis/filtering of the responses.
nodepubkey*     | string  | The node ID.
score*          | int     | The respective node's score based on quality of service. A score of `-200` will ban the node for a 24hr period. You can change the ban threshold with the `xrouterbanscore` setting in `blocknetdx.conf`. See [node scoring](#xrouter-node-scoring) for more details.
reply*          | array   | An array containing objects with the transaction data for each requested transaction on the specified blockchain from the respective node.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).

\* This is only returned if using a `node_count` greater than `1`.










## xrSendTransaction

> Sample Data

```cli
{
  "blockchain": "SYS",
  "signed_tx": "0200000001ce2faed018f4776b41245f78695fdabcc68567b64d13851a7f8277693a23f3e0000000006b483045022100d6e0f7c193e0ae5168e0e8c87a29837f4b8be5c5cdcfa2826a8ddc7cf6cbf43802207ddaa377bc042f9df63eb6f755d23170b9109cb05c18c7ce2fe9993e65434c8b01210323f7e071df863cf20ce13613c68579cdedb6d7c6cf3912f26dac53ec4309c777ffffffff0120a10700000000001976a914eff8cb97723237fe3059774d2a66d02f936e1f1188ac00000000"
}
```
This call is used to submit a locally signed transaction on the specified blockchain.


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrSendTransaction SYS 0200000001ce2faed018f4776b41245f78695fdabcc68567b64d13851a7f8277693a23f3e0000000006b483045022100d6e0f7c193e0ae5168e0e8c87a29837f4b8be5c5cdcfa2826a8ddc7cf6cbf43802207ddaa377bc042f9df63eb6f755d23170b9109cb05c18c7ce2fe9993e65434c8b01210323f7e071df863cf20ce13613c68579cdedb6d7c6cf3912f26dac53ec4309c777ffffffff0120a10700000000001976a914eff8cb97723237fe3059774d2a66d02f936e1f1188ac00000000
```
<code class="api-call">xrSendTransaction [blockchain] [signed_tx]</code>

Parameter       | Type    | Description
----------------|---------|-------------
blockchain      | string  | The blockchain, represented by the asset's ticker (BTC, LTC, SYS, etc.).
signed_tx       | string  | The signed transaction HEX.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : "9f978c91840adbc4e074395f8f793cb7369c48e2ce831a10c32090bf71ae29ae",
  "uuid" : "ACA0874C-C45F-4F40-94AD-794A7E18085A"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | object  | The transaction hash of the sent transaction.










<!-- 
## xrService

> Sample Data

```cli
{
  "service": "BTCgetbestblockhash"
}
```
This call is used to send a request to an XCloud service. XCloud is a decentralized microservice cloud network powered by XRouter that allows for interaction with microservices, blockchains, APIs, and cloud tech that's hosted by Service Nodes. If you'd like to operate your own Service Node, see the [Service Node Setup Guide](https://docs.blocknet.co/service-nodes/setup).


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrService SYSgetbestblockhash
```
<code class="api-call">xrService [service] [param1 param2 paramN]</code>

Parameter       | Type    | Description
----------------|---------|-------------
service         | string  | The service name without the namespace (*xcloud_service*, not *xrs::[xcloud_service]*).
paramN          | unknown | (Optional Parameter)<br>Refer to service documentation for parameter requirements. Information about an XCloud service can be viewed in the service configuration. Use [xrConnect](#xrconnect) to find a node with the service, then use [xrConnectedNodes](#xrconnectednodes) to review the service information.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : "6a29264f48a40cc88f7c56cdc5fd4c62d1daa7f83b204fdcd4a022d8676438c2",
  "error" : null,
  "id" : 1,
  "uuid" : "54b6ec00-8b06-4c2c-9e56-acdff4da69fe"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | unknown | The service's response data.
error           | object  | The native error response if an error occurred, otherwise a successful response will contain a `null` error.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).










## xrServiceConsensus

> Sample Data

```cli
{
  "service": "SYSgetbestblockhash"
}
```
This call is used to send a request to an XCloud service, along with a specified amount of nodes for consensus. XCloud is a decentralized microservice cloud network powered by XRouter that allows for interaction with microservices, blockchains, APIs, and cloud tech that's hosted by Service Nodes. If you'd like to operate your own Service Node, see the [Service Node Setup Guide](https://docs.blocknet.co/service-nodes/setup).


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrServiceConsensus 1 SYSgetbestblockhash
```
<code class="api-call">xrServiceConsensus [node_count] [service] [param1 param2 paramN]</code>

Parameter       | Type    | Description
----------------|---------|-------------
node_count      | int     | The number of nodes that will be used to route calls and use the majority response from for consensus.
service         | string  | The service name without the namespace (*xcloud_service*, not *xrs::[xcloud_service]*).
paramN          | unknown | (Optional Parameter)<br>Refer to service documentation for parameter requirements. Information about an XCloud service can be viewed in the service configuration. Use [xrConnect](#xrconnect) to find a node with the service, then use [xrConnectedNodes](#xrconnectednodes) to review the service information.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : "6a29264f48a40cc88f7c56cdc5fd4c62d1daa7f83b204fdcd4a022d8676438c2",
  "allreplies" : [
    {
      "nodepubkey" : "02c6c79a75846fd9bb064788b03145e347fa5464558fa9030ebb009df2833369f0",
      "score" : 250,
      "reply" : "6a29264f48a40cc88f7c56cdc5fd4c62d1daa7f83b204fdcd4a022d8676438c2"
    },
    {
      "nodepubkey" : "0370874cad6252bb94afa9a253c90122760ce2862e623b515e57bfe0697f3fc515",
      "score" : 300,
      "reply" : "6a29264f48a40cc88f7c56cdc5fd4c62d1daa7f83b204fdcd4a022d8676438c2"
    }
  ],
  "error" : null,
  "id" : 1,
  "uuid" : "54b6ec00-8b06-4c2c-9e56-acdff4da69fe"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | unknown | The service's response data. If using a `node_count` greater than `1`, this returns the most common reply within `allreplies`. If there is a tie then one is chosen, or if one is an error then the non-error is chosen.
allreplies*     | array   | An array of objects with responses from each node. This can be useful if you wanted to do your own analysis/filtering of the responses.
nodepubkey*     | string  | The node ID.
score*          | int     | The respective node's score based on quality of service. A score of `-200` will ban the node for a 24hr period. You can change the ban threshold with the `xrouterbanscore` setting in `blocknetdx.conf`. See [node scoring](#xrouter-node-scoring) for more details.
reply*          | array   | The service's response data from the respective node.
error           | object  | The native error response if an error occurred, otherwise a successful response will contain a `null` error.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).

\* This is only returned if using a `node_count` greater than `1`. 
-->










## xrGetReply

> Sample Data

```cli
{
  "uuid" : "3243a24b-3e9d-40d1-8c33-37a57878ce85"
}
```
This call is used to look up responses from previous XRouter calls without having to request them from the network. There are no fees for this call.


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrGetReply 3243a24b-3e9d-40d1-8c33-37a57878ce85
```
<code class="api-call">xrGetReply [uuid]</code>

Parameter       | Type    | Description
----------------|---------|-------------
uuid            | string  | The UUID of the response you want to look up.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : "464931",
  "uuid" : "3243a24b-3e9d-40d1-8c33-37a57878ce85"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | array   | The response for the previous call associated with the UUID.
uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).










## xrShowConfigs

This call is used to show the configurations received from all nodes as raw text.


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrShowConfigs
```
<code class="api-call">xrShowConfigs</code>

This call does not take parameters.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
[
  {
    "config" : "[Main]\nwallets=SYS,MNP\nservices=SYSgetbestblockhash,SYSgetgovernanceinfo\ntimeout=30\nconsensus=1\nfee=0\nmaxfee=0.5\nclientrequestlimit=50\npaymentaddress=B8zc9PuDJC3XaF7UkQ2CnRDCmXiCs9bDuS\n[xrGetBlockCount]\nmaxfee=0.1\nclientrequestlimit=10\n[SYS::xrGetBlockCount]\nfee=0.1\nmaxfee=0.2\n[xrGetBlocks]\nfetchlimit=10\nfee=0.1\nmaxfee=0.2\n[MNP::xrGetBlocks]\nfetchlimit=1\nfee=0.1\n[MNP::xrGetTransactions]\nfetchlimit=3\nfee=0.1\n"
  },
  {
    "config" : "[Main]\nwallets=SYS,LTC,BLOCK,BTC\nservices=BTCgetblockhash,BTCgetbestblockhash,BTCgettransaction,SYSgetbestblockhash\ntimeout=30\nclientrequestlimit=50\nmaxfee=0.1\nfee=0\npaymentaddress=BGyvu6uHJ9WLftPgwHeAU5HwmgGmpB3Juh\n[xrGetBlockCount]\nclientrequestlimit=-1\n[BTC]\nclientrequestlimit=100\nfee=0.2\n"
  }
]
```

Parameter       | Type    | Description
----------------|---------|-------------
Array           | array   | An array of objects containing the configurations for each node you are connected to.
config          | string  | The raw text contents of `xrouter.conf`.










## xrUpdateConfigs

This call is used to request the latest configuration files from all Service Nodes.


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrUpdateConfigs true
```
<code class="api-call">xrUpdateConfigs [force_check]\(optional)</code>

Parameter       | Type    | Description
----------------|---------|-------------
force_check     | bool    | (Optional Parameter)<br>Defaults to `false`.<br>Signifies whether to force update regardless of rate limit checks.<br>`true`: Force update if rate limit exceeded.<br>`false`: Only update if within rate limit.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : "Config requests have been sent"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | string  | A confirmation that the new configs have been requested.










## xrReloadConfigs

This call is used to reload `xrouter.conf` and all associated plugin configs after changes have been made to apply newly configured settings without needing to restart the Blocknet client.


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrReloadConfigs
```
<code class="api-call">xrReloadConfigs</code>

This call does not take parameters.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response

```cli
{
  "reply" : "Config requests have been sent"
}
```

Parameter       | Type    | Description
----------------|---------|-------------
reply           | string  | A confirmation that `xrouter.conf` has been reloaded.










## xrStatus

This call is used to print your XRouter configuration and has two different outputs depending on if this call is made from a user client or a Service Node.


### Request Parameters

> Sample Request

```cli
blocknetdx-cli xrStatus
```
<code class="api-call">xrStatus</code>

This call does not take parameters.


### Response Parameters

<aside class="success">
200 OK
</aside>

> Sample 200 Response \(User Client)

```cli
{
  "xrouter" : true,
  "servicenode" : false,
  "config" : "[Main]\ntimeout=30\nconsensus=1\nmaxfee=0.5"
}
```

### User Client

Parameter       | Type    | Description
----------------|---------|-------------
xrouter         | bool    | Signifies XRouter activation.<br>`true`: XRouter is enabled.<br>`false`: XRouter is disabled.
servicenode     | bool    | Signifies if your client is a Service Node.<br>`true`: Client is a Service Node.<br>`false`: Client is not a Service Node.
config          | string  | The raw text contents of your `xrouter.conf`.


> Sample 200 Response \(Service Node)

```cli
{
    "nodepubkey" : "03f1bf0087e5a0036176560493fb05fedb0eb841993536c6a86c78687c4e73a527",
    "score" : 0,
    "banned" : false,
    "paymentaddress" : "B2B8HVr7BuxBXx7JTqmPgUZJ11CGcL9vtS",
    "spvwallets" : [
      "SYS"
    ],
    "spvconfigs" : [
      {
        "spvwallet" : "SYS",
        "commands" : [
          {
            "command" : "xrGetConfig",
            "fee" : 0.00000000,
            "requestlimit" : -1,
            "paymentaddress" : "B2B8HVr7BuxBXx7JTqmPgUZJ11CGcL9vtS",
            "disabled" : false
          },
          {
            "command" : "xrGetBlockCount",
            "fee" : 0.00000000,
            "requestlimit" : -1,
            "paymentaddress" : "B2B8HVr7BuxBXx7JTqmPgUZJ11CGcL9vtS",
            "disabled" : false
          },
          {
            "command" : "xrGetBlockHash",
            "fee" : 0.00000000,
            "requestlimit" : -1,
            "paymentaddress" : "B2B8HVr7BuxBXx7JTqmPgUZJ11CGcL9vtS",
            "disabled" : false
          },
          {
            "command" : "xrGetBlock",
            "fee" : 0.00000000,
            "requestlimit" : -1,
            "paymentaddress" : "B2B8HVr7BuxBXx7JTqmPgUZJ11CGcL9vtS",
            "disabled" : false
          },
          {
            "command" : "xrGetTransaction",
            "fee" : 0.00000000,
            "requestlimit" : -1,
            "paymentaddress" : "B2B8HVr7BuxBXx7JTqmPgUZJ11CGcL9vtS",
            "disabled" : false
          },
          {
            "command" : "xrSendTransaction",
            "fee" : 0.00000000,
            "requestlimit" : -1,
            "paymentaddress" : "B2B8HVr7BuxBXx7JTqmPgUZJ11CGcL9vtS",
            "disabled" : false
          },
          {
            "command" : "xrGetBlocks",
            "fee" : 0.00000000,
            "requestlimit" : -1,
            "paymentaddress" : "B2B8HVr7BuxBXx7JTqmPgUZJ11CGcL9vtS",
            "disabled" : false
          },
          {
            "command" : "xrGetTransactions",
            "fee" : 0.00000000,
            "requestlimit" : -1,
            "paymentaddress" : "B2B8HVr7BuxBXx7JTqmPgUZJ11CGcL9vtS",
            "disabled" : false
          },
          {
            "command" : "xrDecodeRawTransaction",
            "fee" : 0.00000000,
            "requestlimit" : -1,
            "paymentaddress" : "B2B8HVr7BuxBXx7JTqmPgUZJ11CGcL9vtS",
            "disabled" : false
          }
        ]
      }
    ],
    "feedefault" : 0.00000000,
    "fees" : {
    },
    "services" : {
      "SYSlistoffers" : {
        "parameters" : "",
        "fee" : 0.00000000,
        "paymentaddress" : "B2B8HVr7BuxBXx7JTqmPgUZJ11CGcL9vtS",
        "requestlimit" : -1,
        "fetchlimit" : 50,
        "timeout" : 30,
        "disabled" : false
      }
    },
    "xrouter" : true,
    "servicenode" : true,
    "config" : "[Main]\nwallets=SYS\nplugins=hanniabu\nmaxfee=0.5\npaymentaddress=B2B8HVr7BuxBXx7JTqmPgUZJ11CGcL9vtS\n",
    "plugins" : {
      "SYSlistoffers" : "parameters=\nprivate::type=rpc\nprivate::rpcip=127.0.0.1\nprivate::rpcport=8370\nprivate::rpcuser=username\nprivate::rpcpassword=password\nprivate::rpccommand=listoffers"
    }
}
```

### Service Node

Parameter       | Type    | Description
----------------|---------|-------------
nodepubkey      | string  | Your node ID.
score           | int     | Not applicable when viewing your own score.
paymentaddress  | string  | Your node's general payment address.
spvwallets      | array   | An array of SPV wallets you're supporting, represented by the asset's ticker.
spvconfigs      | array   | An array of each SPV wallets and command configurations.
spvwallet       | string  | The SPV wallet that the configurations under `commands` pertains to.
commands        | array   | An array of each SPV wallet command and respective configurations.
command         | string  | The SPV command.
fee             | float   | The command fee, overrides the `feedefault` and `fees` values. This priority has already been accounted for in this value.
requestlimit    | int     | The minimum time allowed between calls in milliseconds. A value of `-1` means there is no limit. If a client exceeds this value they will be penalized and eventually banned by your node.
paymentaddress  | string  | Your node's payment address for this specific command.
disabled        | bool    | Signifies if your node has disabled this command.<br>`true`: Call is disabled and not supported.<br>`false`: Call is enabled and supported.
feedefault      | float   | Your node's default service fee. This fee is overridden by the values specified in `fees`, SPV command configuration `fee`, and XCloud service command configuration `fee`.
fees            | object  | Object of SPV commands and respective fees. These values are overridden by the SPV wallet-specific configuration `fee`.
services        | object  | Object of your node's XCloud service calls with respective properties.
parameters      | string  | Information on the parameters the command takes.
fee             | float   | The service command fee. This overrides the `feedefault` value.
paymentaddress  | string  | Your node's payment address for this specific command.
requestlimit    | int     | The minimum time allowed between calls in milliseconds. A value of `-1` means there is no limit. If a client exceeds this value they will be penalized and eventually banned by your node.
fetchlimit      | int     | The maximum number of blocks processed. A value of `-1` means there is no limit. A value of `0` means no blocks will be processed.
timeout         | int     | The value for `timeout` you set in `xrouter.conf` for this call. Defines how long (in seconds) your client waits for a response from another Service Node. The default value is `30`.
disabled        | bool    | Signifies if your node has disabled this service.<br>`true`: Call is disabled and not supported.<br>`false`: Call is enabled and supported.
xrouter         | bool    | Signifies XRouter activation.<br>`true`: XRouter is enabled.<br>`false`: XRouter is disabled.
servicenode     | bool    | Signifies if your client is a Service Node.<br>`true`: Client is a Service Node.<br>`false`: Client is not a Service Node.
config          | string  | The raw text contents of your `xrouter.conf`.
plugins         | object  | An object of each service you have setup and the raw text contents of the respective config file in the `plugins` folder within the Blocknet data directory.










<!-- 
  ## xrGetBalance

  > Sample Data

  ```cli
  {
    "blockchain": "SYS",
    "address": "SYb9Gmcwj1aXUV86cKpnCD8SR7hvZgbKTP"
  }
  ```
  This call is used to retrieve the balance with a precision of X.XXXXXX of the given address with for the specified blockchain.

  __Note:__ This call is not currently supported for querying the balance of addresses with UTXO's older than a few thousand blocks.


  ### Request Parameters

  > Sample Request

  ```cli
  blocknetdx-cli xrGetBalance SYS SYb9Gmcwj1aXUV86cKpnCD8SR7hvZgbKTP
  ```
  <code class="api-call">xrGetBalance [blockchain] [address] [node_count]\(optional)</code>

  Parameter       | Type    | Description
  ----------------|---------|-------------
  blockchain      | string  | The blockchain, represented by the asset's ticker (BTC, LTC, SYS, etc.).
  address         | string  | The address to retrieve the balance of.
  node_count      | int     | (Optional Parameter)<br>Defaults to `1` if no `consensus=` setting in `xrouter.conf`.<br>The number of nodes that will be used to route calls and use the majority response from for consensus. 


  ### Response Parameters

  <aside class="success">
  200 OK
  </aside>

  > Sample 200 Response

  ```cli
  {
    "reply" : "0.000000"
  }
  ```

  Parameter       | Type    | Description
  ----------------|---------|-------------
  reply           | object  | The balance with a precision of X.XXXXXX of the given address for the specified blockchain.
  uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).
-->










<!--  
  ## xrGetBalanceUpdate

  > Sample Data

  ```cli
  {
    "blockchain": "SYS",
    "address": "SYb9Gmcwj1aXUV86cKpnCD8SR7hvZgbKTP"
  }
  ```
  This call is used to retrieve the net change in balance of the given address since a certain block on the specified blockchain.


  ### Request Parameters

  > Sample Request

  ```cli
  blocknetdx-cli xrGetBalanceUpdate SYS SYb9Gmcwj1aXUV86cKpnCD8SR7hvZgbKTP 125157
  ```
  <code class="api-call">xrGetBalanceUpdate [blockchain] [address] [start_block]\(optional) [node_count]\(optional)</code>

  Parameter       | Type    | Description
  ----------------|---------|-------------
  blockchain      | string  | The blockchain, represented by the asset's ticker (BTC, LTC, SYS, etc.).
  address         | string  | The address to retrieve the balance of.
  start_block     | int     | (Optional Parameter) Defaults to `0`.<br>The earliest block to retrieve transactions through. 
  node_count      | int     | (Optional Parameter)<br>Defaults to `1` if no `consensus=` setting in `xrouter.conf`.<br>The number of nodes that will be used to route calls and use the majority response from for consensus. 


  ### Response Parameters

  <aside class="success">
  200 OK
  </aside>

  > Sample 200 Response

  ```cli
  {
    "reply" : "1.430000"
  }
  ```

  Parameter       | Type    | Description
  ----------------|---------|-------------
  reply           | string  | The addresses net change in balance since the given block on the specified blockchain.
  uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).
-->










<!-- 
  ## xrGenerateBloomFilter

  > Sample Data

  ```cli
  {
    "addresses": ["SYb9Gmcwj1aXUV86cKpnCD8SR7hvZgbKTP"]
  }
  ```
  This call is used to retrieves a HEX representation of a bloom filter for the given addresses.


  ### Request Parameters

  > Sample Request

  ```cli
  blocknetdx-cli xrGenerateBloomFilter SYb9Gmcwj1aXUV86cKpnCD8SR7hvZgbKTP 
  ```
  <code class="api-call">xrGenerateBloomFilter [address1] [address2...addressN] [node_count]\(optional)</code>

  Parameter       | Type    | Description
  ----------------|---------|-------------
  addresses       | array   | The addresses to retrieve the bloom filter HEX representation of.
  node_count      | int     | (Optional Parameter)<br>Defaults to `1` if no `consensus=` setting in `xrouter.conf`.<br>The number of nodes that will be used to route calls and use the majority response from for consensus. 


  ### Response Parameters

  <aside class="success">
  200 OK
  </aside>

  > Sample 200 Response

  ```cli
  {
    "reply" : {
      
    }
  }
  ```

  Parameter       | Type    | Description
  ----------------|---------|-------------
  hex             | string  | The HEX representation of the address's bloom filter. 
  uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).
-->










<!-- 
  ## xrGetTransactionsBloomFilter

  > Sample Data

  ```cli
  {
    "blockchain": "SYS",
    "bloom_filter": "0000100800"
  }
  ```
  This call is used to retrieve the transactions on a specified blockchain that fit the given bloom filter.


  ### Request Parameters

  > Sample Request

  ```cli
  blocknetdx-cli xrGetTransactionsBloomFilter SYS 0000100800
  ```
  <code class="api-call">xrGetTransactionsBloomFilter [blockchain] [bloom_filter] [start_block]\(optional) [node_count]\(optional)</code>

  Parameter       | Type    | Description
  ----------------|---------|-------------
  blockchain      | string  | The blockchain, represented by the asset's ticker (BTC, LTC, SYS, etc.).
  bloom_filter    | string  | The HEX representation of a bloom filter.
  start_block     | int     | (Optional Parameter) Defaults to `0`.<br>The earliest block to retrieve transactions through. 
  node_count      | int     | (Optional Parameter)<br>Defaults to `1` if no `consensus=` setting in `xrouter.conf`.<br>The number of nodes that will be used to route calls and use the majority response from for consensus. 


  ### Response Parameters

  <aside class="success">
  200 OK
  </aside>

  > Sample 200 Response

  ```cli
  {
    "reply" : {
      
    }
  }
  ```

  Parameter       | Type    | Description
  ----------------|---------|-------------
  reply           | object  |  
  uuid            | string  | The response ID, which can be used to view this response again with [xrGetReply](#xrgetreply).
-->









