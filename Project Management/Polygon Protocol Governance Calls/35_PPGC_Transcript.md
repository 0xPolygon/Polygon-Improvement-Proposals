# Polygon Protocol Governance Call - Transcript
00:00:00
 
Harry Rook: All righty. Okay, start the recording quickly. Awesome. Okay, welcome everyone to PPGC number 35. Uh in terms of the agenda for today, there are well there's one main bucket which is the VB block upgrades. Um things are looking very much like they're ready to go. Uh we have a full specification now for the witness um based stateless verification. also the mechanics of the uh validator elected block producer um and how that will function within Heimdoll. Uh so those will be the main topics for today. Uh we've got Jerry on the call who's going to be doing a run through of of both of those revised pips. So those that remember there were originally two, one with the economic model, one with the spec for VB block, but you know, this has kind of been uh fleshed out a little bit more into into free pips now.
 
 
00:06:10
 
Harry Rook: So there'll be updates on 7264 and then um yeah finally we'll we'll finish off with some just updates around pip 57 which is a proposal to add a uh migrate to function in the pole migration manager contract. Um cool. Uh without further ado then I'll hand it over to Jerry. I don't know if you want to take over the the screen Jerry or if you want me to just run through 72. I know this one's fairly long, so yeah, up to you how you want to approach it.
Jerry Chen: Thanks Harry. Um yeah, let me take over. I guess it's easier to um for me to present and um Okay.
Harry Rook: Yeah, sure.
Jerry Chen: Yeah. So I will start with um the updates um for pip 64 uh since you know we have presented uh these step uh a while back. So yeah in in summary um pix uh pix 64 is um a proposal that um that we introduce this validator elected producer architecture um and then we basically try to um separate the process of uh block producing and block validation and voting.
 
 
00:07:38
 
Jerry Chen: Um so uh the few major changes um are mostly in the in the specification about how the validator will be selected. Um so let's jump to this part. Yeah. So block producer election right. Um so basically what's going to happen in vblo is that each validator in Hemdo will have the ability to vote for um a specific list of block producers they prefer. Okay. And then this is a subset like um you know going to be a subset of all the validators um and it's a ranked um list. So basically the whoever is um in the top of the list will get the highest vote weight. Okay. And essentially basically like any validator will need to get at least more than 66% of stake voting power in order to be elected as the block producer. Um so this is the voting phase and uh the basically the voting phase will uh start one spent right before the blob hard fork. So it kind kind of give uh the system a buffer to you know um to reach a consensus of eventual list of the block producers during this time.
 
 
00:09:12
 
Jerry Chen: Um and initially there there's going to be um three block producers um in these setup. Um and these parameters is currently fixed but yeah in the future we are uh we can you know increase this um valid uh block producer limit uh with the hard fork. Um yeah so um the next thing is about um how do we maintain a good likeness of the board network with uh fewer number of block producers compared to before. Um so with VB block the core idea is that um there's only going to be one block producer in each span and that single block producer will be responsible for generating all the blocks in between and these basically resolve the biggest issue of these mini rigors that we still like potentially have today in production. Um so yeah I mean um the biggest challenge is that if for some reason the primary or the current block selected block producer is down uh or had a failure uh for some reason and we would like to be able to detect that and switch over to the second block producer uh as soon as possible.
 
 
00:10:43
 
Jerry Chen: So that comes to our you know criteria of defining what is the what what it means when you know when the system or when the current producer fails. So basically we're uh observing these status of the current producer by looking at the milestone voting um during the process. Um so if we're seeing there's you know milestone voting uh for the pending blocks that is produced by the block producer then um then the system would think okay this is good and um at least we're getting some vote um from um the current uh validators and um if we are not seeing any you know milestone voting uh from any of the evaluators it means that most likely the block producer has uh stopped running or it has it network has some block uh propagation issue. Uh so in that case would like to switch to the next one. So that's the major uh criteria that we're looking at whether to rotate to a new spin or not. Um and also in terms of who should we rotate the new span to is basically uh that we will select only the notes that has vote successfully voted for previous milestone.
 
 
00:12:15
 
