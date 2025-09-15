# Polygon Protocol Governance Call: Transcript

### Attendees
Alex PFL, Bella Park, Bojana Tomic, Fireflies.ai Notetaker Michel, George Serntedakis, Harry Rook, Harry Rook's Presentation, Jackson Lewis, Jessie Park, Krishna Upadhyaya, Kwangsung Park, Manav Darji, Mateusz Rzeszowski, Meeting Note Taker, Michel Muniz, Mihailo Bjelic, Nimit Bavishi, Paul O'Leary, Peter Stewart, Pratik Patil, Pratik Patil's Presentation, Sandeep Sreenath, Sergey Bachurin, staking 4all, sw's Otter.ai, Tanisha Katara, Vaibhav Jindal, Vaibhav Jindal's Presentation, Vedant Patel, Volodymyr Hnativ, Yuling Ma

# Transcript

#### **This editable transcript was computer generated and might contain errors.**

Jackson Lewis: Welcome everyone, to this second edition of the Polygon Builder session. For those who weren't present on the first meeting, my name is Jackson. I work a polygon labs invalidator engagement, and these builder sessions are set up to be public and communal forums for us to discuss on chain protocol changes. And ultimately move, you know, Pre-pip discussion through a presentation discussion with the community through to a finalized PIP and ultimately in in the process of these meetings discuss research that's being conducted proposals that have come to the forefront and, and move towards the social consensus, prior to protocol level changes being formalized. That being said this evening, there will be a few people presenting. As I mentioned, some personals Likewise some updates from the last couple of months on chain.
Jackson Lewis:  With that being said, I'd love to hand over to Harry and he'll run us through this evening's agenda.

Harry Rook: Thank you, Jackson. So yeah, first of all, thank you guys for joining. I know a lot of you are on different time zones, so appreciate you making the effort to join. So yeah, I've just shared the agenda in in the chart. So the main things we're going to cover today are kind of a recap and overview of the pit framework and how you was community. Members can raise agenda points for these calls in the future. So you know, say if you've created your own PIP and you want to discussion one of these calls then we'll show you how to do that today. And secondly, the Pip bounty program, which is just launched today.
Harry Rook:  In which polygon labs will be rewarded in PIP offers for kind of meaningful. Impactful changes and proposed by Pips. Then we'll discuss an update to the kind of schedule of these meetings up till now, there's not been a kind of set dates between each call. But going forward, there'll be every four weeks. And then following that we'll discuss the the real that occurred lately, February, so Sandeep and the POS team. We'll be discussing that kind of doing a deep brief on what happened. And then we'll be moving into some of the proposed solutions that have kind of been generated within the team. And yeah, most lately PIP 10, which is increasing confirmations.
Harry Rook:  And then finally, we'll be discussing the Milestones proposal, which again will be sandeep and team. Yeah. Without further ado let's move on to kind of an overview of a pips. I mean, What are these calls all about, right? I mean these calls are about community involvement in the kind of progression and development of, the, of the pos chain. And so, They're they're part of the pit framework in essence, so with open source development, going back to the kind of early days of Python. the way in which the kind of decentralized project management is is coordinated, is through kind of a document framework, where you have like PPS, like Python enhancement, proposals bits, you know, bitcoin improvement proposals and the like and

Harry Rook:  Basically there are way for ideas to go, from kind of rough ideas and then kind of you draft, a formal PIP and then that gets peer reviewed over time. And as it gets refined and people start to believe in it a little bit more. You know. Building consensus around those pips and we discussed the pips on these calls basically. So this is where we open, open discussion's synchronously, where normally on the forum, it's by comments, which can take some time. So this is a good space to kind of talk things through face-to-face. So yeah, let's jump into the pit one. Which I'll link in the In the chat and I'll also put in the show notes for people watching on YouTube.
Harry Rook:  So yeah, Pick one which, you know, if you're going to be enough of a PIP, then you should probably read this document and that basically specifies how to put together a PIP. What should it include? You know what the four main standards. And yeah, just the general areas that you should cover within your PIP. And so as an extension to to pick one, we have PIP A which is more of an advisory implementation documents. So what we've done in Pepe is outlined all the different areas to implement a change on the POS networks. So

