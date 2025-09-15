# Polygon Protocol Governance Call: Transcript

### Attendees


Ben Rodriguez, Can, Daniel Gretzke, Delweng Zheng, Dimitri Nikolaros, Fireflies.ai Notetaker Michel, Harry Rook, Ivan Bjelajac, Jerome de Tychey, Joonkyo Kim, Krzysztof Urbański, Manav Darji, Mariano Conti, Mateusz Rzeszowski, Michael Mugno, Michel Muniz, Mike Brucken, Panagiotis Alexiou (Peter Alexiou), Parvez Shaikh, Rahil's MeetGeek Notetaker, Sajal Agrawal, Sandeep Sreenath, Scott Lilliston, Simon Dosch, stream, Tiago - Stakin, Tobias Geiger, Vasanti Rode, Viktor Bunin, Vincent Taglia, WebDev StakeWorks, William Schwab, Zero Ekkusu

#### _This editable transcript was computer generated and might contain errors. People can also change the text after it was created._

Harry Rook: Okay, Welcome guys to ppgc. number 18 I'll just link the agenda. In terms of the agenda for today theres three main buckets, so we'll start off with a dbreif on the Napoli hardfork. And also the roll out onto Amoy as well which occurred more recently. then dive into pip 22, which is a proposal to merge in data pruning to the Bor client. And then we'll finish off with some discussion around EIP 3074 Account Abstraction.

Harry Rook: Okay, so to kick it off. Obviously Napoli upgrade went live on POS mainnet, March 20th. Initially things looks really smooth so milestones and then checkpoints which was kind of the question as there was a change within heimdel to kind of pass between blob transactions. Sandeep and the Bor team. I don't know if you guys have any further updates as there's been a little bit more time for it to settle in now. Yeah.

Sandeep Sreenath: So, can you repeat that Harry?

Harry Rook: Yeah, so, you know that the Napoli upgrade has had some time to settle on Mainnet initially Milestones and checkpoints were looking good. Yeah, just wondering if there's any kind of findings since now it's had a little bit more time to settle in.

Sandeep Sreenath: Everything looks good after the only hard work. There have been a few sync related issues, but that's nothing through the hard foot. Again. We are continuing to make some improvements that if you have the notes which are stuck at some block numbers, but Yeah, it's nothing really good to the hardware location.

Harry Rook: Okay, cool, so if you're looking good there Do we have any folks from the testnet committee? I know we went live on Amoy a little bit later. Obviously. We did the test net phase on Mumbai, but more recently went live on Amoy I don't know if we have anyone from the testnet committee that Was to present any findings there?

Scott Lilliston: Sure, come on here. I can weigh in a little bit it may be. Sort of no news as good news at least for the Napoli rollout this morning or about 20 hours or so ago all appears to be smoothly operating there. So I guess no it's good news at this point.

Harry Rook: Yeah, certainly is. Okay, cool. Alright. Thank you very much, Scott.

Harry Rook: So moving on next item on the agenda is ancient data pruning. So PIP-32 as proposed by Manav. I think we have him on a call. Manava. Don't know if you want to do a quick run through of The Proposal. I know we have Delweng on here who kind of initially came up with the idea for this or at least to merge it within POS. And so yeah, I don't know if you want to give a quick run for that one.

Manav Darji: Yeah, I can give a belief about this basically. So the aim of this PIP is to enable ancient data pruning inside our primary Bor client, which is more now ideally by the current support for pruning is pretty much what provides out of the box, which is pruning the actual state which gets accumulated over time and there's some dangling data as the chain progresses. It gets accumulated and isn't cleared from the database. So that's that pruning which generally people refer to so

00:05:00

Manav Darji: this approach is pretty different. So because Bor is derived from geth it kind of stores data into two separate structures. One is the ancient data base which is the older blocks. I think there is the limit of 90,000. So all the other blocks apart from the last 90,000 blocks are basically stored in this ancient data store and only the recent 90,000 block block data and the state of the recent most 128 blocks is stored inside this level DB structure. So everything else is most in general I think so we did some analysis that it kind of accounts for around 50% of the total data, but these can depend on when you last pruned your node. But yeah, that's a separate discussion. But yeah, it pretty much is quite good amount of data.

Manav Darji: the aim of this feature or I would say this adding the support is basically allowing someone to prune that data those who don't really need this. So for someone who is serving RPC or giving our business support over a full note. I think it's a simple decision that they don't really need to prune anything because they'll have to rely on that data. but for those not operators who just rely on the recent most state and they can basically benefit because it uses some space for them and we can really prone all those historical blocks. They don't need to keep it so they basically means they won't be able to serve any queries over that data and they won't be able to start this data over the beer Network. for someone so this affects someone was thinking from scratch we had some initial concerns there that

