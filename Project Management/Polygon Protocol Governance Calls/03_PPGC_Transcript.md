# Polygon Protocol Governance Call: Transcript

### Attendees
Alex PFL, Antony Halim, Bojana Tomic, Dan Moore, Dimitri Nikolaros, Fireflies.ai Notetaker Michel, George Serntedakis, Guillaume Dechaux, Harry Rook, Jackson Lewis, Jason Windawi, John Hilliard, Krishna Upadhyaya, Manav Darji, Manav Darji's Presentation, Mateusz Rzeszowski, Michel Muniz, Mohak Agarwal, Nimit Bavishi, Paul O'Leary, Pratik Patil, Pratik Patil's Presentation, Scott Lilliston, Vedant Patel, Vince Reed, Yuling Ma

## Transcript

**This editable transcript was computer generated and might contain errors. People can also change the text after it was created.**

Harry Rook: If a couple minutes and then, yeah, start the recording and kick it off.

Harry Rook: It was given another couple minutes and then, then we'll start.

Harry Rook: Okay, I think we've got everyone in here for now. Let's make a start. I'm just going to record the call, which I'll be posted on YouTube for those that can't watch. So,

Harry Rook:  Awesome. Okay, so welcome guys. To the third installment of the polygon Protocol governance session which is just changed its name. Over the last couple of weeks. For those of you that don't know me, my name is Harry. I work on the governance team, and yeah, I'll be leading the call today and guarding you guys through the agenda. So yeah, just in terms of like a quick summary of what we'll be covering the main things are picked 12. Paper 11. And then we'll have a brief overview. What's to come with? The kind of downstream Shanghai hard fork? And then yeah, we'll also be talking about some kind of rough scheduling. For upcoming hard forks. So yeah, thank you guys for taking making the effort to join. I know a lot of you in different time zones. So yeah, appreciate you guys, John

00:05:00

Harry Rook:  Yeah, so beforehand it over to Krishna who's going to be doing an overview of Pitt 12. I'm just going to give you guys a quick reminder on the

Harry Rook:  pit bounty program. So this call is meant to be like a synchronous place for discussion around pips, which are polygon improve of proposals, proposals that aim to improve the the polygonne network essentially, and this is where we're deliberate on the technical details. And gather kind of technical consensus peer review on those proposals. So, yeah, we we launched a program where we'll be giving people authors the sum of life of forty thousand dollars over the next 12 months. So, Yeah, there's some there's some funding now for PIP authors which is good. Another thing is we recently released a blog post around protocol governance.

Harry Rook:  Around have open source and how it fits into to polygon's protocol governance. Yeah, please take a look if you get the chance. Another housekeeping thing we're thinking about doing alternate timing for these calls so one month will do it. Perhaps later in the in the day or earlier in the day. Yeah, alternate those month a month so we can be a bit more inclusive.

Harry Rook:  For different time zones. And so, yeah, I'll be doing Are we doing a temperature? Check in the discord channel. So keep an eye for that. And as well as that will also be moving over to zoom soon because the the visual audio quality of the recordings of these calls is, it's not great. So yeah, we're probably be moving over to zoom. Maybe not for the next chord, but definitely the one after that. So yeah, keeping out for that. I want.

Harry Rook:  Yeah, and on that note, then I'll hand it over to Krishna. Who is going to be doing an overview of Tip 12. So Krishna if you there is over to you

Krishna Upadhyaya: Yep, I'm here. Thanks Harry. Okay. Okay, I welcome everyone on behalf of the POS team as well. So

Krishna Upadhyaya:  It's not usual for team. I mean, we are. I'm my name is Krishna and I will. Usually, we usually handle technical calls but yeah, governance call is something new for us as well. And a couple of my team members also, we'll be over. We are here to basically discuss a couple of things, regarding some updates, you know, which we are going to do on the pious network in the upcoming months, I can say. Yeah, some of them will take months. So yeah, in the next few months, this includes a couple of hard focus. Well, maybe like on him dial. And someone on both side as well. As you guys already know, we have been like in the last call, whoever was presentation, we introduce 10 regarding the state sing confirmation, which was actually technical cause you know, which caused the 157 Block Reaw back in January. And we identified that there's an issue. So we have actually identified better solution to that. So basically, and we can say, upgraded version of PIP, 10 and pratiki and let Pratika discuss more on that.

