# Polygon Protocol Governance Call: Transcript

### Attendees
Angel Valkov, Anton Gaev, Ben Rodriguez, Christopher Von Hessert, Dimitri Nikolaros, Fireflies.ai Notetaker Bruno, Fireflies.ai Notetaker Nikhil, Fireflies.ai Notetaker Parth, Harry Rook, Joonkyo Kim, Konstantin Ivanov, Liminal Notetaker, Manav Darji, Manav Darji's Presentation, Marcello Ardizzone, Marcello Ardizzone's Presentation, Mark Holt, Mark Holt's Presentation, Mateusz Rzeszowski, Mateusz Rzeszowski's Presentation, Michel Muniz, Mihailo Bjelic, Nikhil Chaturvedi, Parvez Shaikh, Pratik Sanjay Patil, Raneet Debnath, Raneet Debnath's Presentation, Sajal Agrawal, Sandeep Sreenath, Sanket Saagar Karan, staking 4all, Stream, Tiago - Stakin, Tobias Geiger, WebDev StakeWorks

#### _This editable transcript was computer generated and might contain errors. People can also change the text after it was created._

Harry Rook: All right, awesome.

Harry Rook: Welcome to PPGC 27. We do have a pretty stacked agenda today. So, we'll be covering changes to every single major client. So, Heimdall, Bor, and also Erigon. and then we also have some discussions on a proposal to introduce stake token holder signaling or system smart contracts upgradability. so yeah, without further ado, let's kick it off. we'll start off with Heimdall because we've got three PIP’s to discuss on that one.

Harry Rook: So starting with PIP 49 so this was proposed fairly recently and it aims to introduce ZK checkpoints to POS, it'll enable some cool efficiencies and also some potential cool integrations later down the line as well. So yeah I think we have Manav on the call. So yeah I don't know if you want to just do a quick walk through the proposal and…

Harry Rook: then we can do any discussions based on that.

Manav Darji: Yep.

Manav Darji: Hi everyone. let me quickly share my screen. cool. Yeah, I hope the screen is visible. So yeah, PIP 49 aims to basically add zero knowledge proofs for checkpointing.

Manav Darji: So this is a very experimental thing which we were trying out recently as a team and everyone knows we settle to Ethereum through something called checkpoint and every now and then the validators take turns to submit these checkpoints to Ethereum and the motivation behind this is that the whole checkpoint thing flow or the contract call to Ethereum is very expensive in terms of gas usage. because the reason why it's expensive is that due to two major reasons we've given a detailed analysis for the same but if you see the check signature functions takes pretty much 86% of of the total gas usage right now it's not only checking signatures in the function it's also doing reward distribution and a bunch of other things.

Manav Darji: So if you do further breakdown there are quite a few SLOAD’s which happens which is like you're trying to access every validator and whether you're trying to check whether they're active or not and for mainnet this is pretty decently high number so you're kind of trying to load everything from the storage this is costing a lot of gas and the second higher culprit is ecrecovery which is you are doing signature verification on chain. So the aim is we were doing an experimental proof of concept to see if we could use zero knowledge proofs to move the signature verification and everything offchain and just submit a simple proof which can simply verify. So this is a very detailed spec out of it.

Manav Darji: We plan to use SP1 which is sussing zkEVM which is really good production ready virtual machine which can allow us to generate zero knowledge proofs for this and I won't go into the whole circuit design or how we are designing the circuit for generating the proofs but the PIP contains pretty much everything it also contains a pseudo code of what exactly the circuit would do to predict to basically summarize.

Manav Darji: It'll just generate a zero to know proof that given a checkpoint every in the active validator set voted and those who voted there their voting power is more than 2/3 + 1 of the whole validator sets voting power which basically means they are part of the super majority and once that is done we can simply verify this thing on chain through a PLONK or a gro 16 proof. We have some initial results which is that it takes 0.37 million gas compared to 1.9 million earlier. But as I said this also does a lot of other things.