Jerry Chen: Um so for example if a pending producer um has not voted for the previous milestone then that producer will not be eligible for um becoming the next producer. Um yeah so so how fast we can rotate the the spin that's the question we need to answer. Um basically we are looking at uh these two conditions. So the first one is um whether there's at least um you know one/ird of the voting power support. If we we can reach that voting power support like you know above 33% then we will be waiting for roughly five to six seconds. Um so this accounts for u basically the network propagation uh delay in the um in the system. Um and we think that 500 blocks is pretty much the a pretty conservative number that we consider um you know for this type of um criteria. And yeah, so if basically if there's no new board uh board blocks that could be finalized in a milestone within 5 seconds, then we will start a sp uh the system in HDA will create a new span and the new block producer will be able to start producing block.
 
 
00:13:45
 
Jerry Chen: So uh in short if there's a failure uh happened in the current producer um the rotation process will probably be completed within you know five to two uh five to 10 seconds. Um so below here is just a simple example um that we had um for the span rotation. So imagine that each span is 100 blocks um and this is the current span from 200 to 299 and we have a future span of 300 to 399 and currently um we have the block up to uh 279 and you know the current block producer has stopped producing blocks and the new uh span will be starting from 280 until 399. Um so it will cover uh the remaining span of the current span and also the future span as well. Um yeah so that's pretty much the um the process for Sundell um which involves um valular election and also the SP rotation and now let's talk about how board respond to um these cases right so so the main consensus change in B is that now for every single block um block number we will have a definite answer or a deterministic answer who is going to be the producer for this block number and that answer is basically retrieved from uh himo itself right so for uh this is the first uh consensus chain in board we will basically have a a cache in in the consensus which decides okay
 
 
00:15:47
 
Jerry Chen: um who should be the producer and if if the node receives leaves um a a block that is minted by a different um signer then um that block won't be considered valid. Um yeah so in terms of spend rotation we we've designed uh a mechanism of preventing reorgs in bore and yeah in a in a happy path um if a block arrives within 4 seconds um you know after its parents um it's considered valid block and we'll just take the block and then simply validate that this block is uh this block number uh and the block author matches to what the current span is. But if somehow say the block has uh been late you know after four 4 seconds um then we will start waiting for uh if there's a new spin uh that that's that is going to be created by hand within a seconds. Um so if there's no new span found it's most likely that our local network had some issue uh for war and therefore it's not receiving um the new block timely maybe due to um lower you know too small number of um years and um if if there's nothing wrong with the local network then it means that the actually the primary block producer has stop producing blocks and therefore will most likely uh observe an spin rotation and yeah and that block will be adopted.
 
 
00:17:37
 
Jerry Chen: Okay. Um I think yeah I think that's pretty much the like the overview for the change for uh PIP 64. Um let me briefly talk about PIP 72. Um so 72 is um is kind of a expansion of SPIP 64. Um and this pip here is mostly talking about the stless witness uh verification. Right? So uh we talked about that um these still still is witness verification is going to uh remove the requirement of keeping a local state for uh validator and instead it will just use the witness for validating new blocks. Um so I'll briefly go through what the witness is and the witness protocol. Um so basically the witness is very straightforward. is um it contains the data that is required by um by validating a block. Um it's essentially um so this state field here is the most crucial uh component. It contains all the try nodes that um that a node would need in order to calculate the final state roots for um for for a block. Okay. And um here's the witness protocol.
 
 
00:19:09
 
Jerry Chen: It has four types of messages. So the first type is broadcasting the witness data to peers. Um so currently this one is um is not implemented in in bore um as the initial release of vb block because um it's the you know the witness broadcast the witness itself could be um extensive um you know could be pretty large. So if a node has too many peers then it may broadcast um to too many peers um and that might be very costly for you know the block producer. So instead we adopt an you know we we use the rest of these three components or these three messages to uh to transfer the witness. So when a new block is created then um there's these new witness hashes that will be announced by um by a peer or by whoever uh created the block and once it peer receive this announcement then they can send a get witness request. Uh so basically request will contains the hash of the blocks and on receiving the the request the you know the whoever host or announced these winners will be able to respond with the actual data.
 
 
00:20:35
 
Jerry Chen: Um so the witness request itself is um has um yeah so the witness request has um two important things. So the the first one is the hash of the block and the second one is the page number. Um so why do we have the page number? It's because um we sometimes the wings could be too large to be fitted in 16 megabytes um which is the limitation of the single message that is allowed by ROX which is used universally uh in basically all G uh variants. So um so because of this communication trans transfer protocol limitation we can only transfer up to 16 megabytes of data uh in one message. So that's why we have two p uh pagenate span times uh for large witnesses. Um so yeah so um and the witness response is basically the the data itself and the hash of the block the page number and the total number of pages. So say if I request the first page and then I receive this response um and I see the total number of pages is more than one then um I would need to uh go ahead and request more uh the remaining witnesses from this pier.
 
 
00:22:14
 
