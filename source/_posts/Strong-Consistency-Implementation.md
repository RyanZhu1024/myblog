---
title: Distributed System Design - A Simple Strong Consistency Implementation
date: 2017-03-27 23:36:35
tags:
- Distributed System

---



## Introduction

A distributed system with strong consistency model is well known to be good at dealing with financial business. While sacrificing the system availability at some time, it can guarantee that at any given time and given node in the cluster, data is always consistent. *For the sake of completing CS682's homework from USFCA easily, I decide to choose strong consistency model to do the job.*

## Prerequisites

The project is a simplified Slack realtime messaging system based on HTTP protocol. To make the project simple, only 4 APIs are given, which are as follows:

1. Get a history of messages from a given channel name.
2. Get a list of starred messages from a given channel name.
3. Post a message to a given channel.
4. Star a message with a given message ID.

All APIs are completed before implementing the strong consistency model.

## Architecture 

There are multiple web servers running as coordinators to handle requests from any client and forward those requests to any of the data servers in the backend. 

There are multiple data servers running in a master-slave architecture to process all requests coming web servers. The master will be the one to accept all W/R operations and synchronize with other slaves. 	

## Requirements

All data in the data server cluster should be consistent at any given time.

The system should be scalable, which means at any give time, a new web server or a data server can be added to the cluster.

The system should be fault tolerance, which means as long as there is a single data server running, the system should still be able to serve all requests

## Design

### Syncronization

Only when all slaves as well as the master have committed changes to their local replica can the master reply to the web server that the operation has been completed. Any failure during this process will cause inconsistency among all replicas, which will be discussed later.

### Fault Tolerance

A heart beat request will be sent per second from all slaves to the primary node to ensure the liveness of the primary node. If anyone of the slaves has a timeout or get no response during this process, an election will be initiated.

### Election

Simple Bully [election](https://en.wikipedia.org/wiki/Bully_algorithm) algorithm.

### Scalability

When a new data node is added to the cluster, the current primary node's infomation will be given. The new node will synchronize with the primary node while blocking requests from web nodes to the primary to ensure the data consistency. It is acceptable to have the system unavailable during this process and it is assumed the primary node won't fail while synchronizing. 

## Implementation

### Transaction

A 2 phase distributed transaction method is implemented. When a write request arrives at the primary node, the primary node will ask for votes from all slaves. When all slaves vote for commit, a commit message will be sent to all slaves and the data will be written to the storage. If anyone of the slaves votes for abort, the operation will be aborted. 

The primary node will try 3 times for voting. If any of the slaves failed — meaning crashed or timeout — during this process, the primary will set the particular slave status to offline, which will be ignored during the next attemp of iteration. Once reaching a consensus of commit or abort after iteration, the transaction will complete at last.

A strict 2 phase locking machenism is implemented on each single data server to ensure a write operation to have the access to the paricular storage.

### Election

1. P broadcasts an election message (inquiry) to all other processes with higher process IDs, expecting an "I am alive" response from them if they are alive.
2. If P hears from no process with a higher process ID, then it wins the election and broadcasts victory.
3. If P hears from a process with a higher ID, P waits a certain amount of time for any process with a higher ID to broadcast itself as the leader. If it does not receive this message in time, it re-broadcasts the election message.
4. If P gets an election message (inquiry) from another process with a lower ID it sends an "I am alive" message back and starts new elections.
5. If P receives a victory message from a process with a lower ID number, it immediately initiates a new election

#### Election State Machine

### Syncing Data to Newly Added Data Node

One data node can be added to the cluster at a time. The primary node will be given to the new node.

Once a node is added to the cluster, it will send a *begin sync* message to the primary node. The primary node will lock the data store to prevent all write operations and reply an OK. Read operations are still allowed during this process. 

Once the node received OK message from the primary, it will send *give me all data* message. The primary will start flushing all data store to the node. Once the node completes receiving all data, it will send a *end sync* message to the primary. The primary will contact other slaves to tell them the new node information, unlock the data store and get back to normal state. 

Other slaves can crash for timeout during the last step, but it can be ignored. It is the primary node's responsibility to manage the aliveness of all slaves. When there is an election started, all candidates will update their running data nodes group when they receive a *Are you alive message*. Thus, the next primary node will always know the group of nodes that are alive.

#### Sync State Machine

## Testing











