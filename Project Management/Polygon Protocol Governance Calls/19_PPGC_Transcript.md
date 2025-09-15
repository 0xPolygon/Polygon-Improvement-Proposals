# Polygon Protocol Governance Call: Transcript

### Attendees
Ben Rodriguez, Can, David Silverman, David Silverman's Presentation, Dhairya Sethi, Dhairya Sethi's Presentation, Dimitri Nikolaros, Fireflies.ai Notetaker Can, Fireflies.ai Notetaker Parth, Harry Rook, Jerome de Tychey, Jerry Chen, Joonkyo Kim, Krzysztof Urbański, Manav Darji, Marcello Ardizzone, Marcello Ardizzone's Presentation, Mark Holt, Michel Muniz, Milen Filatov, Mirella Guglielmi, Nimit Bavishi, Parvez Shaikh, Pratik Sanjay Patil, Quentin de Beauchesne, Rahil's MeetGeek Notetaker, Sajal Agrawal, Scott Lilliston, Simon Dosch, Stream, Tiago - Stakin, Tobias Geiger, Vasanti Rode, WebDev StakeWorks

#### _This editable transcript was computer generated and might contain errors. People can also change the text after it was created._

Harry Rook: Ok, So welcome everyone to PPGC number 19. In terms of the agenda for today, the main focus will be around finalizing the inclusions for the Ahmedabad hard fork. So, for those that have been following along for a while now will know that over the last six months with deliberated over EIP 3074 and also increasing the max Code Side Limit. So those two have been put forward as candidates. And as well as that we have a couple of new additions that the authors will present today on the call.

Harry Rook: So yeah, we'll essentially finalize the candidates and then we can move forward with the Ahmedabad upgrades. So, in no particular order then moving through the candidates. First of all, we have PIP-22, which is a proposal to bring EIP 3074 style Account Abstraction to POS. So, we have discussed this Almost Ad Nauseam over the last six months. Yeah, I think on the last call we got some good direction in terms of it being included on Amoy but yes, since that point, there's been a push to Include on mainnet as well. So I think we have the author on the call, So Manav, I don't know if you have any updates on that front how things are looking?.

Manav Darji: I joined a bit later. So you're asking about 3074, right?

Harry Rook: Yes, yeah.

Manav Darji: Yeah, Yeah, so I do have some quick updates so We already have a PR in the Bor client for which basically implements AUTH and AUTH CALL over the past two weeks. We're trying to add some additional tests over these upwards to make sure that we are not missing anything and also because we cherry-picked things from geth there were a couple of changes that we had to make sure that it matches with the latest spec of 3074. So yeah with all those things handled, we're currently in the process of testing things on a devnet internally.

Manav Darji: Yeah, there's sufficient tooling around it there are patches of solidity and Foundry to test basically these kind of upwards and there are quite a few involvement contracts one which

Harry Rook: Okay, Yeah makes sense. I'm not sure if you've seen the recent discussion around 5002 but we had some discussion in one of the telegram channels around that I believe they're looking to add that in the fork after picture to mitigate some of the kind of Potential concerns with 3074 but yeah, I just wanted to highlight that I think that's probably a topic for discussion on the next call, but I want to flag it.

Harry Rook: Okay, cool moving on. Then. Next candidate is PIP-36. So this is a more recent proposal. Which effectively allows the replay of failed State sync transactions believed to Dhairya is the author there. I don't know if the Dhairyas on the call and wants to quickly present and go through the proposal.

00:05:00

Dhairya Sethi: Yeah, sure. Hello everyone.

Dhairya Sethi: I'll just quickly share my screens so that roll on the same page and following. The purpose of this clip is that until we have migrated to LxLy completely for State syncs we will be adding the ability to replay state syncs and we'll also patch about the bug in the contract.

Dhairya Sethi: I don't know how many if you guys are aware, but early in the days when we used to write transfer in solidity. There was a change in the gas price in the Berlin hard folk of Sloan what basically that led to was breaking contracts which use transfer to send ether to contracts with code in the receipt for the sea fall back with inside code. Most prominently this brick. No so safe contracts. So the thing is that Access this will On ethereum so that you could prepare for storage slots that you go you're going to access such that the gas cost is lowered such that the contract the call is not brick. so any questions

