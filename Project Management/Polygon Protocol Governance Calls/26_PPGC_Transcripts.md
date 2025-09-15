# Polygon Protocol Governance Call: Transcript

### Attendees

Alex Watts, Ayush Bhadauria, Ben Rodriguez, Carlos Juarez, Christopher Von Hessert, Dan Moore, Dimitri Nikolaros, Fireflies.ai Notetaker Bruno, Fireflies.ai Notetaker Nikhil, Harry Rook, Harry Rook's Presentation, Jerome de Tychey, Jerry Chen, Joonkyo Kim, Jumanzi's Notetaker, Krzysztof Urbański, Léo Vincent, Liminal Notetaker, Mark Holt, Michael Mugno, Michel Muniz, Nikhil Chaturvedi, Parvez Shaikh, Poorthi Hesaru, Quentin de Beauchesne, Sajal Agrawal, stream, Vasanti Rode, Victor Castell, Vincent Taglia, WebDev StakeWorks

#### _This editable transcript was computer generated and might contain errors. People can also change the text after it was created._

Harry Rook: Okay, Welcome everyone to PPGC Number 26, In terms of the agenda for today, it's two main buckets, really? So on the POS Bor side, we've got some updates around version 1.5.0, so that's PBSS. And also, DNS discovery as well which is combined 2 new really cool features that will be discussed today. And as well as that we have some updates on PIP 47. So, yeah, this was posted, I think maybe a month or so ago. Nearly two months, maybe And specifies an upgrade to the Protocol Council. So

Harry Rook: Protocol Council has been a multisig that controls some of the POL contracts. Effectively, we have a new specification in the works. That adds multi-threshold kind of capabilities and we'll be discussing some updates on that as well afterwards. So, yeah, I think we have Jerry on the call, who is one of the POS core devs, who kindly put together PIP 48, which I'll show in the chat. And that PIP basically specifies pbss as it'll exist on POS. And yeah, Jerry, I don't know if you want to. Dive in and share some details on that one.

Jerry Chen: Yeah, Thanks yeah so PBSS is a new sort of scheme that we have in order basically, from if you're ethereum. so it's a different way of storing trying nodes in the database essentially. So in the past imagine that you have, say, a balance of the accounts in the database.

Jerry Chen: If the balance of this account changes, according to your transaction, the try node that stores, this balance will become a completely different new entry in a database essentially. That's a storage sceme in the past. We use as a hash based storage scheme short for HPSS senses and in PBSS which is path based storage scheme. It doesn't have these additional entry because the same balance they store and a store under the same account path. Therefore the path that leads to the database entry will be exactly the same impact-based storage scheme. So these storage schemes

Jerry Chen: Kind of bring us a lot of benefits. I can just list a few. And there's basically the core benefit is that those storage requirements were running a full node has reduced a lot. So in our experiments, if we're only looking at the storage requirement for the state itself, it has reduced from that 2-5 terabytes to 900 gigabytes when we switch from HPSS to PBSS. So that is around 74% storage reduction.

Jerry Chen: besides that the execution time of PBSS, also dramatically reduced by roughly 60% on almost all blocks in average. So before it was In our experiment node Executing one block to 0.8 seconds. Now, it has dropped to 0.3 seconds. Also the memory has decreased By 50% using PBSS. and also one really good benefit is that, in the past we have to frequently turn a node a database for prune and

Jerry Chen: In these pbss solution, the pruning is not required because whenever a new data is written to the database, it simply overrides, the old data for the same try node. So these basically means that you can have the node keep running for quite long until, there's basically a node update. Etc. yeah, so regular pruning is not required. Yeah. So the only kind of disadvantage of previous PBSS right now is that we don't support archive nodes at the moments. We're looking into the solution of using PBSS In archive node but yeah, it's still in development. So

00:05:00

