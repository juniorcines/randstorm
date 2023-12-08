## RandStorm
From 2011 - 2015 many popular crypto exchanges used BitcoinJS to generate private keys. 

There was an issue with BitcoinJS due to the absence of `window.crypto.random` in many browsers. Consequently, this led to a silent failure of the `window.crypto.random` call in JSBN when employed by early versions of BitcoinJS, forcing entropy to be gathered from `Math.random()`.

Using `Math.random()` for cryptographic key generation should never be done. However, during the 2011-2015 timeframe, `Math.random()` was used on all major browsers to generate private keys.

![Project Image](SecureRandom.png)

This program generates a Random Private Key using the python equivalent to SecureRandom() in JSBN javascript library with Math.random(). 
Rather than using the more secure libraries to generate private keys, this private key generator function emulates the weak private key generation used in the JSBN library from 2011 - 2015:

```python
class MathRandomSimulator:
    # Equivalent to SecureRandom() used in JSBN javascript library with Math.random()
    def __init__(self, psize=32):
        self.rng_pool = bytearray()
        self.rng_pptr = 0
        self.rng_psize = psize
        
        while len(self.rng_pool) < self.rng_psize:
            t = int(random.random() * 65536) 
            self.rng_pool.extend(t.to_bytes(2, 'big'))

        self.rng_pptr = 0

    def next_bytes(self, size):
        return self.rng_get_bytes(size)

    def rng_get_bytes(self, size):
        result = bytes(self.rng_pool[self.rng_pptr:self.rng_pptr + size])
        self.rng_pptr += size
        return result
```

The HEX string is created from MathRandomSimulator and converted to a P2P Bitcoin address in thie following format:

<pre>
    Hex: f4389d0921ead29f272294ea790cf4112140e86e347d1933fc302373fb451bdc
    P2P: 1FaVN8XPyNHchgkNRZMwBQGqTMf531yebX
</pre>

It then compares the P2P address in a set of addressess looking for a match. If there is a match it prints to file. 

## Key Generating and Searching Speed

The program uses multiprocessing to utilize all avaiable cores.

Addresses to be searched are loaded into RAM using mmap. 

The P2P address generation function has been optimized to use the coincurve library for very fast key generation. 

Each Core can generate a private key and public key at a rate of ~ 13,000 keys / second.  

With 6 cores you can generate 66,000 Keys / second and search 23,000,000 Addresses / second.

This is 5.729 billion keys / day.

## Download & Installing :

```bash
git clone https://github.com/RandstormBTC/randstorm/
cd randstorm
pip install -r requirements.txt
```
![Project Image](randstorm.png)

The generated P2P Bitcoin address is cross-checked against the addresses in the database: 'Bitcoin_addresses_December_06_2023.txt'

Run On Windows (cmd / powershell):
```bash
python randstorm.py
```
Run On Linux (debian):
```bash
python3 randstorm.py
```
## Download Addressess 
You can find an updated List of all funded Bitcoin addresses at:

http://addresses.loyce.club/

To save all P2P addressess to a file use:
<pre>
grep '^1' Bitcoin_addresses_December_06_2023.txt 
</pre>

Update the addressess to search in P2PSearch.py 

## Legal
This software is for education purporses only and should not be configured and used to find (Bitcoin/Altcoin) address hash (RIPEMD-160) collisions and use (steal) credit from third-party (Bitcoin/Altcoin) addresses. This mode might be allowed to recover lost private keys of your own public addresses only.

Another mostly legal use case is a check if the (Bitcoin/Altcoin) addresses hash (RIPEMD-160) is already in use to prevent yourself from a known hash (RIPEMD-160) collision and double use.

Some configurations are not allowed in some countries (definitely not complete):

Germany: § 202c Vorbereiten des Ausspähens und Abfangens von Daten
United States of America (USA): Computer Fraud and Abuse Act (CFAA)
file_path = 'P2P_addresses_December_06_2023.txt'

Why use p2p Addressess? Becuase they can be generated very quickly and the majority of addressess are in this format. P2SH addressess (address starting with a 3) are also quickly generated but they are not as frequently used as P2P. 

## Source:
https://www.unciphered.com/blog/randstorm-you-cant-patch-a-house-of-cards
https://medium.com/@betable/tifu-by-using-math-random-f1c308c4fd9d

## Donate:
BTC: bc1q2rqz0mzwxdm0umhlllsyd5rt30uh8kswhqcnqp
