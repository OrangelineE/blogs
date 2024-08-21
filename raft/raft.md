# Three Subproblems
+ Leader election
+ Log replication
+ Safety

# Replicated State Machine
Same Initial state + same input = same End state.

# Simplified State

## Follower, Candidate, Leader
  ![state](img/state.png)

+ Every node start with the `follower` status. 
  + If it detects there is no leader in the cluster, it will start election and turn itself into `candidate`.
+ If the candidate wins (receives more than half of the votes) in the electon, it will turn itself into `leader` status. Otherwisem it turns into `follower`.
+ If the leader term ends or the leader node corrupts, it will turn to the `follower` 

## Term
 ![term](img/term.png)
+ Raft divides time into terms of `arbitrary length`, marked by consecutive integers.
+ Each term begins with an `election`.
  + Sometimes, an election fails to elect a leader, so the term will end without a leader, and a new term will start soon after.
+ In each term, there can be at most one leader.

## RPC
In Raft, servers communicate with each other using `RPC` (Remote Procedure Calls). There are two main types of RPCs in Raft:

+ RequestVote RPC: Initiated by a `candidate` during an election.
+ AppendEntries RPC: Initiated by the `leader`, used to `replicate` logs and provide a `heartbeat` mechanism.
  
+ When servers communicate, they exchange the current term number.
  + If the term number on one server is `smaller` than the others, that server will `update` its term to the larger value.
  + If a candidate or leader discovers that its term number is `outdated`, it will immediately revert to the `follower` state.
  + If a node receives a request with an `outdated` term number, it will directly `reject` the request.

# Leader Election
When an election process begins, the follower 
+ first `increments` its current `term` number and switches to the `candidate` state. 
+ It then votes for `itself` and concurrently sends vote requests (`RequestVote` RPC) to other server nodes in the cluster.

## Heartbeat Mechanism
+ If there is a leader, it will `periodically` send heartbeats to all followers to maintain its position. 
+ If a follower does not receive a heartbeat for a while, it will assume that there is no available leader in the system and will then start an election process.

## Three Results
There will be 3 results for a candidate.
1. **It (the candidate) wins the election** by receiving more than half of the votes -> Becomes the leader and starts sending heartbeats.
2. **Another node wins the election** -> Upon receiving heartbeats from the new leader, if the new leader's term number is `not less than` its current term number, the candidate reverts to the follower status.
3. **No candidate wins after a certain period** -> Each candidate increases it term number and `starts a new round` of voting after a random election timeout.

Why might there be no winner? 

For example, if multiple followers become candidates at the same time, the votes may be too `scattered`, and `no` candidate receives `more than half` of the votes.

The random election timeout suggested in the paper is 150~300ms.

## RequestVote RPC

```C++
// RequestVote RPC Request
type RequestVoteRequest struct {
    term         int  // Current term number
    candidateId  int  // Candidate's ID
    lastLogIndex int  // Index of the candidate's last log entry
    lastLogTerm  int  // Term of the candidate's last log entry
}

// RequestVote RPC Response
type RequestVoteResponse struct {
    term         int   // Current term number
    voteGranted  bool  // Whether or not this candidate is granted the vote
}

```

+ For a follower that has not become a candidate, it will cast its vote on a `first-come, first-served` basis for the same term.

+ Why does the RequestVote RPC need to include information about the candidate's last log entry? This will be explained further in the context of `safety` concerns.

# Log replication
After the leader is elected, it begins serving client requests.

How does the client know which node is the new leader?
If the client sends a request randomly to a node, there are 3 cases.
1. the node is leader
2. the node is a follower. It can get the leader id from the heartbeat and inform the client
3. the node doesn't work(no response). Client will go find another node.

When the leader receives a command from the client, it will append the command as a new entry to the log.

A log entry must contain three pieces of information:
+ State machine command
+ Leader's term number
+ Log index (log entry index)

A log can only be decided by both term number and log index.

The leader concurrently sends the `AppendEntries RPC` to the followers, instructing them to replicate the entry. Once the entry has been replicated by `over half` of the followers, the leader can execute the command locally and return the result to the client.

We call the local execution of the command, which is the step where the leader applies the log entry to its state machine, a "`commit`".
![log](img/log.png)


