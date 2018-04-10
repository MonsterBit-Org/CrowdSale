Base contracts are taken from https://github.com/OpenZeppelin/zeppelin-solidity

TOKEN: 
Inheritance structure: 
ERC20Basic, ERC20(ERC20Basic), BasicToken(ERC20Basic), StandardToken(ERC20, BasicToken), BurnableToken(StandardToken), 
Ownable, Pausable(Ownable), PausableToken(BurnableToken, Pausable), MintableToken(PausableToken),
DetailedERC20
MonsterToken(MintableToken, DetailedERC20)

The token is a full implementation of the ERC20 standard. It contains methods for pausing transfers and for burning coins.

All contracts from which the MonsterToken is inherited are taken from aforementioned website with small changes:
- inheritance is made more linear because of Multiple inheritance problems;
- the field icoContract (address), its methods set & remove, modifier whenNotPausedOrIcoContract are added to PausableToken. They are used to check the ability to transfer, for the use during ICO to allow ICO contract to make transfers while users can't;
- removed inheritance from ERC20 in DetailedERC20, because DetailedERC20 is an independent set of fields.



ICO (CROWDSALE):
Inheritance structure: 
Crowdsale, TimedCrowdsale(Crowdsale), FinalizableCrowdsale(TimedCrowdsale, Ownable)
MonsterTokenCrowdsale(FinalizableCrowdsale)

All contracts from which the MonsterTokenCrowdsale is inherited are taken from aforementioned website with small changes:
- inheritance is made more linear because of Multiple inheritance problems;
- added burning of unsold coins in finalization().


How to conduct ICO:
- Create a contract MonsterTokenCrowdsale passing parameters: (number of wei tokens for 1 wei of eth, ETH receiver addess, 
MonsterToken address, UNIX timestamp of start, UNIX timestamp of ending)
- mToken.setIcoContract(ICO_address)  // save current ICO address to token, so that token would allow to transfer during pause()
- mToken.mint(ICO_address, ICO_tokens_supply)  // create the needed number of tokens and transfer to ICO contract;
- before the ICO start:  mToken.pause() // forbid the transfer of tokens for everybody except ICO
- during ICO users can buy tokens by 2 means:
    1) transfering ETH in transaction to ICO contract, then the token equivalent will be sent to buyer's address;
    2) transfering ETH by calling mtCrowdsale.buyTokens(receiver_address), the adress can be either your own or a different one, i.e.       you can pay for tokens with one account and receive them on another one.
- after the ICO end:  mtCrowdsale.finalize()  // will burn unsold tokens on ICO contract
- mToken.unpause() // resume the ability to transfer tokens between users

