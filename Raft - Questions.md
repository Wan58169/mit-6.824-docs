# Questions

> 1. 为什么 Raft 如此重视 `log` ？

 `log` 是一种机制，记录了 `Leader` 按顺序所执行过的操作。 `log` 对于这些 `relicas` 来说是至关重要的，所有的 `replicas` 不但提交了相同 `client` 的操作，而且连执行操作的顺序也都一样。如此，最终结果也一定相同，这也就是 Raft 备份的目的，即是想让集群中的所有 `server` 保存相同的数据

> 2. Raft 为什么需要 `Leader` ?

如果有 `Leader` ，那么对于每个请求，只需通过一轮 `RPC` 就可获知所有 `follower` 对于该请求的处理意见。这样更高效，比无 `Leader` 快两倍

> 3. Raft 的持久化需要记录哪些讯息以及为什么？

Persist 需要记录 `currentTerm` , `votedFor` 以及 `log` ；记录 `votedFor` 是为了保证 `server` 在同一 `term` 内只有一次投票机会。试想一种情景， `server` 在投票给 `server1` 后宕机了但随后又快速恢复，上线后收到了处于同一 `term` 内另一台 `server2` 的拉票请求，如果没有将 `votedFor` 持久话，  `server` 会觉得自己还有票然后会再一次投票给 `server2` ，这样就有可能导致同一 `term` 内选举出两个 `Leader` ，是因为 `server` 在同一 `term` 投了两次票。

