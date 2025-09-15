# Polygon Protocol Governance Call - 2025/02/13 15:54 GMT - Transcript

### Attendees
Ben Rodriguez, Christopher Von Hessert, Dimitri Nikolaros, Fireflies.ai Notetaker Bruno, Harry Rook, Harry Rook's Presentation, Jerome de Tychey, Jerry Chen, Justin Havins, KP, Liminal Notetaker, Marcello Ardizzone, Mark Holt, Mena's Notetaker, Michel Muniz, Nikhil Chaturvedi, Raneet Debnath, Sandeep Sreenath, Sanket Saagar Karan, stream, Traversi Normandi, Vincent Taglia, WebDev StakeWorks

# Transcript

#### This editable transcript was computer generated and might contain errors. People can also change the text after it was created.

Harry Rook: Okay, welcome everyone to PPGC number 30. in terms of the agenda for today, there's three main buckets.  So on the B side, there's three new proposals to effectively increase the bandwidth of the POS chain as well as improve the predictability of gas as well. So we've had a bunch of optimizations leading up to this point with block SDM and PVSS. so yeah, there's potentially some more squeeze out of the max gas.

Harry Rook: pit 58 is a proposal to increase the base fee change denominator from 16 to 64. So this should help reduce gas spikes yeah with the EIP1559 calculation. And then as well as that there's PIP 59 to move the floor gas price to the actual base itself.  So that will actually include it in the protocol which will have some nice benefits for users. so that's on the B sides. in terms of Heimdoll yeah hat tip to all the guys involved Renita especially in Sandep. so we had the day in law Julik hard today on mainnet I think it was about 5 5 a.m. UK time. so yeah well done guys for getting that one over the line.

Harry Rook: So we'll have someone from the Amoy test net committee just briefly discuss how things looked on Amoy leading up to mainet and Renie will also do a brief overview there as well of how things are looking. and then yeah as well as that we'll be discussing some changes to the spec of pip 54.  So from a high level was a change to reassign the POSOS bridge contracts to the protocol council multiig. So yeah, we've kind of slowly been progressing on that front. and there's some updates with regards to how contracts will be passed between the seven of 13 and the 10 of 13. So Chris will be able to talk in a bit more detail about that later on towards the end of the call. all right, awesome.

Harry Rook: So, without further ado, then let's get into the bore side of things. So, in terms of the next B hard fork, it's looking like it's going to be I don't know if early Q2 is a good roundabout estimate for that. but yeah, in terms of some of the potential inclusions of that fork, that will probably include a lot of the PETAR changes. we have three new pips. the first that we're going to discuss is increasing the base fee change denominator to 64. So yeah Jerry I don't know if you want to briefly kind of give updates on that front.

Harry Rook: I know there was some discussion on the forum as well. So yeah over to you

Jerry Chen: Yeah. All right.

Jerry Chen: Thanks, yeah.  So a little bit background for this pet. so this space fee change denominator is basically a parameter that we can set in a protocol that decides or determines how fast the base gas speed should increase when a block is full.  And this value it has been set to eight in death in Ethereum and in board it was changed to increase to 16 two years back. yeah so now we have been observed many gas in the last few months.

Jerry Chen: so for example in February second we saw there's gas line which increase the base fee from around 100 GU to 35,000 guay in a span of 40 minutes. so that's a pretty big spike there and it's not like the first time we have seen this so the simplest approach is to change this parameter to get gas so that it won't affect you so basically the same network usage under this kind of load wouldn't have to increase to 35

Jerry Chen: five ua so it's almost 300 x in 40 minutes. And we kind of simulate the gas behavior like what would it behave if we change the base fee denominator to 64.  And in this simulation we found out that if we had set this parameter to 64 the fee would only increase to around 600 gray. yeah so that's a pretty good improvement. it's also around four times of increase but definitely not comparable to what had happened.

00:05:00

Jerry Chen: So yeah, so basically this is the idea that we're proposing this parameter to be set to 64 and we have also shared the simulation in the forum post down in the comment below this is a good number and also we got a feedback also from immutable ZKBN team that they said  this value actually to be 50 which is similar to our parameter and basically that they also have a 2cond block time so yeah in general I think it's a good idea to move it to 64  Four.