Dhairya Sethi: Please feel free to interrupt me at any point or if you feel I'm going to fast, please let me know. this problem was never fixed on the automatic token and this EIP aims to patch this bug here on the magic token contract where on plasma deposits from L1 to L2 when the state receiver calls the deposit method on the magic token contract which exists at one zero one zero.

Dhairya Sethi: we're going to be replacing this transfer with a call such that first. This is fixed says that these state did this transfer does not fail this the main motivation behind this was because we had a few failed State syncs on this where the receiver was a nose is safe. So essentially a tldr of the issue of the first issue, which motivated this bit is that on plasma deposits when automatic token when the receiver is a Gnosis safe or any contract with the receipt fallback with any amount of code those transactions fail essentially the states thing there fails, but that essentially leads to us since we do not have the ability to replay fail state things and states keep going on. Sequentially those funds are locked on and one and not recoverable. So essentially this fix games to

Dhairya Sethi: make sure that future State sinks for this particular condition matter token using plasma presentation receiver as a noses safe. This does not fail. This is one of the PIP and the second part is to address the state things that I've already failed. Which will essentially from now till the point when we introduce this on the hard folk as you see this requires a change on the magnetic token contract. And since the contract is Not upgradable By Design, we will require a hard folk to update the right code at this address. Any questions so far or should I move on to the second part?

Dhairya Sethi: Okay, cool, so the next thing is we talk about replaying fail State syncs how that would look like is that in the Shanghai hard folk we added the ability to on the contract we added an event which logs all states thanks, whether they succeeded or failed if we just want to see that in code We can quickly see that here.

00:10:00

Dhairya Sethi: So this state committed event was added in the Shanghai Hartford. It did not exist prior to that what this state sync or this event does is that it lets us know whether a particular setting had failed more easily. Such that we don't have to trace the call trace of all states and then figure out whether the state ID field or not. So what this essentially does is that this provides proof on chain that particular State sync had failed. So what the idea is that we collect all these there's two methods one is a completely process method where after this hard Fork the contract is somehow able to prove a past rent emitted on the past event was emitted on the blockchain. This is possible using the block hash of code. But the problem is that in solidarity. We are only able to access the last 256 blocks the block ashes of those.

Dhairya Sethi: By this I mean that if we have the block upward and block hash of a particular block and let's say your fail State sync occurred in that blog. You could generate a Merkel proof against that block cache, which is a block root hash that this particular event emitter was emitted in this book in this block and you can trustlessly where it prove that it was for this state ID and the success field so technically you should be allowed to replay that same now because of the limitation in the VM that we cannot access more than the last 56 blocks the second cleanest solution that we could come up with was that from the Shanghai heart folk till

Dhairya Sethi: When this artwork 36 goes live the Ahmedabad Hartford, what we're going to do is we're going to collect all of the failed State syncs and we're going to build a Mercury out of it and have the root as a state variable on state receiver such that all states failed state things from the Shanghai heart folk till the end of our thoughtful. All of these are collected in a Mercury such that in the future users are able to prove that they had a failed State thanks and estate sink and replay them. So obviously since we'll be collecting the Markle tree, it needs the process of building the tree and posting the route in severe trustless. So there will be and in code snippet available where can since this is obviously public information. Anybody can rebuild the tree and verify the state route that we've posted on state receiver is correct.

Dhairya Sethi: As that there is no malicious state things that are racist states that are being added such that they can be proved as well.

Dhairya Sethi: this is part one of Replay field state things basically replace state things prior to the Hartford up until the Shanghai heart folk and in the future, what we plan to do is that let's say that that comes out to be another bug which we are not anticipating right now. So to make this future proof until we get rid of the state mechanism in Alexa why migration we will be adding the ability. We will be storing sales state things along with emitting these events so such that anybody will essentially be able to replay that fail State sync at any point in the future only once and obviously we'll have to take care of important security considerations here so that these Replay in fail state things we entered it doesn't matter if their front run or not and the cold data that it executes remains immutable to means the same because if we allow a user

Dhairya Sethi: To replay a fail State sync with different call data that breaks the Integrity of the bridge. So what we'll do is from now onwards on each state sync when the system calls commits to the commit State method. We will be storing in storage exact the receiver and the call data for that particular state ID and…

Harry Rook: Other area…

Dhairya Sethi: there will be another external function exposed.

Dhairya Sethi: which can replay these failed States. Only once essentially so as that future says state things are also replayable.