Jerry Chen: Yeah, this kind of overview of it, so it's a very good storage scheme. I think everybody should switch to It reduces a lot of storage requirements, as well as memory requirement increase, the block execution speak, And right now, these could be enabled by essentially using this one flag state scheme and you simply said it to path, either the CLI or the configuration. yeah. So

Jerry Chen: In the next release one. So in current release, the default storage scheme is still HPSS based. But in the next release, we're planning to switch to the PBSS storage scheme by default. So that will be a backward incompatible change. yeah. So that would be a major release coming to the mix.

Jerry Chen: yeah that' pretty much does summary of Pbss. Let me know if you have any questions or any ideas you like to discuss.

Harry Rook: Thanks I don't know if anyone has any questions but are these went out to Amoy today right? And I was just confirm that to someone knocking at my door.

Jerry Chen: Yeah,

Michael Mugno: Hey Jerry, this is Michael with Vault Staking. I have a quick question. So getting started with Pbss. What's the best way to get started? Is there, starting it from scratch and have it sync from Block Zero. Is there a snapshot out there? How do we get a node started?

Jerry Chen: Yeah yeah good question. So you can start from syncing from Genesis block but that can take a lot of time, a couple of days or weeks. So we do also have a snapshot available for Pbss so you can download the snapshot and then use it in a new database. So basically in order to switch from HPSS to PBSS. You need a new data directory. These two storage schemes are not compatible with each other. Yeah but you can simply download the snapshot. That would be much faster to start with.

Michael Mugno: Is that located either in the docs or somewhere on Polygon's website?

Jerry Chen: Yeah, you can find it in the doc.

Michael Mugno: I'll give that a check and let you know if I can't find it. Do you think it's easier to switch over an existing node from hash based to path based or just start fresh with a new node?

Jerry Chen: so yeah, so if your node is just rpc node that doesn't do anything or just syncing the block you can use a new machine, that's totally fine. But if you have to say, some keys sitting locally on your machine. Then I don't know. it's up to you to decide. You want to copy those keys over to the new machine, or just use the old machine. yeah, but in short Only thing you could change the data directory and you can keep the wallets basically the key directory. If that makes sense.

Michael Mugno: It does. Yeah, that's a huge help. It leads to the next question. No issues with running this as a validator node. I know you can't do it as an archive, but no issue being a validator and running PBSS

Jerry Chen: Correct. Yep.

Jerry Chen: We recommend switching to pbss for central nodes and also validators.

Michael Mugno: and this works as a full node, I mean it sounds like all the data is there. It's not like a block pruned node where you lost some of the data.

00:10:00

Jerry Chen: Yeah, you don't lose the data. The node is still able to roll back to previous blocks. We keep the history in different formats or different database tables. But yeah, node is able to work as well as HPSS.

Michael Mugno: That's it for me. Thanks so much.

Jerry Chen: Thank you.

Harry Rook: Nice Jerry. I think there was a question in the chat so estimates on how large An archived node with Pbss. Once archives are supported, I don't know if the same rule applies, Of 75%,…

Jerry Chen: So That's a good question.

Harry Rook: reduction of

Jerry Chen: Right now we don't have a good estimation but if I have to guess the advantage or the storage reduction might not be as good as the full node because we still need to store all the intermediates states for every single historical block. So for that reason the pure amount of data we store should still be roughly the same. But yeah. If we have any new progress or findings, we can definitely share in the future.

Harry Rook: Amazing Yeah, any other questions guys? If not we can move on.

Harry Rook: Alright, thanks again, if anyone has any questions, I've linked all of the release notes, the PIP and I kind of all the other relevant details in the chat, so you can check them out in the forum if you've got any more thoughts All right, so moving on then to DNS discovery. So this is based on EIP 1459. I think we've got Dan moore on the call who's been spearheading it from within the Dev team. So yeah. Dan I don't if you want to do that, could overview of DNS. I know some of the validators have posted the improved peering on Twitter as well already. So yeah, just do a quick over