Harry Rook: Yeah, thanks Jerry. And I think one other thing that he mentioned that was the 50 64 figures puts it in line reasonably with Ethereum's parameters given their block time. So yeah, I think it makes a ton of sense. I don't know if anyone has any thoughts or feedback before we move on. thanks a lot, Jerry. yeah, I've linked the forum post in the chat. So, if anyone has any long form thoughts they want to share, then feel free to dive into the thread. So, next one is the moving of the floor gas price into the base Sandeep, I don't know if you want to cover this one. Obviously, we changed this. I think it was a couple of hard talks back with the Ming gas change.

Harry Rook: I don't know if you want to cover the updates on that front.

Sandeep Sreenath: Yeah, Thanks, yeah, so a bit of background before we get into 59. so I guess initially there was no lower gas pricing in Polygon POS. and we saw a lot of spam transactions that was basically probably some bots which were doing some transactions and yeah this was I think around 3 years ago when we finally wanted to fight spam by adding a floor gas pricing.

Sandeep Sreenath: So this was initially added into the priority fee and the initial value value was 30 G and again this is not a protocol level parameter it's a client level setting and I think most of the validators had increased it from zero to 30 so eventually once we started seeing lesser

Sandeep Sreenath: spam transactions. We also wanted to decrease this floor pricing and that's when we decreased it to 25 way again with a help so we did a small change in the client also I think that was a previous pip where we added this parameter as part of the client code itself so that all the validators do not need to manually do this change in their config.

Sandeep Sreenath: but the downstream applications there is a problem in this approach is that we have to manually ask all the downstream applications like wallets and it could be other gas trackers to update this value because it's not part of the protocol right and there's no way for any developers or dabs to query what's the current flow

Sandeep Sreenath: gas pricing. Yeah, thanks so that's the reason we are proposing this bit where we are moving this floor gas pricing from priority fee to the base fee. So when it's part of the base fee, It is part of the protocol itself and we don't need to take any manual steps in order to update all the wallets or all the gas trackers.

00:10:00

Sandeep Sreenath: whenever there is any change right and since this end this becomes a part of the base fee it is a change in the protocol so there will be a hard fork which is required going forward for this yeah I guess that's the motivation and just to give you some idea so when we decreased it from 30 to 25 when it was part of the priority fee  It took almost weeks together for us to ensure that all the wallets are updated and we had to follow up multiple times right even the gas trackers and polygon scan and all the others. and definitely it's not a good user experience.

Sandeep Sreenath: because whenever there is an increase in this minimum the floor price users transactions will get dropped and whenever there's a decrease users will end up paying higher gas right which is unnecessary and that's why the ideal place for this kind of a parameter is within the protocol.  So that's the reason we are proposing this to be part of the base fee. any questions on this front? I guess not. moving on to so PIP 60 is about increasing the block gas limit to 36 million.

Sandeep Sreenath: so as Harry was mentioning earlier u so over the last year or so or maybe one and a half years we've made a lot of improvements in the protocol in the client so a lot of optimizations as well. One of them is parallel EVM which is where we implemented block htm. so when we enabled this across the network we saw that the execution time for transactions had decreased at least by 30 to 40%.

Sandeep Sreenath: especially when there are many transactions within the block which are parallelizable. But obviously there were cases where many transactions were not parallelizable so they had to be executed serially especially when they all were being sent to the same smart contract. but still we saw a significant improvement in the execution time and that's one there were a few other optimizations that we did and I think the most recent one was pathbased storage scheme this is already rolled out but I think with version 2.0

Sandeep Sreenath: zero of bore it becomes the default option and yeah so we are p pushing all the nodes in the network to upgrade to PBSS even with PBSS we've seen some significant decrease in the block execution time I think that is also again around 30 to 40%.  So with all these improvements it gives us confidence that we will be able to support a higher block cast limit.