Krishna Upadhyaya:  Later on that, half on the overview side. I'll first of all, I'll present an overview for everyone to get in the context. So yeah, the first major hard folk or change, which we are eyeing right now is milestone implementation because that is something which we are very excited as well. As you know, it's very important for us to test this rigorously because it's very tedious from technical perspective. So yeah, a current status is, I mean, from the last in the last call we explained it technically still, if we can. I'm not sure if we have to again or on manav is here to explain or answer any question if we have regarding milestones, but the current status is just to clear for everyone, we have started like an audit performed for the same, you know, so that we can identify if there's any potential security issue or any bug in the milestone. So right now we are waiting for the reports that reports are some pair are some supposed to come next week, somewhere around 24 hours of May and assuming that if reports are clean and there is no major issue and might even in closing including some minor fixes here and there
00:10:00

Krishna Upadhyaya:  We might expect to have a hard folk somewhere in the last June last week of June or early July, which will be actually a hard fork on the involvement. Okay. So noted on this is this is not actually a board but in Hindal article, but yeah, they will be a new version of board to support that himdal Hartford from our side. The status it, it does, it's tested, but we are just waiting for the audit report. And as for the audit report, the hard for date will be, you know, will be decided and all the details regarding this is already shared in the PIP. Well here, I'll share the link quickly. Or just a minute, I'm sharing the 11 Lakes. So this is 11, sorry, because this is what I'm talking about.

Krishna Upadhyaya:  The second hard fork, which we are eyeing in the upcoming few months is the state. Singlet It is tasting. Can introducing states in confirmation hard, folks. So that was basically earlier PIP 10. But now we have, you know, introduce a better version to do it internally and thanks for external communication. Basically, the name for that changes fit 12 pratik. Will this more about this in a while and and the third artwork, which we are eyeing is basically upstream changes. So you guys might observe that in the boat. We have not done upstream changes, you know, for some time but yeah, after the Shanghai Heart folk on the get side, the we are trying to, you know, update our client as well with all the changes. Whichever are Whatever are there. So majorly the changes, which will we will have in the, We are still in the process. So we know couple of things, for example, snaps and improvements are, there we will introduce Pebble TV, which will be, you know, as an option or maybe we will be replacing level TV, going ahead. And as

Krishna Upadhyaya:  All know that level DB has some DB compaction issues. When the size is growing. After certain limit, we have improvements in tracing in matrix and the optimization on the storage scheme and obviously some of some bug fixes, you know, regarding the security and all. So all these things are still under process. I mean the upstream merge is obviously you guys know, like there's a big process. We are merging resolving conflicts, the sting them out, much of things testing them out again and again so still this is ongoing. But yeah, from the next two months or three months we have Shanghai heart folk officially to perform on our own network. So these are the two, I will say technically, three hard folks, one on him doll who we might combine some of them together depending on the status and how we plan ahead. But yeah, okay. I think these days are the overview about the artwork.

Krishna Upadhyaya:  Sorry. Okay, now I think to discuss. I mean the main agenda I speak 12 Because we already discuss Peptan earlier so to discuss Pept oil. I think pratik will you take over it?

Pratik Patil: The answer. Let me share learning for the PIP as well as I can share my screen.

Krishna Upadhyaya:  Up.

Pratik Patil:  Okay. Can you see my screen?

Krishna Upadhyaya: Not yet. I think it's still loading.

Pratik Patil: Okay. Okay,…


Krishna Upadhyaya: Let's wait a couple of seconds.

Pratik Patil: thank you.

Krishna Upadhyaya:  Yes, it's coming up.

Pratik Patil:  Awesome. Okay everyone. So like as you are aware like we mentioned this in the last call as well. Like there was a deep reorg on the networking but I think and which led to like which was basically Caused by one thing, leading to other another. And here, that thing was basically a bad block error because of which the Block broker propagation got delayed and hence, the reorg which was not so big, got like a big reward. Basically. So for this with what's introduced a quick solution,

Pratik Patil:  So basically this was due to the different number of states in transactions, across different forks. So when this for commercials back to where there, while verifying the incoming chain, the board, like the board encountered and issue where the incoming chain had different number of states, in transactions, than that we're being that's from, it's in all. So, this was because the parameters, which gets this chasing transactions from him doll, very different for a particular, like, for a particular block. And this was different because this like basically there are two parameters used to fix this transactions from him doll. The first is from my ID and the second one is it too, which is a timestamp of block.

00:15:00