Dan Moore: That's awesome. Thanks Yeah, we can dive into it and nice work Jerry. Yeah, let's talk about peering, I think everyone on the call is probably intimately familiar with that situation where you've done all the configuration you have your server ready, maybe you have some snapshot data and you hit run and you can't start syncing with the chain because you can't find any peers. It's something that's plagued a lot of blockchain networks, and our team sought out, not just to mitigate that, but to really try to eliminate that problem.

Dan Moore: So my name is Dan. I'm one of the staff engineers on the Devtools team. Our team's mission is to really make life simpler for end users for Blockchain developers and node operators. It's really cool to be here. Harry Thanks for setting us up because we get to actually communicate some of our work to the broader blockchain community. So thanks for putting this on. Yeah, let's talk about some of the upgrades. We've been making to our peering features. It centers around EIP 1459, so node discovery via DNS, in very simple terms that just means more efficient and it's more efficient And secure way to bootstrap your nodes to discover peers using DNS, you don't have to rely on the initial distributed hash table nodes. Which As many of us know, can be unreliable.

Dan Moore: What are some of the key benefits to node operators to validators? Probably the biggest one is near instant connectivity to hundreds of peers out of the box. So, just by adding this ENR URL to your Dev, P2P, DNS discovery config, you have to gain access to this set of healthy nodes. This dramatically can reduce the node sync times in the bootstrap by  dramatically Improving that bootstrapping experience for new node operators to improve network stability. So the way we've designed this, we have a series of high bandwidth relay nodes internally. These are designed to peer with as many peers as possible. We've carefully configured them to have as many connections as possible. We see over 650+ peers on each. What we do is, we periodically query those internal nodes, collate them, de-dupe them. And we load these healthy nodes as DNS records to our ENR tree for the great good of the Blockchain community. That being in place, the more users that kind of adopt this node lists. We can essentially create a very interwoven nicely, connected classical blockchain system, a global network of computers really nicely talking and communicating with each other. This reduces the instances of clusters of nodes, being siloed and separated.

00:15:00

Dan Moore: And this can also reduce disagreements about canonical chains. So, we're seeing some evidence of reduced, reorg depth, and greater network stability, after some of these changes. Enhanced security of the way we have to sign this node list with a private key and we actually include the public key in the ENR tree URL. So a client can directly verify the integrity of these node lists on download. And then, finally, last but not least, for end users, shorter times for broadcasting transactions, which is crucial for consensus. Validators benefit from faster Block propagation: a more interconnected system of computers means your blocks that you validate are getting to the majority of the network faster. So that's what we're working towards. How did we kind of pull this off at a more granular level?

Dan Moore: We implemented this pretty closely to the EIP specification. So we designed a Merkel tree structure for our node list and this allows for incremental synchronization of peers. So Bor doesn't have to download the entire node list every time, they can sync new peers as it's running. Add new peers or remove stale peers as it's still operational. Again we sign that node list with the private key, the public keys included in the ENR tree URL. This enables easy verification of the root signature of our ENR tree and ensures authenticity of our nodes. We have a highly scalable DNS provider behind the scenes, ensuring high availability and uptime and also caching of some of these DNS records.

Dan Moore: And then, in terms of this internal fleet, this is the series of high bandwidth relay nodes that we have. We talked about how we designed these to maximize our number of peers to collect that list and share it with the community. There's some key settings that I think are important to note we talked about of course, I highly encourage you all to integrate ENR tree into the peer to peer DNS Discovery field of your Bor Config

Dan Moore: But there's other settings, you'll probably want to look at as well. Max Peers is a critical setting because I believe the default is 200, and in some of our relay nodes we have this set to 2000. there's a dial ratio to keep in mind where you can only dial one third of your max peers. So it's important to set that sufficiently high just note that of course you'll have some more bandwidth so kind of set that limit to your goals and then of course kind of opening your firewall, UDP, TCP connections. Just to ensure you're connecting with as many peers as possible.