During this process, `there is a possibility that the leader or followers may crash or become slow`. Raft must ensure that it continues to support log replication in an orderly manner under these circumstances and that the log entries on each replica remain consistent (to ensure the state machine is consistent). There are three possible scenarios:

① If a follower does not respond to the leader for some reason, the leader will `repeatedly resend` the additional entry requests (AppendEntries RPC) even if the leader has already returned the result to the client.

② If a follower crashes and then recovers, Raft’s `consistency check` for additional entries will ensure that the follower can recover in order and fill in any missing logs after the crash.

Raft's consistency check: When the leader sends an additional entry RPC to the follower, it includes the index position and term number of the previous log entry. If the follower does` not find this previous entry` in its log, it will `reject` the entry. Upon receiving the follower's rejection, the leader will send the follower the `previous log entry`, `gradually moving` towards identifying and filling in the follower's first missing log.

-------------------------------------------------------------------

If desired, the protocol can be optimized to reduce the number of rejected AppendEntries RPCs.

For example, when rejecting an AppendEntries RPC request, the follower can include the term number of the conflicting log entry and the index of the first entry with that term from its own log.

Using this information, the leader can skip all conflicting log entries in that term and decrease nextIndex; this way, the term for each conflicting log entry requires only one AppendEntries RPC instead of one for each entry.

In practice, this optimization is generally `not necessary` because failures do not occur frequently, and there are unlikely to be many conflicting log entries.

-------------------------------------------------------------------

③ `If the leader crashes`, the crashed leader may have already replicated some log entries to the followers but has not committed them (i.e., has not finalized them), and the newly elected leader may not have these log entries. This can lead to discrepancies where some followers have logs that differ from the new leader's logs.

In this situation, Raft resolves the inconsistency by `forcing` the followers to `overwrite` their logs with the `new leader’s logs`, meaning that any log entries on the followers that conflict with the leader’s logs will be overwritten by the new leader's log entries (because there was no commit, external consistency is not violated).

Through this mechanism, the leader does not need to perform any special operations to restore logs to a consistent state after recovery.

The leader only needs to perform normal operations, and the logs will automatically become consistent when the follower recovers and fails the AppendEntries consistency check.

`The leader will never overwrite or delete its own log entries. (Append-Only)`

This log replication mechanism ensures consistency:

+ As long as a majority of servers are functioning correctly, Raft can receive, replicate, and apply new log entries;
+ Under normal circumstances, new log entries can be replicated to a majority of the cluster with a single RPC;
+ A slow follower will not impact the overall consistency.

```C++
// Append Entries RPC Request
type AppendEntriesRequest struct {
    term int            // Current term number
    leaderId int        // Leader's ID (this is the sender's ID)
    prevLogIndex int    // Index of the previous log entry
    prevLogTerm int     // Term of the previous log entry
    entries []byte      // Log entries to store (empty for heartbeat; may send more than one for efficiency)
    leaderCommit int    // Leader's commit index
}

// Append Entries RPC Response
type AppendEntriesResponse struct {
    term int           // Current term number
    success bool       // True if follower contained entry matching `prevLogIndex` and `prevLogTerm`
}

```
1. Log Consistency Check: The follower will only recognize the log as consistent if both prevLogIndex and prevLogTerm match the follower's corresponding entries. This ensures that the log structure remains consistent across nodes.

2. Follower Commit Behavior: Followers cannot commit log entries immediately upon receiving them from the leader because they lack knowledge of whether the log entry has been replicated by the majority of nodes.

3. Leader's Role in Commit: The leader commits a log entry only after confirming it has been replicated by the majority of nodes. The leader then informs its followers about the committed log through the leaderCommit field in the AppendEntries RPC request. Upon receiving this information, followers can mark the previously uncommitted log entries as committed and apply them to their state machines.

4. Commit Index Update: If leaderCommit > commitIndex, the follower updates its commitIndex to the smaller value between leaderCommit and the index of the last new entry. This ensures that all logs can be committed once the leader’s commit index surpasses the follower's commit index.

5. Success Condition in AppendEntries Response: The success flag in the AppendEntries RPC response is set to true only if the request’s term is greater than the follower's term and the request passes the consistency check (i.e., the log entry matches the follower’s current state).