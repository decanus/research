# Notes: The Byzantine Generals' Problem

Distributed Systems need to be able to deal with failures. An often overlooked failure is when nodes send conflicting information to various parts of the system.

Assume an army camped outside an enemy fortress, they must coordinate an attack. They can only communicate through messages and some of the generals may be trators trying to prevent the loyal generals from reaching agreement.

We must have an algorithm where:

1. All loyal generals agree to the same plan. (Traitors may do anything they wish)
2. The trators cannot cause the loyal generals to adopt a bad plan.

It is hard to say what a bad plan is, therefore we say that a bad plan is one which the majority clearly did not agree to. In a majority vote, the traitors can only affect the vote if the loyal generals were almost equally divided, meaning neither decision could be called bad.

The first condition is satisfied by ensuring all generals use the same method for combinding recieved messages.

The easiest way to solve this would be for every general to send their opinion by messenger to each other general. This does not work because to satisfy condition A every general should obtain the same values. However, a traitor may send seperate values to generals.

For Condition 1 to be satisfied, every loyal general must obtain the same information. We cannot use the value obtained directly from a general since a traitors general may send different values to different generals. A loyal general may use the value `i` for general `i` which is different from the value that general `i` actually sent. Additionally, if the a general is loyal, then the value they send must be used by every other loyal general.

So Condition 1 can be rewritten as the following, any two loyal generals use the same value for a specific general, whether or not that specific general is loyal.

We can restrict our consideration to the problem of how a single general sends his value to the others.

Therefore we can simplify the problem to be the following:

A general must send an order to his `n - 1` lieutenants such that:
 - **IC1**: All loyal lieutenants obey the same order
 - **IC2**: If the general is loyal, then every loyal lieutenant obeys the order.

IC1 follows from IC2 if the commander is loyal, these conditions are interactive consistency conditions.

The Byzantine Generals Problem may seem simple, however if the generals can only send oral messages then no solution will work unless more than two-thirds of the generals are loyal. Meaning that with 3 generals, no solution will work with a single traitor. An oral message is one whose contents are completely under the control of the sender, so a traitor can send any possible message.

So let's show that for **oral messages** no solution can work for 3 generals with one traitor. We allow for the two decisions `attack` or `retreat`.

Let's first consider this, our general is loyal and sent an `attack` order. But one lieutenant is a traitor and reports to the other that he received a `retreat` order. For **IC2** to be satisfied, we the loyal lieutenant should obey the `attack` order.

Now let's consider a scenario where the general is a traitor and sends an `attack` order to one general and a `retreat` to another. In both scenarios, the one lieutenant does not know who the traitor is and hence to that lieutenant both scenarios are identical.

If the traitor continues to lie, then the loyal lieutenant has no way to distinguish the two situations and must honour the `attack` order. So whenever a lieutenant receives an `attack` from the commander, he must obey it. However, we can say the same thing in the other direction, if a lieutenant receives a `retreat` order from the general he must obey it even if the other lieutenant states that he received an `attack` order from the general.

Therefore in our second scenario, one lieutenant obeys the `attack` while the other obeys the `retreat` violating our condition IC1. This concludes that we have no solution that works without a single traitor.

This shows us that no solution can work for `3m + 1` generals with `m` traitors.

It may seem like The Byzantine Generals problem stems from the requirement of reching exact agreement. However, reaching approximate agreement is just as hard.