Dan Moore: One cool, additional feature with this implementation is it does support linking to other node lists. So, in the future, there is the possibility to potentially work with partners who maintain their own node list. And essentially create a federated network of trusted node lists is what the EIP outlines and that's possible with the way we implemented this. Just for clarity our DNS records update hourly. Our Bor nodes will auto detect these new peers. There's no need. Once you have, the initial configuration ENR tree, add it to your config and you do that one time to start. There's no need to stop and start to find new peers. It's all automatic. and then of course, we have monitoring and alerts in place on our side, if we detect any DNS issues or relay node issues, we can quickly address those. For integration, It's super simple.

Dan Moore: Just adjust your Bor Config, we have a post on the community forum with the exact configurations you want to change. We can circulate that and I think, Yeah, probably, a lot of you have already got communication from our team about the config changes, but Jerry and Team have also integrated this natively in Bor v1.5.0, beta 4 and beyond that. you can pass the DNS Discovery flag directly to the Bor command in the CLI. So that's really nice work from his team.

Dan Moore: Just to wrap up some shoutouts. John Hilliard concept of this idea. Props to Sandeep and the Protocol team. Thanks for being open-minded and integrating these changes events and Daniel Jones from Devops for setting up and maintaining our DNS provider and helping us troubleshoot. parvez. And Sajal, If you're on the call for all the validators support and outreach to the broader community, it's been a huge help and awesome team effort. To close, I just want to encourage all the validators on the call, please integrate these changes and take advantage of these improvements. It's our belief that we're not just improving peering but we're really trying to set a new standard for network, stability and performance. And by leveraging these authenticated and these updatable node lists, we're building an even faster and more stable network. So thank you for your time.

00:20:00

Harry Rook: Thanks I feel like in these last two releases, you guys at this absolutely crushed it. Jerry down all the Devtools guys. So yeah hat tips you to you all there. Some networks looking very very healthy. So Thank you very much Yeah, I don't know if anyone has any questions or anything. I don't know if as well. Any of the validators have already integrated this and seen any good findings. I know Dimitri, you posted a tweet about it and it looks like, yeah, here comes kind of got a significant…

Dimitri Nikolaros: yeah, we just tested on one century node just to see and…

Harry Rook: but yeah.

Dimitri Nikolaros: It took five minutes. You said that the creation and it's skyrocketed really fast so it's really cool. It's almost a 2000 peers, it's nuts

Harry Rook: Good to hear. Any other questions guys? If not we can round off this one and move on.

Harry Rook: Okay Yeah thanks again all So moving on there to PIP 47. I don't know if we have Chris on the call. Actually let me just check here we do. Yeah, so awesome.

Christopher Von Hessert: Yeah.

Harry Rook: Yeah so PIP 47's effectively as I said before, an upgrade to the Protocol Council kind of mechanism And yeah I think Chris you had some updates on there, you wanted to share. Just yeah.

Christopher Von Hessert: Yeah, so that's a very quick summary. This PIP was all about, moving the current setup that we have with Gnosis safe into a new setup being built by the Aragon team. Completely communicated with the Aragon Governance Hub. What is being built, to bring that, basically centralized all in one experience to the Governance Council. From a security perspective, we love that idea. No, make things less complex, and a more easier user interface. However, there are a lot of security things that had to be reviewed over here. No, it's not only just smart contracts, it's also the web interface and all the integrations between the web interface, the smart contracts, and giving the security, or basically the criticality of this system. No, let's take into account this system in the future, maybe not right now, but in the future will be responsible for billions of dollars.