Jerry Chen: Um yeah. So um oops one second. Okay. So I think the next important part is the state stalless node um operation. So you might notice that um we don't have um any by code which which is the contract codes included in the winners. Um so that's because we're going to uh even if even for the stateless node we're going to keep these by codes uh locally. Um so fortunately that um this data is not very big. So it's going to be 30 GB for storing all the by codes that we have in the main net and we're also going to ass um store some associated uh try nodes that leads to these by codes um locally as well. The main reason why we like to store this bio because is that um a lot of transactions are sharing or you know are interacting with the same contracts uh in you know in in these blocks and then if we're sending these tren uh these contracts over and over is very in inefficient. So instead of you know always transferring um these by codes we would like to just store it.
 
 
00:23:44
 
Jerry Chen: or cache it locally um so we don't have to um you know waste network bandwidth on these data um yeah so um a serless node also have a mechanism called fast forward um which means that I can start an node from scratch today I don't have to sync every single block from genesis block I can just you know start with a very recent um block that has been finalized by milestone. So this is pretty safe because once a block is finalized by milestones never going to be reorged in the future. So that that is going to become the anchor point or the start start point for the sal node. Um yeah so once the node has been uh started then it will start you know uh syncing and um then the sequential sync mode is basically saying okay we had we have had the um we have had synced previously you know but we maybe we stopped our node for a couple of hours um and between there you know maybe hundreds or thousands of blocks that we have not synced. In that case, we'll just, you know, go ahead and start from uh what we had before and sync up to the tip.
 
 
00:25:11
 
Jerry Chen: Um so this is going to be useful for you know if a node is going to participate in uh checkpoint signing. Um because you know without these blocks in between when the node was down uh this node isn't able to participate in checkpoint uh setting. Um yeah let's see what else. Um the remaining of this talk is mostly about um like some rationale about why you know why we don't want to include by codes in the witness and yeah okay so the so the witness pruning is pretty important. So we don't want to store the winners um for all blocks that a stalless node has synced. You only try to store the you know the recent um for the recent blocks um for the peers who is you know for better witness propagation and for the peers who has not um who has not synced to the tip. Um but yeah for like very old witness like you know even a day old witness we don't actually need to keep them locally in the storage. So we have this witness pruning strategy where if a witness is um older than say uh is 100,000 blocks older than the current uh tip then we just uh remove that witness from database.
 
 
00:26:46
 
Jerry Chen: Um yeah, I guess that's um that's pretty much about the st verification. Um yeah, Harry.
Harry Rook: Yeah, I mean uh one area that I noticed um Pete raised on on the forum was that uh like the egress costs basically um curious as to like what we can uh you know what we know about the
Jerry Chen: Yeah.
Harry Rook: egress costs um and kind of how they compare to like prevop um Yeah.
Jerry Chen: Yeah. Yeah. Good question. So, um I I don't have a uh like a public document ready for pre presenting here, but I can just briefly talk through. Um so we have estimated that the witness is going to be um a larger part of the cost in a uh for the steel nodes and essentially um if we're looking at the network traffic today in a minute um the witness size is roughly um between two to five megabytes per block and that would translate to if you know we're taking the baseline of you know if we're are using uh a cloud service like GCP um that translates to roughly $400 uh $400 to $500 um of egress cost today.
 
 
00:28:19
 
Jerry Chen: Um so yeah so that compare that with you know actually running a beefy software um you know with a lot of memory and CPU um that is you know much less expensive um than running a full node. Um so in the future we're going to increase you know the gas limit for the block. um you know say if we go to 5k TPS then this number might increase but um it will still be um less than you know as if we're running a more beefy full nodes to be able to catch up to to the you know the block. Um yeah that's I think that's a very brief uh overview of the cost um related thing but yeah I I think I would just um post um a detailed version of all the calculations in in the forum after
Harry Rook: Yeah. Yeah, perfect. Yeah, just we could probably put them under the economic model pip. Um or in fact, even under this one probably works better actually. But yeah, perfect. Um yeah, I don't know if you had any more to add there, Jerry, but appreciate you going through all the pips.
 
 
00:29:39
 
