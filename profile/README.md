# Overview 
Main part of this project is an air-gapped cold wallet for the TON blockchain. This software allows
to separate process of signing transactions from sending them to the network, and sign them on an offline device

This allows to reach probably the highest possible level of security, comparable (or even better?) to hardware wallets, like Ledger, etc.

Software consists of 2 parts:
  - [offline client](https://github.com/ton-offline-storage/ton-offline-client), web page designed for usage on an offline computer(browser is required). Creates and signs transactions
  - [online client](https://github.com/mcnckc/ton-airgap-client), android application, broadcasts transactions, shows balances and history

Additional component of the project is [address generator](https://github.com/ton-offline-storage/address-generator), which allows to obtain personalized
address and use it further. It is safer, but not mandatory, to generate address on an offline computer

# TON Air Gap Wallet usage

1. Download [offline client](https://github.com/ton-offline-storage/ton-offline-client), transfer it to an offline computer
using USB stick or another way not involving internet
2. Install [online client](https://github.com/mcnckc/ton-airgap-client) on your android device
3. Use an offline client to create a new address, scan transaction with an online client, follow instructions. Use your new address to receive funds
4. When you want to send funds, go to an offline client, follow instructions. Scan transaction with online client, follow instructions
5. Use online client to watch your addresses' balances and transaction history

### Tips
- You can create multiple addresses with offline client, and track them all in online client
- You can also transfer [address generator](https://github.com/ton-offline-storage/address-generator) to an offline computer, generate
  address here, and import received mnemonic and wallet_id to an offline client, instead of creating random address in offline client
- If you don't want to bother with an offline computer, you can use the address generator, as well as an offline client, on your primary online computer. It is less safe, but will work

# Offline computer?

The idea is that operating system, since the very installation, is *never* connected to the internet. 
So, the basic way to do this, is to have special hardware, physically disconnect it from internet, remove any data from it, 
install a new OS, and never connect to the internet again.

But what if you don't have extra hardware for this single purpose, and don't want to buy one?

Actually, offline computer(with it's OS) can share *some* parts of the hardware with your primary online computer.
For example, you can have a separate hard drive with an offline system installed. When you want to use offline computer, you do
the following:
  1. Turn off primary computer, physically disconnect it from internet
  2. Connect offline hard drive, boot from it into offline OS
  3. Disconnect offline drive, use computer as usual

This already makes two systems fairly isolated, and clearly offline system can never access the internet.

And there is an option to use a USB stick instead of a new drive, which is cheaper and easier. One option is to use [Tails OS](https://tails.net)

You can do whatever you want to obtain an offline computer, we will discuss the Tails OS USB approach.

## Tails OS guide
1. Find a trusted computer, which is unlikely to have malware. If you have only one, it is reasonable to perform several checks for viruses.
3. Find a 8GB or more USB stick, install Tails to it following official [guide](https://tails.net/install/)
4. You need another USB stick, to transfer [offline client](https://github.com/ton-offline-storage/ton-offline-client) to Tails. Wipe all data on it, and put offline client there
5. Turn off your computer, disconnect from the internet. You need to boot and enter BIOS, usually by pressing a special key during boot - Del, F12, etc. - google: Enter BIOS \<your computer model\>
6. In BIOS, choose Tails USB stick as primary boot device - google: <your motherboard/your laptop model> change boot device
7. Boot with Tails, follow instructions. You need to configure [persistent storage](https://tails.net/doc/persistent_storage)
8. Connect USB with offline client, transfer offline client to persistent storage
9. Thats it - when you need offline client, boot with Tails and open offline client from persistent storage

## Additional security measures

- Immediately disconnect USB with offline client after you transferred it to persistent storage, before any sensitive data appeared in the system
- It is better to never connect any USB or other drives further to Tails OS to maintain isolation. You can manually retype recipient address to offline client,
  and verify it in online client
- You can store your mnemonic in persistent storage, but it is safer to store it on several physical mediums, and type it in an offline client every time.
  It insures you from losing USB drive with mnemonic, and makes task harder for potential *very* sophisticated malware
  
You may notice, that *very* sophisticated malware still may establish offline communication between two systems with common hardware. You can take additional countermeasures every time you switch between systems:

- Physically remove all persistent drives
- Physically disconnect hardware from electric power for 1 minute - this will obliterate any data in volatile RAM memory
- Also remove CMOS battery from motherboard - this will erase any data stored in motherboard
- Use separate peripherals(Mouse, keyboard, headphones, etc.) for offline system. Modern peripherals may have persistent memory 

This leaves no room for communication between two systems, there is nowhere else to store data.
It may be baffling to do this steps on a laptop, so it worth noting that even without them communication requires *both*
systems to be infected with *very* sophisticated malware, which is unlikely.

# Security discussion

Why is an air gapped wallet considered secure, and how secure it is?

Here we only discuss non-custodial wallets, where you own your mnemonic phrase.
Custodial wallets are a different category, they don't have fundamental differences from traditional online banking,
and their concept contradicts the very basic idea of any cryptocurrency since Bitcoin - decentralization.
And one can't say custodial wallets are ultimately secure.

In a non-custodial wallet, we need to understand how difficult it is  to steal your mnemonic phrase.
This depends on how a mnemonic phrase is created, stored, and used to sign transactions.
Let's suppose you take every additional security measure described above.

Mnemonic phrase is created in tails OS, stored on physical (e.g. paper) mediums, typed inside tails OS to sign transactions.
Secret can be stolen with a paper, but theft of physical property is outside the topic.
We also do not consider secret cameras inside your room, spies looking through the window, etc.
Actually, we suppose that secret, eventually, can only be stolen inside digital world through the internet, which is a valid assumption, 
since no wallet can withstand other types of attacks, like the aforementioned ones.

Therefore, secret can only be stolen from the tails OS. However, tails OS is never connected to the internet, so
whatever happens in tails OS, secret can not be stolen directly from it, therefore secret firstly has to be transferred to 
another system, which sometimes goes to the internet. It cannot be transferred to your primary online system, because additional
security measures break any theoretical communication between two systems. 

Even if you don't take this measures, you need to
catch a *very* sophisticated malware, which steals secret from offline client memory, writes to, for example, hard drive -
which means the author has found vulnerability in Tails OS, which is designed to be isolated from all hard drives.
And you need other part of malware in online system, which reads secret from special place in hard drive, and sends it
through the internet.

If you follow all additional measures, the only way information can leave tails OS remains the QR code, which you scan in an online client.

Still, this QR code has to be forged in order to hide mnemonic phrase there. So how potential malware can get to Tails? After you transfer
offline client there, the only way information enters tails OS in keyboard input, which is not an option. There are two options to infect tails OS with malware,
both require your primary system to be infected with special malware, at the time you are preparing Tails:

- Tails could be installed with embedded malware.
- Malware could be transferred through a USB, used to transfer offline client(maybe offline client is replaced with malicious software)
  
Now we have a blueprint of the only realistic way to steal the secret phrase. Let's estimate the degree of sophistication this malware needs to succeed.

- It has to be designed with a specific purpose to wait, until you decide to use exactly the TON Air Gap Wallet with Tails OS
- When it happens, it has to track down copying of offline client to a USB drive, and replace offline client with exact copy, with altered QR code creation
- Malware has to invent genius way to spread itself to your android device
- The copy on your android device exactly targets online client, and has to intervene with it's work, intercept the secret from scanned QR code and send it through the internet

This is incomparable to the level of sophistication needed to steal a secret from a classic non-custodial wallet on an online computer - a simple keylogger or spy malware would work.
The last question is, how many hackers even know about TON Air Gap Wallet, and how many of them will prefer to hack it, instead of hacking classic widely adopted wallets, which run on online devices?

That is all an explanation, why air gapped wallet is considered significantly more secure, than classic non-custodial wallets






