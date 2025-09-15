# Polygon Protocol Governance Call: Transcript

### Attendees

Ben Rodriguez, Bienvenido Rodriguez, Daniel Gretzke, Daniel Gretzke's Presentation, Dimitri Nikolaros, Fireflies.ai Notetaker Michel, Harry Rook, Ioannis Tsiokos, Jackson Lewis, Joonkyo Kim, Keefer Taylor, Krzysztof Urbański, Liz Steininger, Marcello Ardizzone, Mateusz Rzeszowski, Michael Mugno, Mirella Guglielmi, Nimit Bavishi, Parvez Shaikh, Paul O'Leary, Quentin de Beauchesne, Rahil's MeetGeek Notetaker, Sajal Agrawal, Sandeep Sreenath, Scott Lilliston, Stream, Tiago - Stakin, Tobias Geiger, Vincent Taglia

#### _This editable transcript was computer generated and might contain errors. People can also change the text after it was created._

Harry Rook: Okay, Welcome everyone to PPGC number 17, main buckets for the agenda today are updates to the Napoli upgrade on mainnet. So some updated timelines and as well as that we will then dive into what we're considering for inclusion in the potential hard fork in Q2 of this year. So the main being and EIP-3074 Account Abstraction as of now.

Harry Rook: So in terms of Napoli, V 1.2.7 was released on testnet yesterday.

Harry Rook: I don't know if we have anyone from the Bor team here Sandeep potentially to give everyone a run through of how that's looking and then what we can expect in terms of updated timelines on that front.

Sandeep Sreenath: Thanks everyone. So yeah, as Harry was mentioning we just released the stable tag for Bor and also for Heimdall. So this would be the Bor hardfork for the Cancun changes. I think all of you already are aware about the hard  which happened last month just before the previous PPGC on Mumbai testnet. So the mainnet hard Fork is scheduled for 20th of March. Yeah, so I think after we deployed on Mumbai there were a few hiccups, but

Sandeep Sreenath: when we investigated, we eventually figured out that and of them were related to the changes in the release. so couple of issues that we found so one was regarding State sync and checkpoint getting installed. So when we initiated we found out that after the Dankun on hard Fork on Goerli.

Sandeep Sreenath: Which is the L1 chain for Mumbai, so there were a lot of transactions or blocks with blob transactions. And so our code base especially on the Heimdal side was not ready for that. Basically, it needed an updated version of both because Heimdal has Bor as a dependency and in the Bors latest version has all the code that's required to handle the blob transactions from L1. So that's the reason why we had to release, a new version of Heimdall. It just had this updated version of Bor as a dependency and

Sandeep Sreenath: Yeah, another reason even after that fix we were seeing that sometimes teaching transactions were not going to in time. And then when we dug deeper we again found out that the issue was on the Goerli side where in general it used to take two to three epochs for finality on gorilli, but recently has been taking much longer.

Sandeep Sreenath: Yeah, so nothing basically on the code base at all. And once all of these things all these reasons were discovered. I think the other challenge that we faced was on Main Network in the previous table release  of a stable version, were  3 of 4, node operators, who complained that Bor, stopped syncing suddenly and then just to restart works, so

Sandeep Sreenath: We also went on to figure out on the issue and then have a fix for that in place because since we have the next version which is going to be a hardfork  version, we didn't want the node operators to be stuck with and these issues where board stops sinking randomly. So yeah, again, we have a fix in place, which is also packaged in version 1.2.7. So there was a race condition that was not handled and we fix that as well.

00:05:00

Sandeep Sreenath: Yeah regarding the hard Fork so 20th of March. I think it's gonna kick in on the around 8:00 am UTC. And yeah, we are already for that. I think Parvez if you want to give an update on the validators how many of them upgraded that will be great.

Parvez Shaikh: Yeah, thanks Sandeep. So close to 30 validators so far upgraded which is good to know and good to see that. everyone has upgraded within the last 24 hours itself. So looking forward for the number to be hundred before 13th of March.