Pratik Patil:  In the local database which is basically a sprint plan behind. So for example, let's say we are on block 32 right now which is the first block of a sprint and assuming 16 is the sprint length. It will be like two value will be the timestamp of 32 minus 16 which is basically the 16th block and this block is fetched from the local database of that particular bore node. And like note that this is not fetch from the incoming scene given that like on different forks, the timestamp of upper decision block will be different because like they, they cannot be same because the time to mine can be different on different folks. Now, for this, we like be introduced a solution basically, which increase like, which stated that instead of going 16 blocks behind, and getting its time.

Pratik Patil:  We should go let's say 128 blocks behind and take the timestamp because like a reorg like cannot like we the probability of happening a New York, which is 128 whose depth is 128, blocks is very low. Therefore this issue will not occur that like That frequently but still this issue can occur that. He auxil and is greater than 128. So instead, like so this VIP has introduced a way where the calculation of two is not dependent on the length of the area, but rather we are

Pratik Patil:  Introducing a way where the value of 2 will be same across the forks or we will be same while this pork merges back together. Where previously we used to get this bad block error. Now the the way we are calculating two, here is basically, we are taking the current block and getting its timestamp. And instead of going like, like few blocks behind and getting the time of that particular block, we are just subtracting a constant time from the current block time stamp. And this current block is not yet in the database. So basically, we are no longer doing in the local database for a timestamp of a particular block. So if someone is importing a fork, so this block will be the

Pratik Patil:  This current block will be the one from the incoming stream, but not like not from the local team. So basically therefore the value of 2 calculated, while verifying this screen will be the same as that calculated from, as that in the canonical scene and hence, the well, and as the value of from id and to ID is same, same dial, will return the same number of states in transactions. and therefore a bad block won't happen because the number of states in transactions and the transactions are basically the same So this is the solution. Now, let's look at the implementation. So here again,


Pratik Patil:  the only thing which we are adding here is this constant? Now, this will be fixed across the network and therefore, this constant will be present in the Genesis file. And again, like we are keeping it as a map. So that in the future if you want to change it or like modified to, let's say 64 or like 200 or two, like whatever value we can just add like we can just do that in the Genesis file instead rather than changing the implementation. So yeah, it will be like this like a structure in the genitals file where this hardcore block will be the block number from where this hard for like this particular implementation will take place, which is basically the hard work number.

Pratik Patil:  The block number and 128 is basically the number of seconds, basically the delay, which we want to give and basically subtract that from the current blocks timestamp and then, which is used to calculate the value of two. now, like so they, the current implementation is that we get the value of 2 by

Pratik Patil:  From getting the time of a block, which is a sprint length behind the current block block and this get header function. Basically takes this block from the database from its local database, which is the problem here. So this is the current implementation and we propose to change it to something like like if we are post hard fork, then we will switch that delay from the genesis file here for the current block. And like in this case it will return 128 and we'll simply do header headers time. Basically the current block style minus the states in the, which is 128. Yeah, so basically, it is just an IFS if we are posted for do this updated implementation? If we are really hard for them to do what they're currently doing. Now yeah, basically, this is the implementation. It is very simple.

00:20:00

Pratik Patil:  Yeah, thanks to note that current sprint. Length is 16 the States and confirmation delay is 128. So this is not yet final but we are confident that 128. Second is a ideal if like but we are still open for discussion around this so yeah we'll free to post your comments on the Forum post or like yeah basically on the Forum post and we'll take that up and yeah. So In VIP, like, in the IP 10, we increase the value from 16 blocks to 128 blocks. But in PIP, 12, we are increasing the range from 16 blocks to 128 seconds.

Pratik Patil:  Now the like this is pretty easy and simple change and there are no threats around security like regarding this, the only thing which will change is that the state thing like the states in transactions will be delayed by 92 seconds and 90 seconds is basically calculated by. So right now as we have 16 blocks like we go 16 blocks behind the current block and assuming 2.25 seconds. As the block time, we subtract that from 128 and get 92 seconds. So yeah, the stating transactions will be delayed by 92 seconds.

Pratik Patil:  Which is not basically, if we look at current states in transactions, the time to which it takes from coming from the main chain to him doll to go, it is like not a significant increase and as this is a break, like a breaking change which is not backwards compatible, therefore it will require a hard fork. Yeah. Like if you have any questions, feel free to ask. They happy to answer.

Krishna Upadhyaya: Thanks for the. Yeah. If anybody has any question they can ask here or the pep is, I think life so they can comment there basically the main motive in. If I have to explain two lines, this to remove dependency on block. You know, any block like earlier, we were trying to increase the block difference from 16 to 64 or 128 by adding. You know, what is a number.