Harry Rook: It's uh quite a lot of content to get through, so I appreciate that. Cool. Yeah, I mean the other thing as well is obviously a lot of people are on a different call right now. Um, so I think we need to keep an eye on the forum uh in terms of feedback. A lot of people have messaged me to say that they'll be you know looking at the recording and and leaving comments there. So um yeah, we'll keep an eye there. Um in terms of like the final few things I wanted to just briefly cover on the vblock side is obviously we're getting closer to a potential deployment. So, uh, Jerry, I don't know if you have a a view of when we would be looking to do that. Potentially a good day for a moy or a good timeline there.
Jerry Chen: Yeah. Yeah. Good question. Um, so basically we're looking at um we're looking at September 9th as the hardwork date for VWAP. Um, so that's basically, you know, a week and a half from now.
 
 
00:30:45
 
Jerry Chen: So we'll be releasing um you know a candidate version um maybe on September the second which is ne next week and yeah we'll inform everybody when that uh when that candidate version is ready.
Harry Rook: Perfect. Yeah. I mean, one thing we should probably spin up as well is like a like the hard fork pip itself, like we do I don't know if we have a code name for the fork yet. If not, we can we can make one spin up a pip for it.
Jerry Chen: Yeah. Yeah. We we we don't have a name for bop name for bop yet, but yeah, we can we can discuss.
Harry Rook: Yeah, we need a better name. Cool. All right. Well, yeah, I'll help out with that. I'm getting a pip spin up. Um, so then like the final thing as well is obviously these new this new block producer role is going to need to be serviced by someone and I'm assuming in the first instance or like in the initial deployment that it's going to be us, right?
 
 
00:31:42
 
Harry Rook: It's going to be Polygon Labs. Um, Arash, I don't know if you wanted to go through like what the road map looks like for uh like the phasing of how this block producer role is going to evolve over time.
Arash Mahboubi: Yeah. Yeah, sure. Um, I can jump in. I don't have any uh, you know, pretty slides or or to present or anything, but I can just quickly talk through the the thinking behind it. Um, so I think there's a there's a couple of considerations when you think through the deployment and the roll out of this. Well, obviously this being kind of like a significant architectural change to how the chain has been operated for uh a number of years now, uh we want to be, you know, we want to be careful with it, right? We want to we want to make sure we validate everything uh and and be super careful with its deployment because the uh you know um the the maintenance of the chain and an operationalization of the chain and the livveness of the chain is is you know uh I think the most important critical component for the validators for RPC providers for and then obviously for our end users who who are using the chain and um I think over the last couple of weeks there's there's been some you know uh we've there's been some outages and and those sort of stuff which kind of like has bubbled that up.
 
 
00:33:03
 
Arash Mahboubi: So uh ensuring the liveness of the chain is I think is is one of the most critical aspects and so especially with a a significant change like this you want to be as careful as possible uh with with the deployment. So, so that's the first thing and um I think keeping that first point in mind uh the the thinking behind it is as Jerry mentioned we're going to start off with three you know validator block producer nodes um so these will be operated by polygon labs just to ensure that we have kind of like confidence and comfort and can respond as quickly as possible in case of any any issues that we face. Obviously, you know, this will be released in a moy, as Jerry mentioned, September 9th, and we'll go through rigorous testing on a moy with all the validator community, but obviously things sometimes happen in a mainet environment that you don't anticipate. And being able to respond as quickly as possible, I think I think is is kind of critical. And so, starting with with those validator nodes um being opportunity by by Polygon Labs just just, you know, uh makes that process just much much simpler.
 
 
00:34:11
 
Arash Mahboubi: and quicker to respond to. The second piece and the second rationale and reasoning behind it is as Jerry mentioned we like we you know there's some estimates have been done on the costing of running both a block producer as well as a stateless node but those are at the end of the day estimates we will get some validation of those on a moy but until you hit mainet you don't really know uh what the usage pattern is going to be both currently as well as what happens when you know we increase the the block estimate reduce block times and generally get a lot more throughput available on the chain and then those block spaces being taken up. So uh to I guess mitigate against that we also want to make sure we don't like posing unreasonable costing or anything like that on the validator community and and so running the chain for you know a couple of weeks with having that data gives us kind of confidence of what what those figures are uh and and how we can you know adjust or or modify things if if we're finding that you know like the egress costs are significant or or whatever.
 
 
00:35:25
 