Sandeep Sreenath: Thanks Parzes. Yeah, that's an important point, Parvez. Thanks for raising it. So like I was saying on Goerli, after the Dencun changes kicked in we started facing issues in Mumbai. Like I was saying it is because The Heimdall code base wasn't able to parse the blocks which had blob transactions. So in order for the upgrade to go smoothly, we request all the validators to upgrade at least their Heimdall before that means and then you have time to get for upgrading Bor.

Sandeep Sreenath: So yeah, like I was saying this mainly for validators because the state syncing transactions and checkpoints are proposed by the validator notes. So it is important for validators to especially upgrade Heimdall before the 13th of March. That's when the Ethereum changes go live on mainnet. That's pretty much it. Harry anything else you want me to cover? Or any questions from anybody?

Sandeep Sreenath: If not, I also have one important point to make so since we also have been seen instability in Goerli, I was saying the finality is taking much longer than two to three epochs that was usually the case. So one reason

Sandeep Sreenath: Could be that a lot of validators and other service providers are pulling out of Goerli. We have already seen some of them announce the last date when they will be supporting Goerli as well. So yeah, which means that we also have to start using Amoy testnet more and driving more traffic there. So I would also encourage everyone overall in the entire Community to start using Amoy as the primary testnet  and start switching soon so that we have good amount of traction on there before Goerlii is completely sunset and yeah, we also would have to sunset Mumbai around the same time.

Sandeep Sreenath: Yeah, that's another point. I wanted to cover. Thank you.

Harry Rook: Thank you very much Sandeep. So in terms of timeline then just to lock it in 20th of March. Yeah, unless there’s any objections. If so, please raise your hand and let us know otherwise, yeah, I think we're good to go with that day.

Harry Rook: All right, perfect. moving on then so Do we have anyone from testnet committee? I understand the release was rolled out testnet only yesterday, but just wondering if there are any preliminary findings from you guys. If so, yeah,  feel free to jump in.

Nimit Bavishi: Thanks Hey guys, this is Nimit from Testnet Committee. So we did roll out both actually on testnet yesterday and the changes today so far all looks smooth. no one's complaining of any major issues, of course, some validator specific issues, which are getting solved by going back and resetting it from the snapshot, nothing that jumps out in the logs so far. And all seems to be going well at least on the grades.

00:10:00

Nimit Bavishi: Just I think while I have everyone's attention, I'm just going to double click on Sandeep's point the same committee. The Mumbai testnet committee is also going to transition to the Amoy testnet and we will soon be kind of also reporting on issues if that comes in so far out of I think five or six of the members of the committee three have actually already transitioned and they're live on the Amoy testnet and we kind of wanting to issues there all seems I'm always in sepolia so already well positioned for the upgrade that's done and

Nimit Bavishi: just that if anyone wants to kind of try out more and sync a node, we are now offering a snapshot, the details of which I think soon will be available in some of the common links. That would be pushed out by the Polygon team. But if you guys want to kind of come up and sync up from that particular snapshot, please use it and if you have any issues that come up again, please hit us up again, and we'll be happy to kind of support any debugging that comes through.

Harry Rook: Thank you very much Nimit. So yeah, I mean just to kind of round off those points you made and on Sandeep's point as well. So yeah, do we have kind of General consensus that Amoys essentially going to act as the preferred testnet from now on. I don't know if anyone has any particular thoughts on that, but it seems like the most sensible solution given Goerli’s instability

Harry Rook: Okay, Lots of thumbs up there so positive sign. All right. So moving on then, next on the agenda is potential inclusions in a fork coming in Q2 this year. So the main discussion point being around PIP-22, which is a proposal to add EIP-3074 style account abstraction on POS main net. So in our last call. We had some members of thedevelopment team for the EIP to kind of run through. some of the potential issues with user safety and mitigation strategies and then we also touched on