Manav Darji: So I mean I wouldn't say it's a very significant improvement but yeah I mean we are yet to analyze at a very intrinsic level of what benefits do we get. There are certain open questions because proving this takes time.  It takes around four or five minutes for Amoy. We are expecting slightly higher time for mainnet as well. But it requires changes in the Heimdall because then the whole checkpoint proposer mechanism changes. Currently it's simply aggregating the signatures and sending it inside an ETH call. Now you'll have to generate the proof as well will take time.

00:05:00

Manav Darji: So there are a couple of open questions as well and we are yet to fully analyze at a very detailed level of how much gas savings we can have but it's definitely better come if we just take it from a cost perspective because proving it, the proof generation is very very cheap. so yeah that's basically it. initially even if this goes ahead we are planning to keep the existing way to submit checkpoints as it is and we introduce this new method inside the contract so that just in case if any liveness failure with respect to the prover one can always have a backup mechanism to submit checkpoints and everything like bridging or checkpointing works as it is. So yeah that's pretty much it from my side.

Harry Rook: Thanks, Yeah, I mean, I know we've got a bunch of validators on the call, so yeah, if anyone has any questions, I'll link the forum post in the thread, so you can check it out and leave any questions there. I think there are some fairly large open questions that still need to be answered, right, in terms of the economics of the proof of costs and things like that.  So yeah, there's still a lot to figure out, but yeah, I mean it's super exciting that you guys have got together a proof of concept. So yeah, hat tip to you guys. looks like a super exciting proposal. All right. If there's no questions, then we can move on.

Harry Rook: So the next PIP is PIP 52. So it specifies a change within Heimdall that changes the Bor selection algorithm. So yeah, I don't know if we have Raneet on the call who could give us a quick walk through of that one.

Raneet Debnath: Yeah, I'm in here just a second.

Harry Rook: No worries. I'll link it in the meantime.

Raneet Debnath: Yeah. hey, hi everyone. yeah, so I'll just quickly present my screen. One second. It should be up at any moment.  So yeah, basically as Harry already mentioned, this PIP essentially proposes a slight change in the Bor producer selection algorithm. So yeah, before diving into what the change actually is, I'll just briefly walk through the POS architecture and how the Bor producer selection algorithm currently works.

Raneet Debnath: So yeah, I mean as you guys already know, Heimdall is the consensus layer and Bor is the execution layer. and essentially Bor depends on Heimdall to select a set of producers for a range of blocks which we call a span and essentially a validator would propose a span which looks like this.  And what would happen is that every validator would run a deterministic algorithm which is seeded by an ethereum block hash and via that we get a set of producers which will produce a range of blocks in Bor. So that's how it works right now.

Raneet Debnath: what we are proposing is to increase the randomness and fairness of the algorithm. We are basically changing the parameters that this algorithm uses to use the block hash of a span which is for so I'll probably explain it via diagram maybe I have it here.  So basically if we are proposing a span n instead of taking the ethereum block as the seed we would take this block hash which belongs to this span n minus2.

00:10:00

Raneet Debnath: Ideally this would be the end block of Nminus2 but it probably will lie at the very end of span Nminus2 as the seed again to increase the randomness of this algorithm and we would use the voting power which was set during the beginning of span Nminus2.  So essentially these are the changes. So I mean the algorithm itself doesn't change, it will still function as it does but we are just tweaking the parameters to increase the unpredictability of the producer selection algo.  So yeah, I mean yeah, this is the spec just the pseudo code of how it works.

Raneet Debnath: yeah what we also try to do is we try to get a block hash that is produced by an author which doesn't collide with the block hash producer or the seed producer I should say for the last 100 spans to increase the fairness that any validator could get to propose a block that would be used as the seed for this pseudo random function which runs.

Raneet Debnath: 

Raneet Debnath: So yeah, this is basically the pseudo code which you guys can go through later on and this would require a hard fork in Heimdall because yeah this is a breaking change and yeah this is mostly it I don't know if you guys have any questions I am open for it.