Sandeep Sreenath: we've also seen observed this in our internal nodes that we are running where we have seen like I said so earlier it used to take around 400 to 500 millconds on average for the execution of transactions especially the full blocks which were nearing 30 million gas and after all these changes after all these optimizations it had reduced significantly  ly between 300 to 400 millconds.

Sandeep Sreenath: There are still some cases where it takes much higher time for execution but this was the case even earlier right and this is probably when you look at 9th percentile where you see a few blocks here and there which have a higher execution time of maybe even about 1 second and few even about 2 seconds. and we also have to encounter or to counter such blocks we also have a feature which preempts block building when it is taking a long time right when it's taking time which is higher than the block time of 2 seconds.

00:15:00

Sandeep Sreenath: So with this feature also in place we are pretty confident that we will be able to handle a higher cash limit.  So it's still I would say a conservative increase to 36 million and yeah we'll observe over the next few months how the current infrastructure is able to handle this and if possible we'll also increase it even further in order to increase the overall capacity of the network. yeah that's PIP 60 in short.

Sandeep Sreenath: Any questions again on this?

Harry Rook: Just thinking aloud Sandy won't that work quite complimentary to the base fee change denominator as well just thinking…

Harry Rook: if blocks in the short term are less full I'm sure the EIP1559 calculation will yeah…

Harry Rook: because the box won't be half full will that not basically reduce any gas spikes. Will that have a slight benefit there? that mechanism makes Yeah.

Sandeep Sreenath: Yeah. …

Harry Rook: Sorry. Go ahead.

Sandeep Sreenath: so with 30 million block gas limit the target is 15 million. It's 50%. So with 36 million it will move up to 18 million. so yeah, in general you're right.

Sandeep Sreenath: So there's going to be more so that we'll see lesser gas spikes because there's overall increased capacity.  But yeah like you said and even with a better base fee change denominator we will also be able to see much more consistent gas pricing and hence more transactions probably included in the blocks especially when there are these spikes.

Harry Rook: Yeah, thanks for the overview there, appreciate that. I don't know if anyone has any questions on any of those three. yeah, I mentioned Sandep earlier. I don't know if there's a rough timeline. probably it's a bit too far out at this point. But yeah, we thinking early Q2 as a rough target or…

Harry Rook: is it too early to tell

Sandeep Sreenath: Yeah. So we are also targeting all the or…

Sandeep Sreenath: most of the changes or EIPs which are part of PCRA hard fork in Ethereum. So we'll try to club all of these together so that we reduce the number of hard forks on board.

Sandeep Sreenath: Yeah, probably early Q2 is a good timeline that we can target.

Harry Rook: Okay, awesome.

Harry Rook: Thank you very much, Sandep. yeah, I've put all all the forum links in the chat, so yeah, if anyone more interested, just jump in there and feel free to take a All then moving on to the Heimdore side of things. Obviously another successful hard fork on mainet this morning. yeah, I don't know if we have someone from the committee on the call that wants to just do a quick debrief of how that looked on Amoy. I think it may be Vincent.

Harry Rook: But yeah.

Vincent Taglia: Yes.

Vincent Taglia: Hello everyone. I'm happy to report that the MOY committee members have reported smooth upgrades with the latest for version 2.0.0 beta 2. there have been no issues reported.

Harry Rook: Short but Okay, Renee, I don't know if you want to do I don't know if you have anything else to add there on top of Vincent's comments.

Raneet Debnath: No not really. I think it was more about the bore upgrade. So yeah anyway from Heimler end of things like we had much a smooth hard folk yet again. So yeah I mean giving a brief summary of the string of events.  So in December basically we released the Yoric hard folk that would bring about certain changes in the block producer selection algorithm which in Amoy and we faced certain issues like certain issues were reported on Amoy by operators that we basically traced it to a non-determinism bug which we

00:20:00

Raneet Debnath: fixed via another hard folk called Danlaw in January in a way and yeah today we finally released both the week and DLO hard folks this morning India time. So yeah the network went pretty smoothly.  few issues reported by operators but that it was mostly related to either some problems in the configs or maybe the versions were not upgraded timely which again some operational harders that happen every now and then.  So yeah, that's a brief gist of it unless anyone wants me to expand more.