Harry Rook:  You know, in terms of the, the pips we're going to be discussing today. They they fall under the core category. So, you know, they're kind of client implementations to Bor and Heimdalll. And so within Pepe, it kind of details how that process works generally. I mean, I'll give you a brief overview now. It works by a roof consensus. I know we've got a couple of validators on the call, but, you know, when, if we kind of think of the story of a pit from start to finish, imagine it starts as a, an idea on the forum which then if you offers get together, crafted pit like a formal document that specifies it You then post the PIP on the forum and upload it to Github, where it kind of stays in a version repository as a source of truth in the future. And then yeah, you're not that pip as seeking peer review. at that point, you know, that's when you're trying to refine the idea and build consensus around it on these calls

Harry Rook:  Yeah, once the pips final and there's enough, you know, consensus within the validate community in the community at large that, you know, the changes ready and it's something that the community wants then, you know, any change to Boren handle or any kind of core. A change is done, virus consensus. And this is basically via upgradation of Of clients by validators full nodes and you know rpcs etc.
Harry Rook:  Yeah. That's kind of a quick overview of how Pips work if you've got any questions shoot me a message my emails in the Github Project Management folder which I'll link for you guys. So if you ever want to ask any questions, Just reach out and be more than happy to kind of work with you guys.

Harry Rook:  So yeah, finally. In terms of the the PIP framework, when you are, you know, crafting a PIP, I know we can seem a little bit daunting and that is why myself and my colleague matase. So our job is to basically guide you through the process and help you craft a PIP, explain to you. You know what steps Anita to take it forwards and yeah that's kind of our role. So we're there to help you with like the grammar grammatical side of things and also just Help understand the process of all. Cool. So moving on from that, let me just do a quick demo for you guys on how to raise agenda points in the future. So It's pretty simple.

Harry Rook:  If you can see my screen, which I hope you can, then I'll just show you quickly.

Harry Rook:  yeah, if you go into the polygon improvement, proposals github repo and go into project management, There's a subfolder here called Agenda. And by the way, I recommend reading all this as well, it's really helpful information to kind of guide you through the process. And as you can see here, we've got the agenda for for this call. But for example, the next call, which is in four weeks, it's currently empty. So, you know, if you wanted to add An agenda item so you know you craft the next pit 11. You want us to talk about it on the next call? Then you just add it in here. And yeah, I happily merge.

Harry Rook:  Cool. Awesome. So let me just move on to the Pitbank room. So, yeah, big news today. So, you know, we've released a incentive program for PIP offers. And yeah, essentially we are with allocated the, the sum of 40k over the next year to incentivize pip offers. And yeah, you know, the guiding rule with, with these incentives is that the more complex and impactful the change. The more the kind of reward will be proportionally. So, Yeah, these rewards are going to be paid out. Quarterly. And yeah I mean well basically look back retrospectively see what's been impactful you know see what the big pips are and then reward those appropriately.
Harry Rook:  And yeah, let me link a blog post to the Hip Incentives program so you guys can find out more information. Awesome. Okay. So that wraps up everything from my side and yeah moving on I'm gonna hand it over to Sandeep and team for a debrief on the real that happened late in in February. So yeah Sandeep over to you.

Sandeep Sreenath: You know that thanks Eddie we might be members Community. Krishna will be Talking about that and also the RFCs that we have for our solution. We're going place.

Pratik Patil: Okay, let me share my screen, as well as the link to the PIP here in the chart.

Harry Rook: And that's when the Pratik.

Pratik Patil:  Can you can you explain?

Harry Rook:  No.

Krishna Upadhyaya: Not yet.

Harry Rook:  There we go. You can see now.
Pratik Patil: Okay, awesome. Yeah. So Thank you. Yeah so here I'll discuss Point Four and point five both as one so because they are dependent on each other I'll first Share a brief context. Then explain why they all happened. We are happened. And then as clear, the state that we are taking to make sure that this won't happen again, Yes. So as we know like in bore at the start of every screen in face, Stacy transactions from him doll.