Harry Rook: The issue of kind of ethereum compatibility as well which is I think probably the main concern in everyone's mind being that we don't want to implement something on POS that will later become, incompatible. Or, there'll be a different, implementation of that style of a kind of Attraction on ethereum mainnet. So yeah between last call called Daniel Gretzke's kindly of produced a very good report on sort of the drawbacks of each of the kind of competing implementations that are kind of running through ACD right now. Daniel I don't know if you want to kind of run through that and present it quickly and we can kind of discuss that..

Daniel Gretzke: Yes, yeah, definitely. So I'll paste the report in here. Just everyone is aware of it again. And then I will share my screen. And then I can go through it with you guys.

Daniel Gretzke: so mainly we were

Daniel Gretzke: comparing three different kind of abstraction proposals. It's EIP 3074, which is the implementation of two new OP codes on and off call which allows a smart contract to. execute transactions on behalf arbitrary Court could execution on behalf of an externally on account and we have VAP 5806 which is a new transaction type which acts very similarly to delegate call within a smart contract. And then finally, there's also RIP 7560 which adds native account abstraction to the polygon that work in that case and

Daniel Gretzke: aims to have very close backwards compatibility to ERC 4337, which is a purely smart contract implementation of account abstraction.

Daniel Gretzke: I think last week on a quarter of call. There was discussion around 3074 verses 5806 and there have been some where in favor of 5806. So the discussion is somewhat heating up again. So I think we're in a good spot To also talk about it and go with one of the solutions. this article that I wrote basically first goes into the different kinds of what is unique about all of these different proposals. their Pros, whether the cons, and then also looking at the impact of the surrounding infrastructure. So while it's clients.

00:15:00

Daniel Gretzke: dev tooling or infrastructure like block explorers then potential conflicts with ethereum if the implementations diverge and then finally also Look at rollback, so. let's say we go with five eight zero six, but ethereum goes with 3074 and we see that five eight zero six doesn't get any usage and we want to again close line more closely with the ethereum client how difficult would it be to undo the things that we've done so these are kind of The topics and I will summarize them here Feel free to go into it later. There is a forum post as well. Maybe someone can link that where we can also.

Daniel Gretzke: Have discussion about this after the call. This was posted last week. So yeah, let me dive into it. I went into kind of what they are already. So I'll jump straight to the pros.

Daniel Gretzke: essentially yet if I have a 5806 and 3074 both allow for arbitrary code execution on behalf of a user.

Daniel Gretzke: Essentially, for both proposals. Someone could write us more contract. And then using that smart contract. You can do basically whatever you want and then perform actions on behalf of an eoa. the core difference between 5806 and 3074 is that 5806 introduces a new transaction type. 3074 is entirely signature based which means that 5806 still needs to be triggered by an eoa and 3074 can be submitted by anyone. This means that 3074, in this case supports also the abstraction of a user where the user does not have to pay gas anymore.

Daniel Gretzke: when executing anything from their account RIP 7506 built on top of your 4337 mainly so it has by part of the most advanced account abstraction features such as key rotation or social recovery. But they are also already available of with your 4337 today. So it's not a benefit. That would only come by implementing rip side 7560 the main benefit from 7560 is decentralizing the mempool. And also allowing for gas sponsoring and also validity ranges. So basically allowing a transaction to only be valid for example within the next five minutes.

Daniel Gretzke: which are added through our RIP-7560 also from what I understand RIP 7560 also acts some of the intermediary step between four three three seven and full account abstraction and in their all right, they outline potential other proposals that could build on top of 7560 to Allow for more advanced accountant abstraction and also the migration of an eoa to becoming a 4337 style account abstraction wallet. coming to the cons is for 5806 to me. The inability of gas sponsoring makes it less attractive.

00:20:00

Daniel Gretzke: to 3074 generally speaking. 3074 and 5806 because they allow for arbitrary code execution There's a risk associated with that.

Daniel Gretzke: And the risk I would say is relatively similar with 5806 you could do a bit more. So while it's good do more simulations. To see the outcome of a transaction that would for example not be possible. With 3074 but the trade-off that you would still have to trigger the transactions is quite high in my opinion. Generally speaking for account abstraction. I see. Three major use cases that people want the first one is patching. And this is possible with 5806 and 3074 and the second one is. A gas sponsoring which allows for much better UX improvements.