Manav Darji: How will this play eventually like going forward? Thank you in the worst case scenario that let's say Everyone goes ahead with pruning the data then. less number of nodes will be there to serve the data in the peer to be a network. We had some concerns around it, but Yeah, I think pretty much a lot of regular users generally rely more on snapshots. just because there is a possibility of less data in the peer to be a network. We can't really prevent this feature from being there. So yeah, that's basically the discussion. in favor and against of this particular feature

Sandeep Sreenath: yeah also to add to it Manav said we will anyway continue to have Archive nodes in the network, which will have all the data and so there will be anyone notes as well which will be able to serve the data if any noticing from scratch.

Sandeep Sreenath: And on top of it, we will also push the community and to at least kind of you notes without ruining the insurance data. So the data availability even if somebody wants to sing they're not from scratch. We will ensure that this is that it's always available and even the snapshots like we will have for snapshots with the notes which have the entire data as So yeah, that will be there for eternity. So yeah, that's all these arguments basically, ensure that data availability will not be a problem. And so yeah, we are also in favor of doing that matching this via this feature.

Sandeep Sreenath: On the other it also like to get other thoughts on this if anybody has any concerns with moving ahead with this.

Delweng Zheng: Hello I'm so also always says a block ancient polling. So also I introduce the feature from BSC as you all say on currently about the 13% of the data. Yes. It's an ancient first DB and I think this is just a trade of and someone wanted to learn his own. No, he needed to turn also ancient data and assess data is really a large amount of disc and so for this reason,

00:10:00

Delweng Zheng: If 1 to a cutt updated data availability, but since your appeal want to learn sandals as a disk resources is very huge. So I don't know which one is paid datability or opens the ocean the data. Maybe we need to read more. Thank you.

Harry Rook: Thanks, Delwang and just to clarify as well. this isn't I guess technically an upgrade that we're going to a hard fork, this is I guess more of an in feature that you guys are essentially supporting within the official Bor repository, right? So

Harry Rook: yeah, just want to clarify that one point and secondly, I think one concern that I've seen within some of the discussion is that we don't particularly want validators to rely On archive note, so I don't know if there's a way in which you can Ensure. That this feature wouldn't be available for validators or if there's any way to mitigate that one concern. Yeah.


Manav Darji: Yeah. Yeah, I don't think there's very simple way to make this feature optionally available for the different kind of nodes. I think even if we do so at A declined level people can easily bypass that by just making the change in. the code and building a new binary Yeah, I mean there's nothing which we could do to enforce this particular thing. but yeah, maybe there are some other opinions hypocally.

Harry Rook: Okay, Sandeep, I don't know if you guys have a tentitive timeline for when you would want to. Potentially go forward with this. we could in the meantime try and have some more async discussion on some of the drawbacks.

Sandeep Sreenath: yeah, I think since the pr is a little old we would need to replace it, it is developed and if there are any much conflict leaving, I have to look at them one until their tests are running fine. All the tests are passing. So yeah, we might need a couple of weeks. yeah, I know if you are dealing if you can help us with that as well.

Delweng Zheng: Yeah, I tried but I don't refuse a code for polygon for a lot of time. Maybe I need them more time to refuse as a changes.

Sandeep Sreenath: Yeah, maybe Manav can also take a look. It's a straightforward and if there are not any conflicts, I think should be done pretty quickly else. Yeah, we will have to see we'll have

Sandeep Sreenath: so yeah.

Manav Darji: Yeah, I think revising shouldn't be major issue. But yeah, because we also have the pbss feature. and I have to give a second look at the pr again to see they get kind of affects those things and I don't think it does it high level. But yeah. Have to take a second. Look.

Sandeep Sreenath: Yeah.

Sandeep Sreenath: So yeah, I think since there are no other concerns. I guess we will go ahead with this. Harry and that we will keep the Community in the next EPG also, I think we can be everybody informed about the changes though.

00:15:00

Harry Rook: Yeah, sounds good Sandeep. But alright if there's no other closing thoughts on that one we can move on.

Harry Rook: Okay, right then. Okay. So the next point on the agenda is EIP 3074. So, for those of you that have joined over the last couple of calls. This is been, kind of the central topic. I guess you could say, Daniel's on the call today's kind of lead those discussion produce reports that have been kind of discussed at length with ethereum researchers on the polygon forum. Said Daniel, I don't know if you want to give a quick summary of how those discussion when kind of the general consensus of. the direction you think is best to take based on those discussion and any other day so you have

