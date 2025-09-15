# Polygon Protocol Governance Call - 2025/05/08 15:55 BST - Transcript

#### Attendees
Adam Dossa, Arash Mahboubi, Carlos Juarez, Dimitri Nikolaros, Harry Rook, Jerry Chen, Joonkyo Kim, Marcello Ardizzone, Mirella Guglielmi, Nikhil Chaturvedi, Norma - Supernormal, Pratik Sanjay Patil, Sajal Agrawal, Sandeep Sreenath, Sanket Saagar Karan, Stream, Tobias Geiger, Traversi Normandi, Vik Kalghatgi, Zaki Manian

# Transcript
**This editable transcript was computer generated and might contain errors. People can also change the text after it was created.**

Harry Rook: Okay, beautiful. So, welcome everyone to PPGC number 32. in terms of the agenda for today, there's quite some kind of in-depth topics to discuss, but they're in three main buckets. So, there'll be some follow-ups on the hard fork. we're past audit stage now. So, we're just resolving some final merge conflicts. some people have an update on timeline there but there's some dependencies on the Heimell v2 migration side I believe as there's going to be some kind of dependencies on board for that whole migration. and so that kind of factors into the timelines on the himo migration side Marcela is going to give us a run of the updates there.

Harry Rook: Again, I think it's kind of an audit update as well as the migration tooling and things of that nature. So, we'll begin with those and then I think that'll take us around 20 minutes and then we'll get into the kind of more lengthy discussions which will be focused on PIP 64. So, I think this was posted around a week ago, a week or so ago now. So, hopefully everyone's had time to take a good look through but that introduces effectively a new vision for scaling POS. there's a lot to unpack with that one. So we're going to be going through that today with the authors and some other folks from the POS team. been looking at the forum threads. a lot of the comments kind of point to the fact that it doesn't have an economic model included in that. and those points are very fair.

Harry Rook: So I think one of the action points of this call today will be going through a high level economic model that Jerry has been working on which will be I think finalized over the next couple of days with the view of effectively doing a kind of a call dedicated to that solely in two weeks. So it'll be like a specialized one-off call. we may keep that on a monthly cadence.  So it would be running in parallel to this but just every in two week intervals if that makes sense. and there may be other things that emerge out of this vblo kind of architectural discussion as so we'll get into those things in due course.

Harry Rook: So to kick things off the billay hard fork so as I was mentioning it's kind of an audit question and emerging conflicts in terms of the repos yeah I think there's been some updates on the timeline so I think we have sand deepep on the call I'm going to hand it over to you sir I don't know if you want to do some updates on that

Sandeep Sreenath: Thanks yeah so just a quick recap. so Bilai hard for contains majorly three e pip pip 60 which is increasing the gas limit to 45 million from the current 30 million. the other one is pip 58 where we are tweaking a few EIP1559 params to be able to have a more smooth spikes when there is very high network activity. so we basically changing the base v change denominator to 64 from the current value of 16 and yeah of course the most important one which is pip 61 that will be the petra eips. so the current status is we are almost done with the development.

Sandeep Sreenath: We are fixing a few test cases which are failing after we took the upstream changes from G for the PRA EIP obviously some of them do not apply for core or for polygon PS so we've disabled a few EIPs and I think all the specifications are there in 61 so yeah  We're coming towards the end of the development and testing life cycle and probably we should still be able to hit the timelines of end of May for the deployment on Amoy test net and maybe 3 to four weeks later on mainet. So yeah that's the current status. Any questions?

Sandeep Sreenath: I guess not. back to you, honey.

Harry Rook: Okay, Yeah, thank you for that, Sandeep. all sounds good from my side. yeah, as I was mentioning before, there are some dependencies I think at least handle v2 migration has dependencies on board. So, it's probably a good segue over to Marcelo. I think we have Marcelo on. Yeah, we do. so yeah, Marcel, I know you wanted to go through the migration tool and updates on the audit side of things as  So yeah, hand that over to you and feel free to give us some updates there.

00:05:00

Marcello Ardizzone: Yeah, thanks yeah, I just wanted to give a quick update on VIP 62 for the V2 upgrade. one of the important point is that the audit has been completed. So no critical issues were find which is great. we're now addressing the feedback from the informal security team. about half I guess of the findings have already been tackled with PRs on the migration tooling. the script is done and it's fully Linux compatible. We have added also some commands for utilities in v1 and v2 and we've added the logic to stop v1 at the migration high so that the operators don't need to do it manually and we improved the genesis verification after it gets imported into v2.

