# Distributed Systems — база знаний

База знаний по распределённым системам для изучения и повторения.
Каждая папка — один раздел: конспекты, код и разборы класть в папку раздела.

## 01-time-order-and-causality — время и порядок событий
- happens-before, причинность
- часы Лэмпорта, векторные часы, version vectors
- физические часы, рассинхрон часов (clock skew), NTP, TrueTime, HLC
- глобальные снимки Chandy-Lamport

## 02-failure-models-and-detection — отказы и их обнаружение
- модели отказов: crash-stop, crash-recovery, omission, Byzantine
- детекторы отказов: heartbeats, phi accrual, SWIM
- FLP-невозможность, задача двух генералов
- синхронные, асинхронные и частично-синхронные системы
- таймауты, backoff, jitter

## 03-consensus-and-state-machine-replication — консенсус и репликация лога
- state machine replication
- Paxos, Multi-Paxos, Raft, Viewstamped Replication, Zab
- кворумы и их пересечение, ballot и term
- выборы лидера, репликация лога, commit index
- смена состава кластера, снапшоты и log compaction
- линеаризуемые чтения: quorum read, ReadIndex, lease read

## 04-consistency-models — модели согласованности
- linearizability, sequential consistency, causal consistency, eventual consistency
- serializability, snapshot isolation, write skew, strict serializability
- сессионные гарантии: read-your-writes, monotonic reads/writes, writes-follow-reads
- CAP, PACELC, tunable consistency (NWR)

## 05-replication — репликация данных
- primary-backup, multi-leader, leaderless
- chain replication, кворумные системы
- CRDT: G-Counter, PN-Counter, OR-Set, LWW-Register
- Dynamo-стиль: sloppy quorums, hinted handoff, virtual nodes
- anti-entropy: Merkle-деревья, gossip, read repair

## 06-sharding-and-partitioning — шардирование
- consistent hashing, range vs hash partitioning
- ребалансировка, перенос шардов без остановки
- hot spots и перекос нагрузки
- directory-based vs algorithmic routing, tablets

## 07-distributed-transactions — распределённые транзакции
- 2PC, 3PC, Saga, TCC
- Percolator, Calvin, Spanner, CockroachDB
- MVCC, OCC vs PCC, SSI

## 08-idempotency-and-exactly-once — идемпотентность
- idempotency keys, дедупликация
- at-least-once, at-most-once, exactly-once (effectively-once)
- идемпотентные продюсеры и транзакции Kafka, transactional outbox
- fencing токены продюсеров

## 09-distributed-locks-and-leases — локи и аренды
- leases, fencing tokens
- Chubby, ZooKeeper, Redlock и его критика
- выборы лидера поверх lock service

## 10-stream-processing-and-event-time — потоковая обработка
- event time vs processing time, watermarks, late events
- окна: tumbling, sliding, session
- checkpointing (Chandy-Lamport), state backends
- exactly-once во Flink и Kafka Streams, backpressure

## 11-queueing-theory-and-scalability — очереди и масштабируемость
- закон Литтла, закон Амдала, USL
- хвостовые задержки (p99), hedged requests, coordinated omission
- балансировка: round-robin, least-connections, power of two choices
- метрики RED и USE

## 12-byzantine-fault-tolerance — византийские отказы
- задача византийских генералов, правило 3f+1
- PBFT, Tendermint, HotStuff
- блокчейн: PoW, PoS, финализация

## 13-security — безопасность
- mTLS, JWT, OAuth, PKI и ротация сертов
- шифрование at-rest и in-transit
- threat models: MITM, replay, Sybil
- zero trust

## 14-observability-and-debugging — наблюдаемость
- трейсинг (OpenTelemetry, Jaeger), метрики, логи, профилирование
- структурированные логи, correlation ID, дашборды p99

## 15-testing-and-verification — тестирование и проверка
- fault injection: tc, iptables, Toxiproxy
- Jepsen, Knossos, Elle, Porcupine
- deterministic simulation, model checking (TLA+), chaos engineering