Daniel Gretzke: Yeah, so since last ppgc we saw some activity in the Forum post where there was debates going on over the different account of Direction proposals the delegation transaction type EAP 5806 received little to no attention and I think that is all It was also a bit influence by 374 coming up again in the ethereum awkward Dev meeting. So there were basically two camps. Certain five six zero and 30 74 were the most vocal support or had them both most vocal supporters. And generally speaking.

Daniel Gretzke: I would propose to go with ap 3074 for the following reasons first of all. Regarding rip 7560 it greatly enhances your C for 337 wallet they already exist and they already have an ecosystem on the polygonne network and they don't offer. too much more benefits to those smart contract wallets because a lot of the benefits would be making them more or cheaper to use on ethereum, for example, but while gas is already very cheap on polygon doesn't make it's so much better to use.

Daniel Gretzke: Additionally, it doesn't offer as much benefits to EOAS, which was the main reason. We actually wanted to go with some account abstraction proposal because features Were highly requested and are also not possible with RIP-7560. and the second big reason is also with

Daniel Gretzke: the Pos Network Transitioning to a zkEVM at some point in the future. Using the zero prover stack. We need to be sure that also these. upgrades that we add the EPM plus upgrades that we add to polygon Poss should be able to be also proven and we have confirmation that 374 could be proven, but for Rips 7560. It's a bit more complex implementation overall. And that's why we don't know yet a timeline or how long it would take to be able to prove this.

Daniel Gretzke: Lastly there is a big kind of chicken and egg problem. This was discussed in the previous calls as well at links where a lot of integrators are nt or even chains are hesitant to implement 3074 because of unknown risks, but at the same time one wants to go first, but everyone would like to try it out. So what I would propose is Implement 374 on the avoid Network have a look at the risks or issues that come up also not just from a user safety perspective, but also client integration. So how does it affect block explorers wallets and so on and so forth and then start working with integrators see where

Daniel Gretzke: Apps how it would be useful to them. What kind of safety team measures do they need and from there kind of iterate on 374 on testnet until we find solution that would we either be comfortable integrating into mainnet or we can always roll back without any big problems. Also, I would suggest working closely with the original authors of ap374. There were some. Interesting suggestions for user safety. So one of them for example is having expiration dates for signatures or also.

00:20:00

Daniel Gretzke: having a conclusion lists, so that off calls based on a signature can only Call Smart contracts, and also nested calls can only cause more contracts that were authorized.

Daniel Gretzke: by the user in the signature, so We're exploring different options how to maybe make the proposal itself also safer and obviously. Collaborating with the original author team makes sense. If this is something that is not going to be only used on polygon. But also on other l2's and eventually also hopefully on ethereum itself.

Daniel Gretzke: So yes, that's why I'm proposed just deploying it on the way because even if we don't end up going with 3074, we can always roll it back without any.

Harry Rook: cool, and this wouldn't be so for example the rollout on my order particularly would be with the view of that being included in the next upcoming hardfork, right? It would just be there mostly to trial that proposal specifically and not an entire upgrade.

Daniel Gretzke: We could add it To the next upgrade, but I would wait for more feedback from users integrators the community in general also the entire L2 landscape. Because hopefully this is not something that just enhances our Network. But the entire evm ecosystem. And so if everything goes if we deploy to Moi and everything just works fantastically. No one has any if you issues with it, we can also include it in the next hard Fork obviously if we can counter a big issues with it, then we shouldn't include it.

Harry Rook: Yeah, for I mean we're gonna finalize inclusion list for the next hardfork. I believe either in the next call the one after that. So, we do have time to trial it on Amoy before. We finalize what's included.

Harry Rook: Sandeep manav, I don't know if you guys have an implementation that you've been working on and what the perspective timelines would be pushing it at some point soon.

Manav Darji: yeah, I can talk a bit about the current status of where three zero seven four is from Orange, so you're kind of initially reviewing the AP and kind of going through some of the initial audits which are done for this EAP. I think mad which is playing from get already has a POC there in yet. But yeah, there are some recent additions to the EIP which are not included in that POC. So yeah, I think the first step is simply do that for more which is the client which we use and we already have I think an over context of probably do the modifications required over there and price bring it to a devnets where?

Manav Darji: We can probably other integrators who are interested in testing and tested and then probably decide adding it to my

Harry Rook: William did you?