Krishna Upadhyaya: Going to. So we are trying to increase but later we realized that while we are still dependent on blocks, this thing can still occur. If you know like for example, if you have if you would have increase it from 16 to 128 and there is a reed more than 128 That same situation would have still arrived, okay? Like because two nodes which are which are on different reox, we still get different time, two different values, your multiplies or whatever. So so we decided that we have to remove this dependency on block because when there are, when there's a reed and two different nodes are under two different rooks and then, obviously their block time and their block calculation and all like, well, differ. So we wanted to remove that. So that's why we are now going to use the incoming, current block, whatever we have from the incoming folk and we subtract some common time factor out of it. So that even if there's a real happening, I think this will still give the same two value for all the notes across all the reox, whoever are even importing the new new ones. So that's the, I mean, just summary for the well,

Krishna Upadhyaya:  So we change it. I mean if you compare 10 to 12, that's the major difference.

Krishna Upadhyaya:  Okay. And I'm sorry we don't have any pip for Shanghai, heart forget. But yeah that's one of the hard one again. One thing which will be doing and we already have also have 11 for milestone. Do we have to hurry just took a question. Do we have to explain Pip 11 like you should we should go over it again or

Harry Rook: I mean, it's yeah, I think we explain it on the last on the last call but Yeah. Maybe it'd be good to do kind of a summary of like…

Harry Rook: where the where the order is like what testing needs to be in place as well. I think that would probably helpful is finished.

Krishna Upadhyaya:  Sure. Okay Manav, can you do a quick review of Step 11 as well?

Manav Darji: Yeah, for sure. Yesterday.

Manav Darji:  I'm sharing my skin.

00:25:00

Manav Darji:  Is it visible?

Harry Rook: Yeah.

Krishna Upadhyaya: I'm not yet, it's there. We can see your presenting, but the screen is still not as

Manav Darji:  Okay, let me we

Krishna Upadhyaya: Yeah, we'll be quick this time only just a quick review and then we'll, we have on anyway. We already shared well managing. So this thing is on the technical front already implementation has been done and we are, you know, we have started an audit almost last year like last month, April. And the reports will be again. I'm replacing Will. We saw will be, should be there by 24th,…

Manav Darji:  Up.

Krishna Upadhyaya: or 25th of May, and, yeah, if there are nothing serious. Maybe in one month after that, June, end, or July first week, you might go ahead with this. If everybody approves obviously, okay, yeah, overtime.

Manav Darji:  Yeah, thanks Krishna. Oh yeah. So basically just to like quickly someone summarize the implementation of milestones in a couple of minutes it's basically like the goal is to have deterministic family, why are milestones? So, because of the dual architecture, Or manual client architecture of polygon viewers, which is the boards. And in darji where the Him dollar chain runs on a tenderment consensus, which has fast finality or I would say a single block finally. So, basically milestone leverages those properties of that mean chain.

Manav Darji:  To make sure that it we achieve finally, the faster on the board chain which is the main block producing year of the polygon viewers network, right? So yeah, I mean also just to clear out some things that Currently, it's probabilistic until a very high number, which is the last checkpoint. So, the average checkpoint is of of length, find it in 12 more blocks. So, currently, it's kind of probabilistic until that point, of course. So basically that is the number of logs or the block confirmation which

Manav Darji:  Which is like the like recommended. Theoretically, of course, like a lot of people in the community have been using a lower number or some something close to 256 for now to to rely on finality. But yeah, like the current status of the probabilisticness is until the last checkpoint technically so yeah, by of course, introducing my stones, they use it experience for those bats will of course improve because it gives them This finality, much faster as compared to the current checkpoint thing. So, consider milestones as a subject point, as, as mentioned, in the struct over here, I mean And and like the length for now is fixed at 16 blocks.

Manav Darji:  Sorry 16 blocks is the conformation, but yeah, the length of the milestone is like 12 more blocks. So it, the thing, The difference with checkpoint in milestone is that checkpoints are submitted to L1. Hence, the frequency of checkpoints is has to be kept by by keeping, the L1 gas is in mind, but for milestones, they only are committed at the Himalaya once they achieved by three, plus one consensus on the gender mean so. So the frequency like we can tune tune around and increase the frequency of those milestones. Making it reach finality faster, so