Marcello Ardizzone: we've also written a runbook and the readme for external notators who may want to adapt the process. We've completed the DevNet testings. what is missing is more like a end to end test phase including some of the steps that you usually skip when you're in dry run mode like check some validation and so and we've also revived successfully the old Mumbai test net mainly because it has a large state right so we will use it to simulate the real world migration scenario the execution is coming up soon I believe in the next week on bore side the refactor to decouple board from MDL is essentially done bore can run completely kind of standalone which is critical for the migration because is going to go down for some time

Marcello Ardizzone: but also give us a solid fallback in the future if V2 ever goes down again bore can run alone and commit spans independently and we also have a logic now that once is back up those spans get back filled as transactions in Mel and those changes by the way are ready to go into audit in the next few days as well on Aragon we've aligned with the Aragon team and started applying the same board related changes  is there as well. and then we evaluated a new feature called blob which is coming from comet BFT to reduce the long-term state bloat.

Marcello Ardizzone: we've implemented the changes together with informal on comet and cosmos side but yeah post for now the development on v2 because it will take anyway up to two years for the state size to become a problem again in malle so we are prioritizing other work yeah we're also currently implementing a kind of a higher coverage on the ABCI+ testing and we've developed a tool to decode and inspect both extensions  which is now part of the blocks in MLB V2 currently in testing and we are also evaluating whether to build and host an indexer for B2 and if we move forward we'll aim to get it listed on MC scan as well.

Marcello Ardizzone: yeah, last point, we need to finalize the documentation and API updates in a formal way so to say for third party apps and services relying on M.2 to update time and we'll be focusing on that shortly to ensure a smooth integration. and yeah, that was my last point. Thank you guys. if you have any question, feedback or concern, please let me know. Thank you.

Harry Rook: One thing I wanted to touch on Mars though is I'm assuming we're going to put all of the run book in a forum post at some point. I think we mentioned this last time, but it was a bit too early, but yeah, I'm assuming we're just going to have all of this in one canonical doc on the forum so everyone can take a look right at some point. Happy to help get that out there as well.

Marcello Ardizzone: Definitely. Yeah, it will be there and…

Harry Rook: Cool.

Marcello Ardizzone: also there will be some updates posted on GitHub as well. So, yeah, we will follow that on the forum for sure.

Harry Rook: Any questions on the bor and highend side guys? If not, we can move on to the scaling PS section. All right, cool.

Harry Rook: So yeah, next up is PIP 64, which is the kind of VBOP vision for scaling POS. I think we have Jerry on the call. Let me just double check. We do. yeah, Jerry, do you want to do a walkthrough of the proposal? I know it's still early, so I think at this point in time, we'll just do a run through.

00:10:00

Harry Rook: We can answer any questions if there are any. and then we can kind of discuss next steps with the economic model and how that's currently shaping and how we're going to basically handle that over the next couple of calls. So yeah, I mean over to you sir I don't know if you want to share screen or Okay.

Jerry Chen: Yeah, sure.

Jerry Chen: Okay. Yeah, let me present. Thanks, Harry.

Jerry Chen: Yeah. So this is the 64 validator elected block producer aka VB block. And the main motivation of this proposal is to increase the throughput of polygon to higher level. so the current limitation of our infrastructure is there basically two main points.

Jerry Chen: One is we have 100 validators and these validators they are located in different locations through all over the world and it takes time for these nodes to communicate and there's this latency that would take for a generated blog but from a validator to be propagated on the other side of the world. yeah so this kind of latency usually cause these mini reorgs and it's painful for developers to have a develop for really good experience for their users. So one of the goal is to just get rid of this mini reorg.

Jerry Chen: then the second point is that we would like to increase the throughputs by either increase the block size or reduce the block time and if we go with the solution of reducing block time it's going to be more challenging and causing more reords and if you continue use this existing architecture therefore we're proposing this new solution called so from a very high level VB block is an evolution of our current system. It's not like a very dramatic change although there's some part of the components is changing.

Jerry Chen: so the biggest change here is that in the current system we have these concept called span which is roughly 3,000 or 4,000 blocks and these span contains information about what validator sets can propose a block during this time.

Jerry Chen: And right now we have roughly 20 validators who can propose blocks and then each validator will be assigned with a different diff difficulty number and when they build up the chain essentially as the time has passed whoever has the highest total difficulty will win and become anode eventually right so this is the root cause of the mini reorgs. because sometimes the primary producer or whoever has the highest difficulty generate a block but that block is not propagated fast enough to the secondary. Therefore the secondary will also propose and maybe later on that block would get reorg and then things like that would happen. Right?

