# SberDataBases_HW1

## CAP - Теорема

Вкратце, САР теорема утверждает, что для любых распределенных вычислений выполнимо только два из трех свойств: согласованность (С), доступность (А) и устойчивость к разделению (Р). Такое утверждение вытекает из формальной математической модели описания таких вычислений и описывает причины возникновения типичных весов, с которыми приходится встречаться в проектировании.

## Dragonfly DB

Насколько мне стало понятно после короткого ознакомления, Dragonfly скорее даже не большая и тяжёлая распределенная система хранения данных, которые мы обычно себе представляем когда говорим о базах данных, а система эффективного хранения данных. Это большими буквами сообщается на [главной странице проекта](https://dragonflydb.io/features), на которой утверждается высокая производительность продукта, низкие задержки и высокая пропускная способность.

В этой связи сразу хочется дать ответ СА, исключив Р моментально, ведь о каком разделении может идти речь, когда у нас только одна хранящая нода. Однако как оказывается, на деле все не так просто. К примеру, сами создатели базы данных активно ссылаются на базу данных Redis, можно даже смело запускать код, предназначенный для Redis, на Dragonfly (от самой Redis Dragonfly db при этом отличает вертикальная архитектура, которая и позволила добиться высокой по сравнению с ней производительности). Но по Redis в открытом доступе гораздо больше [свободных материалов](https://subscription.packtpub.com/book/big-data-and-business-intelligence/9781784392451/9/ch09lvl1sec52/the-cap-theorem), в которых в том числе сообщается:

"Since Redis Sentinel and Redis Cluster are distributed systems, it is fair to analyze them using the CAP theorem. Network partitions are unavoidable in a distributed system, so it should ensure either consistency or availability; that is, it should be either CP or AP"

"Theoretically, Redis Sentinel and Redis Cluster are neither consistent nor available under network partitions. However, there are some configurations that can minimize the consistency and availability problems. They cannot provide availability because there is a quorum that needs to agree on a master election, and depending on the quorum's decision, part of the system may become unavailable."

То есть так тривиально выводы, подобные приведённым выше, не делаются, и надо погружаться глубже в реализацию и осознавать как именно и какие дополнительные параметры базы данных помогают достичь тех или иных её свойств.

Не проводя таких исследований, я все же укажу принадлежность Dragonfly db к CA классу, ведь как минимум этот пример разительно отличается в этом смысле от следующих.

## Arenadata DB

Arenadata это российская разработка, по ней уже гораздо больше открытых материалов.

Вот, например, на [главной странице проекта](https://arenadata.tech/products/arenadata-db/) сообщается:

"ADB прекрасно справляется с задачами построения корпоративного хранилища данных (КХД), аналитики с помощью BI-инструментов, AD-HOC запросов и data science уровня предприятия. По мере роста объёма данных вы просто добавляете новые сервера в кластер — обо всём остальном ADB позаботится сама."

Отсюда понятно что в основном база предназначена для хранения большого массива данных и для аналитики на их основе, то есть она отвечает тем самым нашим базовым представлениям о больших неповоротливых базах данных, или ещё принадлежит классу CP.

## Scylla DB

У разработчиков Scylla есть на этот счёт отдельный [комментарий](https://www.scylladb.com/glossary/cap-theorem/), откуда можно процитировать:

"CAP theorem eventual consistency. Some NoSQL databases (for example, ScyllaDB) use  a model of tunable eventual consistency to deliver multi datacenter high availability and fast and efficient write and read operations."

"An AP database provides availability and partition tolerance but not consistency in the event of a failure. All nodes remain available when a partition occurs, but some might return an older version of the data. CouchDB, Cassandra, and ScyllaDB are examples of AP databases."

То есть Scylla это прям суперраспределенная, без какого либо центра система (что позволяет достучаться до неё в любой момент времени), в которой есть отдельная поддержка сохранения consistency и возможность к её тюнингу. Ясно, что назвать её принадлежащий к классу AС самый - логичный вывод.