William Schwab: Yes, I just wanted to say that I'm fairly certain the IP itself has not changed as in one of the things that's been done with 3074 is 374 itself. I believe the authors would like to try to keep as simple and Frozen as possible. But there is a potential for future at eips to add features on to it. I think it would be worthwhile to look at 30 74 as complete as something that can be implemented by itself, especially if we're talking about testing that level and then considering future eips based off of 3074 doing things like adding an a374 specific nons or anything like that expiry kind of as a separate operation like a separate hard fork or something like that.

00:25:00

Daniel Gretzke: Yeah, I agree with that you're right. It wasn't changed in a while the original version of the IP included nonsense, but it required a compile that had storage which was unheard of yet. So they removed but non's part and basically added the nonsense to the commitment.

Daniel Gretzke: But then obviously they are not required by the EAP. But I also agree with that we can just start with barebones 3074 and then see if there's room for improvement.

Harry Rook: Okay, cool.

William Schwab: And it's zero in the chat saying expiry will probably be required. obviously I'm not the Ops guy who will have to claim this up but to some degree if we're pitching a new test net like a fresh Chestnut. on 374 there's a part of me that really wants to say. What's the worst thing that can happen? And I actually want to steal man this argument for a second as in to meet the worst thing that can happen would be a little bit of an Ops nightmare. we finally convinced Circle their usdc implementation on the new test net and these people just migrated this whatever over and all of that and now the chain is irreparably broken if that's even gonna be possible and there's a hassle there for sure.

William Schwab: But especially going in eyes open, so to speak to a new testnet saying we're embracing the chaos. We are putting this in things might break your nose. That's why it's a test not I kind of feel like this is almost an optimal opportunity to try and see if some of these attack vectors that people have been talking about with 30 74 are possible now expiry to some degrees and as a I don't think expiry very cool correctly is an attack Viktor so much as there's a lot of amazing things you can do if expiry is there but

William Schwab: The same time just the idea of having the basic raw 3074 EIP on the chain and then literally just letting people go ape over it and see if they can break it. let you know of try his DDOS Factor everything, to some degree either we prove that it's not a problem. Or it actually is but of all the production environments in the world. I would like to say that to some degree. This actually seems to be the best outside of a dedicated ephemeral net. Which is a little bit harder to coordinate around dedicated enough emeralds a bit of Nazi moron. I mean the intent of the chain would be a femoral. Yeah, Daniel.

Daniel Gretzke: Yeah. I also went to business with detail with that in the comparison, basically. implementation challenges for integrators and also

Daniel Gretzke: the potential rollback consequences. So for integrators mostly nothing changes because you can still use normal solidity to deploy. You don't need to use a specific solidity that supports then you often off call update. Everything would basically still operate as usual for Block explorers. For example. Maybe transaction tracking if this could break.

Daniel Gretzke: In cases where an eap374 signature or off call was the smart contract verification tool that would break if there is 74 feature would be used but these are small things could be then fixed while it's support would not be there from the start because it's just a blind ecd as a signature. So they would have to enable blind signature support in their wallet security settings. I don't think that it's something that will break anything out of the gate because it doesn't really change too much and also the consequences of rolling back to changes and going back to the previous version basically where it's not supported. is

00:30:00

Daniel Gretzke: just that all eap374 contracts with just stop working and that's it. There is no real consequences to it. because they don't hold any funds or state or anything. The consequences are really minimal. Also just mentioning one thing what they expiry. It not only allows you to. Have additional features but one main concern associated with 374 is that if you get compromised ones you basically sign away your rights to your account forever and you can never basically use it again with an expiry. You would be of the possibility to continue using your account after it has been compromised. So that's I think what it's mainly targeted at.

William Schwab: I take it everybody's silence means that we're doing this and that's going on, right?

Harry Rook: opinion

Daniel Gretzke: I hope so.

William Schwab: excellent, but

Harry Rook: Cool I mean at least we agreed. on deploying on a might at some point soon And taking a view on Main afterwards. I think that's the most sensible approach so. Yeah, I mean if everyone's agreed, I think we can go ahead with that plan.

Harry Rook: also All right, I think that wraps up EIP-3074 then so that's the main discussion points out the way there was just one final thing that parvez's kindly reminded me of and that's the sun setting of sad face, that's not so that will be on the April 13th. I don't know parvez if you have any other updates on that front kind of the process for Sunset anything of that nature, but yeah when we feel free to add that if you want to

Parvez Shaikh: Here as of now know that updates, but we'll definitely share any updates as usual on Forum post as well. Nothing so far.

Harry Rook: Okay, cool.

Harry Rook: That's everything then for this call guys. Next meet will be on May 2nd. And yes, I'll be sharing the agenda a week before that call. Thank you all for tuning in and see you on the next call.

Meeting ended after 00:33:28 👋