Pratik Patil:  Now himdal to send the states in transaction, it leads to parameters. The first one is the from ID which is a unique incremental ID for, for that particular query and state. And the second one is and now what this, what board tells him down is that you give me the states in that has happened. After from this particular spacing ID, is this particular time? So himdal in response to this query will return all the states saying that has happened in this duration and then board will commit this. Board will commit these transactions in the block, which is the first block in that particular screen, and then commit that blog or basically.
Pratik Patil:  Yeah, so here. Thank you, notices. How we calculate the value of two. So as I mentioned from is a unique ID which is incremental. So basically this last id plus why but the value of two it basically a timestamp and it is calculated, it is a timestamp of a blog. And this block is basically the current block which is the first block of that particular sprint, minus the sprint length. So let's say right now we are at block number 32 assuming that we have print length of size 16. So this 32 is the first block of the third sprint. Now at this particular moment the

Pratik Patil:  Two value will be the timestamp timestamp of 32 minus 16, which is 16th block. So, two value will be the timestamp of and block, Sorry, 16 block and the bore features this particular block from, its local database. Right now in case of three odds, where to hooks are running in parallel, what happens is as this folks are like not connected to each other. They mind their own blocks separately. So the timestamp of particular block can be different. Like let's say 30 second block or 33rd block all four. A will not be at the exact same time on the floor. Be so,

Pratik Patil:  This like this, this is normal thing, this is not a big deal. It happens like like in everyday are where the timestamp of a particular block is different on both forks. Okay, so this is how we calculate the value of 2. Now, what happens is, like, now we go back to the incident which took place where they we all serve this deeply on.
Pratik Patil:  Now, add this particular block number, we got like a big reorg of 157 block and nodes, where through, and this 157 blocks is basically greater than the sprint length, which is 16 at the moment. Now, at this time, what was happening, is that the nodes where throwing bad block error and this bad blockader was because there was a mismatch in the state of the coming blocks and local blocks right now upon for their digging into this. We found out that this was actually because of different values into where what was happening is that let's say right now let's say there are two force which are whose length is greater than a sprint length? Now when this work merges, what happens is that, let's say for a

Pratik Patil:  Yeah, he is a he is an example. Now let's say there are two forks running for a and four B, and like, at a particular block, which is the first block of a particular sprint. There has been two staking fetched from blog in, for 4k, from its name, dial. And please take from the same doll. Now, this will happen because the range that we mentioned here, could be shorter for for a and therefore, for me, which allowed one extra state sink to to be included from the query, which board like from the query from board to him that therefore him doll in for be returned one more thing than hey, that in 4k. Now, let's say after some time

Pratik Patil:  Be much just back to 4k. Now, in this case, what will happen is that B will try to re-execute all the blocks that has taken place when it was on the other. It was on the other quote. Now, when doing so, when it comes to that particular block, basically the first block of a straight office print that it has missed. It will try to query from its same down the number of state sync from like for that particular id and two. Now as this block which it is trying to import is from work, a the value of two in that particular fort will be different, which will return to states in transactions to bore from him, rather than three now. Because
Pratik Patil:  Be like, no. Because so let's say four cup happens at block number Let's say 30. Now in, in both, cases block ad block number 32, when it fetches, the states in transaction and calculates that to the value of 2 will be same because 16 block is has the same time stamping work for A and for me, but when it goes to next

Pratik Patil:  Next spring start, which is 48th block, it will go back to block number 32. Now, add this point both timestamp in, like both blocks 32 in 4k and block 32 in 4, we will have different timestamp and hence that value. The value of will be different. And therefore it will, there will be more. What about state sync in for b? Now during merging. Like when this must happens back, it will try to be will try to get that particular block from its local database. As I said earlier, this n is calculated from its local database and not the chain. It is important. So it will. Again, get the value of two which has resulted in three states think, but the block 38 second block in A and the timestamp which gives back to state things

