# RANDAC, making RANDAO commercially viable

Random number generation is very difficult on a blockchain. A blockchain is characterized by the consensus of nodes, and convergence to a single history of the chain, based on a consensus parameter. Bitcoin and Ethereum are characterized by their “Proof of Work (PoW)” protocol, where the chain with the largest amount of total work is agreed upon as canon, and all nodes in the network try and lengthen this chain via the same PoW protocol. The question we try and answer with this paper is simple: how can we securely generate a random number, while having consensus from all nodes that this specific number is the “correctly chosen” random number.

This question is quite important. There are a multitude of applications that use random numbers for their basic functionality, and current decentralized applications which require random numbers are forced to use a centralized “oracle” service, which determines the random number, and pushes it to the blockchain, after a considerable delay. Why have a decentralized application when integral pieces are dependent on centralized third parties? Random number generation must be determined in a decentralized fashion to have a truly decentralized application.

In this paper, we craft a secure protocol for decentralized random number generation through a Decentralized Autonomous Corporation. We theorize ways we can offer this service initially to the entire EOS.IO blockchain, and with scalability improvements, to all compatible blockchains. We will tokenize participation in our protocol, so that users  receive rewards in proportion to their contribution to the protocol, and their stake of tokens. This is required for security guarantees.

This protocol borrows from a predecessor, [RANDAO](http://randao.org/whitepaper/Randao_v0.85_en.pdf) heavily, but seeks to optimize the reward system of the protocol. RANDAO is characterized by a lack of participation [contract 1](https://etherscan.io/address/0x6C8060507273A0ff175361C6bf9F86e97f8Cf2C8) [contact 2](http://etherscan.io/address/0xb2A1ac7F7253B0EBF6410920ED1342c974bcA67A), and a highly expensive way of generating random numbers compared to centralized counterparts, like the [oraclize.it random datasource](https://blog.oraclize.it/the-random-datasource-chapter-2-779946e54f49).

We argue that random number generation protocols need to reward participants heavily to function correctly. By tying a casino to our random number generation protocol, we give our participants a large source of potential profit for participation in the protocol. In fact, we argue that a decentralized random number generation protocol **requires** an easy way to convert random numbers into profit. Converting random numbers into profit is what a casino does best.

### Why EOS?

The decision to start our randomness generation service on the EOS.IO blockchain might be controversial. Our developers and leads have been involved with cryptocurrency pre-Ethereum, and have seen the ecosystem grow steadily into the construct it is today. We have experience building decentralized applications on Ethereum, and leaving the blockchain we know and love was a tough decision. However, development decisions made by the Ethereum and block.one (parent company of the EOS.IO blockchain) have forced us to move to the EOS.IO chain, at least initially.

We can summarize the decisions in a few sentences. The Ethereum team focusses on being able to sync their chain, from scratch, with a (high end) consumer-grade computer. The EOS.IO team has chosen to forsake this requirement, and requires high-end server architecture to sync their chain, and validate the state of the entire network. There are downsides and short comings to both approaches, however the EOS.IO approach to scaling suits our needs in the short- to mid-term.

With research and development into off-chain RANDAC protocols, we hope to achieve consensus on the random output within a single block, with minimal on-chain footprint. After we achieve this, we will port RANDAC to all suitable blockchains, including Ethereum, and our master-nodes will eventually provide randomness to the entire ecosystem.

### Different Ways of Random Number Generation (RNG)

#### The Trusted Approach

A basic way of random number generation is to nominate a trusted party to generate a random number. This trusted party’s number is law, with zero way to contest the result of the number. 

Many online casinos use this form of randomness generation. The user places a bet, and the casino “reveals” the card after. Whatever the casino revealed is law, and your bet wins or loses by this number. There is a vulnerability here, given that the casino both choses the randomness, and makes money if you lose. Casinos are directly incentivized to cause you to lose more bets, and can do this easily by “fixing” their random number generator to cause you to lose more often than statistically likely. This has happened many times in [past history](https://wizardofodds.com/online-gambling/blacklist/).

#### The Commit-Reveal Approach

Next up, is the basic “provably fair approach” where a central party generates a random number, and then publishes a **hash** of the number. A hashing function is easily remembered by a “scrambling” function, where an input gets scrambled into a single output. Taking the input, and finding the output is easy, while taking an output and trying to determine the input is impossible. Further, the output for a given input is **deterministic** (there is only ONE output for a given input), and **collisions** are exceedingly rare (where TWO or more inputs result in the SAME output).

This is a great way to have a “fair” randomness generation protocol, because after the central party commits their hash of their random number, they cannot change their number. So a player may place a bet, the casino may reveal their random number, and the player may prove that the revealed number, matches the committed hash. This is called a two-party commit-reveal approach for random number generation, and is used in just about every provably-fair centralized casino, including [Funfair](https://medium.com/@eosbetcasino/eosbet-vs-funfair-decentralization-b7a765f7be87).

However, the drawbacks to this approach are simple. The casino (or some central party) must keep the random number secret, and only publish the hash. If the bettor gets ahold of the random number, then it would be quite easy to cheat, because he would know every card he is going to be dealt ahead of time. Again, this approach is centralized, and requires the casino to “reveal” the random number, or **pre-image**, after the bet takes place. A pre-image is the prior input to a hashing function, in this case, it is the secret random number.

Since EOSBet is the first fully decentralized casino, we cannot use this random number generation approach, because this approach is inherently centralized, and requires a central party to keep the random number a secret, until after the bet takes place.

#### The Blockhash Approach

Another way of randomness generation takes advantage of the intrinsic nature of the Proof-of-Work protocol of a blockchain. Proof-of-Work was Satoshi Nakamoto’s [major breakthrough](https://bitcoin.org/bitcoin.pdf), and established the first ever form of “decentralized consensus”. **Mining** works where miners compete to craft a hash lower than a certain number. The first person to generate a hash that is less than a certain number is considered to have mined the block, and receives a pre-determined reward. Generating a hash greater than a certain number is a random process, where the miners increment a number (nonce) and see if it hashes into a low number. If it doesn’t, they increment this number, and try again. If it does hash into a sufficiently low number, the entire process repeats, and miners try to solve the next block in the chain in the same fashion.

This is an easy candidate for randomness, because the correct nonce (or the number that is being incremented until a block is found) is randomly determined. If this nonce was non-random, then PoW would be a broken protocol, where miners could easily guess the nonce, and solve blocks at will. Since the nonce is random, then the output of the mining function (or the blockhash) is also random. However, since anyone can easily verify that this hash is a correct solution to a block, this form of randomness generation is provably-fair, because we can see that this block hashes correctly.

*An easy protocol for decentralized randomness generation is as follows:*

1. Bettor places a bet against the house. It is agreed that the source of randomness is the *next* blockhash. If the next blockhash is even, bettor will win 2x his initial bet. However, if the next blockhash is odd, then the bettor will lose his bet.

2. Once the next block has been mined, the bet resolves. If even, bettor wins. If odd, the house takes the bet. This can be done in a wholly decentralized way, because the randomness is determined in a decentralized manner.

However, there is an attack vector, which occurs, in varying degrees, with all decentralized randomness generation techniques. The issue is that miners actually have some lee-way in choosing the next blockhash. If the miner successfully mines a block, but doesn’t like the blockhash, then the miner can discard this block & not publish the hash. The miner can then try and mine a new block with a different hash. This might occur if a miner is also a bettor, and that block would cause him to lose his bet. However, this requires the miner to “throw away” their hard work at finding a correct hash, and possibly miss out on the block reward. Therefore, performing this sort of attack must be economically profitable for the miner.

*We can simulate this with a python script as follows:*

```python
win_chance = 50 # out of 100
block_reward = 12.5 
uncle_reward = 0
number_trials = 100000
house_edge = 0.01

def play_game(bet):
	profit = 0
	for i in xrange(100000):
		profit -= (bet + bet * house_edge)

		if win_chance >= randint(1,100):
			# game won
			profit += bet * 2

	return profit / 100000

def play_game_cheater(bet, hash_power):
	profit = 0
	for i in xrange(100000):
		profit -= (bet + bet * house_edge)

		if hash_power <= randint(1,100):
			# won block, play game, if lost, withhold block and play again
			if win_chance >= randint(1,100):
				# won game
				profit += bet*2
			else:
				if hash_power <= randint(1,100):
					# miner won the next block too!
					pass
				else:
					# miner didn't find the block
					profit -= (block_reward - uncle_reward)
				# new chance at game
				if win_chance >= randint(1,100):
					# game won
					profit += bet * 2
		
		else:
			if win_chance >= randint(1,100):
				# game won
				profit += bet * 2

	return profit / 100000
```

If we set the `block_reward` to 12.5 and the `uncle_reward` to 0, we are simulating a randomness generation strategy based on a future hash of the Bitcoin network.

```
Hash Power on Bitcoin network     |      Minimum Bet for Profitable Cheat
-------------------------------------------------------------------------
							20%   |								11.5 BTC
							10%	  |								13.0 BTC
							5%    |								15.0 BTC
-------------------------------------------------------------------------
```

If we set the `block_reward` to 3 and the `uncle_reward` to 2.625, we are simulating a randomness generation strategy based on the future hash of the Ethereum network.

```
Hash Power on Ethereum network    |      Minimum Bet for Profitable Cheat
-------------------------------------------------------------------------
							20%   |								0.35 ETH
							10%	  |								0.43 ETH
							5%    |								0.60 ETH
-------------------------------------------------------------------------
```

Note, that these calculations do not take into account any miner revenue from transaction fees (which would lower profitability of an attack). 

Also note that this sort of randomness generation protocol requires a **mined** blockhash. A blockhash generated by a Proof-of-Stake (PoS) or Delegated-Proof-of-Stake (DPoS) scheme can be trivially crafted to reward the producer of that block. Therefore, this scheme cannot secure any bets on a PoS or DPoS blockchain.

This randomness generation strategy is much more fallible on the Ethereum network, because of the introduction of uncle rewards. By rewarding blocks submitted late, this cheating miner can still get a partial reward for his withheld block. On the Bitcoin network, the miner must orphan the block if he choses to withhold it (and doesn’t mine another valid block in time).

This randomness generation strategy would work well, if one were to anchor it to blocks produced on the Bitcoin network. A casino could cap bets at around 10 BTC (~$100,000 at todays rate), and be confident that this form of decentralized randomness generation is safe against miner cheating (assuming miners are rational actors, as we should). However, on the Ethereum network, we would be forced to cap bets as a very small amount (~$200 at todays rate), and this would be an unsatisfactory experience for many bettors.

There is another downside to basing randomness on a future Bitcoin blockhash. Bitcoin blocks, on average, take 10 minutes to produce, so bets at your casino would take 10 minutes (on average) to resolve. This would be an awful user experience, and you can forget about playing interactive games (needing multiple random numbers) like blackjack. However, running a form of decentralized lottery, where tickets are purchased over a period of time, and the lottery resolves on a (much later) future blockhash, is quite viable with a Bitcoin blockhash based randomness strategy.

We can reduce the average bet resolution time to \~ 15 seconds using an Ethereum blockhash, however bets must be quite small, as outlined above. This is still a fairly long time for bet resolution, and devising a PoW protocol where blocks are produced every second with an even smaller reward, would mean that we could only take bets to the tune of a few dollars at our casino, before miner cheating becomes a profitable measure.

So we are stuck with a problem. We need a **large** reward on each randomness generation to discourage cheating. However, we need **fast** production of the random numbers to ensure a satisfactory user experience. Giving large rewards to randomness producers at a high frequency is a non-solution, so how do we optimize this problem?

### Randomness Generation Through a Decentralized Autonomous Corporation (DAC)

We have devised a protocol for distributed, permissionless, and fair randomness generation that runs through a **Decentralized Autonomous Corporation** (or DAC). The basics of this protocol are again, quite similar to the previously conceived RANDAO. However, we optimize our protocol via our governing DAC, so that providers have a very low incentive to cheat, by both maximizing the reward for good behavior, and punishing nodes who exhibit provable bad behavior. We are able to incentivize participation in our protocol via higher and scaled rewards.

Again, all forms of decentralized randomness generation have an **economic confidence interval** where bet sizes are secure against cheating. However, above this interval, it is profitable to cheat. We saw this in our previous Bitcoin/Ethereum blockhash exercize.

We also are able to achieve very low latency with our protocol, and can decrease this further by moving the bulk of the protocol off-chain. We seek to provide secure random number generation to the EOSBet Casino platform, as well as to many other decentralized applications based on the EOS.IO blockchain. We will create a smart contract based API, so that other applications can use our protocol for real-time random number generation in their own applications. We will provide the first Decentralized Random Number Generation as a Service (RNGaaS) on any blockchain.

### What is a Decentralized Autonomous Corporation (DAC)? 

A Decentralized Autonomous Corporation (DAC) is simply, a piece of code that governs interaction between employees of the corporation (E), coordinates their labor (L), outputs a product (P), and sells the product to a consumer (C). While a traditional corporation would have a complex, human-ran managerial structure which coordinates and governs these four elements, a DAC is wholly and autonomously governed by code, usually an immutable smart contract on a public blockchain.

**INFOGRAPHIC HERE**

There are many benefits to this structure, but the primary benefit is an extremely low overhead. Since all the employees interactions are governed by code, there are no “managers” to employ. Therefore, the vast majority of profits of the DAC are given to the employees, not managers or executives. The code simply needs to be submitted to an open, public blockchain like the EOS.IO blockchain, and then will run in perpetuity.

Decentralized Autonomous Corporations are considered by some to be the future of work. Profits are shared amongst all employees, strictly by how much they are contributing to the corporation. Since all interactions with the corporation are permanently stored in a public blockchain, there is no room for embezzlement or malfeasance by executives or managers. Everyone is paid for their share of work, in an instant, codified way.

### RANDAC — Decentralized Randomness Generation Protocol

We have devised a DAC which coordinates individuals to produce provably secure random numbers in a fast, efficient, and fully decentralized way. We reward participants for good work, while punishing malicious behavior.

Our **RNG** token will allow you to participate in our protocol, and receive payment for your work. These rewards will be in proportion to the quantity of **RNG** held, and the quantity of (good) work one has submitted to the protocol.

#### Step 1: Agree to Contribute Randomness

First, a willing contributor will sign up for a “shift” or a period of contribution to the protocol. The contributor will turn on their client, and stake an amount of RNG. By entering into a contribution period, a contributor enters a binding agreement with the DAC smart-contract that they are going to submit provably random shares in a timely manner. If the contributor abides by this contract, they will be paid at the end of the period. However, if the contributor does not abide by this contract and submits late shares, incorrect shares, or engages in other malicious behavior, their pay will decline. In some cases, the contributor will be “booted” from the randomness generation period, and will not receive any pay for that period. *Note, that in no case will users lose their staked RNG.* Instead, the user will lose out of pay for that period, and must wait until the next randomness generation period to contribute random shares.

*A user is incentivized to submit provably random shares in a timely manner.*

#### Step 2: Start Contributing Randomness

At prescribed time periods (outlined in the binding agreement between contributor and DAC), the contributor is required to **commit** the hash of a randomness share, and **reveal** the pre-image to a prior committed randomness share. Note that this will be completely automated, and a holder of our RNG token only needs to install our client software, agree to a staking period, and let this software run while they collect payment for good behavior.

*A contributor that installs node software, and contributes provably random shares will receive payments through the DAC.*

#### Step 3: Collect Rewards

At the end of a contributors staking period, our DAC smart contract will assess the quality of your work. Assuming one was a non-malicious participant, and contributed your shares on time, you will receive a reward at the end of the period. Each consumer of randomness will pay a fee in EOS. These fees will be distributed amongst our randomness generating nodes. Please see section **EOSBet and RANDAC are Intrinsically Tied** for more information on the reward scheme.

We assume that the consumption of decentralized random numbers will be high on the EOS.IO blockchain, and our RNGaaS will be quite lucrative for our participants.

*By purchasing RNG tokens, downloading our client software, and contributing to our protocol, you are doing the following:*

1. You are getting paid for your services in EOS.

2. You are an integral part of the EOS.IO blockchain, because decentralized apps NEED a decentralized source of random number generation.

3. You are a member of one of the very first Decentralized Autonomous Corporations.

### Decentralized Random Number Generation - Under the Hood

Technically speaking, our protocol is relatively simple, with a few possible attack vectors which will be elaborated upon later. Our protocol is a distributed, multi-party, commit-reveal random number generator. We can wrap this simple protocol in a governing structure (the DAC) so that we can reward good participation, and punish malicious actions.

First, users sign up for the protocol and post a required stake in tokens.

Users then generate 32 bytes of random entropy on their client nodes, and submit a hash, or commitment to the smart contract. Any user that does not submit a commitment to the smart contract in a timely manner is dropped from the round.

*Assemble:*

`[commitment A, commitment B, … commitment X]`

Users then reveal the pre-image to their hash, which is verified to hash correctly against the commitment. After all users have revealed their pre-image, these pre-images are hashed together. This output is our random number!

*Assemble:*

`[pre-image A, pre-image B, … pre-image X]`

*Return __Rand__:*

`Rand = Hash(pre-image A, pre-image B, … pre-image X)`

Note, that depending on how we calculate this hash, the ordering of the list does, or does not matter. If we use an order independent operator, like + or \*, then the order of the pre-images do not matter.

`Hash(pre-image A * pre-image B) == Hash(pre-image B * pre-image A)`

*Using an operator where the ordering matters (ex: __.append()__) will change the hashes.*

`Hash(pre-image A.append(pre-image B)) != Hash(pre-image B.append(pre-image A))`

These have tradeoffs which we will explore in the next section, namely speed versus security. Requiring the pre-images to be ordered reduces the effectiveness of a “last pre-image withholding attack”, because only one member may withhold their pre-image & perform this attack each round. Using an order independent operator allows greater speed (users do not have to reveal their pre-images in a strict order). However, a malicious user has a chance, each round, to perform a pre-image withholding attack.

It might be best to punish nodes equally for withholding a pre-image, use an order independent operator like \*, and optimize for the speed of the protocol. Revealing can then take place in a single phase, and the order doesn’t matter. Nodes that refuse to reveal are punished (by withholding their future profits).

This simple protocol is the basis of our RANDAC. If all users behave with good intention, it will be highly successful at generating random numbers. However, we must inspect all possible attack vectors, and attempt to make all forms of attacks economically unprofitable for users.

### Decentralized Random Number Generation - Attack Vectors

#### Incorrect Pre-image Submission

This is a basic attack vector where, after a user has submitted their randomness commitment, they submit a false pre-image that does not hash to the randomness commitment, and instead hashes differently. This is trivial to detect.

Assuming we are using a cryptographically secure hashing function, it will be probabilistically impossible to craft a collision, and users must submit the exact randomness they previously committed. Any pre-image submitted that was not the prior generated randomness will be discarded, and the user will be punished similarly to a pre-image withholding attack (see below).

#### User Collusion Attack

If 100% of users collude in our protocol, they can set the randomness to any number that they like. However, due to the nature of hashing, if a single user refuses to collude with the other users, the output of our protocol will still be securely random. So by nature, our protocol is quite resistant to user collusion attacks, and only requires a single user to be honest to function properly.

The issue is that attempting a collusion attack is unpunished in our basic protocol, as shown above. If the user collusion attack is attempted, but fails, then there is no economic negative enacted on the colluding users. Similarly, if we take all users as rational economic actors, and a user collusion attack is shown to be profitable for all users, then we should expect a user collusion attack to occur. Since a user collusion attack would surely take place through off-chain communication channels, it would be undetected though our basic DAC.

However, we can devise a system where a user can “report” another user for attempting a collusion attack. After the commitment period, the pre-image of a users randomness must be kept private. If a users randomness is known to anyone but that user, we can assume malfeasance, and that the user has been leaking his random number in order to sway users to cheat with him. If a user discovers another users pre-image before the user reveals it, then we should allow the user to publish another pre-image, and capture their stake. Therefore, you must keep your pre-image private until is it time to reveal, or other users will gladly reveal your pre-image for you, disqualifying you from the protocol, causing financial harm to you, and benefitting the revealing user.

A coordination attack could take two, very broad, forms. The first, is that a user could go around posting on forums “Everyone chose **1234…** as your random number! Then the RNG DAC will always return **5678…** and we can win our hands of blackjack!”. This is easily stopped by this addition to our protocol. Users can watch for other users submitting **Hash(1234…)** and easily punish them and capture their stake.

Another, more insidious form of an attack would be as follows: A user personally contacts individuals, promising them payment if they fix their random numbers to a predefined number. Since a specific user is coordinating this attack, only he will know the rigged random number, assuming he can convince all other users to engage in the attack. However, with this improvement to the protocol, a user is highly discouraged from participating in the attack. The coordinating user could easily be trying to “steal” your pre-image, and slash your stake instead. Therefore, the attacking user must pay you (ahead of time, or through some sort of smart contract that guarantees payment if you fix your random number) a value **greater than** the value of your deposit, for the risk of fixing your pre-image to be worth the reward given by the attacker.

Again, a user collusion attack is only possible if every **single user** is colluding. Unlike Bitcoin, this is not a 51% attack, but a 100% attack. In practice, it is highly unlikely that this attack vector could be exploited, especially if users can punish other users for revealing their pre-image ahead of time. Further, by requiring a collateral be posted in RNG, an attacker would have to acquire nearly all RNG on the market to perform this attack, if he was unable to convince other participants to fix their randomness.

Similarly, by tying participation in the protocol to a native token, abuse of the protocol by acquiring a massive amount of tokens will hugely devalue the tokens that you just purchased. Therefore, an attack based on acquiring massive amounts of RNG will be likely be unprofitable for the attacker. Similarly, users will be reluctant to participate in attacking the protocol that they hold stake in (and would surely cause drastic reduction in the price of their tokens if participants were discovered to be colluding).

*A successful execution of this form of attack is quite unlikely, especially if we allow contributors to reveal other’s pre-images and “steal” their deposit.*

#### Pre-image Withholding Attack

This is perhaps the largest attack vector in our system. Let’s show an sample execution of this attack. Note, that this attack can only be executed by the **last pre-image revealing user** in a round.

Say that all users except the last user have revealed their pre-images, and they hash to:

*Hash of all users except the last user:*

`0xbb42a430a98a3c58a52e987562e0a49114f58e4adf93bfa8d97385dceb34a58a`

Before the last user has revealed his pre-image (and necessarily changed the above hash), he notes that this hash causes him to **win** his hand of blackjack. He then calculates the output of the RNG protocol, and finds that:

*Hash of all users, and output of the RNG protocol:*

`0x6eb4cdfd417ecef262fd9f60ccd27d7d64c5bf2ff7ccd63ac8f36e960f94a97d`

This final user necessarily knows that hash, and the output of the RNG protocol, before anyone else. This is because the user is still holding his pre-image private, but since he reveals his pre-image last, he knows the pre-image of all other users, and can determine this hash by himself. Also, note that the user will lose his hand of blackjack if he reveals his pre-image to the smart contract (and the smart contract / RNG protocol outputs the above hash).

Therefore, the user choses to **withhold the pre-image**. In a naive system, the smart contract may chose to disregard this, and output the first hash:

`0xbb42a430a98a3c58a52e987562e0a49114f58e4adf93bfa8d97385dceb34a58a`

In this case, the attacking user has successfully performed an attack, and won a hand of blackjack that the user should have instead lost (if he did not perform this attack).

If the protocol instead discards this randomness generation and bases the result of blackjack on a future randomness generation round, then this attack was still successful, albeit less so. The player of blackjack (the attacker) still has another chance to win the hand. The player would have guaranteed lost this hand had he decided to not engage in this form of attack. Therefore, the protocol must **punish** this form of attack, and not just discard the faulty result of that randomness generation round.

If we discard any result where an attacker did not reveal their pre-image, an attacker has a 50% chance of winning the next bet, with the punishment of losing the security deposit. However, if we do not discard the randomness, and use the output from the first randomness generation round, then we can assume that the attacker has a 100% chance of winning the next bet (while losing the security deposit, of course).

The RANDAO protocol solved this as follows: given a bet size of `B`, any and every randomness provider must post a deposit of `B` to be able to securely participate in the randomness generation round, to have a secure protocol (that disincentivizes the pre-image withholding attack). Posting a deposit `< B` would allow a randomness provider to cheat profitably. If a pre-image is withheld, the perpetrator will lose their entire stake, and the randomness generation process will repeat.

However, a problem with RANDAO is that contributors have small incentive to actually contribute. Sure, there is a clear disincentive to cheat, shown by stake slashing if a user does not reveal their pre-image in a timely manner. However, asking users to risk a relatively large sum of money to be rewarded in pennies is a tall task. Note that a highly trafficked randomness producer on the Ethereum chain, [oraclize.it](https://oraclize.it), charges $0.05 for a securely generated random number (and attached proof that this number securely generated). Say that 50 members are participating in this randomness generation protocol, that means that they will each profit $0.001 for their participation. While we can theorize that decentralized apps might pay slightly more for an (even more secure) decentrally generated random number, it’s doubtful that they would pay significantly more than $0.05 per number generated.

The basic RANDAO protocol asks users to put up a large security deposit (to make sure that the number won’t suffer a withholding attack) for a small fiscal reward. These sort of protocols suffer from very low participation rates. RANDAO on Ethereum, has zero participation rate, due to high fees per transaction, and the fact that a 50 member RANDAO needs 100+ Ethereum transactions to complete (unfeasible).

RANDAO solved the pre-image withholding attack by slashing a users stake in event of malicious behavior, however gave users very small rewards for their participation. Our new protocol RANDAC transforms the incentive structure, so that users make a large sum of money for a period of contribution. Contributors *cannot lose their deposit for any reason*, however contributors may lose out on profits of a randomness generation period. This is fair because its impossible to determine what is a malicious pre-image withholding attack, and what is simply an accidental computer failure. A contributor that loses significant financial stake due to a simple computer failure would be very reluctant to contribute to our protocol.

These prior sections have shown our similarities to the RANDAO protocol, while only hinting at the differences. This next section will highlight the differences of RANDAC, specifically focussing on our incentive structure.

### RANDAC Incentive Structure

The basics on the original RANDAO protocol were sound, and the protocol is capable of producing provably secure and auditable random numbers. However, there was an extreme lack of participation due to a skewed reward structure. Users had very small reason to participate (because of a very small reward per randomness generation round), however a participating user would be punished **extremely** heavily if their computer failed mid-round, and they could not reveal their pre-image.

*The two main issues with RANDAO are:*

1. Large punishment for accidental disconnections & computer failures (since there is not determinable difference between an accidental disconnect vs. a malicious pre-image withholding attack)

2. Small reward for participation

Individuals will be reluctant to contribute to RANDAC if there is a possibility of losing their initial deposit. To combat this, we propose a series of master nodes. Nodes contributing randomness to a $100 bet need to post $100 in some form of collateral. Instead of requiring nodes to post exactly $100 in collateral (and possibly lose it), we will require master nodes to hold a large amount of RNG, corresponding to $100 of future profits within a defined time. As a simplistic example, if there are 100 master nodes, and our casino is returning $100,000 in profit per quarter to the master nodes, each master node will receive $1,000 of profit per quarter for their participation. Bad behavior will result in this amount of profit decreasing. If a node drops connection during a $100 bet (synonymous with a pre-image withholding attack), then $100 of profit will be withheld from this master node at the end of the quarter. 

With this, we allow users to risk their future profit on the good performance of their node. If their node drops connection frequently mid randomness production round (which is equivalent to a pre-image withholding attack), they will lose future rewards, but will not lose any held tokens making up their master node. If a master node is slated to be granted less profits at the end of a quarter than the size of the bet, they will not be allowed to participate in that randomness generation round, and will lose profits.

Here, we can outline a system of tiered master nodes. A grouping of nodes with a very large amount of collateral will grant them a large amount of profits for their participation. Therefore, these nodes may generate randomness for random numbers with a very large amount of value resting on them (ie: a large bet in a casino), because the possible loss of (large) future profits will make it economically irrational to cheat. These nodes, and only these nodes will reap the profit from these very profitable (but less common) randomness generation rounds. Since these nodes have the highest amount of collateral of all master nodes, they will be able to participate in all randomness generation rounds, no matter the amount of profit residing on the specific number.

Nodes with a smaller amount of collateral will only be able to participate in randomness generation rounds with a smaller amount of value residing on the individual random number. Therefore, the benefit of holding a larger master node is two fold. First, you receive a larger proportion of profits of the Decentralized Random Number Generation service. Secondly, you are able to participate in randomness generation rounds where there is a larger value resting on the individual number. Since these type of randomness generation rounds require higher security by their nature, only the higher tier of master nodes (denoted by their collateral, which is proportional to future profit).

Our predecessor, RANDAO, had a different approach. A dropped connection in the middle of a round would mean **complete** loss of an individuals security deposit. In RANDAC, a user cannot lose their staked tokens (comprising their master node) for any reason, however may lose out on future profits for their poor performance.

To combat a small reward, we propose two solutions. First, we will batch the rewards until the end of a randomness contribution period. If a user performs any attack, including a pre-image withholding attack, they will lose out on some or all profit of that period, not just profit of that single round.

Secondly, we need a way to easily convert decentralized, provably secure random numbers into profit. Selling random numbers to external services will be a large profit vector, but only when the EOS.IO blockchain has massive reach, and we have many queries for random numbers. Before a large number of applications are developed on EOS.IO, we need a way to **bootstrap** RANDAC into existence. There is no simpler way to do convert random numbers to profit, than a casino, which is quite literally a Random Number Generator (RNG) combined with a front-end.

We propose a novel gambling application, EOSBet Casino. EOSBet Casino is a fully decentralized, provably fair, permissionless gambling application on the EOS.IO blockchain. We will develop RANDAC and EOSBet Casino in parallel, because gambling is the easiest way to translate random numbers into profit. EOSBet Casino will demonstrate all the possibilities of RANDAC, and the requirements of a decentralized random number generator, namely high security for random numbers and quick random number production. EOSBet will be giving a full 70% of profits to RANDAC contributors for unrestricted consumption of RNG.

In this next section, we will argue that EOSBet and RANDAC are intrinsically tied. In other words, *a decentralized random number generation protocol needs a way to turn random numbers into large profits*.

### EOSBet and RANDAC are Intrinsically Tied

##### *(or a Decentralized Random Number Generation Protocol requires a way to turn random numbers into large profits)*

The issue with the original RANDAO protocol was not an issue with the specific method of random number generation, but with the incentive structure. RANDAO could output secure, decentrally generated random numbers, however users do not have a reason to participate in the DAO because it is economically non-sensical. Due to this flaw in its reward system, RANDAO does not output sufficiently secure random numbers, because of a lack of participation.

By giving contributors to our randomness generation protocol a large reward per random number generated (majority of casino profit), there will be a larger incentive to participate. We will also require users to sign up for an “epoch” (length of time) to contribute randomness, and then reap the rewards of the casino at the end of this period.

Contributors who engage in malicious behavior (namely, refusing to reveal a pre-image, or the other two forms of attacks outlined) will be punished, initially by reducing profit given to the user, and later, by slashing their deposit. Secondly, by making contributors “shareholders” in the casino, they will be less likely to conspire amongst themselves and fix the random numbers, because they are effectively stealing from their own future revenue source, and (drastically) reducing the value of your tokens.

Almost by definition, random numbers in a casino have different “values” attached to them. If RANDAC is requested to perform a randomness generation round with a bet valued at $10, then only the contributors to RANDAC with ≥$10 in future profits may engage in this randomness generation round (and receive profits from this randomness generation round). Holding more of our native token RNG allows you to provide randomness for larger bets. You will receive profits from all bets that you provide randomness to.

We can now outline a system of “master-nodes”. Individuals with larger holdings of RNG can supply more secure randomness, and will partake in greater profits. The more RNG an individual holds, the more secure the randomness they create is, the more casino profits the user receives.

Assume our casino is profiting:

- $400k per quarter on bets sized $10 or less

- $300k per quarter on bets size greater than $10, but less than $100

- $250k per quarter on bets greater than $100, but less than $1,000

*$1,000 is the maximum bet for our casino in this hypothetical scenario.*

The next quarter, we implement RANDAC, our decentralized randomness generation protocol:

- We have 1000 junior-master nodes staking 1,000 RNG

- We have 200 master nodes staking 10,000 RNG

- We have a final 50 super-master nodes staking 100,000 RNG

A junior-master node participates in all randomness generation rounds where bets are $10 or less. A master node (since it needs 10x as many RNG) counts as 10 junior master nodes. A super-master node (since it needs 50x as many RNG) counts as 50 junior master nodes. This is to prevent users from being incentivized (in some cases) to break up larger nodes, and create multiple smaller nodes. Both master nodes & super-master nodes participate in these randomness generation rounds as well.

Given the nodes specific weightings, junior master nodes will make $50 per quarter from bets size $10 or less. Master nodes and super-master nodes will make $500 and $5,000 per quarter from these bets, respectively. This is assuming non-malicious behavior from all nodes.

For larger bets, between $10 and $100, only master-nodes and super-master nodes may participate in randomness generation. Given the nodes specific weightings, master nodes will make $430 per quarter, and the super-master nodes will make $4,300 per quarter.

For the largest bets, between $100 and $1,000 (the maximum bet), only super-master nodes may participate in randomness generation. Given casino profit of $250k from this bet size, the 50 super master nodes will make another $5,000 per quarter.

To summarize, assuming good participation from all our nodes, junior master nodes will make $50 per quarter for providing randomness to all bets below $10. Master nodes will make $930 per quarter for providing randomness to all bets below $100. Lastly, super-master nodes will make $14,300 per quarter for providing randomness to all bets, up to our maximum bet size of $1,000.

If a junior-master node performs a malicious action on a bet of size $7.50, including a pre-image withholding attack, then this node will see their profits slashed the size of this bet. Since they were slated to make $50 this quarter, this instance of cheating will see the node making $42.50 this quarter. Remember, that nodes cannot lose their staked tokens, they simply lose future profits. If a junior master node performs multiple attempts at cheating, so that they future profits dip below $10, then they will be booted from all future randomness generation rounds this quarter, and lose all profits from this quarter.

As expected from a system of tiered master nodes, nodes with a very large amount staked tokens will receive a very large amount of profit. We assume it will be very lucrative to hold enough tokens for a super-master node, however this is not without risk. You will be required to provide randomness for the largest bets in the casino (and the safest grade of randomness we will offer).

### Enhancements to RANDAC & Off-Chain RANDAC

In our basic protocol, outlined above, we have a simple on-chain version of RANDAC.

Assume there are 50 randomness contributors in a round. Each randomness contributor initially posts their commitment hash to the blockchain. When a request for randomness happens, users submit their pre-images within a period of time — say 6 blocks (3 sec. on EOS.IO blockchain). The pre-images are hashed together, and the result is a securely generated random number. This round requires two transactions per contributor, per round. 

We can reduce this to one transaction, per contributor, per round as follows: Require users to sign up for multiple contribution periods. Users generate random entropy, and create a “hash onion”:

`H^r(E) = C`

*`H^r` = a function where the argument is Hashed r times*
*`E` = entropy generated*
*`C` = initial commitment*

For the initial commitment, users commit `C`. On the first reveal phase, they submit `H^r-1(E)` as their pre-image. This pre-image is then used as a commitment for the next period. For an arbitrary reveal phase `n`, given that `n < r` the user submits `H^r-n(E)`, or `E where n = r`.

With this enhancement to the protocol, we have reduced the amount of transactions per round to the amount of users per round.

For an off-chain protocol, we first network all contributors through some sort of gossip protocol, where each contributor shares him and his peers messages throughout the network. Each round, we randomly elect a “round leader”. The round leader is in charge of collecting signatures from all contributors of the form:

`SigP(r, i)`

*`SigP` = a signature by contributors public key, P*
*`r` = round number*
*`i` = pre-image*

These signatures are then posted as a transaction to the blockchain, and the pre-images are hashed together as before. Since we require a contributor sign their pre-image, and a round number (used as a nonce) if a user signs the wrong pre-image, it is simple to detect, as before. 

However, a user refusing to submit their signed pre-image is indistinguishable from the “round leader” refusing to attach a users signed pre-image. In this case, we enact a voting process where we query other users if they “saw” the signature when it was passed around the network. If these users reply that they did see the signature, and can produce the valid signature, then we assume that the “round leader” refused to attach the signature to his transaction. If these users reply that they did not see the contributor-in-question’s signed pre-image being passed around the network, then we assume that the user failed to sign and broadcast his pre-image. In either case, we slash the contributor that performed the malicious action, and reward a portion of this slashed stake to the majority voters. 

Using an off-chain protocol can allow us to reduce the amount of time taken to reveal the random number. We will also reduce our on-chain footprint on the EOS.IO blockchain, which will reduce fees incurred by block producers processing our transactions, and therefore increase profits to our RANDAC contributors.

Most importantly, once we have implemented off-chain RANDAC, we are able to provide secure, decentrally generated randomness to other blockchains, like Ethereum. Similarly, we should be able to reduce the time spent to complete a randomness generation round to 1 block (on its respective chain), instead of up to 6 blocks.

### The RNG Token

Unlike a classic “utility token”, holders of our RNG token have a way to receive payment, in EOS, in relation to their holdings. However, these holders must participate in our protocol to enjoy these profits. This is not a “dividend token” or “security" in any way, shape, or form. Our RNG tokens simply allow you to contribute to our protocol, and receive payment for good work.

The only use of RNG tokens is to stake tokens and become a master node. Master nodes contribute randomness shares to RANDAC, and are rewarded for their good work and behavior. RNG tokens are wholly unrelated to EOSBet Casino. EOSBet Casino is simply a way to bootstrap our randomness generation protocol into existence, because any sort of randomness generation protocol needs a way to transform random numbers into large profits.

EOSBet Casino is a fully decentralized, permissionless, and trustless casino on the EOS.IO blockchain. Users place bets in EOS, and all casino profit is realized in EOS. Master node holders must stake RNG tokens, however all payment will be received in EOS, the native token of the EOS.IO blockchain.

*To ensure a fair and equitable distribution, 2% of RNG tokens will be airdropped to all EOS token holders.*

EOSBet Casino will be giving RNG token holders 70% of casino profits for their contributions of randomness. However, we expect a multitude of decentralized applications to require a source of decentralized randomness, which RANDAC will provide to the entire EOS.IO blockchain. **All** revenue from these applications' requests for randomness will be distributed amongst all participating master nodes.

### RANDAC on the Blockchain

While a provably fair, fully decentralized casino has a massive requirement for securely generated random numbers, there are many applications which also have requirements for random entropy. Here is an example.

The team at RANDAC expects blockchain based gaming to be a huge growth industry. While not all gaming actions will take place on-chain, most relating to character inventory will be. Player inventory will be stored in a decentralized manner on the blockchain. This should also mean player inventory is generated in a provably fair, decentralized manner. Currently RANDAC is the only project seeking to provide a decentralized source of randomness to any blockchain including EOS.IO.

Generating a “loot box” or other type of random player reward system doesn’t have a large security requirement, nor will the game makers pay a large amount for a random number generated in this manner (and would probably revert to a trustless, but centralized system if required to pay a large amount). If a game developer was forced to “roll their own” RANDAO, they would also suffer from the lack of participation problem. To combat this issue on the EOS.IO blockchain, we plan to open our randomness generation protocol to other decentralized apps, and offer random number generation as a service. Contributors to the RANDAC protocol will receive both profits from the casino, as well as payment for the services they are providing to other decentralized applications on the EOS.IO chain.

If a large amount of decentralized applications are created on the EOS.IO blockchain, we RANDAC will be receiving far more requests than will be made through our casino. However, as seen with the previous RANDAO, a decentralized source of randomness needs an initial source of profit to incentivize contributors. EOSBet Casino will provide this source of profit.

