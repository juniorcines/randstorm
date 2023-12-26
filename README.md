
<p align="center">
  <img src="RandstormProgram.png" alt="Your Image Description">
</p>
Between 2010 and 2015, many exchanges and websites relied on BitcoinJS-lib v0.1.3 for Bitcoin wallet generation. The issue was that many browsers didn't use window.crypto.random, which lead to entropy being collected from Math.random(). <br><br>

<p align="center">
  <img src="secure_random.png" alt="Your Image Description">
</p>

The code inside the if block will only be executed if all three conditions are true. If you aren't using an old version of Netscape, then the code block will be skipped. As of 2011, most people were using more modern browsers like Internet Explorer, Firefox, Google Chrome, Safari, or Opera. This means that the random number generator was seeded using rng_seed_time() with Math.random() as the only source of entropy. 

## Generating a Bitcoin address
I made a simple HTML file you can load in the browser that creates private keys with a set seed using the SecureRandom function is found in [rng.js](http://www-cs-students.stanford.edu/~tjw/jsbn/rng.js) 

Currently I am running it in Node.js to test private key collision rates.

<p align="center">
  <img src="SecureRandomGen.png" alt="Your Image Description">
</p>

This is an example of the bitcoinjs-lib browser implementation used. Many of the wallets were generated like this, without additional passwords: 

 ```js
key = new Bitcoin.ECKey()

// Print your private key (a hex string)
console.log(key.toString())
// => 8c112cf628362ecf4d482f68af2dbb50c8a2cb90d226215de925417aa9336a48

// Print your public key (defaults to a Bitcoin address)
console.log(key.getPub().getAddress())
// => 14bZ7YWde4KdRb5YN7GYkToz3EHVCvRxkF
 ```

## Vulnerable Wallets

This vulnerability is only for wallets that were created using [BitcoinJS-lib v0.1.3](https://github.com/bitcoinjs/bitcoinjs-lib/releases/tag/0.1.3). Since we can't determine when the wallet was generated or even if the wallet was generated using BitcoinJS-lib v0.1.3, it makes things very difficult. 

We can examine when the first transaction took place using the blockchain. Using Get_First_Transaction.py we can get the first transaction date using a free API call on [btcscan.org](https://btcscan.org/). If the address was generated on December 24, 2012, 2:44:56 AM, then using the [Unix epoch time](https://www.epochconverter.com/) we can see that the seed = 55793394904000. This is the Unix epoch time in milliseconds. This seed will always create the same private key.

```bash
Address: 1NUhcfvRthmvrHf1PAJKe5uEzBGK44ASBD
First Transaction: 2014-03-16 23:48:51 GMT -7
Current Balance: 1.9999 BTC
```
Convert the date 2014-03-16 23:48:51 GMT -7 to Unix epoch time in linux with command:

```bash
date -d "2014-03-16 23:48:51 GMT -7" +"%s" | awk '{print $1 * 1000}'
```
1395038931000

Now, get the date from whatever time period you want to try before March 3, 2014.

March 1, 2014 = 1393635661000

1395042082000 - 1393635661000 = 1.4 Billion Seeds

## Generate Seeds

Using the implementation of SecureRandom(), set the seed to March 1, 2014 = 1393635661000 and generate keys incrementally until the date of the first transaction:

Seed: 1310691661000 <br>
Hex: 6ad2d763712eae6428e2922d7181f92fb70d0e564d1dd38dd0aa9b34b844c0cb <br>
P2PKH: 1JbryLqejpB17zLDNspRyJwjL5rjXW7gyw<br>

Seed: 1310691661001 <br>
Hex: fb6ad847a48da87b332b565b548347078a1b9890b9c352a4d9993ae09c189fa6 <br>
P2PKH: 1273EG6iByUWoDY8PrCBEhJsEBLEzk1rEi<br>
...
## Check for a Match 

The script generates the private key from the seed, converts it to the Bitcoin address and checks if it matches. 

## Download & Installing

Download and install the latest version of Python. 

```bash
git clone https://github.com/RandstormBTC/randstorm/
cd randstorm
pip install -r requirements.txt
```

Run On Windows (cmd / powershell):
```bash
python randstorm.py
```
Run On Linux (debian):
```bash
python3 randstorm.py
```
## Download Addressess 
All funded Bitcoin addresses can be downloaded at:

http://addresses.loyce.club/

## Disclaimer
This software is for education purporses only and should not be configured and used to find (Bitcoin/Altcoin) address hash (RIPEMD-160) collisions and use (steal) credit from third-party (Bitcoin/Altcoin) addresses. This mode might be allowed to recover lost private keys of your own public addresses only.

Another mostly legal use case is a check if the (Bitcoin/Altcoin) addresses hash (RIPEMD-160) is already in use to prevent yourself from a known hash (RIPEMD-160) collision and double use. Some configurations are not allowed in some countries.

## Questions and Comments

Please feel free to share any questions. This is a work in progress and replicating the exact SecureRandom() function in python from Javascript has not been very easy. There still may be some issues...

## Contributors 

Special thanks to ChatGPT and DeepSeek Coder for the help. 

## Sources:

 <https://www.unciphered.com/blog/randstorm-you-cant-patch-a-house-of-cards>

 <https://jandemooij.nl/blog/math-random-and-32-bit-precision/>

 <https://medium.com/@betable/tifu-by-using-math-random-f1c308c4fd9d>

 <https://thehackernews.com/2023/11/randstorm-exploit-bitcoin-wallets.html>

 <https://cointelegraph.com/news/randstorm-crypto-wallet-vulnerability-bitcoin-dogecoin-litecoin-zcash>

 <https://www.kaspersky.com/blog/vulnerability-in-hot-cryptowallets-from-2011-2015/49943/>


## Donate:
BTC: bc1q2rqz0mzwxdm0umhlllsyd5rt30uh8kswhqcnqp