Let's assume that the generals need to agree on an approximate time of when to attack. To do this, we require the following conditions:
 - **IC1\`** - All loyal lieutenants attack within 10 minutes of one another. 
 - **IC2\`** - If the commanding general is loyal, then every loyal liutenant attacks within 10 minutes of the time given by the commander.

The time at which an order is received is irrelvant, only the attack time given matters. The orders are processed far enough in the past so agreement can be reached in time.

This problem is unsolvable like the Byzantine Generals Problem, unless more than two-thirds of generals are loyal. We can prove this because if we could solve this problem for a solution of three generals that was able to cope with one traitor then we could construct a three geneeral solution to the Byzantine Generals' Problem that also worked in the presence of one traitor.

The general orders an attack by sending an attack time of `1:00`, and orders a retrat by sending an attack time of `2:00`. Each lieutenant uses the following procedue to obtain the order.

1. Upon receiving a message a lieutenant does one of the following:
    1. If the time is 1:10 or earlier then attack
    2. If the time is 1:50 or later then retreat
    3. Otherwise continue to step 2.

2. Ask the other lieutenant what decision he reached in step 1.
    1. If the other liutenant reached a decision, make the same as they did.
    2. Otherwise, retreat.

If the commander is loyal, then a loyal lieutenant will obtain the correct order in step 1. So we know that IC2 is satisfied. If the commander is loyal, then IC1 follows from IC2, so we only need to prove that IC1 under the assumption that the commander is a traitor. There is only one traitor meaning we know that both lieutenants are loyal. **IC1\`** shows us that if one lieutenant decides to attack in step 1, then the other cannot decide to retreat in step 1, or one of them should defer decision until step 2. We can see that they both arrive at the same solution meaning that IC1 is satisfied. We have constructed a solution to the Byzantine Generals Problem that handles a single traitor, which is impossible. Hence, we cannot have a three-general algorithm that maintains IC1\` and IC2\` in the presence of a traitor.

The above uses the simulation of `m` generals to try and construct an algorithm that works in the presence of one traitor which we know to be impossible. We can now use this solution to prove that no solution with fewer than `3m + 1` generals can cope with `m` traitors.

## A solution with Oral Messages

It was proven that to deal with `m` traitors, `3m + 1` generals are needed. We now construct a solution for `3m + 1` generals.

An oral message is embodied in the following assumptions:
 1. Every message that is sent is delivered correctly.
 2. The receiver of a message knows who sent it.
 3. The absence of a message can be detected.

The assumption 1 and 2 mean a traitor cannot interfere in 2 generals communication. 3 will stop a traitor from preventing a decision by not sending messages.

The algorithm requires that generals can send messages directly to every other general.

A traitorous commander may decide not to send any order. As the lieutenants must obey the same order, we need a default, in this case we use `retreat` as the default.


Let's define our algorithm `OM(m)` for all nonnegative integers `m`, where a commander sends an `attack` or `retreat` order to `n - 1` lieutenants.

Firstly, our algorithm assumes a function called `majority`, this function either returns the majority of the recieved orders or the default value, in our case `retreat`.

The algorithmn only requires the `majority` property.

For `OM(0)` it works as follows:
 1. The commander sends his value to every lieutenant.
 2. Each lieutenant uses the value he recieves from the commander or uses the value `retreat` if no value was received.

Now let's look at it for `OM(M), m > 0`:
 1. The commander sends his value to every lieutenant.
 2. The lieutenant stores the received value, or `retreat` if not value was received. The lieutenant then acts as the general to send the value to other lieutenants, by executing the algorithm `OM(M - 1)`.
 3. The lieutenant stores the value received from other lieutenants in step to or `retreat` if no values were received. The lieutenant then uses the `majority` of all these stored values.

Let's consider the example with 4 generals, annd a single malicious lieutenant. The commander sends his value `v` to all the lieutenants, then one if the non traterous lieutenant sends his value to another, and the traitor sends some random value to that same lieutenant. The lieutenant has then received 3 values, he obtains the correct one by using the `majority` of values. Which would be the ones he received from both the non traitorous lieutenant and the commander.

Next, let's see what happens if the the commander is malicious. The commander sends 3 arbitrary values to the lieutenants. Each lieutenant will receive all 3 of those values as they invoce the algorithm multiple times recursively and investigate the values received from the other lieutenants. This means that the majority of those 3 arbitrary values is used.

## A solution with signed messages

The Byzantine Generals' Problem is hard to solve because our traitors can lie, however we can remove this ability. One way to do this is by ensuring that the generals send unforgeable signed messages. This means that we add the following assumptions to our previosuly made assumptions on what an oral message embodies:
 1. A loyal general's signature cannot be forged and any alteration of the contents of his signed message can be detected.
 2. Anyone can verify the authenticity of a general's signature.

We make no assumptions on traitor general's signatures. This means that traitors could collude with eachother. With our signature introduced, our previous argument saying we need `3m + 1` generals to cope no longer holds. We can now construct an algorithm that copes with `m` traitors for any given number of generals.

In our new algorithm the commander sends a signed message to the lieutenants. Each of which then adds their signature to that order and sends it ot the others, who in turn add their signatures and send it to the others, and so on. Generals can now know if another is a traitor by simply checking whether several signed messages were received originating from the same general.
