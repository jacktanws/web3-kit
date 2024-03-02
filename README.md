# web3-kit
web3-kit is a library for dApps to interract with the Htmlcoin blockchain. web3-kit communicates to a Htmlcoin node via the provider provided.

## Get Started
Run the following in your project folder:

	npm install web3-kit --save

## Web Dapp Usage
This is example is meant for web dapps who would like to use web3-kit's convenience methods with HtmlcoinChrome's RPC provider. HtmlcoinChrome is a Htmlcoin wallet [Chrome extension](). More details about HtmlcoinChrome [here](https://github.com/denuoweb/htmlcoin-chrome-wallet).

### 1. Construct web3-kit instance
If you have HtmlcoinChrome installed, you will have a `window.htmlcoinchrome` object injected in your browser tab. Pass that into web3-kit as a parameter to set the provider.
```
const web3-kit = new web3-kit(window.htmlcoinchrome.rpcProvider);
```

### 2. Construct Contract instance
The Contract class is meant for executing `sendtocontract` or `callcontract` at a specific contract address with a given ABI.
```
const contractAddress = 'f7b958eac2bdaca0f225b86d162f263441d23c19';
const contractAbi = [{"constant":false,"inputs":[{"name":"_eventAddress","type":"address"},{"name":"_eventName","type":"bytes32[10]"},{"name":"_eventResultNames","type":"bytes32[10]"},{"name":"_numOfResults","type":"uint8"},{"name":"_lastResultIndex","type":"uint8"},{"name":"_arbitrationEndBlock","type":"uint256"},{"name":"_consensusThreshold","type":"uint256"}],"name":"createDecentralizedOracle","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_eventAddress","type":"address"},{"name":"_eventName","type":"bytes32[10]"},{"name":"_eventResultNames","type":"bytes32[10]"},{"name":"_numOfResults","type":"uint8"},{"name":"_lastResultIndex","type":"uint8"},{"name":"_arbitrationEndBlock","type":"uint256"},{"name":"_consensusThreshold","type":"uint256"}],"name":"doesDecentralizedOracleExist","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_oracle","type":"address"},{"name":"_eventAddress","type":"address"},{"name":"_eventName","type":"bytes32[10]"},{"name":"_eventResultNames","type":"bytes32[10]"},{"name":"_numOfResults","type":"uint8"},{"name":"_bettingEndBlock","type":"uint256"},{"name":"_resultSettingEndBlock","type":"uint256"},{"name":"_consensusThreshold","type":"uint256"}],"name":"createCentralizedOracle","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_oracle","type":"address"},{"name":"_eventAddress","type":"address"},{"name":"_eventName","type":"bytes32[10]"},{"name":"_eventResultNames","type":"bytes32[10]"},{"name":"_numOfResults","type":"uint8"},{"name":"_bettingEndBlock","type":"uint256"},{"name":"_resultSettingEndBlock","type":"uint256"},{"name":"_consensusThreshold","type":"uint256"}],"name":"doesCentralizedOracleExist","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"","type":"bytes32"}],"name":"oracles","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"inputs":[{"name":"_addressManager","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_contractAddress","type":"address"},{"indexed":true,"name":"_oracle","type":"address"},{"indexed":true,"name":"_eventAddress","type":"address"},{"indexed":false,"name":"_name","type":"bytes32[10]"},{"indexed":false,"name":"_resultNames","type":"bytes32[10]"},{"indexed":false,"name":"_numOfResults","type":"uint8"},{"indexed":false,"name":"_bettingEndBlock","type":"uint256"},{"indexed":false,"name":"_resultSettingEndBlock","type":"uint256"},{"indexed":false,"name":"_consensusThreshold","type":"uint256"}],"name":"CentralizedOracleCreated","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_contractAddress","type":"address"},{"indexed":true,"name":"_eventAddress","type":"address"},{"indexed":false,"name":"_name","type":"bytes32[10]"},{"indexed":false,"name":"_resultNames","type":"bytes32[10]"},{"indexed":false,"name":"_numOfResults","type":"uint8"},{"indexed":false,"name":"_lastResultIndex","type":"uint8"},{"indexed":false,"name":"_arbitrationEndBlock","type":"uint256"},{"indexed":false,"name":"_consensusThreshold","type":"uint256"}],"name":"DecentralizedOracleCreated","type":"event"}];

// Create a new Contract instance and use the same provider as web3-kit
const contract = web3-kit.Contract(contractAddress, contractAbi);
```

### 3. Get Logged In HtmlcoinChrome Account
To get the current logged in account in HtmlcoinChrome, you will have to add an [Event Listener](https://www.w3schools.com/jsref/met_element_addeventlistener.asp) to listen to messages sent from HtmlcoinChrome.
```
let account;

function onHtmlcoinchromeAcctChange(event) {
  if (event.data.message && event.data.message.type == "ACCOUNT_CHANGED") {
    account = event.data.message.payload;

    // You now have the logged in account.
    // account = InpageAccount {
    //   loggedIn: true,
    //   name: "My Wallet", 
    //   network: "TestNet",
    //   address: "hJHp6dUSmDShpEEMmwxqHPo7sFSdydSkPM",
    //   balance: 49.10998413
    // } 

    // You may also get the account from `window.htmlcoinchrome.account`.
    // account = window.htmlcoinchrome.account
  }
}
window.addEventListener('message', onHtmlcoinchromeAcctChange, false);
```

### 4. Execute sendtocontract
The last piece is to execute a `sendtocontract` on your Contract instance. This will automatically show a HtmlcoinChrome popup to confirm that you would like to send the transaction.
```
// Does a sendtocontract call on a function called setResult(uint8)
const tx = await contract.send('setResult', {
  methodArgs: [1],    // Sets the function params
  gasLimit: 1000000,  // Sets the gas limit to 1 million
  senderAddress: account.address,
});
// tx = txid of the transaction
```

## web3-kitProvider
The provider is the link between web3-kit and the blockchain. A compatible web3-kit Provider adheres to the following interface:
```
interface web3-kitProvider: {
  rawCall: (method: string, args: any[]) => Promise; // returns the result of the request
}
```

## web3-kit
Instantiate a new instance of `web3-kit`: 
```
const { web3-kit } = require('web3-kit');

// Instantiate web3-kit with HttpProvider
// Pass in the URL of your Htmlcoin node RPC port with auth credentials.
// Default Htmlcoin RPC ports: testnet=14889 mainnet=4889
const web3-kit = new web3-kit('http://htmlcoin:htmlcoin@localhost:14889');

// Instantiate web3-kit with HtmlcoinchromeRPCProvider
// HtmlcoinchromeRPCProvider is a provider for the HtmlcoinChrome Wallet Extension.
// Please note HtmlcoinchromeRPCProvider only allows the rawCall() method to be used.
// It is specifically used for `sendtocontract` and `callcontract` only.
const web3-kit = new web3-kit(window.htmlcoinchrome.rpcProvider);
```

### isConnected()
Checks if you are connected properly to the local htmlcoin node.
```
async function isConnected() {
  return await web3-kit.isConnected();
}
```

### getHexAddress(address)
Converts a Htmlcoin address to hex format.
```
async function getHexAddress() {
  return await web3-kit.getHexAddress('hKjn4fStBaAtwGiwueJf9qFxgpbAvf1xAy');
}
```

### fromHexAddress(hexAddress)
Converts a hex address to Htmlcoin format.
```
async function fromHexAddress() {
  return await web3-kit.fromHexAddress('17e7888aa7412a735f336d2f6d784caefabb6fa3');
}
```

### getBlockCount()
Gets the current block height of your local Htmlcoin node.
```
async function getBlockCount() {
  return await web3-kit.getBlockCount();
}
```

### getTransaction(txid)
Gets the transaction details of the transaction id.
```
async function getTransaction(args) {
  const {
    transactionId, // string
  } = args;

  return await web3-kit.getTransactionReceipt(transactionId);
}
```

### getTransactionReceipt(txid)
Gets the transaction receipt of the transaction id.
```
async function getTransactionReceipt(args) {
  const {
    transactionId, // string
  } = args;

  return await web3-kit.getTransactionReceipt(transactionId);
}
```

### listUnspent()
Gets the unspent outputs that can be used.
```
async function listUnspent() {
  return await web3-kit.listUnspent();
}
```

### searchLogs(fromBlock, toBlock, addresses, topics, contractMetadata, removeHexPrefix)
Gets the logs given the params on the blockchain.

The `contractMetadata` param contains the contract names and ABI that you would like to parse. An example of one is:
```
# contract_metadata.js
module.exports = {
  EventFactory: {
    address: 'd53927df927be7fc51ce8bf8b998cb6611c266b0',
    abi: [{ constant: true, inputs: [{ name: '', type: 'bytes32' }], name: 'topics', outputs: [{ name: '', type: 'address' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: false, inputs: [{ name: '_oracle', type: 'address' }, { name: '_name', type: 'bytes32[10]' }, { name: '_resultNames', type: 'bytes32[10]' }, { name: '_bettingEndBlock', type: 'uint256' }, { name: '_resultSettingEndBlock', type: 'uint256' }], name: 'createTopic', outputs: [{ name: 'topicEvent', type: 'address' }], payable: false, stateMutability: 'nonpayable', type: 'function' }, { constant: true, inputs: [{ name: '_name', type: 'bytes32[10]' }, { name: '_resultNames', type: 'bytes32[10]' }, { name: '_bettingEndBlock', type: 'uint256' }, { name: '_resultSettingEndBlock', type: 'uint256' }], name: 'doesTopicExist', outputs: [{ name: '', type: 'bool' }], payable: false, stateMutability: 'view', type: 'function' }, { inputs: [{ name: '_addressManager', type: 'address' }], payable: false, stateMutability: 'nonpayable', type: 'constructor' }, { anonymous: false, inputs: [{ indexed: true, name: '_topicAddress', type: 'address' }, { indexed: true, name: '_creator', type: 'address' }, { indexed: true, name: '_oracle', type: 'address' }, { indexed: false, name: '_name', type: 'bytes32[10]' }, { indexed: false, name: '_resultNames', type: 'bytes32[10]' }, { indexed: false, name: '_bettingEndBlock', type: 'uint256' }, { indexed: false, name: '_resultSettingEndBlock', type: 'uint256' }], name: 'TopicCreated', type: 'event' }],
  },

  TopicEvent: {
    abi: [{ constant: false, inputs: [{ name: '_resultIndex', type: 'uint8' }, { name: '_sender', type: 'address' }, { name: '_amount', type: 'uint256' }], name: 'voteFromOracle', outputs: [{ name: '', type: 'bool' }], payable: false, stateMutability: 'nonpayable', type: 'function' }, { constant: true, inputs: [], name: 'totalBotValue', outputs: [{ name: '', type: 'uint256' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [{ name: '_oracleIndex', type: 'uint8' }], name: 'getOracle', outputs: [{ name: '', type: 'address' }, { name: '', type: 'bool' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [{ name: '', type: 'address' }], name: 'didWithdraw', outputs: [{ name: '', type: 'bool' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [], name: 'resultSet', outputs: [{ name: '', type: 'bool' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [], name: 'status', outputs: [{ name: '', type: 'uint8' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [], name: 'getFinalResult', outputs: [{ name: '', type: 'uint8' }, { name: '', type: 'string' }, { name: '', type: 'bool' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [{ name: '', type: 'uint256' }], name: 'resultNames', outputs: [{ name: '', type: 'bytes32' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [{ name: '', type: 'uint256' }], name: 'oracles', outputs: [{ name: 'didSetResult', type: 'bool' }, { name: 'oracleAddress', type: 'address' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: false, inputs: [], name: 'finalizeResult', outputs: [{ name: '', type: 'bool' }], payable: false, stateMutability: 'nonpayable', type: 'function' }, { constant: false, inputs: [{ name: '_oracle', type: 'address' }, { name: '_resultIndex', type: 'uint8' }, { name: '_consensusThreshold', type: 'uint256' }], name: 'centralizedOracleSetResult', outputs: [], payable: false, stateMutability: 'nonpayable', type: 'function' }, { constant: true, inputs: [], name: 'totalQtumValue', outputs: [{ name: '', type: 'uint256' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: false, inputs: [{ name: '_consensusThreshold', type: 'uint256' }], name: 'invalidateOracle', outputs: [], payable: false, stateMutability: 'nonpayable', type: 'function' }, { constant: true, inputs: [], name: 'getBetBalances', outputs: [{ name: '', type: 'uint256[10]' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [], name: 'owner', outputs: [{ name: '', type: 'address' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [], name: 'calculateQtumContributorWinnings', outputs: [{ name: '', type: 'uint256' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [], name: 'getVoteBalances', outputs: [{ name: '', type: 'uint256[10]' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [], name: 'getTotalVotes', outputs: [{ name: '', type: 'uint256[10]' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: false, inputs: [{ name: '_better', type: 'address' }, { name: '_resultIndex', type: 'uint8' }], name: 'bet', outputs: [], payable: true, stateMutability: 'payable', type: 'function' }, { constant: false, inputs: [{ name: '_resultIndex', type: 'uint8' }, { name: '_currentConsensusThreshold', type: 'uint256' }], name: 'votingOracleSetResult', outputs: [{ name: '', type: 'bool' }], payable: false, stateMutability: 'nonpayable', type: 'function' }, { constant: true, inputs: [], name: 'getTotalBets', outputs: [{ name: '', type: 'uint256[10]' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [], name: 'getEventName', outputs: [{ name: '', type: 'string' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [], name: 'invalidResultIndex', outputs: [{ name: '', type: 'uint8' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: true, inputs: [], name: 'numOfResults', outputs: [{ name: '', type: 'uint8' }], payable: false, stateMutability: 'view', type: 'function' }, { constant: false, inputs: [], name: 'withdrawWinnings', outputs: [], payable: false, stateMutability: 'nonpayable', type: 'function' }, { constant: false, inputs: [{ name: '_newOwner', type: 'address' }], name: 'transferOwnership', outputs: [], payable: false, stateMutability: 'nonpayable', type: 'function' }, { constant: true, inputs: [], name: 'calculateBotContributorWinnings', outputs: [{ name: '', type: 'uint256' }], payable: false, stateMutability: 'view', type: 'function' }, { inputs: [{ name: '_owner', type: 'address' }, { name: '_centralizedOracle', type: 'address' }, { name: '_name', type: 'bytes32[10]' }, { name: '_resultNames', type: 'bytes32[10]' }, { name: '_bettingEndBlock', type: 'uint256' }, { name: '_resultSettingEndBlock', type: 'uint256' }, { name: '_addressManager', type: 'address' }], payable: false, stateMutability: 'nonpayable', type: 'constructor' }, { payable: true, stateMutability: 'payable', type: 'fallback' }, { anonymous: false, inputs: [{ indexed: true, name: '_eventAddress', type: 'address' }, { indexed: false, name: '_finalResultIndex', type: 'uint8' }], name: 'FinalResultSet', type: 'event' }, { anonymous: false, inputs: [{ indexed: true, name: '_winner', type: 'address' }, { indexed: false, name: '_htmlcoinTokenWon', type: 'uint256' }, { indexed: false, name: '_botTokenWon', type: 'uint256' }], name: 'WinningsWithdrawn', type: 'event' }, { anonymous: false, inputs: [{ indexed: true, name: '_previousOwner', type: 'address' }, { indexed: true, name: '_newOwner', type: 'address' }], name: 'OwnershipTransferred', type: 'event' }],
  },
};
```

Usage:
```
const ContractMetadata = require('./contract_metadata');

async function(args) {
  let {
    fromBlock, // number
    toBlock, // number
    addresses, // string array
    topics // string array
  } = args;

  if (addresses === undefined) {
    addresses = [];
  }
  if (topics === undefined) {
    topics = [];
  }

  // removeHexPrefix = true removes the '0x' hex prefix from all hex values
  return await web3-kit.searchLogs(fromBlock, toBlock, addresses, topics, contractMetadata, true);
}
```

## Contract.js
Instantiate a new instance of `Contract`: 
```
const { web3-kit } = require('web3-kit');

const web3-kit = new web3-kit('http://htmlcoin:htmlcoin@localhost:14889');

// contractAddress = The address of your contract deployed on the blockchain
const contractAddress = 'f7b958eac2bdaca0f225b86d162f263441d23c19';

// contractAbi = The ABI of the contract
const contractAbi = [{"constant":false,"inputs":[{"name":"_eventAddress","type":"address"},{"name":"_eventName","type":"bytes32[10]"},{"name":"_eventResultNames","type":"bytes32[10]"},{"name":"_numOfResults","type":"uint8"},{"name":"_lastResultIndex","type":"uint8"},{"name":"_arbitrationEndBlock","type":"uint256"},{"name":"_consensusThreshold","type":"uint256"}],"name":"createDecentralizedOracle","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_eventAddress","type":"address"},{"name":"_eventName","type":"bytes32[10]"},{"name":"_eventResultNames","type":"bytes32[10]"},{"name":"_numOfResults","type":"uint8"},{"name":"_lastResultIndex","type":"uint8"},{"name":"_arbitrationEndBlock","type":"uint256"},{"name":"_consensusThreshold","type":"uint256"}],"name":"doesDecentralizedOracleExist","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_oracle","type":"address"},{"name":"_eventAddress","type":"address"},{"name":"_eventName","type":"bytes32[10]"},{"name":"_eventResultNames","type":"bytes32[10]"},{"name":"_numOfResults","type":"uint8"},{"name":"_bettingEndBlock","type":"uint256"},{"name":"_resultSettingEndBlock","type":"uint256"},{"name":"_consensusThreshold","type":"uint256"}],"name":"createCentralizedOracle","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_oracle","type":"address"},{"name":"_eventAddress","type":"address"},{"name":"_eventName","type":"bytes32[10]"},{"name":"_eventResultNames","type":"bytes32[10]"},{"name":"_numOfResults","type":"uint8"},{"name":"_bettingEndBlock","type":"uint256"},{"name":"_resultSettingEndBlock","type":"uint256"},{"name":"_consensusThreshold","type":"uint256"}],"name":"doesCentralizedOracleExist","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"","type":"bytes32"}],"name":"oracles","outputs":[{"name":"","type":"address"}],"payable":false,"stateMutability":"view","type":"function"},{"inputs":[{"name":"_addressManager","type":"address"}],"payable":false,"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_contractAddress","type":"address"},{"indexed":true,"name":"_oracle","type":"address"},{"indexed":true,"name":"_eventAddress","type":"address"},{"indexed":false,"name":"_name","type":"bytes32[10]"},{"indexed":false,"name":"_resultNames","type":"bytes32[10]"},{"indexed":false,"name":"_numOfResults","type":"uint8"},{"indexed":false,"name":"_bettingEndBlock","type":"uint256"},{"indexed":false,"name":"_resultSettingEndBlock","type":"uint256"},{"indexed":false,"name":"_consensusThreshold","type":"uint256"}],"name":"CentralizedOracleCreated","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_contractAddress","type":"address"},{"indexed":true,"name":"_eventAddress","type":"address"},{"indexed":false,"name":"_name","type":"bytes32[10]"},{"indexed":false,"name":"_resultNames","type":"bytes32[10]"},{"indexed":false,"name":"_numOfResults","type":"uint8"},{"indexed":false,"name":"_lastResultIndex","type":"uint8"},{"indexed":false,"name":"_arbitrationEndBlock","type":"uint256"},{"indexed":false,"name":"_consensusThreshold","type":"uint256"}],"name":"DecentralizedOracleCreated","type":"event"}];

// Create a Contract instance from the web3-kit instance
const contract = web3-kit.Contract(contractAddress, contractAbi);
```

### call(methodName, params)
Executes a `callcontract`
```
// callcontract on a method named 'bettingEndBlock'
async function exampleCall(args) {
  const {
    senderAddress, // address
  } = args;

  return await contract.call('bettingEndBlock', {
    methodArgs: [],
    senderAddress: senderAddress,
  });
}
```

### send(methodName, params)
Executes a `sendtocontract`
```
// sendtocontract on a method named 'setResult'
async function exampleSend(args) {
  const {
    resultIndex, // number
    senderAddress, // address
  } = args;

  return await contract.send('setResult', {
    methodArgs: [resultIndex],
    gasLimit: 1000000, // setting the gas limit to 1 million
    senderAddress: senderAddress,
  });
}
```

## Encoder
`Encoder` static functions are exposed in web3-kit instances.
```
const { web3-kit } = require('web3-kit');

const web3-kit = new web3-kit('http://htmlcoin:htmlcoin@localhost:14889');
web3-kit.encoder.objToHash(abiObj, isFunction);
web3-kit.encoder.addressToHex(address);
web3-kit.encoder.boolToHex(value);
web3-kit.encoder.intToHex(num);
web3-kit.encoder.uintToHex(num);
web3-kit.encoder.stringToHex(string, maxCharLen);
web3-kit.encoder.stringArrayToHex(strArray, numOfItems);
web3-kit.encoder.padHexString(hexStr);
web3-kit.encoder.constructData(abi, methodName, args);
```

## Decoder
`Decoder` static functions are exposed in web3-kit instances.
```
const { web3-kit } = require('web3-kit');

const web3-kit = new web3-kit('http://htmlcoin:htmlcoin@localhost:14889');
web3-kit.decoder.toHtmlcoinAddress(hexAddress, isMainnet);
web3-kit.decoder.removeHexPrefix(value);
web3-kit.decoder.decodeSearchLog(rawOutput, contractMetadata, removeHexPrefix);
web3-kit.decoder.decodeCall(rawOutput, contractABI, methodName, removeHexPrefix);
```

## Utils
`Utils` static functions are exposed in web3-kit instances.
```
const { web3-kit } = require('web3-kit');

const web3-kit = new web3-kit('http://htmlcoin:htmlcoin@localhost:14889');
web3-kit.utils.paramsCheck(methodName, params, required, validators);
web3-kit.utils.appendHexPrefix(value);
web3-kit.utils.trimHexPrefix(str);
web3-kit.utils.chunkString(str, length);
web3-kit.utils.toUtf8(hex);
web3-kit.utils.fromUtf8(str);
web3-kit.utils.isJson(str);
web3-kit.utils.isHtmlcoinAddress(address);
```

## Running Tests
You need to create a `.env` file in the root folder with the following variables in the following formats. Change it to how your environment is setup.
```
HTMLCOIN_RPC_ADDRESS='http://htmlcoin:htmlcoin@localhost:14889'
SENDER_ADDRESS='hMZK8FNPRm54jvTLAGEs1biTCgyCkcsmna'
WALLET_PASSPHRASE='htmlcoin'
```
