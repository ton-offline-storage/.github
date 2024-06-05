# Overview 
Main part of this project is an air-gapped cold wallet for the TON blockchain. This software allows
to separate process of signing transactions from sending them to the network, and sign them on an offline device

This allows to reach probably the highest possible level of security, comparable (or even better?) to hardware wallets, like Ledger, etc.

If classic hot wallets are secure enough, why more than 8 million people bought a hardware wallet?

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

## Android client alternative
If you don't have an android device, or don't want to use online client, you can transfer the `.boc` file of transaction to online PC, and
send from there with [liteclient binary](https://docs.ton.org/develop/smart-contracts/environment/installation).

It is less safe, because you need to succesively connect USB to both online and offline systems, which is a potential line of communication, but it works.

Download liteservers [config](https://ton.org/global-config.json), and run following command in terminal:

**Windows:** `lite-client -C global-config.json -c "sendfile transaction.boc"`

**Linux:** `lite-client -C global-config.json -c 'sendfile transaction.boc'`

## Additional security measures

- Immediately disconnect USB with offline client after you transferred it to persistent storage, before any sensitive data appeared in the system
- It is better to never connect any USB or other drives further to Tails OS to maintain isolation. You can manually retype recipient address to offline client,
  and verify it in online client
- You can store your mnemonic in persistent storage, but it is safer to store it on several physical mediums, and type it in an offline client every time.
  It insures you from losing USB drive with mnemonic, and makes task harder for potential *very* sophisticated malware
- After you transfered offline client to offline system, calculate SHA256 hash of the zip file you transfered. In linux and Tails,
  run `sha256sum <filename>.zip`. Compare hash with hash of your [release](https://github.com/ton-offline-storage/ton-offline-client/releases), they must match.
  This way, it's not enough for malware to simply forge the offline client in your primary system.

You may notice, that *very* sophisticated malware still may establish offline communication between two systems with common hardware. You can take additional countermeasures every time you switch between systems:

- Physically remove all persistent drives
- Physically disconnect hardware from electric power for 1 minute - this will obliterate any data in volatile RAM memory
- Also remove CMOS battery from motherboard - this will erase any data stored in motherboard
- Use separate peripherals(Mouse, keyboard, headphones, etc.) for offline system. Modern peripherals may have persistent memory 

This leaves no room for communication between two systems, there is nowhere else to store data.
It may be baffling to do this steps on a laptop, so it worth noting that even without them communication requires *both*
systems to be infected with *very* sophisticated malware, which is unlikely.

Moreover, regardless the taken measures, under assumption that there were no *very* specialized malware on your primary system when you have prepared tails and offline client,
offline system is safe forever, because after that, whenever primary system is active, offline USB is not connected to other hardware, meaning malware cannot get from primary to offline system.

# Security discussion

Why is an air gapped wallet considered secure, and how secure it is?

Here we only discuss non-custodial wallets, where you own your mnemonic phrase.
Custodial wallets are a different category, they don't have fundamental differences from traditional online banking,
and their concept contradicts the very basic idea of any cryptocurrency since Bitcoin - decentralization.
And one can't say custodial wallets are ultimately secure. But can you face troubles when using popular and trusted
custodial storage? Turns out you can:

 - Something like [this](https://en.wikipedia.org/wiki/Bankruptcy_of_FTX) can happen to anyone
 - Something like [this](https://www.binance.com/en/support/announcement/changes-of-services-to-users-in-russia-4887e569afdf4b1e89e024371d3a49b9) can happen if you are unlucky with your citizenship

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
- But you calculate SHA256 of the offline client in Tails, so malware also has to forge the installed Tails system itself, making it show wrong SHA256 result for the forged offline client.
- Malware has to invent genius way to spread itself to your android device
- The copy on your android device exactly targets online client, and has to intervene with it's work, intercept the secret from scanned QR code and send it through the internet

This is incomparable to the level of sophistication needed to steal a secret from a classic non-custodial wallet on an online computer - a simple keylogger or spy malware would work.
If a hacker managed to go through all this, imagine how easy is it for him to develop a malware targeting any classic hot wallet.

Moreover, if there was no *very* specialized malware on your primary system, when you installed Tails and copied offline client, the offline system is safe forever, because no
information enters it, except for keyboard input. That's not true for classic wallets - you can catch malware anytime, and your secret, stored or used in the primary system is endangered.
The longer you hold the crypto, the bigger is the chance.
The last question is, how many hackers even know about TON Air Gap Wallet, and how many of them will prefer to hack it, instead of hacking classic widely adopted wallets, which run on online devices?

That is all an explanation, why air gapped wallet is considered significantly more secure, than classic non-custodial wallets

### Comparsion with hardware wallets

There are commercial hardware wallets, like Ledger for example. They are also cold wallets, which sign transactions on a separate device.
Are they better than Ton Air Gap Wallet, are they safer?

There is one clear advantage of hardware wallets, they are easier to use than Ton Air Gap Wallet.
This is the only advantage known to us, here is the list of disadvantages:

- Price of Ledger starts from 70 USD. For the Air Gap wallet you only need 2 USB sticks - you probably already have them, if not, that would cost less than 10 USD.

This could actually be the end of the list, because at least 7 times price difference puts wallets in different categories.
Nonetheless, more thoughts:

- Both wallets are theoretically hackable, but hacking any hardware wallet is the ultimate dream of every hacker in the world - how many of them even know about
Ton Air Gap Wallet?
- Can you be sure, that the hardware wallet that has been delivered to you, is the real device from the manufacturer, and it has original firmware? Hardware wallet is a very desirable thing to
  design identical, but malicious analogs. Obviously, you **must** order hardware wallets from the official manufacturer website. Chances of getting fake device vastly increase, when ordering from marketplace, or
  buying in a third-party store. Even if you bought from the official web-site, can you be sure that nothing happens during delivery?
  In addition if you were unlucky to be born in some of [this](https://support.ledger.com/hc/en-us/articles/360006562214-Unavailable-Countries?support=true) countries, you **cannot** buy a popular hardware wallet from the official website at all.
  Most of these countries also do not appear in the list of available ones for [trezor](https://trezor.io/) as well.
  
  In comparison, what is the chance that the random USB stick you have bought has a backdoor, specifically targeting TON Air Gap Wallet? What this backdoor should even be?
  
- It is not a big problem, but worth mentioning - Ledger's code is not completely open source. Ton Air Gap Wallet is absolutely open source, and, speaking about the most
sensitive part - offline client - you only have to inspect 400 lines of JavaScript, which is *relatively* easy.
- Ledger is designed, on a hardware level, in such a way, that recovery phrase can never ever theoretically leave the device, right?
  
  **WRONG.** Ledger has a recovery feature, where your recovery phrase is split into 3 parts, it leaves the device and is sent over the internet.
  Recovery phrase **CAN** leave the ledger. Will it, and will a vulnerability be found? We don't know

  Will Ledger company, under government pressure, or for other reasons, make this feature mandatory? We don't know
- There were no security incidents with hardware wallets, which were the fault of the wallet provider, right? [Wrong](https://www.ledger.com/blog/security-incident-report)
- Hardware wallets are not as anonymous as you could expect. You **must** order the device from official website, and you have to provide the company with your email address,
  and address for shipping. Many people also provided the companies with additional personal information. And this information is **not** [safe](https://www.ledger.com/message-ledgers-ceo-data-leak)
  
  But funds stored on hardware wallets were not affected right? Yes. However, as soon as client's personal information was available to scammers, ledger owners were attacked
  with all sorts of phishing scams - email, phone numbers, and paper letters to a home address, everything was used. Would you be among those who fell for a phishing? We don't know.
  Nonetheless, there are different people in the world, and what some of them would do, knowing you store large amounts of crypto, and knowing your home address?
  
  In contrary, *which data* and *from where* is going to leak, if you are using TON Air Gap Wallet?

It is an open question which cold wallet to use, but we personally prefer Ton Air Gap Wallet to established hardware wallets.

### Trusting TON Air Gap Wallet

But maybe the contents of this project's repositories are malicious in the first place? It could be, though, it is *comparably* easy to ensure that's not the case.
Assume the offline client is not malicious, and there is no third party malware. As we discussed earlier, your secret firstly has to leave offline system - it cannot leave through internet,
it cannot go to primary system, because there are no malware in offline system, and it cannot leave through QR code, because there are no malware in offline system.
  
Therefore, it is enough to trust the offline client. For that it is enough to inspect 400 lines of JavaScript, and 300 lines of HTML, or trust someone who inspected it.
Speaking about open source projects, that's a relatively small number.

  