Harry Rook: 

Harry Rook: I've got a question on my side,…

Harry Rook: Rene. So, in terms of timeline, what are we looking at in terms of a potential target for Amoy or anything like that? Are we looking pretty soon or…

Raneet Debnath: sooner. Yes.

Raneet Debnath: We would try to aim for maybe the end of November. If not, maybe the beginning of December and…

Raneet Debnath: probably mainnet would have to be in January. but yeah that's…

Harry Rook: Yeah.

Raneet Debnath: what it looks like. we are done with the implementation mostly just probably need to evaluate because there's a big change. We need to evaluate it very well from the security perspective as well. but we are mostly good. I think we've done the testing which looks good. So yeah it's the time line. Thank you.

Harry Rook: anyone have Any thoughts or comments, feedback? Okay, yeah, thanks for presenting that, Appreciate so yeah, moving on then. The next PIP on the Heimdall side is PIP 43.  So, this is a work stream that's been going on for quite some time and Marcelo's been spearheading this. and we haven't discussed it for probably a couple of months. So, yeah, there's been some updates. The PIP has evolved. So, it's changed to reflect the new spec and a lot of the details were a little bit more fuzzy before, but now we have clarity. Those have been added in.

Harry Rook: So yeah, Marcelo, I don't know if you're on the call and able to just do a walk through of some of the updates there as well.

Marcello Ardizzone: Thanks, Let me share the screen. So, yeah. Hi everyone. I want to walk you through PIP43. As you can see, it's a coordinated effort between Polygon POS team and Informal Systems. for the ones who don't know this is the company behind comet BFT and one of the biggest contributors in the cosmos ecosystem and here we are proposing basically an important upgrade to him which is as the consensus layer for polygon POS chain right now him relies on a tendermint fork which is called peppermint but yeah it's outdated tough to maintain and limits the addition of new features on top of

Marcello Ardizzone: or stack. So the proposal here it's about replacing tendermint with comet BFT basically which is a modern solution that simplifies things and aims to boost the performance of the network overall. yeah we have several reasons to perform the upgrade being the backbone for the network.  It handles consensus, governance, validators management, syncing with Bor and checkpointing to Ethereum. and Peppermint honestly has worked well so far, right? But it's stuck on a version which is pretty old BT.32 years behind the current standards. So, keeping it updated takes development effort and it still lacks the latest features that could improve our network's performance and UX.

00:15:00

Marcello Ardizzone: so switching to comet BFT will basically allow us to move forward right it will bring modern consensus mechanics and introduces the ABCI++ which is a new interface for blockchain and application communication which is basically updating the stack providing faster block time and simpler maintenance plus new feature to leverage in the future.  Because at the moment this migration is going to be one to one with respect to the current Heimdall features. But what we can expect to achieve will be faster and if you will smarter blocks because with features such as prepare proposal or process proposal or the vote extension themselves. comet BFT optimizes the transactions handling and validates the blocks earlier.

Marcello Ardizzone: it reduce wasted time on invite blocks and streamlines the transaction processing and also the upgrade theoretically could shrink the block times by three four times potentially going from 6 seconds blocks time as it is currently for Heimdall to 2 seconds on average but yeah everything here needs to be tested it's an ongoing effort for quite some months now and also this will be our vehicles to replace peppermint side transactions  with the boot extension mechanism. So this is basically a way comet BFT provides to handle external data and we will use that to modify what we call special transaction which are like state syncs, etop paradas operation or checkpoints. and this replace basically the need of for our custom site transaction logic which resides in peppermint at the moment.

Marcello Ardizzone: this will make the system cleaner, easier to maintain and ready to expand with additional features. and of course all of this will help polygon network by addressing tech debt first of all and reducing the maintenance costs and also will bring eventually more scalability and it's going to be future proofing right so again it's going to be one-to-one migration at the moment but there's a ton of potential to explore down the road for example we can finally get rid in the future of our fork we might leverage new features

