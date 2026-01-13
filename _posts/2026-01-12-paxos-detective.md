---
title: "The 2-Second Mystery: Debugging Paxos with Probability"
layout: post
categories: systems distributed-systems paxos
---

It started like any other bug report. The continuous integration pipeline turned red. **Test 18** had failed.

The test constraints were strict: a client operation must complete within **2 seconds**.
The result? A delay of **2.07 seconds**.

It wasn't a logic error. The code "worked." The data was correct. But the system was just... slow. Too slow. 

As I dug into the logs, I realized this wasn't a coding bug—it was a math problem. Here is the story of how **Packet Loss**, **Partitions**, and **Probability** conspired to kill my leader election.

## The Suspect: Paxos Leader Election

Before we inspect the crime scene, let's do a quick refresher on the victim: **Paxos**.

To make progress in Paxos, we need a **Leader**. If a leader dies, a new one must be elected. This happens in **Phase 1**:

1.  **P1a (Prepare):** A candidate leader broadcasts a "Prepare" message to all servers.
2.  **P1b (Promise):** Servers respond with a "Promise" to follow this leader (if the proposal number is high enough).

Crucially, the candidate needs a **Majority** (Quorum) of promises to become the leader. 

### The Scene of the Crime

Our test environment (`Test 18`) was hostile by design:
*   **5 Servers Total:** A majority requires $$\lfloor \frac{5}{2} \rfloor + 1 = 3$$ votes.
*   **Partition:** The network was split. Our candidate was stuck in a partition with exactly **3 servers** (Servers 2, 3, and 5). It had no access to the other two.
*   **Unreliable Network:** The test forced a **20% packet loss** rate.

To win the election in this specific partition, our candidate (Server 5) needed votes from **every single reachable server** (itself + Server 2 + Server 3). If it missed even one P1b response, the election failed.

![Paxos Partition Diagram](/images/partition.png)


## The Clues: Doing the Math

The logs showed the system failing to elect a leader **4 times in a row** before finally succeeding on the 5th try. Each attempt took about 400ms (the election timeout).

$$400ms \times 5 \approx 2.0s$$

There was our 2-second delay. But was this bad luck, or was the system destined to fail? Let's calculate the **Probability of Success**.

### 1. The Probability of a Single Link
We have a 20% drop rate. That means a packet has an $$0.8$$ chance of arrival.
For a leader to get a vote from *another* server, two things must happen:
1.  The Request (P1a) must arrive ($$0.8$$).
2.  The Response (P1b) must return ($$0.8$$).

$$ P(\text{LinkSuccess}) = 0.8 \times 0.8 = 0.64 $$

### 2. The Probability of an Election Round
In our partition of 3, the candidate already has 1 vote (itself). It needs **2 external votes** from the 2 other specific servers in its partition. Both links must succeed simultaneously.

$$ P(\text{RoundSuccess}) = P(\text{LinkSuccess}) \times P(\text{LinkSuccess}) $$
$$ P(\text{RoundSuccess}) = 0.64 \times 0.64 \approx 0.41 $$

We only had a **41% chance** to elect a leader in any given round. Consequently, the chance of failure was **59%**.

## The Verdict: Mean Time to Success (MTTS)

This is a classic **Geometric Distribution**. We are flipping a coin that is weighted against us ($$p=0.41$$) and asking: "How many flips until we get a Head?"

The **Expected Number of Rounds** ($$E[R]$$) is the inverse of the success probability:

$$ E[R] = \frac{1}{P(\text{RoundSuccess})} = \frac{1}{0.4096} \approx 2.44 \text{ rounds} $$

Given our timeout was **400ms**, the **Mean Time to Success** is:

$$ \text{MTTS} = 2.44 \times 400ms \approx 976ms $$

On average, we expect to elect a leader in ~1 second. This is well within the 2-second limit. So why did Test 18 fail?

### Variance is the Killer
Averages hide the truth. While the *average* run takes 2.4 rounds, a run of **5 rounds** is not statistically rare.

The probability of failing 4 times in a row is:
$$ P(\text{4 Failures}) = 0.59^4 \approx 0.12 $$

There was a **12% chance** that any run of this test would take at least 5 rounds. In the world of automated testing (and distributed systems), 12% is not an edge case; it's a certainty.

![Timeline of Election Rounds](/images/election.png)

## Conclusion

The "root cause" wasn't a bug in the Java code. It was a conflict between the **system configuration** (400ms timeout) and the **environment physics** (20% packet loss + minimal partition).

We effectively built a slot machine that paid out a leader 41% of the time. We pulled the lever 5 times. It cost us 2.07 seconds.

The fix? We have two levers to pull:
1.  **Increase the Timeout:** If we wait longer, we don't change the probability of success, but we reduce the *frequency* of retries, which doesn't actually help MTTS here (it would just make the delay longer!).
2.  **Improve Reliability:** This is a test constraint, so we can't change it.

In reality, for this specific test, the solution is acknowledging that **statistical outliers happen**. When designing distributed systems, "Mean Time" is a good baseline, but you must always design for the "Tail"—the unlucky 12% who have to wait for the 5th coin flip.