Dhairya Sethi: This is the gist of 36 and initially the first few parts talks about the motivation the rationally behind why we're doing this and then we talk about the three changes that we're going to do this to summarize again first is fixing the transfer bug such that in the particular case of plasma deposits on non-eoa addresses there.

00:15:00

Dhairya Sethi: There is a receipt fallback with some code in it.

Dhairya Sethi: We provide some sufficient amount of gas.

Dhairya Sethi: This gas will be around 3,000 gwei

Dhairya Sethi: Such that the transfer succeeds and…

Dhairya Sethi: in case this will obviously be logged as in the sales State…

Dhairya Sethi: since part array such that this can be replayed.

Dhairya Sethi: The second thing is that we're going to be adding the ability to replay sales stations on from the future.

Dhairya Sethi: And the final thing is that we're going to be building a local tree and…

Dhairya Sethi: posting that on chains as that from the Shanghai Hardware from this block number till. The end of our heart for goes like…

Dhairya Sethi: if this is included in it those state things are Rel playable as well.

Dhairya Sethi: So that's the Shanghai helpful that was around.

Dhairya Sethi: We created the block number here this I can tell you exactly and…

Mark Holt: Hi, it's Mark Holt from Eragon here. I just have a question…

Mark Holt: which is Cidry to this. You mentioned the lxly bridge.

Mark Holt: Have you got time scale for that?

David Silverman: Yeah, I can answer on that.

David Silverman: We do not currently have a time scale for that. That's part of the zkpos migration. The last we had polygon Labs were aware. There should be a proposal coming in the next two to three weeks from some developers who are working on scoping out the initial migration. So I expect this to be a topic for the next ppgc on…

David Silverman: what that migration could look like in timing…

David Silverman: but no timing to share yet. I would say stay tuned to watch The Forum.

Mark Holt: I'm just asking because we probably need to do an impact analysis on that. So I think it might have quite well.

Mark Holt: We'll see.

David Silverman: There should not be a major from has as we have seen that they …

David Silverman: I don't believe it should be a major impact on the client itself. Yes.

David Silverman: At that was one of the proposal that's floating around is a gradual migration. So first state sync will be both States sinking. Lxli Alex. Why being the new bridge and then overtime once there's a replacement once there's kind of a backwards compatible method for State sink in the new system, then it will be deprecated.

Mark Holt: just so I'm clear my understanding from that earlier conversation is effectively States since as they exist in the client at the moment will go away once that transitions happened.

David Silverman: So it'll be a period of time. We're both are operating simultaneously. Yes.

Mark Holt: Okay, great. Thank you.

Harry Rook: Also, Thank you for that. Any other questions guys. If not, we can move on.

Harry Rook: Okay, I'll hand it back over to today would actually because next candidate pitfer is to increase the max code size limit so we discussed this one. Some time ago. I think it was ppgc number 13 David. I don't know if there's any updates on that front. and over to

David Silverman: But yeah, major updates just to share the details again. The intent to hear is to increase the max code size and the max Nick code size by 33% going from

David Silverman: To those a two to the 14 plus to the 13 bytes to the 15 and then double that furniture code. The intention here is to allow for the deployment of more complex contracts without needing to resort to Alternative patterns to be clear when this was initially set in the spurless dragon hard Fork. The number was set arbitrarily as quote. There was no contracts at that time that have exceeded this amount if there's one thing I think developers have come to learn we're now definitely seeing many developers start hitting these limits quite frequently analysis from the various ZK teams inside polygon stated that this would not have an impact on the ZK migration or any long-term concerns, the intent is to slowly move this up. So we're going to move this to the 15 potentially considering moving this to a higher value later, but

00:20:00

David Silverman: Any further increases would be done in conjunction with the roll call group the coordinates role of improvement proposals.

Marcello Ardizzone: Yeah, thanks, Eddie. I'm also going to share. This green quickly.

Marcello Ardizzone: Yeah basically the goal of this VIP is to solve a long-standing issue in the POS network with regards to the underpriced transactions. So earlier we had a foreign post here where we recommended to increase the minimum gas price to 30 gray from the current default value of one for all validators. To reduce the span transactions in the network, but that appears to be like a trivial approach and fubernetes or seven planet and this is actually a node client setting right? Not the network level one. And unfortunately when such validators are primary block producers, the required priority fee decreases and user can then submit transaction with lowercase price and those transaction could basically get stuck in the transaction pool for a long time.