Marcello Ardizzone: from comet BFT like PBTS if we want to expand the number of validators or enable IBC in the future and with this DOG from comet BFT we could achieve 70% reduction in bandwidth usage which would be a good plus for node operators and with Cosmos v2 in the next years this promises also a faster storage and 5x faster queries which could mean higher TPS and parallel execution through state transaction  functions. the catch here is of course backwards compatibility. Such upgrade requires migrating ML to a completely brand new chain because of the change in the block structure and also the dependencies of the new version of Cosmos SDK which is compatible with comet BFT. For that we will be releasing a new PIP soon.

Marcello Ardizzone: 

Marcello Ardizzone: But yeah the migration basically will involve a coordinated upgrade across the whole ecosystem and currently we are in the development phase but yeah we are prioritizing backward data compatibility testing security audits and a smooth transition to minimize the risk for the node operators. yeah I guess that wraps it up. thanks and…

Harry Rook: Awesome. …

Marcello Ardizzone: in case you have any question or feedback feel free to ask or post on the link here in the forum. Thank you.

Harry Rook: I've got a couple of questions on my side. So, in terms of timelines, is Q1 next year likely or are we looking maybe into Q1, Wondering if we're spec ready at this point or we're still pre-testing phase.

Marcello Ardizzone: Yeah, good point.

Marcello Ardizzone: 

Marcello Ardizzone: Basically at the moment we are almost done with the development on Heimdall V2 and the new Cosmos SDK application which lets us spin up a local DevNet. now we are adopting the testing tooling that we have around Maddox CLI for example but also the changes that we need to do on the Bor side in the communication via gRPC or REST API with Heimdall. and we plan to be able to test on a real DevNet by end of this year which means in Q1 we would like to have a code freeze to start with the auditing process from a security perspective and then finalize everything by theoretically Q1 next year …

00:20:00

Harry Rook: Yeah,…

Marcello Ardizzone: which means doing this migration on Amoy first and then planning on

Harry Rook: I'm just thinking out loud here, but we took a lot of the details about the migration process out of the PIP. So, yeah, we probably need to publish something at some point because it's a pretty complicated procedure, isn't it?  So yeah, we can do that at some point before then.

Marcello Ardizzone: 

Marcello Ardizzone: Yeah, definitely. It's going to evolve an hard fork on Bor, an hard fork on Heimdall, export of the genesis, some tooling we will be publishing, to verify the check some of the genesis data, then yeah, some script to import that into the new chain. and again, it's a coordinated upgrade. It's not like a Ethereum based hard fork,…

Harry Rook: Cool.

Marcello Ardizzone: but it requires all the node operators to perform that at a specific block height. So, it's a lot of effort, but yeah, we will get our results by doing that.

Harry Rook: Thanks for that, Marcelo. I don't know if anyone else has any questions. feel free to jump in. I know what I've just thought of actually, Marcelo though, is we're going to need to upgrade the state receiver as well, right? With the new chain ID.

Marcello Ardizzone: Yes, definitely.

Harry Rook: Very complex procedure. So, All right. Yeah, we'll keep everyone in the loop on all the tooling we make available and also a high level overview of the process as well.  So, keep your eye out for that on the forum. if there's no other thoughts, comments on that one, then we can move into the Bor side of things. All right, cool. So, you can see, there's a lot in the pipeline on the Heimdall side, and there's also a fair bit within Bor as well. So, one of the main things being PIP 51.  So this is basically just EIP7702 which we're looking to implement on POS at some point soon.

Harry Rook: it'll probably come within the next hard fork which is likely Q1 next year but yeah we have a PIP I mean this has been discussed almost ad nauseam on ACD calls so we'll not go too much into the details of the actual proposal but yeah Manav I don't know if you have any thoughts or anything you want to discuss around 7702 in terms of cherrypicking PRs or anything of that nature.