Manav Darji:  Yeah, of course, like a lot of technical nitty gritties have been mentioned in the wave itself. So, yeah, basically, there is, there is a whole process of whole process which the validator goes through, when proposing, a milestone, and the corresponding board, nodes vote on the, on each of those milestones. And once the milestone is conformed, they basically phage those milestones in the board layer and try to follow, or try to speak to that particular for. So, and ideas, for is the fork which contains milestones and in cases where it tries to, you know, fetch blocks from other. Folks. It will very quickly figure out whether it's on a wrongful God not because Someone cannot attempt to.

Manav Darji:  Throw out a very long forth which which does not have a minus one. So like that won't be possible, which is currently possible for now. But yeah, like with my response it won't be possible. So it's like you are like you have some goal posts, which are continuously moving ahead, and this whole post is milestones for more.

00:30:00

Manav Darji:  Ah and yeah basically considered milestone just like a white listing feature of both so more continuously. White is this milestones and use leverages those things when it tries to connect two peers when it tries to the involvements and so on and of course like with that we also like it will also include a final detail which is quite similar to. What if you don't currently has basically you can provide you can call a method like number with a tag finalized and it would give you the last finalized block. And of course, like this also applies to all all such RPC methods, which have this finalized style. so, yeah, of course again as Krishna mentioned like this involves

Manav Darji:  Ment hard fork on the same earlier because it introduces a new type of transaction, which is which we call as the milestone transaction. And like, of course, it has a separate round of votings and everything. So it requires a hard fall and in order to leverage those milestones more, will also be requiring a security Upgrade along with but like it won't be a high for or say, but yeah, it would be like the values will need to upgrade more along with the hind availability.

Manav Darji:  Yeah, and sharing my two cents on on the testing front. So yeah, of course, we haven't mentioned those things in the VIP itself. But you kind of performed to broadly, classify, you performed for kind of testing. One is, of course, the unit test. So we have tried to include unit test for every module which has been modified or introduced newly in the Boolean and, of course, the Himalaya and we'll try to mock a lot of components in order to create different kind of scenarios to make sure that the milestone feature works well and that is in doing test, which is We have attempted doing a lot of long-running tests where we are, mocking a couple of components and more. We are explicitly.

Manav Darji:  Managing, managing the springs and everything and the validator said, and we are explicitly quite listing, some milestones to see how it works under different cases. The third kind of testing of courses, a lot of manual testing has been also involvements like again, we have tried to play around with a lot of protagors and try to replicate them how they work currently, and how they would work with the milestone feature enabled. So that is it. And in last, like, finally we have, we have a tool, which is taking also, probably available, which is metically. So it allows you to spin up dimitri's and try tries to, and like allows you to play around with them. Like we

Manav Darji:  Named it as a black box testing for my stones so we we spin up a lot of divinates using the diet particular tool and conducted a few tests. A few simulation based to also be more confident So yeah, like those are the kind of testing which we've been performing.

Manav Darji:  Yeah, that's it.

Krishna Upadhyaya: And ultimately an audit over it.

Manav Darji:  Yeah, of course.

Krishna Upadhyaya:  External audit is also going on. So yeah, we'll I think Once we have the reports, I'm not sure about the official process. But I think as for the reports, the details will be shared out publicly as well. When whenever we have whenever we will release this, right?

Manav Darji:  Yeah.

Krishna Upadhyaya:  Yeah, that's it from our side guys. Let us know if you have missed anything Harry or anything else we should cover.

Harry Rook: No, no, if you missed anything, I just if there are any questions about any of those, even those pips then? Please. Yeah. Feel free. We've got a little bit of time left. So Yeah, there's an open floor for the next five minutes if anyone I mean, what's asking me questions?

Dimitri Nikolaros: Yeah. Hey guys can ask you something. Um, you know, for, you know, for the the milestones, you know, when you said, it'll go up to a hundred and then when it increases, it'll delete the previous, what happens to the state that do you have any more info on? Like, As I understand, it will require less disk space, right? So you have any more info on what happens on the size of discs.

00:35:00

Manav Darji: Yeah, I could take that. So I don't think it really affects the the underlying state which is being stored in in the level DV. There are I would say there are a couple of things which are related to milestone which are being stored in memory. and there's only two of the keys, which we store in this for, for miles, which gives updating once the minds once the chain progresses,

Harry Rook: Awesome. Thank you Dimitri. Yeah, any other questions if not we can Yeah, we can probably wrap up a bit early.

