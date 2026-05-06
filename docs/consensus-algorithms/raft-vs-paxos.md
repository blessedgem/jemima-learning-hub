# Paxos and Raft Comparison

## Purpose: How should I approach Paxos after learning Raft?

This note helps me understand Paxos after first studying Raft.

Raft and Paxos both address distributed consensus, but they are structured differently.

## Paxos Study Order

To understand Paxos, I should study it in this order:

1. Consensus problem
2. Single-decree Paxos
3. Paxos roles
4. Prepare and Promise phase
5. Accept and Accepted phase
6. Quorum intersection
7. Multi-Paxos
8. Leader optimization
9. Comparison with Raft

## Paxos Roles

Paxos commonly uses these roles:

- Proposer
- Acceptor
- Learner

## Key Paxos Idea

The central idea in Paxos is quorum intersection.

If two majorities overlap, then a later round can discover information about earlier accepted values. This helps preserve safety.

## Raft vs Paxos

Raft was designed to be easier to understand.

Raft separates the problem into:

- Leader election
- Log replication
- Safety
- Membership changes

Paxos begins with single-decree consensus and then extends it into Multi-Paxos.

## Strong Leadership in Raft

Raft uses a strong leader model.

The leader:

- Handles client requests.
- Appends log entries.
- Replicates entries to followers.
- Decides when entries are committed.

This makes the system easier to reason about.

## Blockchain Relevance

Understanding Paxos helps me understand the theoretical foundation of consensus.

Understanding Raft helps me understand practical leader-based replicated logs.

Together, they help me compare blockchain consensus protocols against classical distributed systems consensus.

## Key Questions

- What is single-decree Paxos?
- What is Multi-Paxos?
- Why is quorum intersection important?
- How does Raft’s leader model simplify consensus?
- What tradeoffs does Raft make compared with Paxos?