Daniel Gretzke: due to being able to just do anything on behalf of a user and just abstract the wallet away from them entirely. So that for example, they log in on the website or into their game and they automatically have a wallet. Within the website that they do not really control, but it can still be non-custodial through social logins and NPC wallets. But not having to send any Matic to these wallets for them to be able to actually use that work and having to continuously top up these wallets is a huge burden on many application Developers. and 3074

Daniel Gretzke: does exactly Target that while 5806 might be more useful to the approve and swap mechanisms that we see today where I can approve and then perform a swap within the same transaction where previously those would have been two transactions.

Daniel Gretzke: with RIP-7560 like I mentioned before to EOS existing EOA it essentially only offers two. To benefits over normal EOS which would be gas sponsoring and also the validity time ranges, but it is missing. The additional features you get with a 4337 wallet like batching. so if we were to go with something like RIP-7560 this would mean that. the intended path for all Eos would be to become four three three seven wallets and four three three seven comes also with its own set of challenges and

Daniel Gretzke: RIP-7560 as well. It's by far the most kind of complex to integrate and Implement and that's something I will go. Into a bit a bit later.

Daniel Gretzke: so when we talk about mitigations to these cons.

Daniel Gretzke: The biggest con for 5806 and 3074 and that's what also was mentioned during the last governance call. Is the arbitrary code execution and the prevalence of wallet trainers?

Daniel Gretzke: to this I see a lot of this right now already because we've moved as an industry a lot towards signature-based approvals with permit 2 or even creating listings on Open Sea, which is then also entirely signature based. So this already allows attackers to drain your wallet within one or two signatures. very quickly Especially when you do most of your trading through unit Swap and have most of your assets approved with permit 2 it's very easy to get these with a permits or batch signature-based transfer transaction. So yes, it does increase the risk of becoming a victim to wallet trainers. This is already happening today and are already

Daniel Gretzke: wallets like Rabbin that are trying to tackle this through various methods like in integrating transaction simulations to see the inflows and outflows of assets and being able to mitigate that

00:25:00

Daniel Gretzke: but also with signature, we're basically have assigning trust scores to certain smart contracts that you're interacting with. in order to be able

Daniel Gretzke: to warn people when they're about to interact with the potentially malicious more contract just to raise their awareness. the good thing about 3074 is that by default? It's just an ecdsa signature of a few values and blind signing is mostly permitted by most major well as applications, so in order to be able to support 3074

Daniel Gretzke: we would need to find a new signature scheme. maybe find a way to make it follow the 712. standard

Daniel Gretzke: or have some special implementations on the wallet provider side, but it's unlikely that a user who just installed the wallet would be able to just sign it because metamask for example would prevent it because of what people in signature of just a hash.

Daniel Gretzke: Let me quickly look at

Daniel Gretzke: Yeah, I think that's pretty much it to the mitigations. You can't really mitigate the fact that 5806 cannot sponsor transactions that just a con that's there and that we would need to live with. And even though blind signing is disabled by default 3074 could still bring a lot of value to first of all people who know what they're doing somewhat. So I've heard that batching is something highly requested on the MVD side. For example, they could write their own smart contracts and they wouldn't use a wallet to interact with these smart contracts to make their own operations more efficient. It would just allow them to expand their options. additionally when we talk about NPC wallets with social logins for applications that just want to completely abstractly the functionality from the user.

Daniel Gretzke: in these cases all the wallet interaction is happening. in behind the scenes just in the code without the user having to confirm something explicitly in entering a password to unlock their private key. it just happens. So if such an application wanted to steal all their assets, they would be able to anyways, but this would allow them to

Daniel Gretzke: you are a better user experience for applications like this. When it comes to or the kind of implementation challenges and maintaining. A different client from the Ethereum times 3704 is the one that requires the least amount of changes. So here we only have to change the EVM execution environment to implement the new OP codes auth and auth call. We already have a patch solidity version that we experimented with and for future updates whenever we want to. And a new update we would just have to make sure that rebates them in a way that the off and off call functionality would still be able to work.