Jason Windawi: Hey, so I had a question for you guys around timing and the hard forks and what was just wondering. Is there a plan in terms of phasing? So, you guys that the presentation's were great, when you give a lot of great information about the timing of what we can expect in terms of the audits and then the, the time to sort of release an implementation after is there an interim step where something's going to go on to Mumbai first before it gets it released in Hartford or what's the sort of timeline?

Krishna Upadhyaya: Yes. Understood your question? Yeah, just yeah, obviously. So whatever we do, whatever hard thing will do. Obviously we'll go to Mumbai first. I mean there will be no neither an upgrade or not a hard f***, which will go to midnight first. Okay, neither I'm I'm saying it. Very in general, like whatever, we upgrade either, it be him doll or a hard folk, or a non-heartful. Even release. Everything goes to Mumbai first. So the timing which are saying is obviously inclusive of that. So whatever we do on Mumbai, like the ideal scenario is let's say we deploy Monday this week on Mumbai. So and if and you observe for a couple of days, let's say three days four days or maybe then after that, we'll give one more week for the main networking x week. We do Mumbai then x. Plus two week. We do one minute that's like 14, at least a 14 days difference. Ideally will be there between Mumbai and Main Network that I can say. So you so yeah,…

Jason Windawi: Great. Okay.

Krishna Upadhyaya: and whatever will come on Mumbai first. So yeah, we'll for Mumbai. We might give a shorter, you know frame. Let's say for Days of seven days at Max like we cannot give 14 days for Mumbai. So let's say we say on Monday and we might ask you to update by you know Wednesday or Thursday or Friday that okay in four days there's artwork in Mumbai because Mumbai is something we have to do it quickly. But yeah for Mainnet will give you ample amount of time. We can assure that and once it is on Mumbai, people will anyway. Know that okay, one whatever is coming on Mumbai, most probably will hit the main it. And next couple of weeks, anyway, Yeah.

Jason Windawi: Okay, great. Thank you.

Krishna Upadhyaya: Okay, I just wanted to share one thought I just. So I'm really glad for all the external guys joining here. I mean, as a developer from the previous time this is I want to say thank you because as a developing team we are developing this solutions for you internally. And we, I'm really proud of my team for doing a great job idea. Always the external contribution and questions and suggestions and are always, welcome. Like we feel great as a team, you know, as a pos team. I personally feeling really good at that. Whatever number of people we have from the external. You know, other guys. Apart from polygon Here, I really appreciate your comments, your thoughts and yeah, what? If we can increase this going forward, that is a really good. What do you say motivation and feel good factor for our team as well. Thank you guys for joining like I mean I really appreciate you guys taking out your time and joining us to listen all you know, saying all those technical stuff.

Harry Rook: Awesome. Lovely words Krishna. Yeah, echo in that as well. Okay, so if there's no other questions then I think we can wrap it up a little bit early. Still got 20 minutes, so yeah, I think it's never questions. We can wrap it up.

Harry Rook:  In terms of the next call, it's gonna be on the 15th of June. And so, yeah, keep your eye on the Github Project Management folder and if you have any agenda points, That and feel free to add those in the next call.

Harry Rook:  Then yeah, kind of one final now I don't know if we have any validators on the call right now, but it's obviously there's quite a few hard folks in the in the pipeline potentially and you know, one of the things that's, you know, we're thinking about is obviously, like, which hard forks are going to be. you know, going together, which updates are going to be clubbed into, you know, one hard focus essentially And you know, in order to, you know, do that efficiently. We wanted to get some feedback from from validators about kind of what how they see the difficulty of implementing hard. Folks, on board and handle. From our point of view, seems as they'll handle hard forks, are kind of simpler easy to implement and maintain once they've been launched, But yeah. I mean, if there's any validators on a call, then feel free to weigh in. But if not I'll probably put something in the discord so you guys can can do like a though or kind of just leave your thoughts in there about which you think it's more difficult.

00:40:00

Harry Rook:  Okay, well yeah, I'll put something in the discord then for the validators. And yeah we can go from there. Awesome. Okay. Well, I think that's everything so yeah, we can wrap it up. As always, guys, thank you for joining. Appreciate you taking times out your day, to join this call and yeah, have a great rest of your week and we'll see you next month.

Krishna Upadhyaya: Thank you. Thank you.

Jackson Lewis: Thanks Harry.

Mateusz Rzeszowski: Thanks everyone.

Jason Windawi: Thank you.

Pratik Patil: Thank you.

Meeting ended after 00:42:13 👋