Jerry Chen: So in this new proposal, we're saying okay, we're getting rid of all the validator sets and instead we will just have one validator per span. and that will lead to no reorg at all. because there's just going to be one entity that keep producing blocks during this time and the rest of the validators will simply validate the block and keep voting on the milestone and checkpoints as usual. yeah. So in this system there's also a concept of validator rotation.

00:15:00

Jerry Chen: So things can happens when we need to be prepared for scenarios like the primary producer is acting maliciously or simply the block somehow is not propagated to the network due to hardware failures. therefore the consensus would need to be able to rotate validators automatically based on the performance of the validator.

Jerry Chen: Say over a few seconds of time where there's no new block being observed by the majority then all the validators will start preparing for new span and select a different validator and that backup validator will become the primary in the new span. yeah so in this scenario we also need to consider force transactions because in a current system it's really hard for somebody to censorship is taken care of by rotating the block producers very frequently like every sprint which is 16 blocks.

Jerry Chen: So if there's some transactions that is censored by a specific value then essentially in the next sprint that transaction will be accepted right but in this example since we have a long much longer span then it's essentially what can happen is that the primary block producer will censor the transaction from a specific user address and then that user will be blocked  from transferring any assets or withdraw the assets to L1. So for that reason we designed this force transaction mechanism which is essentially using the existing stay sync mechanism in board.

Jerry Chen: So the user is able to basically send a transaction in L1 contract which is specifically designed for force transaction and they can put in their transaction call and when the sync happens these data will be transferred to Bore and Bore will execute this on user's behalf automatically. And if the validator fail to include these call data transactions, the block being generated will be rejected by the majority of almost the validators and a new rotation will happen. therefore we will switch to a different entity that produce these blocks.

Jerry Chen: yeah so the next thing is stless verification. So because we're going to increase the throughput with this new architecture it's going to be more expensive for validators to run infrastructures if they continue to use full nodes to validate the blocks. therefore we are designing this new mechanism where the validator will not need to keep a full node locally.  Instead they can just run a stateless node which is very similar to a live client and basically the stateless node doesn't keep any state locally. It would just take the witness for each block and then run through the block execution simply by using the witness itself which is roughly like a few megabytes for each block.

Jerry Chen: yeah so therefore the hardware requirements such as the memory and CPU will decrease and also the storage requirement will decrease dramatically. yeah so the last point is the e economic incentives or in this model the transaction fee is going to change or the allocation of transaction fee is going to change. In the past or in the current system each validator will responsible for mining their own blocks and then they will take all the fees being generated in the block including MEV fees. Right?

Jerry Chen: So in this new system because we have less number of block producers these fees will be sort of concentrated in basically most of these fees will not be earned by the validators where whoever are generating today. so our solution to this problem is basically saying okay we should reward still validating these blocks.

00:20:00

Jerry Chen: therefore all these transaction fees and NV fees will be deposited into a smart contract in L2 and that contract will be responsible for distributing the fees collected for all blocks to different entities such as the block producer and the block validators. yeah so these pip 64 doesn't have exactly the mechanism that I'm describing but we're drafting a different pip which is pip 65 that contains this information.

Jerry Chen: So yeah, I guess I'll pause here and maybe take some questions or if anybody has and then if not then maybe we can go through the next one which is the economic model if 65. Does that sounds good to you Harry?

Harry Rook: Yeah, all sounds good. I'm super curious on the I guess interplay between the block producer and the validators doing the witness verification and what that means for I guess governance and hard forking upgrades in the future. So, that's something I'm personally interested to find out more about. so I guess we can probably chat about that.

Harry Rook: But aside from that, all sounded great. I don't know if anyone has any questions. but if not, yeah, I think we can move on to the economic section. I think it'll probably be high level at this point. I think there's still some open questions, which we need to address.

Jerry Chen: Yeah. Yeah.

Harry Rook: But yeah, like I was saying before, we'll probably set up another call in two weeks and we'll deep dive into that.

Harry Rook: So if you want to do a high level run through then feel free to take it away.

Jerry Chen: Okay, thanks Okay, let me close the preview of this one and sure the economic model. Yeah. so like I said before all the fees and MV fees and transaction fees collected from all the blocks generated by the block producer will be allocated to a smart contract right and then this pit 65 is talking about how we're going to distribute these fees from the cost the smart contract. Okay.