Daniel Gretzke: Yet 5806 requires a new change to the clients to introduce a new transaction type. According to the EIP and alternative mempool is most likely not required to the similarity of delegate cult transactions and type 2 transactions. Additionally. There's also changes required to the evm because there are some special circumstances. when using that delegate transaction type with a smart contract, so in that case when I'm del sending a delegation transaction the smart contract It can do a lot of things so it can revert it can.

Daniel Gretzke: It can emit events, but it cannot store anything. For example, I think it can also not self-destruct. So there are a few site cases that we would also need to implement in the evm. So there is already changes in two places that we need to do with need to have to RIP-7560 by far requires the biggest changes because it uses an alternative metal. That would need to be implemented. And also there's the new transaction type and on top of that also a lot of RPC endpoints or existing rpca and points that need to be modified to support the new transaction type. So this would be by far the biggest change out of all those three.

00:30:00

Daniel Gretzke: when looking at dev tooling and wallet interactions, 3074 should not affect most of the developers because as long as people. off and off call existing developers can just use solidity version that does not support it and their code should deploy and compile very fine only when someone wants to build Some use case utilizing 3074 and off and off call they would need to use the patch solidity version in that case.

Daniel Gretzke: We would need to provide patch solidity versions as long as there aren't any official solidity versions supporting off and off call and kind of the second challenge that comes with that is also that local nodes like Foundry.

Daniel Gretzke: Yeah Foundry Anvil node, and the hard head node. They do not support 3074 yet. So. that makes it very difficult to test the author and off call and the environment internally we use a devnet for that but not everyone has access to such a devnet. So either we would need to work with The Foundry Foundation. And I'll make Foundation to kind of build that functionality out or we would need to passionate ourselves. But that's something that we would need to support if we want to support 3074 long term, but hopefully this gains more adoption and such a case so that we don't have to do a lot of the heavy lifting and maybe only do they have a lifting in the beginning.

Daniel Gretzke: With ease by eight zero six and seven five six zero. They both introduce a new transaction time and that means that when you just send a normal transaction using ethers or web3.js. Those wouldn't be supported straight out of the box. So these transaction types would most likely to be called using RPC methods manually submit these transactions until this Library support those alternative transaction types. when it comes to block explorers or indexers most likely most often work was just events but block explorers also, obviously look at the transactions and display them to users. 3074 again is the one that

Daniel Gretzke: should not really affect existing infrastructure because we're not changing how transactions are executed. or the types of the transaction so emitted events and calls should mainly be unaffected. only when interacting with a smart contract that supports 374 then there might be cases where existing Public Storage would not work properly. So one example of that would be I wouldn't be able to verify a smart contract that uses these off codes. But in that case maybe block explorers could also utilize the patch solidity versions for that. with EIP-5086 and RIP-7560

Daniel Gretzke: they both because they have new transaction types that also have different parameters. They would need custom implementations by block explorers to display that probably to the user. now coming to potential future conflicts with ethereum. so the addition so when we talk about adding new opcodes or new transaction types. There is the risk that we choose one thing and then ethereum. Chooses another thing and so for example what's transaction types there are identified by a certain ID. We know the Legacy transactions as type 0 for example, and then the new EIP-1559. There are type 2 of so

00:35:00

Daniel Gretzke: RIP-7560 while 5806 proposes a transaction type of greater than three. So you already see that there might be multiple proposals that aim to use the same transaction type. For example, where such a Divergence could occur.

Daniel Gretzke: I was looking for. Difference proposals that are also talking about integrating new opcodes. and more specifically opcodes for Xerox F6 and 0xF7 where often off call will live and I only found I think two eips. It's 2997 and 5 4 7 8 to use the same occurs and Slots, but they're both segment. the risk basically with

