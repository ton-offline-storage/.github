# Overview 
Main part of this project is an air-gapped cold wallet for TON blockchain. This software allows
to separate process of signing transactions from sending them to the network

Software consists of 2 parts:
  - [offline client](https://github.com/ton-offline-storage/ton-offline-client), web page designed for usage on an offline computer(browser is required). Creates and signs transactions
  - [online client](https://github.com/mcnckc/ton-airgap-client), android application, broadcasts transactions, shows balances and history

Additional component of the project is [address generator](https://github.com/ton-offline-storage/address-generator), which allows to obtain personalized
address and use it further. It is safer, but not mandatory, to generate address on an offline computer

# TON Air Gap Wallet usage

1. Download [offline client](https://github.com/ton-offline-storage/ton-offline-client), transfer it to an offline computer
using USB stick or another way not involving internet
2. Install [online client](https://github.com/mcnckc/ton-airgap-client) on your android device
3. Use offline client to create new address, scan transaction with online client, follow instructions. Use your new address to receive funds
4. When you want to send funds, go to offline client, follow instructions. Scan transaction with online client, follow instructions
5. Use online client to watch your addresses' balances and transaction history

### Tips
- You can create multiple addresses with offline client, and track them all in online client
- You can also transfer [address generator](https://github.com/ton-offline-storage/address-generator) to an offline computer, generate
  address here, and import received mnemonic and wallet_id to an offline client, instead of creating random address in offline client
- If you don't want to bother with offline computer, you can use address generator, as well as offline client, on your primary online computer. It is less safe, but will work