Manav Darji: Yeah, yeah, I'd like to mention a couple of things. So yeah, I think G already has the core implementation locked in. and it's a bit of a low effort thing to cherrypick them inside Bor. but I think during devcon gone there was one not issue one consideration which requires a bit of changes in the transaction pool because after EIP 7702 there will be transactions due to this 7702 type transactions it can invalidate other transactions inside transaction pool.

Manav Darji: 

Manav Darji: So there are still some changes which will go inside before it gets fully finalized from a code perspective. So we are just kind of waiting on those things because I think it's definitely logged in for pectra. So yeah it should be available upstream soon. yeah so at this moment we'll definitely have it in the next hard fork but yeah we are not rushing in on by doing changes our side from our end to make sure that there is no issues or bugs that we encounter later.

00:25:00

Manav Darji: Yeah.

Harry Rook: Nice. Yeah,…

Harry Rook: it sounds good. And I remember with 3074 we were thinking of waiting until the implementation had been scheduled in a fork and then implemented on a couple of test nets. So maybe we need to have a discussion at some point as to if we kind of take the same signal with this or if we do it a slightly different way. Awesome.

Manav Darji: Yeah, I think we'll definitely wait for it to go on a couple of test nets before even putting it on Amoy.

Harry Rook: All thanks, I'm personally super excited about 7702. So yeah, I'm looking forward to it being included. So, yeah, I don't know if anyone has any questions, thoughts, comments on that one. All right, so yeah, moving on then to the next item, which is, Erigon version 3. there's a bunch of new features in this one. I think we have Mark from the Erigon team, who's going to give us a run through and…

Mark Holt: 

Harry Rook: we can have any discussions around it if needs be as so yeah, Mark, over to you.

Mark Holt: Hi, thank you very much.

Mark Holt: I'm just sharing my screen hopefully. I'm just sharing the wrong bit of my screen. Bear with me two sec. yeah, so this presentation is really just a heads up that Erigon has had a long-standing piece of development which probably been going on for two years now, which is an upgrade from Erigon 2 to Erigon 3. which essentially we're in the final stages of alpha.

Mark Holt: We expect it to go into beta probably by mid December. Our top level goal is to get it live for Petra because a lot of the Petra changes including 7702 incidentally which we've got integrated and are involved in the DevNets for aren't unless we have to going to go into Erigon 2.  So we're basically looking at sun setting the Erigon 2 stream coming Q1 next year. so I thought I'd take this opportunity to just give you a heads up to what is the main change of Erigon 3 over Erigon 2. and that essentially we've got a new distribution model for chain data.

Mark Holt: and if you look at the top picture, it kind of tries to explain it in schematic form. so essentially what we've been doing is we started with Erigon 2 where we use Bit Torrent to distribute flat file data around the network for transactions. what we've done is added the same transition process for state now. So basically when Erigon starts and syncs from scratch it will basically download all of its data across the torrent network which massively improves the sync time.

Mark Holt: the other change associated with that is Erigon 2 has typically been very dependent on NVME drives to deliver its performance and as the base those drives have grown ever bigger. so basically what we've done is now split the storage.  So we have a pretty small live database which is to give you an example for bore mainnet runs at about 50 or 60 GB and the rest of the data is stored in read only file storage.

Mark Holt: What this means in practice is we've been able to tune it so that you can use essentially a standard SSD or potentially a hard disk drive in order to store that data without seeing any degradation in performance. So that's the headline change that's coming and part of the reason for mentioning it is it kind of changes the deployment model for Erigon. essentially. So that top picture is the picture and then I thought I'd include in this just to give you what this means in practice. so the first table is essentially the database sizes we're seeing now.

00:30:00

Mark Holt: So with the latest, these were done about a month ago, so they're pretty indicative of where we are. For the archive node, it takes about 4.2 terabytes. we've also actively support two other modes of operation one is a full node which takes two terabytes and essentially a full node is state plus block history and then we also have this additional minimal mode now which is part of a development we've done as part of the Ethereum 44's proposal of coming to this validator node that has the minimum it needs to operate and that's just essentially meant