Marcello Ardizzone: As the next primary producer might require higher gas price by then. So the pap not only enforces the minimum minor deep of 30 Quay for validators, but also sets by default to 30 gray and other parameter, which is only A level it's called price limit for the transaction pool which represents the lower Bound for the nodes to accept a transaction in the pool. And lastly. There's another parameter which takes place in all of this. It's called ignore price and it's for the gas price Oracle. And that's represents a threshold below which you already ignore the transaction. So by setting all these parameters are coded in the clients for and Degen. We ensure to prevent this time transactions from overburdening the manpool which could also create by the way. It does attack vector.

Marcello Ardizzone: From code perspective we have to requests. This one is for bore and each other one is for edgon. And basically the purpose of this code changes to introduce some utility functions that will basically check for such values when a new back and this initialize in the clients at startup time and then force them to be 30 way ensuring, consistency across the whole network. Of course validators are still free to set their own parameters. They can do that in the bottoms of the protocol here. And that would require though, modification to the clients and hence building a custom version of the binary. But yeah, we took this approach which looked like the

Marcello Ardizzone: one who provide more consistency for the whole network and reduce the Enterprise transactions for protocols. Here's the link for the following case. You want to start any discussion again, if you have any questions, feel free to ask. Thank you.

Marcello Ardizzone: Okay, I think there's no question so back to you edit. Thank you.

Harry Rook: Also, thank you Marcello. Okay, cool, so that wraps up the inclusion candidates, so Yeah moving forwards. I just do a quick check around that everyone's happy to move forward with those inclusions. Yeah, especially Eragon four teams. Just to get your go ahead there.

Marcello Ardizzone: We're good for myself.

Mark Holt: so yeah, so I think we're basically fine with that. I think the only question we've got which is a coordination one is around 3074 in terms of how that's likely to line up with Petra. Because it feels like it'll go into polygon ahead of the ethereum release. Is that fair?

David Silverman: Correct. I believe that that is the intention I believe and…

Mark Holt: Yeah.

David Silverman: and in our discussion with acds they're looking for the else use to move ahead of me. Net on this one in the same way that we've moved ahead on seven two one two in the previous artwork.

Mark Holt: right, so kind of the expectation is that It won't diverge. I guess that's really what I'm asking the question in terms of.

David Silverman: Correct.

00:25:00

Mark Holt: So that's the basic flow. It's just I'm asking from a kind of different management spectacular purpose. Yeah point of view because ideally we'd like it to operate saying the same across chains. so my follow-on question for that is have you got input into that theorem process or

Mark Holt: do you ask help out with it? As kind of weird directly involving the process.

David Silverman: We attend ACD, I know that other contributors on this call attend a CD. We also attend the roll call initiative which they all feed into the same kind of comment theory of governance.

Mark Holt: Yeah.

David Silverman: again these piece calls follow the community's desires and the community started to move quick here it seems and I hope that if we're all aligned here, we all advocate for better ux to cross a theory whether that's in l2's like polygonquis and some of the other Stacks or if it's at aetherium base layer to be clear. we've worked very closely with our other roll up peers. I' you've seen there's some optimism networks that have 30 74 implemented already. There's testing that's another Stacks as well. I believe we're all using the same implementation. I would hope that EAC folks, collectively. Do you understand that the role-ups are moving ahead and don't intentionally try to break in that ability. That would be my recommendation towards them and that's I think what we have shared as polygon ourselves.

Mark Holt: Yep.

Mark Holt: Okay, that's great. Thanks. I just wanted. Just it was a clarification. Really.

Harry Rook: Thanks Mark. I've just linked a comparison run through the Daniel Gretzky did on 374 and talks about all those edge cases in terms of ethereum. Coordination and stuff like that. So yeah, feel free to check it out.

Mark Holt: Okay, great. Thank you.

Harry Rook: so if we're all lines concerns on the inclusion list, then we can confirm that and proceed to ship it so. Yeah.

Harry Rook: that concludes. The agenda items for today. Next call be probably on the 30th of Mayor that we may move it a week or two either side. to suit the rollout of a manavads final point I think it's looking likely that testnet deployment. Would probably Target the end of May.

Harry Rook: And so yeah, we would likely fit the next chord to work around that day. So yeah, I'll be sharing the agenda.

Harry Rook: The Tuesday before so staying in. keep your eye on the space and I'll see you guys in the next call.

Meeting ended after 00:28:44 👋