Daniel Gretzke: using this so that the risk would be essentially we Implement 3074 ethereum adopts another EIP that also uses the same opcode that would mean that perhaps the smart contract built on Ethereum with compile and it could be deployed on Polygon. But because we use a different opcode at zero X6, for example, that could have unintended side consequences and we would like to avoid that but the introduction of new OP codes is very infrequent. And like I said, there are only two and both stagnant and So I would personally say the risk of this happening is pretty low.

Daniel Gretzke: when it comes to 5806 and 7560 there is multiple EIPs proposing new transaction types with an idea of four or greater and here we basically have the problem. So let's say we were to go with 5806. and we give it the transaction ID for but then ethereum may not adopts rip 7560 and gives it also transaction type form. That means that if someone tries to send a transaction Type 4 with their wallet on mainnet and then switches to polygon. it's need to kind of keep track of what network is using what for a transaction types and this could also be

Daniel Gretzke: not only wallets would have to do that. But also probably providers if there's Jazz and what 3.js. So we would have a Divergence there for a conflict. Rolling, that back could be very very difficult. Similarly. If we anticipate that ethereum is not going to go with 5806 but we are but we know that they might use another ID and then we say, okay we give

Daniel Gretzke: 5806 ID 10, but then it's implemented at a later point at id9 on ethereum. We're run into a very similar issue where we have different ideas and for different transaction types and different networks, which would be somewhat of a problem long term

Daniel Gretzke: and then finally when rolling back the upgrade, so I wanted to also consider the case where for example the top 5806 but then if you're in a minute adopts 3074 and then we do as well, but 5806 doesn't see any usage anymore. There is a lot of conflicts. We need to continuously support it but no one is using it and what would happen if we were to remove? That functionality. I wanted to have a quick analysis of that obviously goes a lot deeper in the end when it comes to the final implementation, but this is more of a high level overview. so in case emp374 becomes absolute the implementation of off and off call could just be deleted from the EVM implementation. The impact basically would be that all

Daniel Gretzke: It's about contracts that use 3074 would no longer work. And they would just revert due to they would panic because there is an unknown opcode.

Daniel Gretzke: because a is delegate control over their own accounts to a contract like 3074 that uses 3074. No funds would really be at risk. They would at most lose some functionality or transaction gas sponsoring. In such a case. Funds could be recovered by just sending a small amount of Matic to the wallet. So they have fun, to pay for gas fees. So I would deem that risk of rollback. very low. For five eight zero six. We're very similar.

00:40:00

Daniel Gretzke: territory year because all the smart contracts would be usually called through the delegate cult transaction type. They could still be called normally so there might be some side effects, but depending on the individual transactions, they could just not be used anymore to delegate call. And would probably just also be rendered useless. So here I also deemed the risk of that affecting funds pretty low. with RIP-7560 it's a bit more complicated because the

Daniel Gretzke: The Proposal in itself is very complex and the ability of upgrading existing you receive for three seven wallets to use RIP-7560 is I think not very likely to be feasible to roll back and undo all of these upgrades. And in the worst case this might result in funds that are stuck. But especially on RIP-7560 this would require further investigation. This was just a high level overview.

Daniel Gretzke: So in the end, I think that's pretty much.

Daniel Gretzke: the summary of a proposal analysis I would love to hear some thoughts and some feedback.

Daniel Gretzke: Okay doesn't seem like we have a lot of feedback. Personally, I'm still very much in favor of 3074. due to the ability of gas sponsoring and badging and just elevating

Daniel Gretzke: is to do just a lot more. than they were previously able or capable of and also the overhead is seemingly very little compared to 3074 and allows applications. I mean the actors or others to really enhance their own experience on polygon. And regarding the risk, I think due to Blind signing. Not being supported by most wallets it's deemed a bit lower. I think we can also work with major wallet providers to have a list of whitelisted or invoke contracts that could be trusted there so this is also mentioned in the 3074 proposal that basically