Mark Holt: maintaining the state and about 100,000 blocks of history which is enough to manage reorgs. So yeah we're going to 4 terabytes for an archive node and just under a terabyte for a minimal node if you just want to run which we think will make Erigon a lot more flexible to operate. and another thing just to illustrate the change in model one of the big noticeable things from a development perspective and I think it will be the same for anyone deploying is the sync times are orders of magnitude faster now and they're basically dependent on the amount of network availability you've got.

Mark Holt: So for a polygon archive node now to start from sync on a 100 megabyte per second network which we think is a fairly standard kind of consumer network these days. Sync from scratch takes under two days now for Erigon and that's effectively to get from an empty disc to the tip of the chain. and as you can see for the lower storage storage configurations it's essentially less. And in practice what we found which was sort of surprising from our perspective if you start this in a data center that's got a significant amount of bandwidth the time to sync an archive node comes down to actually a ridiculously small amount of time.

Mark Holt: So I'm including on the bottom of this slide what we're seeing running on. We run in OVH and it basically starts from scratch on a machine. It takes 2 hours 40 minutes to get to a running archive mode. which as I say makes a massive difference for us because we're typically running and configuring a lot of these things. So yeah, that's basically just heads up of what's coming down the line because I think by mid December we'll be getting to a point where we're saying we're trying to encourage people to run it to find out how it works for them. And that's it for me.

Harry Rook: Yeah, thank you very much, Mark. …

Mark Holt: 

Harry Rook: yeah, I don't know if anyone has any questions. I mean, those are some pretty staggering numbers. So, yeah, feel free to jump in, guys. I don't know, Mark, do you have any details that we can throw in the forum, of that?

Mark Holt: Yeah, I can…

Mark Holt: What I'll do is I'll take this slide I've produced and summarize it in a forum post basically…

Harry Rook: Cool.

Mark Holt: because as I say I don't think there's much to do out of it. It's just we're now at a stage where We can see us actually launching this out into the wild.

Harry Rook: Thank you very much for presenting and if anyone's got any questions, feel free to jump moving on then, next items. So, yeah, we're moving away from clients sent into meta governance changes. from the low end detail to the high and slightly abstract. So, there's a recent PIP, PIP 50 that we discussed a little bit before in a previous call and this PIP aims to add stake token holder signaling to system smart contract upgrades.

00:35:00

Harry Rook: So these are upgrades that effectively change the level one layer one contracts for POS and potentially later other chains as well. So yeah, I believe we have Mateusz who's my esteemed colleague on a governance team. So yeah, Matt, I don't know if you want to jump in and walk us through it.

Mateusz Rzeszowski: Thank you Harry and thank you for bringing up a little bit of that context. yes, as Harry said and let me perhaps also share my screen in the meantime, PIP 50 is now up there, proposed and it introduces an initial decision-making framework for POS stakers to participate in signaling on PIPs that relate to those systems smart contracts that live on L1.

Mateusz Rzeszowski: in the current implementation that relates only to the contracts controlled by the protocol council which means the POL token contracts and we hope to soon propose expanding this set of controllable contracts. but that is going to be coming up in another proposal. Now the framework proposes three stages in terms of community participation in the entire process. Firstly following the regular PIP flow which as we know includes the protocol governance calls and majority consensus from the protocol council is reached. As a second step and for those who may not know what the protocol council is I would refer to I believe PIP 29 which goes into detail on that front.

Mateusz Rzeszowski: once that consensus is reached the community of POS stakers, meaning any user that is currently staked in POS can use their voting power which maps one to one to their staked POL tokens to either vote on proposals directly meaning an approval or veto vote or to delegate their voting power to any  address. Finally, the protocol council has to confirm the proposal for onchain execution or decide not to execute it if a veto has been reached. Now this is an optimistic governance system. So, it relies heavily on that veto portion and this is enabled by the governance hub. and I'll show that in a second. I just want to quickly cover some of the details that are laid out in PIP 50.