Pratik Patil:  So this is where the problem has happened and therefore a node B, will throw a bad block error because the importing chain has two staking transactions. But when it try to relieve it, it is getting presentation transactions promise from its same down. Now, this has like this probability has increased after the daily hard for because for this to happen, the rook should be at least greater than the number of the length of sprint and in Delhi hard for you. The sprint length from 64 to 16 and therefore, the probability of this event, happening has increased Now, we cannot like we don't want to change the screen.
Pratik Patil:  To like solve this because Sprint reducing sprint, length has helped us in different aspects. So what we have to do is we have to make sure that the value of two that we calculate on both forks are the same. now that is why what we are doing is that instead of, instead of

Pratik Patil:  Going a sprint. Length behind to calculate the value of 2. We are going bigger amount back that is we are proposing to go back around 128 blocks so that this big reward, the charges are to be this much big are less than, right? Even like this has this reward is 157, length longer, but if this bad block thing would not have occurred. It wouldn't have like gone till 150 so 157, blocks.

Pratik Patil:  Now what we are doing is that we are adding a multiplier, which will multiply with this particular sprint length, so that the block N is around 128 blocks behind the current block, so that when importing a chain, the block, the end block will be same across the across both the fork. So that the timestamp we take from that particular block are same. In both forks, a, and B. And this won't result in a bad block because the number of state syncs return will be same and equal.

Pratik Patil:  No. Yeah this is basically how we are going to implement that. We'll modify the Genesis file with that particular multiplier. And as we just have a sprint length, like as we right now, go a sprint length, back the multiplier from 08 Block till the hard work will be well and once the hard work it's that multiplier will be eight. So that eight into 16, will be 128 blocks and the value of 2 will be same across the force given that York's length is less than 128, which it will be once like because in future, we are going to have Like, you know, improvements so that the Dior doesn't go this much bigger.

Pratik Patil:  Now the current implementation is that we go back change blocks and take the timestamp of that particular block. And the proposed changes to fits that particular. The proposal is to add a multiplier in front of that and multiply it with the sprint length. And for this, we face the multiplayer for at the current block from the genesis And then you multiply that multiplier with this print length and get the timestamp of that particular block. And here, the current sprint, length is session and the proposed value of that multiplier is 8 so that we can get 128 block as the value. Now this is pretty significant, like this is really safe change. There are no security threats.

Pratik Patil:  But the only thing that will happen is the states in transactions will be delayed because right now we have we just go back. 16 blocks, but you will go back 128 blocks after this hard work and thus, it will be delayed by 252 seconds. Assuming that the average block time is 2.25 or polygonquist.

Pratik Patil:  Now, as this PIP is not backward compatible, and therefore, this will require a hard fork and if in the future we again change the sprint length from like 16 to maybe 8 or 32. Then again, we'll need to consider changing this multiply because like we want to keep this difference to a safer number. And therefore, we might also have to consider changing the states in multiplayer, which is not, which will not be that hard because we just have to add an extra line here corresponding to the heart and multiply

Pratik Patil:  So this is how we planning to solve this particular issue. We are still. We are also thinking apparently about other issues, where we don't have to depend on a particular, on a timestamp of a particular block and on, but again you will hear more from us in the near future. Thank you. If you have any questions, feel free to ask.

Krishna Upadhyaya: Yeah, that was a lot of information.

Krishna Upadhyaya:  So, yeah, if anybody has any kind of question any clarifications they need any detail more detail. They are seeking. We are here to answer.

Jackson Lewis: Alex posed, a question in the chat. If practical or Krishna can you give an example of how this delayed state sync? Transactions might manifest

Krishna Upadhyaya: Sorry. Yeah. Okay so this delays see considering the fact that even before the daily heart folk our the confirmation gap was 64 anyway with the sprint line being 64. So yeah, this was like, you know, since we reduce the sprinkle in size the confirmations might have got a bit quicker but now to, you know, reduce the uncertainty of, you know, the Rio's and you know, this this thing which recently happened where a very big Rio happen and you know, this is kind of a worse situation for network.

