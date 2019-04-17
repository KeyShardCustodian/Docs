# Documentation
 
## KeyShard Custody Services
KeyShard custody service provides the advanced technology and the architecture to secure crypto assets. 

KeyShard provides the framework to create wallet, which is controlled by two or multiple participants. Access control is managed by the Quorum Policy for all of the participants. The underlying framework is based on threshold signature, which is a specific protocol of secure multi-party computation.

## Getting Started
Integrating custody services within your application, such as an exchange, a token fund, or any other application managing crypto assets, is accomplished with the KeyShard API.

This reference includes operations: 
 
1. Create accounts 
2. Transfer coins

### Supported Coins
* BTC, BCH, ETH, ETC, XRP, EOS, USDT, LTC,ADA,TRX,DASH,XTZ, XLM ,ERC20

* for free trial

Energon - [PlatON Testnet](https://www.platon.network/#/)



### Pre-requisites

Linux platform is supported. SDKs for other platform are coming soon.

#### Linux
##### Hardware
- CPU: 2.0 GHz 64-bit (Intel, AMD)
- RAM: 2 GB
- Disk space: 200 MB

##### OS
- Ubuntu 16.04/CentOS 7.4 and later

##### Platform
- GCC 4.8.5 or newer
- Oracle Java 64-bit JVM 8 or newer
- JDK 1.8.0 or newer


### Java API

1. Get KeyShard API SDK package (Please contact us from the homepage);
2. Decompress the package.

#### Package Details

- libs - libraries that are dependencies for the Java files.
- docs - HTML descriptions of the APIs.
- jar - contains the Keyshard SDK Jar(KeyShard.{version}-SDK.jar ).
- sample - sample bin.

##### Config

- config  lib path in LD_LIBRARY_PATH
```bash
export LD_LIBRARY_PATH = {your_libs_path}
```
- config keyshard server hosts  
```bash
sudo vim /etc/hosts
insert item “183.56.161.210 testks.platon.network”
```

#### Usage

##### Initialize Keyshard SDK

The main entry to the KeyShard SDK for Java is the class com.platon.keyshard.client.KeyShardSDK.

Use its instance function to get a singleton instance to work with:

```java
public static KeyShardSDK getInstance()
```

Returns:
- The instance of the SDK

Example:
```java
KeyShardSDK keyshard = KeyShardSDK.getInstance();
```

Before using the KeyShard SDK you must initialize it with the following command:

```java
keyshard.init(STORAGE_URL);
```

Replace STORAGE_URL with the path of private key share will be stored by client,it is relative path of of the current directory.

You must check the return status to determine if this initialized successfully.

##### Init Wallet
A KeyShard participant can be a participant of several wallets. When creating a new wallet on the KeyShard server, the relevant participants are added to the wallet according to policy.

```java
class Participant {
	private String email;
	private String companyName;
	private String name;
}

enum Policy {
	NONE("NONE"),
	KEYSHARD22("2-2"),
	KEYSHARD23("2-3")
}

public int initWallet(Participant participant, Policy policy, String walletName, String coinType)
```

Parameters:

- participant - contains the participant info.
- policy - policy which controls members access wallet
- coinType - type of crypto currency

Returns:
- The id of wallet

For example:

```java
int walletId = keyshard.initWallet(participant, KEYSHARD22, "platon", "Energon");
```

##### Join Wallet
When one participant requests to generate wallet key-pair,KeyShard server will notify relevant participants to approve operations.
Once all relevant participants approve the request, KeyShard will start the process of creating a new wallet key-pair(each participant has its own private key share,but public key is same ).

To generate wallet key-pair, KeyshardSDK provide the joinWallet method:

```java
public void joinWallet(int walletId, KeyshardSdk.CompletionListener listener)
```

Parameters:

- walletId - wallet ID to load
- listener - handle response msg, when joinWallet success ,  publickey and private key share will stored in STORAGE_URL

For example:

```java
keyshard.joinWallet(walletId, listener -> {
// check status
});
```

After the wallet key-pair is generated, the application can get wallet information:

```java
public String getWallet(String walletId)
```

Parameters:

- walletId - wallet ID to load

Returns:

- The info of wallet

##### Wallet Transfer
KeyShard provides a multi-participant (quorum) transfering API, used to sign transaction.
To enhance security, transfer transactions are verified on the participant's device, If verification fails, then the SDK returns an error and the transfer is halted.


To transfer wallet balance , KeyshardSDK provide the applyTransfer method:
```java
public void applyTransfer(int walletId, String toAddress, BigInteger amount,
keyshardSdk.CompletionListener listener)
```

Parameters:

- walletId - wallet ID to load
- toAddress - transfer to address
- amount   -  transfer amount
- listener - completion listener

For example:

```java
keyshard.applyTransfer(walletId, toAddress, amount, listener -> {
// check status
});
```

After the minimum number of participants (as configured in the wallet policy) approve the transaction, threshold signing is ready for execution.

#### SAMPLE USAGE
The following file are provided for the keyshard client in the sample folder:

- KeyShard.{version}-Demo.jar

before execute the jar, config for libs must be effective

##### Init wallet:

```bash
java -jar KeyShard.{version}-Demo.jar -e {your email} -c {your company} -n {your name} -w {wallet name}
```
Parameters:
- e - participant email address
- c - participant company name
- n - participant name
- w - wallet name

Returns:

- The id of wallet

##### Join wallet

```bash
java -jar KeyShard.{version}-Demo.jar -i {walletId}
```
Parameters:
- i - the walletId which return in init wallet step

Returns:
- the wallet address

##### Wallet transfer

```bash
java -jar KeyShard.{version}-Demo.jar -i {walletId} -t {recipientAddress} -v {transfer amount}
```
Parameters:
- i - the walletId which return in init wallet step
- t - the address which the token transfer to
- v - the amount of transfer  

Returns:
- the transfer transaction hash