Mateusz Rzeszowski: So as I've already mentioned this is a delegate based system where individuals or entities can act as technical delegates to whom stake token holders can delegate their voting power. delegation can be done partially and also in a split manner meaning any POS staker can delegate to any number of preferred delegates. and so there is not a limitation that you can only choose one. You can distribute your voting power in a free manner. The actual spec contains some components that I will not be getting too deeply into here. But it's very important to understand that this is an off-chain signaling system. Meaning there are no real onchain dependencies.

Mateusz Rzeszowski: the execution over those key smart contracts still relies on the protocol council to actually pull the trigger after having their vote be informed by the sentiment that is in the community. So it serves a similar purpose as the PIP 8 I believe which has laid out some of the off-chain consensus gathering frameworks in the past.  Now this is an expansion on the specific component of those upgradable system smart contracts. One other important component that is being used here is that of an adaptive quorum meaning that there is no set quorum.

Mateusz Rzeszowski: Instead, it is a moving target. Or rather to put it in kind of simpler terms, the more people vote, the more of the votable supply participates on a particular vote, the easier it is for a proposal to be vetoed. and so it is a system in which values efficiency in decision-making.  However, it also ensures that any number of community participants can veto a change if there's overwhelming apathy in the voter set. so it eliminates the challenge that many other organizations face when no one is participating.

00:40:00

Mateusz Rzeszowski: and it also does so because it relies heavily on that optimistic model where the protocol council are the ones who first propose a change after consulting it with the technical community and then execute that change ultimately as well based on the vote the signal coming from that community. backward compatibility. everything has been built to adopt the same processes that we've been using with PIPs in the past. so if you go to the governance hub which I'll show in just a second, you will see pretty much everything is the same. and all of the PIP frameworks and flows are being followed on that front as well.

Mateusz Rzeszowski: security considerations. Importantly, any onchain components have been reduced to a minimum and they are being used solely for those off-chain actions, meaning snapshot voting most importantly. Now, if an issue were to be identified, all of those off-chain components can be updated or may point to a new contract fixing the issue. and the goal with that approach is to slowly adapt towards future versions.  based on past learnings which once again this proposal is the first step into more direct stake token holder participation and you can expect future proposals we'll be expanding on that as well. Now to show what this looks like in practice, this is the Polygon governance hub. So if you go to governance.polygon.technology,

Mateusz Rzeszowski: you will see on this app right here which essentially allows any staked POL token holder to either delegate their voting power or to participate directly. and so you will see the list of all the PIPs as well as some nice to have the featured posts from Twitter or the upcoming events where you can see calls like the protocol governance call that we are on right now happening and a featured article section.

Mateusz Rzeszowski: There is the learn portion of the web page that also shows some of the articles on the different pillars of polygon governance. the proposals tab which once again if there is a vote happening on a contract proposal it will be here and so any community and member would be able to go in here and participate in signaling his preference.  And then lastly, we have the community members tab, which is the delegate portion of it all as well as the protocol council portion of it all. So any staked POL token holder once again can go in here and delegate their voting power to any of those fine folks. and that's I think pretty much it from my end. are there any questions or comments on that?

Harry Rook: Yeah, I just wanted to circle back on one thing you kind of started off with. So to kind of put it simply in terms of what the framework covers right now, it's just the POL contracts, right? and then there's a proposal in the works. and there's been an ongoing discussion around essentially changing the upgradability rights from the current multisig that controls it to the protocol council. So then it will encompass the POS bridge as well. Right.

Harry Rook: So although it doesn't really affect too many folks on this call right now obviously POL contracts super important pretty soon it'll be controlling the POS bridge or…

Mateusz Rzeszowski: 

Harry Rook: the staking contracts. So just wanted to like flag that for everyone kind of just highlight on that. so it was definitely worth checking out the proposal there.