Krishna Upadhyaya:  I still think like three to four assuming 2.25 seconds and a deal of 4 minutes. Um, I don't think it will be very significant or comparing to the benefits, which we will getting. Basically, you know, we will not have this kind of situation in future. And yeah, with milestones coming in which Sandeep will take over in a minute. I think that thing will. Anyway, come down, like, we will have more finality and we can leave maybe, you know, one once we have finality on the pos chain we can reduce this value again you know back to normal like maybe 64 again. And yeah, the delay might be up just to keep it safe for now. I think this much delay we have to play with it.

Krishna Upadhyaya: Sorry, if I have nothing to the question. I think that's what.

Krishna Upadhyaya: Show our, you know, appear, what do you say to get executed on the polygon side, you know? So wherever if you have 
transferred, some us DC, let's in simple terms of and whatever. So, yeah, they will. Like, right now they are coming, let's say in 20 minutes, so they will take 24 to 25 minutes, that's it.

Krishna Upadhyaya:  Okay, any other question, please let us know. Otherwise we can go ahead with the milestones.

Mihailo Bjelic: To the pos chain itself, right?

Krishna Upadhyaya: Yes, yes, only those. The incoming was not the outgoing.

Mihailo Bjelic:  and I, And I believe those are currently happening much faster than 20 minutes, right?

Krishna Upadhyaya: I'm just giving an example like X number.

Mihailo Bjelic: But can we get the current set? I think we're basically doubling at least the waiting time. At least. I would say that, if not even more

Krishna Upadhyaya: No, I don't think we are increasing the four minutes. I don't think they are have happening in four minutes as of now because the ebook time on ethereum is itself. 18 minutes the finale.
Mihailo Bjelic: Can we take that, can we take include include in the PIP itself to to understand how much it is, impacting the current user experience?

Krishna Upadhyaya: So, so we we can find out the current average time and let's see how much for how much percentage. Yes. And we

Sandeep Sreenath: Yeah, so I just wanted to come in there. I mean, we had done this analysis, quite some time back, regarding how long it takes for the transactions. The states in transactions, to be related from the changing ethereum to four. Yeah. Also, I mean, some of the additional things that you take care of or consider here is that we also wait for a certain number of block confirmations on the ethereum chain on L1 before we consider it as final obviously. Now, after the emerged we are using
Sandeep Sreenath: The finalized API, but I don't have the exact numbers, right? So, so we wait for finalization on the L1 chain. So, that is, you know, I think a significant amount of time and only after that, you know, we actually start pulling for these events in the month, you know, which, which then comes to India layer. And then this, additionally, you know, for every sprint. Plus, plus the, you know, the delay that pratika and krishna out of more basically, which we have increased. So overall I would say, You know, like the this addition of four to five minutes,…

Pratik Patil: Guys.

Sandeep Sreenath: you know, is on top of the already existing 10 to 12 minute delay. But yeah, in halo, we will definitely work out the numbers after the lines. What's what's the current situation?

Mihailo Bjelic: Okay, great.

Krishna Upadhyaya: Yes. So basically to 20 minutes, I said includes the 30 minutes 13 minutes of ethereum finality. I mean with 64 blocks and 12 seconds, 13 minutes itself is taken on ethereum and after those 13 minutes, 5 to 6 minutes is taker between bridge, you know, then himdal, then the sprint length. So yeah, approximately takes around 18 to 20 if I'm not, normally I will come up with exact numbers for sure. And I add into the same VIP.

Mihailo Bjelic: Great, thanks for Clarifications.

Krishna Upadhyaya: Okay.

Harry Rook: Awesome. Okay. Well thank you guys. Thank you Dick and Krishna for a great explanation of the Pip and Sandeep as well. So yeah, let's move on to the Milestones proposal and I think Sandeep you're gonna take this one.

Sandeep Sreenath: Yeah. Yeah, presentation.

Vaibhav Jindal: Oh yes. Just one minute.

Sandeep Sreenath:  Yeah in short, I'm not sure how many of you have already gone through the Forum post. The People post that's out there. So what we intend to do with milestones is, you know, we try we are trying to bring deterministic finally, on the POS chain, you know, similar to what Etherium offers the final year. So we have the same and