Jerry Chen: so in a very high level we have two entities. One is the validator who are going to only validating the blocks instead of producing the blocks and then we have second entity which is the block producers. now comes down to the distribution mechanism. there are basically a few concept. One is the total fee which is all the transaction fees plus fees that will be deposited into the contract and these total fee will then be divided into two pools. One is the block producer commission pool.

Jerry Chen: so the commission fee is essentially a percentage that we take from the total fee collected across all the blocks in the past checkpoint for example or we can maybe define a period of time which is open to discussion in the future but for now we can just assume that all the fees collected in the past checkpoints. so this is the first the block producer will be able to configure a commission which is a percentage 10% and basically 10% of all the fees will goes to the block producer and then the second bull or the second component is just the validator distribution

00:25:00

Jerry Chen: which is essentially the total fee minus the commission charged by the block producer. So within the block producer itself we also have a concept of splitting these fees between the block producers. so the reason or the motivation is that we have some backup nodes for we have a smaller set of block producers we have primary block producer and backup producers and it's going to be pretty expensive for running the hardware the block even if the machine is not creating blocks just as backup

Jerry Chen: so we need to allocate some of the fees to rewards those backup nodes or these backup producers some of these fees can offset their hardware cost.  And the other part is basically the proportional share which simply is saying okay the block producer should be also rewarded proportional to how many blocks they generated right so the more blocks that is generated by a specific block producer then more fees will be allocated to that so these two subco components all together forms

Jerry Chen: the total distribution that will be allocated to the block producer. yeah. So in terms of the validator rewards the calculation is or the distribution is going to be pretty similar to what we're doing for point so today checkpoint rewards are calculated based on the total delegated stake that each validator pool has and therefore our transaction fee distribution mechanism will be very similar to that with one additional attribute which is the performance score of the validator.

Jerry Chen: So basically the performance score is saying each validator because they will need to vote on the validity for each new blocks generated by the primary producer essentially they need to vote for all the blocks that they see right and then if some of the validators goes down for example or not following up to the network those votes will be missing and that will be recorded in HMO and for that from these data we're able to calculate a performance score to each valid and attribute that to each validator and the score will be a number from zero to one. So zero means the validator didn't give any milestone votes during the last checkpoint.

Jerry Chen: So basically the validator is down all the time and the validator is up all the time and has voted for every single blob in the past set points. so essentially the calculation is going to be influenced by this performance score. which means the longer and the more votes that this value gives in the past checkpoints the more rewards you will get. yeah so I think that is the rundown of the major concept.

Jerry Chen: there's a few detailed calculation and some formulas that have listed in this tip but yeah that's for yeah I guess whoever is interested in these calculation can look into these numbers later after the call we'll post these in the forum yeah

Jerry Chen: after.

Harry Rook: Amazing stuff. Yeah, thank you so much, Jerry. yeah, I don't know if anyone has any questions. I appreciate this is quite I guess a dense subject. There's a lot within this to unpack. so as I was saying before, we will go through this on a number of calls as it develops and progresses. but yeah, I don't know if anyone has any early questions or concerns that they want to raise now. it's fine  if you want to do it on the forum, that's also also good. but yeah, thanks again, if there are no questions, then I think we can move on and close up the call, but I was going to leave it open for a second here. Okay.

00:30:00

Harry Rook: all good then. If there's no questions, as Joe was mentioning, this will be posted on the forum. I think there's also some kind of backing modeling that's been undertaken as well. So, kind of how this looks in USD terms as of today with current pole prices. Obviously, we wouldn't want to include that in the pip just because in six months time could be very different. but that will, I think, be posted alongside this on the forum if I'm not mistaken. and yeah, we'll do a follow-up call. I'll kind of play it by ear depending on what the feedback is likely in two weeks time, so 22nd of May, should fall nicely, in terms of the cadence there. So, yeah, I'll leave it at that for now, guys. If there's no questions, we'll, have everything posted on the forum.

Harry Rook: and we'll keep that monitored for all of your feedback. and then we'll keep the conversation All righty, as always guys, thank you very much for joining. I know you're all in different time zones and whatnot, so it's difficult one to kind of get a date for, but appreciate everyone joining and might see you all in two weeks time on the next call.

Sandeep Sreenath: Thanks everyone.

Harry Rook: Thank you again.

Jerry Chen: Thank you, Carrie. Thanks, guys.

Meeting ended after 00:32:22 👋

This editable transcript was computer generated and might contain errors. People can also change the text after it was created.