Mateusz Rzeszowski: That is correct.

Mateusz Rzeszowski: And then even more so in the further future shared components of the Agg-layer are also expected to be covered by this framework. so it would go beyond the components of public polygon networks and…

Harry Rook: Awesome. …

Mateusz Rzeszowski: into those shared Agg-layer components. but thank you Harry for also clarifying that.

Harry Rook: and then yeah, just one last point. So the adaptive quorum, you mentioned it's like a moving target and that idea is broadly based on polka-dot substrate. so people can check that out if they want a little bit more context there. And then we've also tweaked the params a little bit, haven't we? So the multiplier, we've effectively doubled it, right? and that's a specific consideration while we bootstrap the delegate set.

00:45:00

Mateusz Rzeszowski: 

Harry Rook: And yeah, I don't know if you want to kind of add a little bit more on that front as well.

Mateusz Rzeszowski: Yeah, in terms of the specific parameters around the adaptive quorum,…

Mateusz Rzeszowski: that will need to still be assessed and we are doing that assessment right now as we are gathering delegations from users. it will be highly dependent on how many tokens actually participate in voting and so we want to make sure that set it so right now you will see that the proposal mentions that this multiplier will be set and we hope to do that very soon as well.


Harry Rook: Yeah, thank you very much, Matt. I don't know if anyone has any questions, thoughts, feedback, comments, feel free to jump in. All right, And yeah, thanks, Ja the Erigon team who we partnered with to develop it, did a great job.  So, hat tip to that as Okay, that wraps up the agenda. if we want to circle I don't know if you want to circle back on the whole SP1 comment you made, but yeah, I mean, feel free to kind of discuss anything you want. then we can also discuss it offline or on the forum.

Harry Rook: 

Harry Rook: But yeah, I mean, feel free to jump in if you want to bring that up.

Anton Gaev: The answer from Manav was okay so yeah we'll see how they will decentralize their system but yeah I agree with you that SP1 is great it's just again the centralization issues that we circle back to so yeah we'll wait for further announcement from succinct

Harry Rook: Yeah, because I think you mentioned you're worried about cost I think right because it's the fee

Anton Gaev: not so much costs in particular but just the fact that we're relying on one entity which gets basically monopolistic rights on the proof costs and just additional risks …

Harry Rook: 

Anton Gaev: which may not be so attractive for us.

Harry Rook: Mhm. Yeah.

Harry Rook: Yeah. I mean that makes sense. it's always good to be concerned about those things and yeah I think that's the format mechanism is super important in that context. so yeah I mean thanks for bringing it up. and…

Harry Rook: yeah happy to discuss it further as well on the forum.

Anton Gaev: Yeah. Yeah.

Anton Gaev: And Risk Zero as I specified this boundless marketplace which wasn't test net in November basically in October so it ended recently and it's already decentralized and it's risk zero versus SP1.

Harry Rook: 

Anton Gaev: I guess there are a lot of discussions within the community which is better. Everyone has fans of each of the decisions. but maybe worth taking a look from the perspective is that it is already decentralized and maybe just from this point it's worth considering.

Harry Rook: Yeah.

Harry Rook: I mean, thanks for raising it. and we can discuss it further as well on the forum if needs be. does anyone else have anything they want to discuss? If not, we can wrap things up. All right, cool. So, next call is December the 12th.

Harry Rook: was thinking of pushing it a week to align on the four-week cadence, but it's kind of a bit close to Christmas, so I don't know, might be a little bit hard to get folks in one place. So yeah, 12th of December in 3 weeks ish will be the next call. Very likely we have some pretty significant things to discuss, some pretty spicy proposals potentially in the works that could be discussed on that one. So yeah, as always guys, appreciate you all joining the call.  I know you're in different time zones. And yeah, I'll see you on the next one. And yeah, thanks for joining.

Meeting ended after 00:50:32 👋