Arash Mahboubi: it might be. So um I think I think that's the second point and and really I think the third point is finding the right balance between uh you know running uh uh I think a block producer there's been kind of like some questions you know very valid questions raised on the forum around uh how do we manage ME particularly extractive me so there's been you know uh on the road map there's bunch bunch of thinking around how to mitigate against that there's been some proposals and thoughts around like environments and those sorts of things and we're working still through the best possible approaches of you know mitigating harmful MEV and and uh but also obviously increasing you know value ad services for for validators and block producers. So also you know that gives us an additional kind of buffer of time to to see how these uh this new architecture operates while we kind of address and and solve for those things. So that's kind of the rationale behind starting with uh polygon labs operated nodes and but obviously during this time we want uh additional validators once they have the data behind it.
 
 
00:36:35
 
Arash Mahboubi: So once they know the costing the operational nature of it uh to to you know let us know who who I guess else would be interested in operating uh uh block producer not not purely just because of the costing nature but obviously in terms of the the there is a lot more kind of like demand both also operationally placed on a on a block producer. Um so you know we we want to make sure that the chain is kind of like uh operating very well. Um but but we do obviously want other block producers uh being engaged in in the process and and we want to work with the community to onboard uh additional block producers as soon as possible um after the roll of that of this. Um so yeah that's that's kind of like the the thinking uh through it. I think um you know we'll see how things go on Amoy over the course of September um and then soon after that the release onto mainet and then I think over the next few months towards the tail end of the year um we should be hopefully looking looking to on board uh additional validators in into that role for those that are interested.
 
 
00:37:41
 
Harry Rook: Awesome. Yeah, thanks for that, Arash. I think it makes a ton of sense. Um, in terms of like how how is it I guess this is more of a question for Jerry, but like in terms of how this is handled in Heimdoll, I'm assuming it's a simple fork to just add and remove block producers when we want to change this going forward, right? It's it's all kind of handled uh yeah, via handle.
Jerry Chen: Yeah. Um yeah. Yeah. Good question, Eric. Um so I um yeah so so since these um this hardwork involves both board and handle um it's going to be a co coordinated hardwork which means the um the validator would need to upgrade their both work and pendal um but we make it very easy um for the um for the upgrade which means like we don't require everybody to like upgrade at the same time like we had uh like we uh you know like we did for um handle B2 migration. So in this halfwork um basically HMO is going to um be monitoring the block number or the milestone that is finalized in bore and um and based on that information HDA will decide whether u to create a vblo span or not.
 
 
00:39:08
 
Jerry Chen: So um so in in short um for any validators who is going to um participate in these or they simply need to uh just simply upgrade their handell and board it's at least one spend before the hard work. Um so and that's it. They don't even need to like you know um look for a specific timing uh stuff. So everything will be handled uh internally by the protocol.
Harry Rook: Awesome. Cool. Um, yeah, that all sounds good. Thanks for going through those points. Uh, Arash, Jerry, appreciate you guys coming on. Um, yeah, I think we can move on to the final point. Uh, I appreciate most people are on a different call. So, yeah, like I was saying, we'll have to keep our eye on the forum for thoughts and feedback on those two. But, um, in terms of the last point I wanted to cover today, uh, is PIP 57. So, this is a proposal we discussed some time ago. has kind of been sat on the back burner unfortunately.
 
 
00:40:11
 
Harry Rook: Um but it's to add a migrate to function to the migration contract for for pole which is the contract that handles the matic to Paul migration. Um yeah in short the proposal effectively allows users to um kind of migrate matic to a new address that's specified in the um kind of in the call data um when you interact with the contract. So kind of a fairly trivial change. Um there was just received positive feedback. Um the protocol council were happy to to implement the change. Um so let me just quickly share this screen and so the payload was shared with the protocol council at the start of this week. Um so that's currently with them for signature and the transparency report can be found on the on the forum here uh which details kind of everything included in that payload that the protocol council is signing. Um okay short little uh point there to round it off but that's everything I wanted to discuss today. Um, unless anyone has any other points, I think we can I think we can wrap things up. Okay. Awesome. Well, thank you guys, Arash, Jerry. Um, especially our next call is on the 25th of September. So, um, yeah, keep your eyes peeled and we'll see you guys then.
 
 
Transcription ended after 00:42:07

This editable transcript was computer generated and might contain errors. People can also change the text after it was created.