Vaibhav Jindal: Oh yes. Hi everyone. So as we all know, in our present system, we use the probabilistic finality. So what do what do I mean by the probabilistic finality? so we don't have any conformity, we can't That particular block is final all the transaction inside, this are final. We just wait for 256 blocks. To be assured. That 256 blocks have passed. It means that Block, which is less than 256. From the tip is almost finance. And but to bring the deterministic finality, like ethereum just to say that this particular block is final, we are working on the milestone. And I just want to show you how technical this milestone will work. So, what will happen? As you can see, this is

Vaibhav Jindal: All one of the validator in hindal will propose, the will propose the milestone using the bore block data. And pass it on the hemdahl network. And before going forward, I just want to show you the milestone structure. As you can see this, the milestone structure it has the start block. It has the and block, it has a hash of the Android. Data is gathered by the proposal validator proposer. Validator, who is a proposal from the board?

Vaibhav Jindal:  And after passing this milestone sector to the handle network. All the others notes of the network, in Himdal will receive that. Milestone structure will check their local board. Whether the hash, as you can see this hash, used in the milestone is, Or not, whether it matches with their local board chain or not. Match with the local board chain. The validator what? Yes. And if 2, by 3 plus 1, validator by stake, not by number by stake, if 2, by 3 plus 1 will later by stay. What? Yes for the milestone. That means, that milestone is final that hash is now final and the block corresponding to that. Hash, as you can see, we are also having the end block. this and block corresponding to the hash is final now,

Vaibhav Jindal:  And now himdal store that milestone in the now, or him does not store that milestone in its locker DB

Vaibhav Jindal:  and now comes up out of Handel board interaction. So bore will continually keep on fetching, the hem dull give me the latest milestone. So more give the latest milestone and bore also keep it in its DB. And now bore will use this milestone. While checking for the imported chain. So what happens or the bore not keeps on importing the chain block chain from its peer? And how it will ensure that chain is correct. It can use. Now the milestone. That if that chain matches the milestone data, that means that changes correct. And but here's comes. The main usage of the milestone. so, as I have told, as I told you here, That this and block gets final.

Vaibhav Jindal:  so now the user Can ask the board. Give me the latest finalized blow. And now, both can give the Have stored milestone block. Let us store milestone and block. because, And block is final. It is not going to change.

Vaibhav Jindal:  And how it is going to help our users. Or let me present one, the result of the milestone. Previously. We used to assume that in 256 blocks, it will be final. So they previously used to take

Vaibhav Jindal:  To assume that block is final, transactions are final. But now, but now the milestone we can say in just 35 blocks. Or we can say in 80 to 90 seconds, we can say, this particular transaction is final, it is not going to reward back. So, with milestone feature, our main With the files of future, our main point is to bring the determinist, finally improve the user experience on our polygon chain.

Vaibhav Jindal:  Bringing out bringing down the transaction finality. Time by more from more than 500 to less than 100 seconds. Yeah. Out over this.

Sandeep Sreenath: Thanks thanks. Hello for the explanation. I would also like to add a couple of points. So we are I mean I'm sure all of you are aware of the checkpointing look. So you can think of milestones as a form of checkpoint onto the hindusting. And since Hindal has or offers instant finality, we are basically using the layer as source of and You know, and

Sandeep Sreenath:  Depending on it for the finality as well in getting some respect. Right? So yeah. So I think that that's going to be a very easy not being for a lot of you guys because you already know that checkpoint how checkpointing works. And obviously the difference is that we checkpoint to the L1 chain and instead of that milestone will be written on to the same value.

Sandeep Sreenath:  Yeah. Let's open the questions.

Krishna Upadhyaya: Yes, limited.

Harry Rook: Go for it.

Krishna Upadhyaya: And limitations.

Krishna Upadhyaya:  Oh, okay. Is that emoji and hand icon being side-by-side?

Harry Rook: Yeah.

Krishna Upadhyaya: Google, please update on others as well. I know Google, you understand.

Sandeep Sreenath: What?

Krishna Upadhyaya: okay, I think You know versus that's it. So yeah basically

Krishna Upadhyaya:  It is updated in some of the I mean some for some people for me, the hand icon and the emoji are separated. Now I have the present button in between like left side, I have the emoji then the present now and then the hand item.

