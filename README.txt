Базовые контракты взяты из https://github.com/OpenZeppelin/zeppelin-solidity

ТОКЕН: 
Структура наследования: 
ERC20Basic, ERC20(ERC20Basic), BasicToken(ERC20Basic), StandardToken(ERC20, BasicToken), BurnableToken(StandardToken), 
Ownable, Pausable(Ownable), PausableToken(BurnableToken, Pausable), MintableToken(PausableToken),
DetailedERC20
MonsterToken(MintableToken, DetailedERC20)

Является полной реализацией ERC20, содержит методы для временной остановки переводов и сжигания средств

Все контракты, от которых наследуется MonsterToken, взяты с вышеуказанного сайта c небольшими изменениями:
- изменено наследование на более прямолиненйное из-за проблем множественного наследования
- в PausableToken добавлено поле адреса icoContract, его методы set & remove (доступные только админу), модификатор whenNotPausedOrIcoContract, 
который используется для проверки вожмости использования transfer (чтобы во время распродажи Ico могло посылать токены, 
но пользователи не могли)
- в DetailedERC20 убрано наследование от ERC20, т.к. он предсавляет собой несвязанный с другими объектами набор данных




ICO (CROWDSALE):
Структура наследования: 
Crowdsale, TimedCrowdsale(Crowdsale), FinalizableCrowdsale(TimedCrowdsale, Ownable)
MonsterTokenCrowdsale(FinalizableCrowdsale)

Все контракты, от которых наследуется MonsterTokenCrowdsale, взяты с вышеуказанного сайта c небольшими изменениями:
- изменено наследование на более прямолиненйное из-за проблем множественного наследования
- в finalization() добавлен burn некупленных монет


Последовательность проведения ICO:
- Создать контракт MonsterTokenCrowdsale с передачей параметров: (количество wei токенов за 1 wei эфира, адрес куда будет переводиться ETH, 
адрес MonsterToken, UNIX timestamp начала, UNIX timestamp окончания)
- mToken.setIcoContract(адрес_ICO)  // сохраняет в токене адрес текущего ICO, чтобы он мог перемещать токены во время pause()
- mToken.mint(адрес_ICO, сумма токенов на продажу)  // создает нужное количество токенов и переводит на контракт ICO
- перед началом ICO: делаем mToken.pause() // запретит передачу токенов всем кроме контракта ICO
- во время ICO пользоветели могут покупать токены двумя способами:
    1) передавая ETH транзакцией на контракт ICO, тогда эквивалент в токенах будет отпрален на адрес отправителя
    2) передавая ETH с вызовом mtCrowdsale.buyTokens(адрес_получателя), адрес может быть как свой так и чужой, т.е. 
    можно оплатить покупку одним аккаунтом а токены перевести на другой 
- после окончания ICO: делаем mtCrowdsale.finalize()  // сожгет непроданные токены в контракте ICO
- mToken.unpause() // возобновит возможность передачи токенов между пользователями

