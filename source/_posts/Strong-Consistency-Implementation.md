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

When a new data node is added to the cluster, the current primary node's infomation will be given. The new node will synchronize with the primary node while blocking requests from web nodes to ensure the data consistency. I assume it is acceptable to have the system unavailable during this process and the primary node won't fail while synchronizing. 

## Implementation

### Transaction

A 2 phase distributed transaction method is implemented. When a write request arrives at the primary node, the primary node will ask for votes from all slaves. When all slaves vote for commit, a commit message will be sent to all slaves and the data will be written to the storage. If anyone of the slaves votes for abort, the operation will be aborted. 

A strict 2 phase locking machenism is implemented on each single data server to ensure a write operation to have the access to the paricular storage.

### Election

### Semaphore

With semaphore provided by JDK, isolation can be guaranteed between a write operation and a sync operation from a newly added node. There are 2 types semaphore created on this purpose. 

To preceed a write operation, a thread has to have all permits from the sync semaphore, once it gets it, it will try to acquire a write semaphore. The sync semaphore will be released either right after the write semaphore being acquired or it failed to acquire the write semaphore, and vice versa for the sync operation.

There are other types of semaphore being created for the purpose of isolation between different operations, which are omitted here.

### CountdownLatch

To improve the performace of the system, the communication between nodes will be executed concurrently. For example, when primary initiates a voting process for a transaction or a slave initiates a election. A CountdownLatch is used to ensure the next step being reached after all threads complete their jobs.