Christopher Von Hessert: Total value locked, not only polygon, but also the whole polygon ecosystem and all the projects running out there. So we're talking about the most critical system that the polygon ecosystem has now. And therefore we're not going to take security lightly. And we've literally basically said, let's please put this on hold for a moment because there's areas that I don't feel comfortable that we have really digged into now. And one of them is obviously smart contracts that has been built. It works very well, it has been audited, but we still feel that it could have even more testing and more reviews and maybe a face approach in order to give the whole responsibility of it to a new set of contracts. Apart from that, we also have all the user interfaces and also the Web assets and where those Web assets are going to be hosted who's gonna be managing them. what are the operational aspects around them? And those are areas that I still feel, we

Christopher Von Hessert: We probably need to. It's not only about reviews but it's really about building operational processes monitoring to ensure that those systems are secure and there's little to no risk of somebody manipulating or anything like that. So with that said, again, we put this a little bit on Hold until we rebuild the new plan on how to get there. And so it's not happening right now. That doesn't mean it's not going to happen.

00:25:00

Christopher Von Hessert: Yeah it was really the mentioning that we're delaying this a little bit more. So for the time being what is going to happen, we're going to continue using Gnosis safe to do all the transactions. Gnosis safe, it's used by a ton of projects, it's been battle tested, it not only in the smart contracts, but the user interface where people sign and submit transactions, it's just top of the line, basically the best you can get over there and we want to get to that level. We want to make sure that whatever we put there it is at the same level of a Gnosis safe and the infrastructure behind it, so that's still gonna remain for. Now, we're gonna remain with the two multisigs, the two Gnosis safes, and those are gonna be connected to the new governance hub that Aragon and the governance team. You and polygon are building together now and so there is going there. We're still gonna be using the governance help.

Christopher Von Hessert: They're going, we're still gonna be using the governance hub but obviously at the end they will still have to be Signatures being done on the Gnosis safe and on the Gnosis UI. I don't know Harry or Matteo if you want to add anything to that.

Harry Rook: I mean not particularly I think you did a fantastic job of outline in it. just to go back as well. So to what you mentioned, the amount of TVL of this is going to secure just requires the utmost diligence and I'm personally appreciative that we've got you guys here to kind of use your skills in that field because you guys are the best in the industry. So yeah, really appreciate that for one. And yes, the second thing I wanted to add is that, when we do have more details as to this phased approach, that you mentioned as well, we'll obviously release those on the forum. PIP 47 will likely get an update at some point as well. So, yeah, I just wanted to add that.

Christopher Von Hessert: Yeah, and just to make it clear, this is our recommendation. No, again in the end we're all here to make sure that we're taking the best decisions for the protocol. So if anybody has concerns or anybody wants to understand more about what does this really look like and what would a perfect set up look like, I'm happy to take those questions off line and happy to discuss outside of this conversation outside of this call. Yeah, we're just trying to make sure that we're doing the best for the protocol itself for the community. And specifically, in this case, if we need to take more time, we're gonna take more time, we have a great solution right now. it may not be the best from a user experience perspective, but it's the most secure and most common solution right now. And there is no reason to move right now and make any hurry.

Harry Rook: Thanks Christopher. Yeah. I don't know if anyone has any questions or if anyone wants to add anything there. Yeah, if not we can run that one off. So yeah, let's open the floor. If anyone wants to add anything there.

Harry Rook: And I'm just like the forum post. If anyone has any more thoughts, they want to then feel free to head over to there. That wraps up the agenda item, so I don't know if anyone has anything else. They want to discuss? Anyhow points. If not, we can probably wrap it up now.

Harry Rook: All thanks again to everyone that joint. Christopher Down and Jerry my hats it to you guys latest ball releases. Crushing teams crushing it. So yeah, appreciate all you guys for joining. I know you're in different time zones. Next call is currently scheduled for the 14th but the cadence of these causes kind of staggered a little bit, so we may realign it to four, kind of every four weeks. Yeah, thanks again to everyone and hopefully see you all in the next call.

Meeting ended after 00:29:55 👋

This editable transcript was computer generated and might contain errors. People can also change the text after it was created.