Krishna Upadhyaya:  So yeah, we already have it. Maybe they are rolling it out. If you

Harry Rook: Awesome. Okay. We've got a little bit of time left. So Yeah, I mean if any of you guys have got any other questions, like if you've got any questions about Milestones free Alex. Please jump in, go for it. We've been waiting.

Paul O'Leary: Go ahead, Alex.

Krishna Upadhyaya: If you're speaking, you are not able to hear.

Harry Rook: As.

Paul O'Leary: he pushed the right button, but still

Krishna Upadhyaya: Okay, yeah, type it. If it's smaller like it, please.

Harry Rook: Mm-hmm. Yes. Good question Alex. So

Harry Rook:  Yeah, I mean the so the way in which there's no votes for. So I mean if you talk about bugs in relation to like the core implementations of like born Heimdall, Then there's no votes really? There's the way things get implemented is by roof consensus. Using my head stake, right? So there's no actual votes. But that is the kind of the vote de facto, but

Harry Rook:  yeah, if there's a book fix then it wouldn't need to go through the Tip process because you know if you imagine that there's a book that could be exploited and then you know you put that in a pit that's like a public document then It wouldn't be great upset practice, so yeah. Things like that I won't be pips. Yeah. They'll be kind of emergency fixes which will be implemented through you know, client upgrades by by the validators. So Yeah.

Harry Rook:  Awesome. Cool. Okay.

Harry Rook:  So I think that wraps everything up in terms of the agenda, we've still got a couple minutes so if any of you guys have got any questions and feel free to ask. But if not, I think we can probably wrap up a little bit early.

Krishna Upadhyaya: And just have one suggestion to the. Like whoever is present with bead polygon, or from outside polygon. So, yeah. And as Harry said, the objective of this initiative is to involvement external guys, the community as well and as as we experiences right off. So if you guys have any suggestions, if it is a, I know we are these pips are awesome, but yeah. There's always room to serve for improvements and all. So if you guys have any suggestions on these tips or any suggestions, what we just said there, you know, this is the way we are trying to solve the real or the finality. We know the problems that we have with the POS things, you know, with the POS architecture. So, if you guys have any other suggestions that, okay, this is how you can improve this or make it better or make it less complex, please. You know, we will be very happy to hear it out. Obviously, in the longer run. Not, I'm not saying just right now,

Harry Rook: Beautifully. Put Krishna?

Krishna Upadhyaya:  thank you.

Harry Rook: Yeah. We've had another question from Nimit. This one's probably for you guys Sandeep and Krishna about milestones. So milestones are voted on by two first date plus one any reason? Why not By the absolute number.

Sandeep Sreenath: Okay, because this is because, you know, we are using endurement for the consensus here. And yeah, which is a VFD consensus and two by three plus one for all the other candidates following the same, Automation system. I mean, it's the same with checkpointing as well.

Paul O'Leary: Yeah, and you can't always guarantee liveness, you can't guarantee that all the votes will be available at all time. And you can't help the protocol just because some people don't get their votes in. So that's kind of one of the bases of two by three plus one as well.

Harry Rook:  Okay.

Sandeep Sreenath: yeah, so Jackson regarding Explorer or dashboards. I mean, this is more of I would say an internal, You know, data Scripture but yeah, we can you can always it will be available to APIs who involvements. Ment if required, we can build something. Similar to our beautiful checkpoints about staking work for example.

Harry Rook:  Perfect. Okay, well, if there's no more question, then no more points. Anyone wants to raise then I think we can wrap it up.

Harry Rook:  Awesome. Okay. Well, thank you guys very much for your time. As I mentioned before, these would be every four weeks now the same time. Yeah. And if you have any other questions feel free to shoot me an email or a message and yeah happy to help you out with those.

Harry Rook:  Awesome. Okay guys. Take

Krishna Upadhyaya: Okay, thank you. Thanks everyone.

Paul O'Leary: Thanks everybody.

Vaibhav Jindal: Thank you, everyone.

Meeting ended after 02:01:19 👋