Harry Rook: I think you covered it perfectly, Renee. yeah, thank you for that and hat tip to everyone that was working on that. I know you're driving that one pretty heavily, Renee. So, yeah, shout out to you on that another smooth hard foot. Well done, guys. All right, if there's no other comments on that front, we can move on to the final point, which is the PIP 54 updates. I don't know if we have Chris on the call, Just double check.

Christopher Von Hessert: Yes. Yes.

Harry Rook: Okay, so yeah, Chris, I know we've been working on this for a little bit. we're kind of figuring things out a little bit more now. We're getting closer to being able to actually execute this first stage of what PIP 54 is going to look like. And yeah, I don't know if you want to give a quick update on how things are looking.

Christopher Von Hessert: So, we're basically done with all the technical details. we're only fine primed to finalize our testing right now in terms of moving the multisig and all the responsibilities over there. unfortunately we are missing somebody on the POS side of things to finish their multisig setup. Now as you remember we needed to create new multisigs on the polygon network side of things because those didn't exist. So we've created a multisig we created the time lock exactly the same to replicate.

Christopher Von Hessert: We've used the same addresses thanks to Santos safe's new functionality to ensure that everything remains exactly the same. We're just missing one of the signers to actually us confirm that they're going to be using the same address on the POS side of things. after that hopefully next week I will probably request all the signers to please do a test transaction. So we're going to test that multisig setup on the polygon network just to ensure that everything works as expected and after that we will be sitting the payload sending the payload for it to be sent out.

Christopher Von Hessert: I expect that the proposal may be opened in around two weeks time. again it all depends on when the missing signer is finished with their setup. sometimes it gets a little bit complex because there are multisigs multi in this case. but I expect that in the next two to three weeks at most we should be able to put the payload for everybody's review of course I hope everybody that is signing will actually review the payload as well and at the same time start the signature process another point that is important to mention over here is that one of the contracts specifically the governance proxy that manages a couple of operational aspects inside

Christopher Von Hessert: basically the polygon bridge and the polygon set of contracts it does have some administrative and operational functions as well as the typical upgradability unfortunately given these contracts are very old they have not this dual ability to manage roles no so they have no type of role manager or anything like that and therefore we have decided to put it at the highest  level to keep it with the 10 to 13 monthly sick. So 10 approvals and 13 people just to ensure that we're keeping into the safest way possible. we will be looking in the next weeks, months probably as we start communicating more about the move into the CK world of POS.

00:25:00

Christopher Von Hessert: No, I don't want to spill any crazy stuff over here, but as we start communicating more about our plans over there, we will be looking at refactoring some of these contracts to ensure that we have more flexibility around roles and responsibilities and as well to be able to work even better with the new portal, governance hub that our governance team, Harry over here, Mateos are actually working on. I think that's it.  Harry, am I missing anything?

Harry Rook: No, I think you covered everything. yeah. Yeah, I think one thing we need to do on that front is update PIP 54. just to reflect that the governance proxies under the 10 of 13. so I'll probably do that today. and then other than that, I think we're good to go. So then we'll keep the protocol council members appraised as we approach execution.

Christopher Von Hessert: No problem.

Harry Rook: I think we have Drew on a call. But yeah, I think that was everything. Chris, thank you for the overview there. that wraps up all of the agenda points. I don't know if anyone has anything else they want to briefly cover. if not, I'll be able to give you guys 30 minutes back of your day. So, okay,…

Jerome de Tychey: Thank you guys.

Harry Rook: Thanks everyone. so next call scheduled March 13th. may change to a week after depending on how things land, but yeah, that's kind of roughly when we're going to be having the next one. So, yeah, again, thanks everyone for joining and I'll see you on the next call.

Sandeep Sreenath: Thanks everyone.

Raneet Debnath: Thank you folks.

Meeting ended after 00:27:22 👋
This editable transcript was computer generated and might contain errors. People can also change the text after it was created.

