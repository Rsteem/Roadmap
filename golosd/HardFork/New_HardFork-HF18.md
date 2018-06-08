﻿# Новости GOLOS•CORE 
***Golos·Core подготовил к выпуску новую версию блокчейна HardFork (HF•18) с обновленным API***
## Общее
В версии HF•18 реализованы новые функциональные и технические возможности, предоставляющие пользователям более удобное взаимодействие с системой, повышенную защиту от злоупотребления голосованием,  а также повышающие быстродействие системы за счет более оптимального перераспределения методов между плагинами API. Обновления поддержаны большинством голосов делегатов.

**Рекомендация пользователям**  
Новые функциональные и технические возможности вносят изменения в правила экономической модели блокчейна. Поскольку измененные правила в обновленном блокчейне отменяют действия старых правил, всем пользователям системы следует обновить кодовую базу на своих серверах блокчейн до новой версии HF•18.
## На этой странице:
* [Новые функциональные возможности в HF•18](#F1)
* [Новые технические возможности в HF•18](#F2)
* [Измененные и новые плагины API в HF•18](#F3):
  * [private_message](#F4)
  * [witness_api](#F5)
  * [account_history](#F6)
  * [operation_history](#F7)
  * [social_network](#F8)
  * [tags](#F9)
  * [follow](#F10)
  * [database_api](#F11)
 ***
 
<a name="F1"></a>
## Перечень новых функциональных возможностей в HF•18
* Блокирование различного вида злоупотреблений делегированием Силы Голоса.
* Обеспечение более гибкой ценовой политики на бирже за счет удаления старых данных и использования актуальных в определении курса и соотношения крипто-валют.
* Возможность пользователям вносить изменения в профиль с использованием ключа posting.
* Возможность пользователям предлагать транзакцию на подписание, состоящей из нескольких операций. 
* Возможность пользователям вносить изменения в посты и комментарии независимо от срока их публикации и придавать им актуальность.

<a name="F2"></a>
## Перечень новых технических возможностей в HF•18:
* Повышение быстродействия системы за счет размещения в разных объектах полей с часто и редко используемыми данными.
* Повышение производительности API протокола за счет удаления неиспользуемых полей из методов плагинов, а также за счет перераспределения методов между плагинами для более эффективного их использования.
* Применение фильтрации тегов для настройки ленты в более удобной для пользователя форме.
* Возможность постраничного вывода личных сообщений на экран пользователя.

<a name="F3"></a>
## Измененные и новые плагины в HF•18
<a name="F4"></a>
### Изменения в плагине private_message
В данный плагин добавлена возможность постраничного вывода списка личных сообщений. Изменены следующие методы (шрифтом bold выделены добавленные аргументы):

* get_inbox(string to, time_point newest **, uint limit, uint offset**)
* get_outbox(string from, time_point newest **, uint limit, uint offset**)

Метод `get_inbox` возвращает список входящих сообщений начиная с даты, заданной в newest.  
Метод `get_outbox` возвращает список исходящих сообщений начиная с даты, заданной в newest.  
`limit` — ограничивает количество сообщений (максимально допустимое значение  — 100).  
`offset` — номер сообщения, с которого выполняется операция (начиная с №).


<a name="F5"></a>
### Новый плагин witness_api
Плагин `witness_api` образован перемещением части методов с часто используемыми полями из плагина `database_api`.   В него вошли следующие методы: 
* get_current_median_history_price
* get_feed_history
* get_miner_queue
* get_witness_schedule
* get_witnesses
* get_witness_by_account
* get_witnesses_by_vote
* get_witness_count
* lookup_witness_accounts
* get_active_witnesses // <- из выдаваемого результата удалены пустые строки

Все методы перемещены `в witness_api` без изменений во входных параметрах и выдаваемых результатах. Исключение составляет метод `get_active_witnesses`, у которого из выдаваемого результата удалены пустые строки.

<a name="F6"></a>
### Изменения в плагине account_history
Плагин `account_history` дополнен следующим методом:
* get_account_history

Метод перемещен из `database_api` без изменений во входных параметрах и выдаваемых результатах.

<a name="F7"></a>
### Новый плагин operation_history
Плагин `operation_history` образован перемещением методов из плагина `database_api`. Создание данного плагина позволяет отделить индексирование операций в блокчейне от истории пользователей. Пользователь может запрашивать и получать информацию об операциях в транзакциях (блоках) без ведения истории по аккаунтам. Это обеспечивает снижение нагрузки на сервер и на потребляемую память.  

Плагин `operation_history` дополнен следующими методами:
* get_ops_in_block
* get_transaction

Методы перемещены из `database_api` без изменений во входных параметрах и выдаваемых результатах.

<a name="F8"></a>
### Изменения в плагине social_network
Часть методов из плагина `social_network` была удалена и перемещена во вновь созданный плагин `tags`. Перенос методов осуществлен для того, чтобы создание дополнительных индексов и поиск информации по ним проводились в отдельном плагине. В плагине `social_network` оставлены методы, обеспечивающие обращение к корневым элементам, а методы, обрабатывающие теги, перемещены в новый плагин `tags`.  

Введен опциональный параметр `vote_limit` для задания максимального количества проголосовавших в ответе пользователю. По умолчанию его значение принимается равным 10000. Например, количество комментариев к посту может достигать 1000 и более, что затрудняет их просмотр и поиск интересующей информации. Пользователь может с использованием тэга сформировать запрос на получение комментариев, выбранных (отсортированных) в соответствии с указанными в тэге признаками. Параметр `vote_limit` позволяет уменьшить размер отправляемого от сервера ответа. При этом в ответе добавляется дополнительное поле `active_votes_count`, информирующее об общем количестве проголосовавших (без необходимости получать полный список).

Изменена результирующая структура:  
```javascript
struct discussion {
	"active_votes_count": uint, // <— Добавлено. Общее количество голосов для vote_limit
      ...
}
```
Изменения внесены в методы, возвращающие структуру `discussion` и массив `discussion`. В этот перечень вошли следующие методы:  
* get_replies_by_last_update(string start_parent_author, string start_permlink, uint limit `[, uint vote_limit]`)
* get_content(string account, string permlink `[, uint vote_limit]`)
* get_content_replies(string author, string permlink `[, uint vote_limit]`)
* get_all_content_replies(string author, string permlink `[, uint vote_limit]`)
* get_content(string author, string permlink `[, uint32_t limit]`)
* get_active_votes(string account, string permlink `[, uint vote_limit]`)
* get_account_votes(string account `[, uint from, uint vote_limit]`)

Фоном выделены параметры, которыми были дополнены методы.

<a name="F9"></a>
### Новый плагин tags
В плагин `tags` перемещены методы, выполняющие операции с тэгами (например, получение наиболее популярных тэгов; получение списка языков; получение тэгов, используемых автором). Это позволяет сэкономить размер файла разделяемой памяти тем пользователям, которые не используют данную функциональность.  

Методы, которые перемещены из `social_network` без изменений:
* get_trending_tags(string start, uint limit)
* get_languages()
* get_tags_used_by_author(string)
* get_discussions_by_payout(query)
* get_discussions_by_trending(query)
* get_discussions_by_created(query)
* get_discussions_by_active(query)
* get_discussions_by_cashout(query)
* get_discussions_by_votes(query)
* get_discussions_by_children(query)
* get_discussions_by_hot(query)
* get_discussions_by_feed(query)
* get_discussions_by_blog(query)
* get_discussions_by_comments(query)
* get_discussions_by_promoted(query)


В метод `get_discussions_by_author_before_date` добавлен новый параметр `vote_limit` (шрифтом bold выделен добавленный параметр): 

* get_discussions_by_author_before_date(string author, string start_permlink, datetime before_date, uint limit **[, uint vote_limit]**)
```javascript
struct discussion_query {
    	limit: uint,
    	select_tags: [string],
    	filter_tags: [string]
    	select_languages: [string],
    	filter_languages : [string],
    	truncate_body: uint,
    	vote_limit: uint,   // <— Новый параметр, по умолчанию принимает значение 10000 
    	select_authors : [string],
    	start_author: string,
    	start_permlink: string,
    	parent_author: string,
    	parent_permlink: string
}
```
Изменена результирующая структура метода:  
```javascript
struct discussion {
     “reputation”: uint, // <- Поле отсутствует, если выключен плагин follow
	"active_votes_count": uint,   // <- Добавлено. Общее количество голосов для vote_limit
      ...
}
```

<a name="F10"></a>
### Изменения в плагине follow
В плагине `follow` изменена структура метода `get_account_reputations` в соответствии со следующим представлением:  

* get_account_reputations(array<string> accounts) 
  * Результат array<uint>

<a name="F11"></a>
### Изменения в плагине database_api
Плагин `database_api` дополнен новыми методами для выполнения новых операций.  

Новые методы:
* [get_proposed_transaction](#F11.1)
* [get_database_info](#F11.2)
* [get_vesting_delegations](#F11.3)
* [get_expiring_vesting_delegations](#F11.4)  

Новые операции:
* [изменение или возврат делегирования Силы Голоса](#F11.5)
* [создание аккаунта с делегированием Силы Голоса](#F11.6)
* [изменение json_metadata  аккаунта](#F11.7)
* [предложение на транзакцию](#F11.8)
* [обновление предложенной транзакции](#F11.9)
* [удаление предложенной транзакции](#F11.10)
* [изменение параметров блокчейна](#F11.11)

#### Неизменяемая часть database_api
Часть методов из `database_api` перемещена в плагины `witness_api`, `account_history`, `operationt_history` и `follow`. Сохраненными в `database_api` без изменений остались следующие методы:
* get_block_header
* get_block
* set_block_applied_callback
* get_config
* get_dynamic_global_properties
* get_chain_properties
* get_hardfork_version
* get_next_scheduled_hardfork
* get_account_count
* get_owner_history
* get_recovery_request
* get_escrow
* get_withdraw_routes
* get_account_bandwidth
* get_savings_withdraw_from
* get_savings_withdraw_to
* get_conversion_requests
* get_transaction_hex
* get_required_signatures
* get_potential_signatures
* verify_authority
* verify_account_authority
* lookup_account_names
* lookup_accounts


### Изменения в методах database_api 

#### Внесены изменения в выдаваемый результат следующего метода:
* get_accounts
```javascript
struct account {
	reputation, // <- Поле отсутствует, если выключен плагин follow
	transfer_history, // <- Удалено
	market_history, // <- Удалено
	post_history, // <- Удалено
	vote_history, // <- Удалено
	other_history, // <- Удалено
	tags_usage, // <- Удалено
	guest_bloggers, // <- Удалено
	blog_category, // <- Удалено
	...
}
```
Удаление полей из структуры account обусловлено тем, что ранее эти поля никогда не использовались.


<a name="F11.1"></a>
#### Новый метод get_proposed_transaction
Этот метод обрабатывает запрос на выдачу списка предложенных для подписания транзакций, поступивший от какого-либо аккаунта.  Метод возвращает массив транзакций, в который входят транзакции, созданные непосредственно данным аккаунтом, а также транзакции, которые ему необходимо подписать.
* get_proposed_transaction(account: string)
```javascript
struct proposal_object {
	author: string,
	title: string,
	memo: string,
	expiration_time: datetime,
	review_period_time: datetime, // может отсутствовать
	proposed_operations: [ operation ],
	required_active_approvals: [ string ],
	available_active_approvals: [ string ],
	required_owner_approvals: [ string ],
	available_owner_approvals: [ string ],
	required_posting_approvals: [ string ],
	available_posting_approvals: [ string ],
	available_key_approvals: [ string ]
};
```


<a name="F11.2"></a>
#### Новый метод get_database_info
Этот метод возвращает информацию о текущем статусе разделяемой памяти, в том числе: общий объем разделяемой памяти, размер свободного и зарезервированного пространства для определенных нужд, а также список индексов, хранящихся в разделяемой памяти (название индекса, количество записей).
* get_database_info()
```
struct database_info {
	total_size: uint,
	free_size: uint,
	reserved_size: uint,
	used_size: uint

	index_list: [
    	    {
        	  name: string,
        	  record_count: uint
    	    },
	]
};
```


<a name="F11.3"></a>
#### Новый метод get_vesting_delegations
Этот метод возвращает массив значений с описанием операции делегирования (далее — блок делегирования) аккаунта (делегированный другим аккаунтам или же полученный от других аккаунтов).
* get_vesting_delegations
```
get_vesting_delegations(
	string account,
	string from, uint32_t limit = 100,
	delegations type = delegated
)
```
`account` — аккаунт, по запросу от которого выдается блок делегирования. Отправитель*/получатель определяется аргументом `type`.  
`from` — начальный аккаунт, парный в операции делегирования.  Получатель/отправитель - задается аргументом `type` (для пагинации).  
`limit` — количество возвращаемых элементов (для пагинации). По умолчанию принимается равным 100. Максимальное значение равно 1000.  
`type` — тип запрашиваемого блока делегирования: "delegated" (делегированный), "received" (полученный). По умолчанию принимает значение "delegated".  

Пример возвращаемого блока делегирования: `массив vesting_delegation_api_object [{id: 0, delegator: "zzz", delegatee: "zxcat", vesting_shares: "90.000000 GESTS", min_delegation_time: "2018-04-25T21:48:15"}]`.


<a name="F11.4"></a>
#### Новый метод get_expiring_vesting_delegations
Этот метод используется для получения списка возвращаемых (отозванных и «замороженных») делегированных средств аккаунта. Метод возвращает результат в виде массива значений с описанием операции возврата делегированных средств (далее — блок отозванного делегирования)  

* get_expiring_vesting_delegations
```
get_expiring_vesting_delegations(
	string account,
	time_point_sec from,
	uint32_t limit = 100)
```
`account` — аккаунт, возвращающий делегированные средства.  
`from` — начальное время возврата делегированных средств (для пагинации).  
`limit` — количество возвращаемых элементов (для пагинации). По умолчанию принимается равным 100. Максимальное значение равно 1000.  

Пример возвращаемого модулем блока отозванного делегирования: 
`массив vesting_delegation_expiration_api_object, [{id: 0, delegator: "zxcat", vesting_shares: "123.000000 GESTS", expiration: "2018-05-25T11:18:45"}]`.


<a name="F11.5"></a>
#### Новая операция: изменение или возврат делегирования Силы Голоса
Эта операция позволяет изменять количество делегированного, а также осуществлять возврат делегированного в полном объеме. Для выполнения этих операций необходима подпись ключом `active`. Операции выполняются с использованием следующей процедуры:
```javascript
delegate_vesting_shares
{
	delegator: string,	// делегирующий аккаунт
	delegatee: string,	// аккаунт-получатель
	vesting_shares: asset	// новое количество делегируемой Силы Голоса
}
```
Подпись: `active`.  

Для изменения количества делегирования Силы Голоса в поле `vesting_shares` следует задать новое значение. Для полного возврата делегированной части Силы Голоса в поле `vesting_shares` следует задать значение вида “0.000000 GESTS”. При изменении значения в сторону уменьшения делегированная Сила Голоса сразу снимется с аккаунта-получателя и переходит в «замороженное» состояние сроком на 7 дней (в случае создания аккаунта с делегированием этот период составляет не менее 30 дней с момента создания).  

Минимальная сумма делегирования зависит от параметра `account_creation_fee` и вычисляется по формуле
```
	min = account_creation_fee × 10,
```
где  
10 — плавающий коэффициент, значение которого зависит от результатов голосования делегатов с помощью операции `chain_properties_update_option`.  


<a name="F11.6"></a>
#### Новая операция: создание аккаунта с делегированием Силы Голоса
Для выполнения этой операции необходима подпись ключом active. Операция выполняется с использованием следующей процедуры:
```javascript
account_create_with_delegation
{
      fee: asset,		// комиссия в GOLOS
      delegation: asset,	// делегируемая доля, в GESTS
      creator: string,		
      new_account_name: string,
      owner: authority,
      active: authority,
      posting, authority,
      memo_key: string,
      json_metadata: string,
      extensions: extensions_type	// Не используется, исходное значение — пусто
}
```
Подпись: `active`.  

В `account_create` изменен расчёт стоимости создания аккаунта. В новой версии размер минимальной комиссии вычисляется по формуле
```
	account_creation_fee × 30,
```
где  
`account_creation_fee` — значение, выставляемое делегатами;  
30 — множитель, показывающий во сколько раз затраты на создание аккаунта превышают комиссионный сбор. Значение этого множителя определяется по результатам голосования и выбирается по медиане из списка всех значений, полученных от делегатов с помощью операции `chain_properties_update_operation`.  

Для создания аккаунта с делегированием необходимо, чтобы выполнялись следующие два условия:  
```
	1. (5 × fee + delegation) ≥ (30 × 5 × account_creation_fee)
	2. fee ≥  account_creation_fee
```
где  
`fee` — размер комиссионных отчислений;  
`delegation` — делегированная часть Силы Голоса;  
5 — коэффициент комиссионного сбора в GOLOS. Значение этого коэффициента определяется по результатам голосования и выбирается по медиане из списка всех значений, полученных от делегатов  с помощью операции `chain_properties_update_operation`.  

При создании аккаунта с делегированием бо́льшую часть комиссии можно оплатить с «заморозкой» на 30 дней. Делегированная Сила Голоса может быть отозвана в любой момент (например, в случае злоупотребления полученной делегированной частью новым аккаунтом). При этом делегированная Сила Голоса будет снята с нового аккаунта сразу, а на делегирующий аккаунт будет возвращена по истечении срока «заморозки». Время «заморозки» является параметром, определяемым по результатам голосования. Его значение выбирается по медиане из списка всех значений, полученных от делегатов с помощью операции `chain_properties_update_operation`.


<a name="F11.7"></a>
#### Новая операция: изменение json_metadata  аккаунта
Пользователю предоставляется возможность изменять метаданные своего профиля без использования ключа `active`. Поля профиля хранятся в `json_metadata`. Для изменения любого поля профиля требовалась подпись только ключом `active`, что не всегда было приемлемым.  

Было принято решение разделить поля на две группы. В состав первой группы вошли поля с наиболее значимыми данными профиля (ключи: `posting`, `active`, `owner` и `memo`), а в другую — с менее значимыми, но часто используемыми (аватар, пол, местонахождение и пр.).  Процедура изменения полей первой группы сохранена и требует подписи только ключом `active`. В версии HF•18 вносить изменения в поля второй группы стало возможным с использованием ключа `posting` для подписи.  

Операция выполняется с использованием следующей процедуры:
```javascript
account_metadata
{
	account: string,	// изменяемый аккаунт
	json_metadata: string	// новое значение json_metadata
}
```
Подпись: `posting`.


<a name="F11.8"></a>
#### Новая операция: предложение на транзакцию 
Пользователю предоставляется возможность предлагать транзакцию на блокчейне с несколькими отдельными операциями, требующих одобрения от пользователей на их выполнение.  

Операция выполняется с использованием следующей процедуры:
```javascript
proposal_create_operation
{
	author: string, // автор предлагаемой транзакции
	title: string,  // заголовок предложенной транзакции.
	memo: string,   // примечание
	proposed_operations: [ operation ], // список операций в предлагаемой транзакции
	expiration_time: datetime, // максимальное время, отведенное на транзакцию
	review_period_time: datetime, // время на принятие решения участников транзакции. Опциональный параметр
	extensions: extensions_type // расширение, по аналогии с другими операциями. Исходное значение — пусто
}
```
Подпись: `active`.


Создаваемая транзакция должна быть уникальной по сочетанию полей author-title. По этой паре author-title идентифицируется транзакция в случае внесения в нее изменений или ее удаления.  

В поле `proposed_operations` задается список операций для выполнения их в транзакции (например, `{["delete_comment_operation":{"autor":"jim", "permlink":"hello"}}]`). На каждую операцию из этого списка должна быть получена подпись автора, которому она предназначена. Право проставления подписи на выполнение любой операции из списка имеет также каждый из участников транзакции. Например, операция публикации поста назначается одному автору, а операция перечисления вознаграждения за нее — другому. В случае несогласия другого автора с выполнением второй операции он может удалить подпись на выполнение первой операции предложенной транзакции.  

В поле `review_period_time` проставляется время, отводимое на принятие решений участников транзакции. Этот период должен быть меньше максимального времени из поля `expiration_time`, которое отводится на транзакцию. По истечении этого периода подписи в транзакции могут быть только удаляться.  

Если параметр `review_period_time` не задан, то транзакция будет сразу выполняться после сбора всех необходимых подписей.


<a name="F11.9"></a>
#### Новая операция: обновление предложенной транзакции 
Автору, а также участникам транзакции, предоставляется возможность менять свое решение и обновлять подписи до истечения времени, отводимого на транзакцию.  

Если транзакция не получает одобрения на выполнение какой-либо из операций, эта транзакция не отменяется и ее актуальность будет сохраняться до отведенного на ее выполнение времени. После получения всех необходимых подписей транзакция сразу будет выполняться и все последующие обновления будут считаться недействительными.  

Операция выполняется с использованием следующей процедуры:
```javascript
proposal_update_operation
{
	author: string, // автор предложенной транзакции
	title: string,  // заголовок предложенной транзакции.
	active_approvals_to_add: [string], // список аккаунтов для подписи. Транзакция должна быть  
					// подписана ключом active
	active_approvals_to_remove: [string], // список аккаунтов для удаления подписи из предложенной  
					// транзакции. Транзакция должна быть подписана ключом active
	owner_approvals_to_add: [string], // список аккаунтов для подписи на операции в предложенной  
					// транзакции. Транзакция должна быть подписана ключом owner
	owner_approvals_to_remove: [string], // список аккаунтов для удаления подписи из предложенной  
					//транзакции. Транзакция должна быть подписана ключом owner
	posting_approvals_to_add: [string], // список аккаунтов для подписи на операции в предложенной  
					// транзакции. Транзакция должна быть подписана ключом posting 
	posting_approvals_to_remove: [string], // список аккаунтов для удаления подписи из предложенной  
					// транзакцию. Транзакция должна быть подписана ключом posting
	key_approvals_to_add: [string], // список ключей public для проставления подписей в предложенной  
					// транзакции
	key_approvals_to_remove: [string], // список ключей public для удаления подписей из предложенной  
					// транзакции
	extensions: extensions_type // расширение, по аналогии с другими операциями. Исходное значение — пусто
}
```
Подпись: зависит от списка операций, предлагаемых на подписание.


По этой паре author-title идентифицируется транзакция в случае внесения в нее изменений или ее удаления.
Поля, в которые записывается псевдоним для подписи транзакции, выбираются в зависимости от типа операций в предложенной транзакции.


<a name="F11.10"></a>
#### Новая операция: удаление предложенной транзакции
В случае отказа кого-либо из участников транзакции в выполнении операции он может сформировать запрос на удаление предложенной транзакции. Идентификация транзакции осуществляется по паре author-title.  

Операция выполняется с использованием следующей процедуры:
```javascript
proposal_delete_operation
{
	author: string, // автор предложенной транзакции
	title: string,  // заголовок предложенной транзакции
	requester: string, // псевдоним запросившего удаление транзакции или автор предложенной транзакции
	extensions: extensions_type // расширение, по аналогии с другими операциями. Исходное значение — пусто
}
```
Подпись: `active`.  

Удалить предложенную транзакцию может как ее автор, так и любой участник, от которого требуется подпись. Если хотя бы один из участников запрашивает удаление транзакции, то, независимо от подписей остальных участников, данная транзакция будет удалена.


<a name="F11.11"></a>
#### Новая операция: изменение параметров блокчейна
Предыдущие версии блокчейна обеспечивают поддержку операции `witness_update`, которая позволяет делегатам Голоса изменять по результатам голосования значения следующих параметров:   
* `account_creation_fee` — размер комиссионных отчислений, требуемых на создание пользователя;  
* `maximum_block_size` — максимальный размер блока блокчейна;  
* `sbd_interest_rate` — процент, начисляемый на SBD.  

Ввиду того, что `witness_update` не позволяет расширить перечень параметров, значения которых определяются голосованием, в версии HF•18 реализована новая операция `chain_properties_update`, которая поддерживает более расширенный перечень таких параметров и позволяет наращивать его в будущем. В версии HF•18 операция `chain_properties_update` поддерживает следующий перечень параметров, значения которых определяются голосованием:  
* `account_creation_fee` — размер комиссионных отчислений, требуемых на создание пользователя;  
* `maximum_block_size` — максимальный размер блока блокчейна;  
* `sbd_interest_rate` — процент, начисляемый на SBD;  
* `create_account_with_golos_modifier` — множитель, используемый при создании аккаунта (по умолчанию принимает значение 30, см. операцию [account_create_with_delegation](#F11.6));  
* `create_account_delegation_ratio` — коэффициент комиссионных отчислений, используемый при создании аккаунта (по умолчанию принимает значение 5, см. операцию [account_create_with_delegation](#F11.6));  
* `create_account_delegation_time` — время “заморозки” делегированной Силы Голоса (в мкс, по умолчанию принимает значение за период в 30 дней, см. операцию [account_create_with_delegation](#F11.6));  
* `min_delegation_multiplier` — коэффициент, используемый в определении минимального количества делегирования Силы Голоса (см. операцию [delegate_vesting_shares](#F11.5)).  

Операция выполняется с использованием следующей процедуры:
```javascript
chain_properties_update_operation
{
     owner: string, // автор изменения
     props: {
       account_creation_fee: asset,
       maximum_block_size: uint,
       sbd_interest_rate: uint,
       create_account_with_golos_modifier: uint,
       create_account_delegation_ration: uint,
       create_account_delegation_time: uint,
       min_delegation_multiplier: uint	
     }
}
```
Подпись: `active`  
***