Daniel Gretzke: an invoker contract should be trusted on the same level as smart contract wallet implementation that you choose. so this could be something that while the providers to help us to really make that safest possible. And then lastly we're also very open to work with the original team behind 3074. To discuss additions or modifications to that proposal. So for example adding also something like validity ranges so that we can make sure that signatures are only valid within a certain time range. and other ideas, we also might have but We're committed to also work on that and should we go with that proposal?

Daniel Gretzke: To summerise. It was a lot of information and a lot of digest we have the Forum post. Maybe said, yeah, I think someone listed Thanks for linking that. Go through the proposal or analysis again if you have any thoughts questions.

Daniel Gretzke: Go to The Forum. I'll be there I'll be able to. Respond and discuss with you and hopefully we can then settle on something soon and bring it in an upgrade in Q2

00:45:00

Harry Rook: Thank you very much time. That was a great analysis. I appreciate you presenting that. So just thinking then in terms of Ethereum compatibility I get the sense and the last couple of acds that. people are getting more excited about, kind of account obstruction Primitives on layer 1. So the question in my mind is like do we want to be the first Movers? despite the fact that 374 may be and we kind of practical sense, potentially roll it back if needs be.

Harry Rook: but yeah, I mean do we want to essentially wait and see what Ethereum does before making a move or wondering if you have any thoughts on that.

Daniel Gretzke: I think Paul O’Leary really said that pretty well in the last governance called and we're someone in a chicken and egg problem that we don't want to Compatibility with Ethereum but Ethereum doesn't want to act until they've seen that something can work. So someone has to do. The first step at some point and also knowing the Ethereum foundation and their care and caution when it comes to new features that are untested.

Daniel Gretzke: I'm not very confident that they would do the next step. Let's say this year, right but we do have the possibility to do it this year we can so Sandeep says we can deploy it to our tests and just Tested out and we can always choose not to go to mainnet with it. So I think it's actually important that someone does go first and if we're not going to go first. I am afraid that no one else might maybe some other L2 is also thinking about 3074 but we're also the only one duly publicly speaking about this and I mean, we've been already working on it for over a year now internally.

Daniel Gretzke: So I think it makes sense to go with it. And also like I mentioned 3074 should have the lowest. rollback cost in case we actually need to remove it again.

Harry Rook: Yeah, I mean it makes sense. So I mean, this is kind of looking towards the hard foot. So probably be part of the last stages of Q2. So, we still have time together more feedback and consensus on the report you wrote so. Yeah, We go over this discussion in the next call. and then we can form a concrete plan on how we tackle this. And just kind of on that thread as well, Sandeep. I don't know if you have any thoughts on how long 3074 would take to kind of get implemented on testnet.

Sandeep Sreenath: Yeah, I think we already have some reference implementation. but yeah, we'll have to see to be honest. We haven't really started working on it because you're still waiting for the decisions on this which part we are taking. yeah, I guess there was some initial work that we did last year. I think prathi when he was working on the account abstraction like he was exploring different, eapes and probably that's when we finalized on 4337 and we went ahead with that. But yeah, I think on 3074 the implementation still needs to be done. So

Harry Rook: Thank you we're nearly at time guys. So we've also finished the agenda points unless there's any closing thoughts on 3074. I think we can wrap it up.

00:50:00

Harry Rook: Okay.

Tiago - Stakin: Thank you guys.

Harry Rook: Awesome, just one final point guard.

Tiago - Stakin: That's super expensive.

Daniel Gretzke: Yeah, I probably should have put it into a presentation or something. I just ran out of time. But yeah, that's why I say take your time digested and then come to the forum. So we'll discuss further.

Tiago - Stakin: Thank you.

Harry Rook: Okay, the next call will actually be in two weeks this time guys. So we're gonna have an impromptu call to discuss updates on the LxLy Bridge as well as the Staking hub so yeah, I'll be sharing a calendar invite for that one shortly. Yeah. Thank you all for joining and I'll see you on the next call.

Tiago - Stakin: Thank you.

Sandeep Sreenath: Thanks, everyone.

Tobias Geiger: Thank you sirs. Bye-bye.

Meeting ended after 00:51:13 👋

