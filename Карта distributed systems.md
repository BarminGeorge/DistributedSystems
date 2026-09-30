
## 1. Время, порядок и причинность

Это фундамент, на котором стоит всё остальное.

- **Happens-before (Lamport)**: частичный порядок событий. Если A произошло до B, то B не может быть причиной A.
- **Logical clocks (Lamport timestamps)**: скалярные часы, дают полный порядок, но не различают параллельные события.
- **Vector clocks / version vectors**: позволяют определить, что два события параллельны или одно предшествует другому. Основа для разрешения конфликтов в AP-системах.
- **Physical clocks и их проблемы**: clock skew, NTP, TrueTime (Spanner), HLC (Hybrid Logical Clocks) в CockroachDB.
- **Chandy-Lamport distributed snapshots**: как снять консистентный глобальный снимок без остановки системы. Основа для checkpointing и state transfer в некоторых системах.

Без этого нельзя ни упорядочить операции, ни понять, какие версии данных конфликтуют.

## 2. Модели отказов и detection

- **Crash-stop, crash-recovery, omission, Byzantine** — модели отказов. От выбора модели зависит, какой протокол применим.
- **Failure detectors (Chandra-Toueg)**: heartbeats, phi accrual, SWIM. Классификация: perfect, eventually perfect, strong, weak.
- **FLP impossibility**: в асинхронной системе с одним crash-отказом нельзя гарантированно достичь консенсуса детерминированным протоколом. Поэтому все практические протоколы либо используют таймауты (частично синхронная модель), либо рандомизацию.
- **Two Generals Problem**: нельзя гарантировать согласованность через ненадёжный канал без потерь. Отсюда — необходимость подтверждений и идемпотентности.

## 3. Модели согласованности

Это не «мелочь», а отдельная большая теория.

- **Linearizability** — сильнейшая: все операции выглядят как атомарные в реальном времени.
- **Sequential consistency** — порядок есть, но не привязан к реальному времени.
- **Causal consistency** — упорядочены только причинно связанные операции.
- **Eventual consistency** — реплики сходятся, если прекратятся обновления.
- **Serializability** — для транзакций: результат эквивалентен некоторому последовательному выполнению.
- **Snapshot isolation** — транзакция видит консистентный снимок на момент старта. Не эквивалентна serializability (write skew).
- **Strict serializability** — linearizability + serializability. Это то, что даёт Spanner.
- **Session guarantees**: read-your-writes, monotonic reads, monotonic writes, writes-follow-reads.

Иерархия: linearizable → sequential → causal → eventual. Чем слабее, тем выше доступность и ниже latency.

## 4. Репликация: дополнительные модели

Кроме RSM и state transfer:

- **Chain replication**: цепочка реплик, head принимает записи, tail коммитит. Даёт strong consistency при линейной топологии.
- **Quorum systems**: read/write quorums, grid quorums, tree quorums. Теория о том, как пересечение кворумов гарантирует согласованность.
- **CRDT (Conflict-free Replicated Data Types)**: состояния сходятся без координации. Бывают state-based (CvRDT) и operation-based (CmRDT). Примеры: G-Counter, PN-Counter, OR-Set, LWW-Register, RGA.
- **Primary-backup vs multi-leader vs leaderless**: три модели репликации с разными гарантиями.
- **Anti-entropy**: Merkle trees, gossip, read repair. Как чинить расхождения фоново.

## 5. Шардирование и партиционирование

- **Consistent hashing**: минимальное перемещение данных при изменении числа узлов. Используется в Dynamo, Cassandra, Riak.
- **Range partitioning vs hash partitioning**: trade-offs.
- **Rebalancing**: как перемещать шарды без остановки. Raft joint consensus, dynamic membership.
- **Hot spots и skewed workloads**: теория распределения нагрузки.
- **Directory-based vs algorithmic routing**: кто знает, где какой шард.

## 6. Распределённые транзакции

Это отдельная большая область.

- **2PC (Two-Phase Commit)**: prepare + commit. Блокирующий, coordinator — single point of failure.
- **3PC (Three-Phase Commit)**: неблокирующий в некоторых моделях, но всё ещё не устойчив к partition.
- **Saga**: длинные транзакции через цепочку локальных транзакций с компенсациями. Используется в микросервисах.
- **TCC (Try-Confirm-Cancel)**: резервирование ресурсов, подтверждение, откат.
- **Percolator (Google)**: 2PC поверх Bigtable с timestamp oracle. Основа для распределённых транзакций в TiKV.
- **Calvin**: детерминированное упорядочивание транзакций до выполнения. Используется в FaunaDB.
- **Spanner**: TrueTime + 2PC + Paxos. Внешняя согласованность глобально.
- **MVCC (Multi-Version Concurrency Control)**: версионирование данных для изоляции. Основа snapshot isolation.
- **OCC vs PCC**: optimistic vs pessimistic concurrency control.

## 7. Идемпотентность и exactly-once

- **Idempotency keys**: дедупликация запросов.
- **Exactly-once semantics**: в потоковой обработке (Kafka, Flink) — через идемпотентные producer'ы и транзакционные writes.
- **At-least-once vs at-most-once vs exactly-once**: trade-offs.
- **Fencing tokens**: защита от «зомби-лидеров». Используется в distributed locks (Chubby, ZooKeeper).

## 8. Distributed locks и leases

- **Leases**: лидер получает аренду на время. Пока аренда активна — он лидер. По истечении — выборы.
- **Fencing tokens**: монотонно растущий номер для каждой аренды. Старый лидер с меньшим токеном отвергается.
- **Redlock (Redis)**: спорный алгоритм, Kritik от Мартина Клеппмана.
- **Chubby / ZooKeeper**: реализация распределённых блокировок через консенсус.

## 9. Потоковая обработка и event time

- **Event time vs processing time**: когда событие произошло vs когда обработано.
- **Watermarks**: механизм для обработки late events.
- **Windowing**: tumbling, sliding, session windows.
- **Exactly-once в стримах**: Flink, Kafka Streams.
- **Backpressure**: flow control в потоках данных.

## 10. Теория очередей и масштабируемость

- **Little's Law**: `L = λW`. Связь между числом запросов, интенсивностью и временем.
- **Amdahl's Law**: ускорение ограничено последовательной частью.
- **Universal Scalability Law (USL)**: учитывает и contention, и coherency. Показывает, что после некоторого числа узлов throughput падает.
- **Tail latency**: p99 важнее среднего. Hedged requests, speculative execution.
- **Load balancing**: round-robin, least-connections, power of two choices.

## 11. Byzantine fault tolerance

- **Byzantine Generals Problem**: как достичь консенсуса, если узлы могут врать.
- **PBFT (Practical Byzantine Fault Tolerance)**: `3f+1` узлов, три фазы.
- **Tendermint / HotStuff**: современные BFT-протоколы.
- **Blockchain**: по сути BFT-консенсус с экономическими стимулами (PoW, PoS).

## 12. Безопасность в распределённых системах

- **Аутентификация и авторизация**: mTLS, JWT, OAuth.
- **Шифрование**: at-rest, in-transit.
- **Threat models**: MITM, replay attacks, Sybil attacks.
- **Zero trust**: не доверять сети.

## 13. Наблюдаемость и отладка

- **Distributed tracing**: Jaeger, OpenTelemetry.
- **Metrics, logs, traces**: три столпа.
- **Chaos engineering**: проверка отказоустойчивости.
- **Formal verification**: TLA+, Coq, Isabelle для доказательства корректности протоколов.
