A library for sending notifications on Windows using Flutter

## Features

- Sending notifications by ready-made templates of the plugin
- Send notifications by user-created templates
- Receive notification events sent
- Delete all notifications sent by you from Windows Action Center
- Delete notifications group sent by you from Windows Action Center

## Usage

### Send notification with plugin template

Sending notifications through the plugin's own templates, there are currently 3 template available in the plugin.

```
import 'package:windows_notification/notification_message.dart';
import 'package:windows_notification/windows_notification.dart';

// Create an instance of Windows Notification with your application name
final _winNotifyPlugin = WindowsNotification(applicationId: "ASGHAR ONLINE");
// create new NotificationMessage instance with id, title, body, and images
NotificationMessage message = NotificationMessage.fromPluginTemplate(
      "test1",
      "TEXT",
      "TEXT",
      largeImage: file_path,
      image: file_path
    );
// show notification    
_winNotifyPlugin.showNotificationPluginTemplate(message);

```

You can also create Wallet files with this library. To do so, you first need
the private key you want to encrypt and a desired password. Then, create
your wallet with

```dart
Wallet wallet = Wallet.createNew(credentials, "password", random);
print(wallet.toJson());
```

You can also write `wallet.toJson()` into a file which you can later open
with [MyEtherWallet](https://www.myetherwallet.com/#view-wallet-info)
(select Keystore / JSON File) or other Ethereum clients like geth.

#### Custom credentials

If you want to integrate `web3dart` with other wallet providers, you can implement
`Credentials` and override the appropriate methods.

### Connecting to an RPC server

The library won't send signed transactions to miners itself. Instead,
it relies on an RPC client to do that. You can use a public RPC API like
[infura](https://infura.io/), setup your own using [geth](https://github.com/ethereum/go-ethereum/wiki/geth)
or, if you just want to test things out, use a private testnet with
[truffle](https://www.trufflesuite.com/) and [ganache](https://www.trufflesuite.com/ganache). All these options will give you
an RPC endpoint to which the library can connect.

```dart
import 'package:http/http.dart'; //You can also import the browser version
import 'package:web3dart/web3dart.dart';

var apiUrl = "http://localhost:7545"; //Replace with your API

var httpClient = Client();
var ethClient = Web3Client(apiUrl, httpClient);

var credentials = EthPrivateKey.fromHex("0x...");
var address = await credentials.extractAddress();

// You can now call rpc methods. This one will query the amount of Ether you own
EtherAmount balance = ethClient.getBalance(address);
print(balance.getValueInUnit(EtherUnit.ether));
```

## Sending transactions

Of course, this library supports creating, signing and sending Ethereum
transactions:

```dart
import 'package:web3dart/web3dart.dart';

/// [...], you need to specify the url and your client, see example above
var ethClient = Web3Client(apiUrl, httpClient);

var credentials = EthPrivateKey.fromHex("0x...");

await client.sendTransaction(
  credentials,
  Transaction(
    to: EthereumAddress.fromHex('0xC91...3706'),
    gasPrice: EtherAmount.inWei(BigInt.one),
    maxGas: 100000,
    value: EtherAmount.fromUnitAndValue(EtherUnit.ether, 1),
  ),
);
```

Missing data, like the gas price, the sender and a transaction nonce will be
obtained from the connected node when not explicitly specified. If you only need
the signed transaction but don't intend to send it, you can use
`client.signTransaction`.

### Smart contracts

The library can parse the abi of a smart contract and send data to it. It can also
listen for events emitted by smart contracts. See [this file](https://github.com/xclud/web3dart/blob/development/example/contracts.dart)
for an example.

### Dart Code Generator

By using [Dart's build system](https://github.com/dart-lang/build/), web3dart can
generate Dart code to easily access smart contracts.

To use this feature, put a contract abi json somewhere into `lib/`.
The filename has to end with `.abi.json`.
Then, add a `dev_dependency` on the `build_runner` package and run

```dart
pub run build_runner build
```

You'll now find a `.g.dart` file containing code to interact with the contract.

#### Optional: Ignore naming suggestions for generated files

If importing contract ABIs with function names that don't follow dart's naming conventions, the dart analyzer will (by default) be unhappy about it, and show warnings.
This can be mitigated by excluding all the generated files from being analyzed.  
Note that this has the side effect of suppressing serious errors as well, should there exist any. (There shouldn't as these files are automatically generated).

Create a file named `analysis_options.yaml` in the root directory of your project:

```dart
analyzer:
  exclude: 
    - '**/*.g.dart'
```

See [Customizing static analysis](https://dart.dev/guides/language/analysis-options) for advanced options.

## Feature requests and bugs

Please file feature requests and bugs at the [issue tracker][tracker].
If you want to contribute to this library, please submit a Pull Request.

[tracker]: https://github.com/xclud/web3dart/issues/new
