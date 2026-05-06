# Review: Raft Consensus Algorithm

## Overview

Raft is a consensus algorithm designed to manage a replicated log across a group of servers. Its goal is to make a distributed system behave like one reliable state machine, even when some servers fail. Raft was designed as an understandable alternative to Paxos by separating consensus into clearer parts: leader election, log replication, safety, membership changes, and practical system operation.

In Raft, the replicated log is the central structure. Each server stores a sequence of commands in its log, and each server’s state machine applies those commands in the same order. If the logs remain consistent, the replicated state machines produce the same results.

## Main Idea

Raft works by electing one server as the leader. The leader receives client commands, appends them to its log, replicates them to followers, and decides when entries are safe to commit. This strong-leader design simplifies the flow of data because log entries move from the leader to the followers.

## Server Roles

Raft servers can be in one of three states:

- **Follower**: Responds to requests from leaders and candidates.
- **Candidate**: Starts an election when it believes there is no active leader.
- **Leader**: Handles client requests and manages log replication.

In normal operation, there is one leader and all other servers are followers.

## Terms

Raft divides time into **terms**. Each term begins with an election. If a candidate receives votes from a majority of servers, it becomes the leader for that term. If no candidate wins because of a split vote, a new term begins.

Terms act like a logical clock. They help servers identify outdated messages, stale leaders, and newer election attempts.

## Leader Election

Leader election begins when a follower does not receive communication from a leader before its election timeout expires.

The election process works as follows:

1. A follower times out and becomes a candidate.
2. The candidate increments its current term.
3. The candidate votes for itself.
4. The candidate sends `RequestVote` RPCs to the other servers.
5. If it receives votes from a majority, it becomes leader.
6. The new leader sends heartbeat messages to followers to maintain authority.

Raft uses randomized election timeouts to reduce the chance of split votes. This makes it unlikely that many servers become candidates at the same time.

## Log Replication

Once a leader is elected, it begins accepting client requests. Each request is treated as a command for the replicated state machine.

The log replication process works as follows:

1. A client sends a command to the leader.
2. The leader appends the command to its own log.
3. The leader sends `AppendEntries` RPCs to followers.
4. Followers append the entry if it is consistent with their logs.
5. Once the entry is replicated on a majority of servers, it is considered committed.
6. The leader applies the committed entry to its state machine.
7. Followers eventually learn the committed index and apply the same entry.

## Commit Rule

A log entry is committed once the leader that created it has replicated it on a majority of servers. Once committed, the entry is durable and will eventually be applied by all available state machines.

Raft also ensures that committed entries are not lost during later leader changes.

## Log Consistency

Raft uses the `AppendEntries` consistency check to keep logs aligned.

When the leader sends entries to a follower, it includes:

- The index of the previous log entry.
- The term of the previous log entry.
- The new entries to append.

If the follower does not have a matching previous entry, it rejects the request. The leader then backs up and retries until it finds the point where the logs match. After that, the leader overwrites conflicting uncommitted entries on the follower and brings the follower’s log into agreement with its own.

## Safety Properties

Raft guarantees several important safety properties:

- **Election Safety**: At most one leader can be elected in a given term.
- **Leader Append-Only**: A leader never overwrites or deletes entries in its own log.
- **Log Matching**: If two logs contain an entry with the same index and term, then the logs are identical up to that entry.
- **Leader Completeness**: If a log entry is committed in one term, it will appear in the logs of all future leaders.
- **State Machine Safety**: If one server applies a command at a given log index, no other server will apply a different command at that same index.

These properties ensure that Raft does not produce inconsistent results, even during crashes, delays, leader failures, or network problems.

## Why Safety Matters

Safety means the system must never return an incorrect result. In Raft, this means servers must not apply different commands at the same log position.

This is especially important in distributed systems and blockchain research because once a value, transaction, or command is committed, future leaders should not be able to replace it with a conflicting history.

## Handling Failures

Raft is designed to tolerate server failures as long as a majority of servers are still available and can communicate.

If a leader fails, followers eventually time out and start a new election. If a follower or candidate crashes, the leader or candidate simply retries RPCs until the server recovers.

Raft assumes non-Byzantine failures, meaning servers may crash or become unavailable, but they are not assumed to behave maliciously.

## Timing and Availability

Raft safety does not depend on timing. Slow messages, packet loss, or clock issues should not cause incorrect results.

However, availability does depend on timing. Raft needs enough time to elect and maintain a stable leader. The paper summarizes this timing relationship as:

broadcastTime << electionTimeout << MTBF