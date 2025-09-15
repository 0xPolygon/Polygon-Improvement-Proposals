# Polygon Protocol Governance Call: Transcript

#### Attendees

Aakriti Jain, Aleksei, Derek Meyer, Devesh Gupta, Fireflies.ai Notetaker Flavian, Frisson, Harry Rook, Jackson Lewis, Jerome de Tychey, Junji Hashimoto, Justice Conder, Mariano Conti, Mateusz Rzeszowski, Michel Muniz, Mike Brucken, Mirella Guglielmi, Nicholas Cannon, Panagiotis Alexiou (Peter Alexiou), Parvez Shaikh, Quentin Chabert, Quentin de Beauchesne, Sandeep Sreenath, Scott Lilliston, Stephen Ross, Takasugu Murata, Tiago - Stakin, Vincent Taglia, WebDev StakeWorks, Will White, Yuling Ma.

# Transcript

**This editable transcript was computer generated and might contain errors. People can also change the text after it was created.**

**Harry Rook:** In terms of the agenda for today, it's fairly brief, but the main things are Agra folk related. So we had the test net roll out on the first of November and we've recently had the main net release patch with a mainnet block number countdown, which I can share for you in the chat. So, in terms of the main buckets for today, we're going to have some feedback from the Mumbai testing committee. And then we can discuss in more depth the plan for mainnet rollout. I'll just share the agenda in the chat and then I can hand it over to Proton gaming. I don't know if you guys are in the call yet, but if you are feel free to begin some of the feedback you've had from Agra test Network.

**Scott Lilliston:** Sure, we're here. Thanks Hope everyone is having a great Thursday Wherever You Are. Okay, so just going to summarize a lot of the feedback and they're just some of the steps that have that we've gone through with the Mumbai committee since the hard Fork the beta updates relative to that went the hard Fork went relatively smoothly leading up to it after the hard Fork. We had a couple of issues that came up and will briefly go through those. the hard Fork essentially a problem block faulty block was identified anybody that wants to keep score on that probably already known to everyone of call but that block seemed to be four one eight seven four zero six eight. community members all noted that there were issues with the century notes stalling or crashing out after that block some of the committee members saw Auto restarts for their Century which resolved the issues some not we can provide which at some point if that information is necessary. So the auto seemed to resolve it for majority of the committee members, but some that did not resolve the issue. So soon after that the root cause of the issues that were encountered by the notes after the hard Fork was identified that happened really quickly. Apparently smop code was enabled in bore version 1.1.0. And when beta 4 was released that disabled the opcode and that seemed to resolve almost everyone's issues with being out of sync on any of the crashes that occurred.
Once that upgrade was put in a place all the notes seem to come back into sync pretty quickly for all the committee members. Excuse me. After the hard Fork they were just a couple of issues noted some were known issues. But from the polygon team and some were just fixing that the community members needed to make on their own. So and the data Nexus team noticed that after the beta 4 update that Heimdall was not progressing with Milestones after the restart, but that seemed to be a known issue based on the feedback from parvez. 
from our standpoint with Docker. We experienced a Centry crash after the beta 4 upgrade but that appeared to be relative to container issue with Docker which and we eventually had to restart the container. And so that may not be an issue going forward. Also. Those are really the only two problematic. Things that popped up after that beta 4 was released and so far nothing else reported. We have had a restart.
After that for Docker, but otherwise, it seems the community members are experiencing pretty smooth go of it after that beta 4 release.
That's pretty much it. Also, just wanted to thank Parvez and the team, they communicated really quickly with the issues and the fixes and got that out to us really fast. Got everybody restarted and upgraded and it seems to be going very quickly so very much appreciated. Thanks to the team there.

**Harry Rook:** Yeah, thank you Scott. And shout out to the validator team for being so quick on that as well.
That's good then, let's move on then to mainnet release.
Sandeep, did you want to cover some of the plans around, main Network rollout block number? any kind of additional things that maybe Blocking roll out or any other thing that you would want to discuss on that front as well?

**Sandeep Sreenath:** So thanks yeah, nothing much. I think you covered pretty much everything. So like you mentioned the block number as well. We expect to hit that block around 29th of November. That's Wednesday. I'm not wrong. Yes and The releases out Go version 1.1.0. Again, we've tested it internally on a lot of nodes and looks pretty stable. so Scott was mentioning. I guess all the issues have been fixed which were fixed in the Mumbai rollout also have been taken into account. Yeah, so I think we are good for hard for 29th on the mainnet.

**Harry Rook:** Okay.

**Sandeep Sreenath:** Yeah, anything else you wanted me to cover Harry?

**Harry Rook:** No, no, I think unless there are any comments in feedback  in the call then we should be good to go.

**Parvez Shaikh:** As of now, we are at 30% have updated, So I would request everyone to upgrade their nodes. We will be sending out reminder number one. shortly

**Harry Rook:** Okay, cool. That's all good, then, so in terms of more forward looking things, what's included in Agra is essentially a change to EIP-1559. So PPGC-13 discussion will probably be more centered on PIP-25, which is essentially the change to the EIP 1559 burn and will be the main topic of discussion for the next call. So we've got a little bit of spare time. So if anyone's got any questions any other points, they want to bring up, then feel free.

**Will White:** Yes, I'll topic, but we put in the application form for the Mumbai Testnet Committee to see how we can go about joining the validator set there we've been active in main Network for a long time but really looking to put some more energy there. So anything we can do to kind of expedite that process will be supremely helpful.

**Harry Rook:** Yeah, absolutely. And Jackson is heading up.

**Jackson Lewis:** Yes has already filled it out. There's a list of people that have put up the hand and expressed their interest in joining. Currently. We only have the six slots available that are full and we're kind of managing them on a performance and social inclusion level. so far everyone is meeting the expectations of their agreement to join that Community with the rollout of Amoy the new testnet coming soon.
We are discussing the ability to potentially increase those numbers. So yeah, the people that have put up the hand and so they'd like to join that waitlist can potentially fill those slots when they open. So everyone that's expressed interest or will be in touch and if that does happen, but yeah, Julie noted will and there's a few other valid edges that have expressed their interest and personally would love to include you in the committee. So working on that at the moment will bring some updates when we have them.
Will White: Cool and alongside the committee itself. Is it possible for us to actually get some Mumbai testnet infrastructure included into the validator set. You mean to say to run a test net validator?

**Will White:** Yeah, yeah.

**Jackson Lewis:** If you want to reach out to parvez myself either on TG or just call we can have a discussion around it because again, there's the potential ability to increase the slot if it's required, but they're not just open and available to anyone that wants to jump on Mumbai also because that network still needs to have up time and adherence and in the past we have had validators that have joined and then used the test net node for a certain period of time for whatever testing they want to do and then kind of drop off and not really maintain this slot. I know that you guys wouldn't be in that drop me a message but it is something that we'd have to consider. So yeah again, it will probably happen around the same time as the committee potentially being able to expand as well on a more.

**Will White:** Okay, sounds good. Thanks Jackson.

**Harry Rook:** Okay, cool. Unless there's any other points or questions. I think we can wrap it up.
Question for you Jackson any news on the delivering State and transactions warnings?

**Jackson Lewis:** I think I'd handle that one to Sundeep Sandeep. Was that the log that was potentially going to be moved to debug level?

**Sandeep Sreenath:** Not sure I will have to check. I don't remember this one, but I remember having seen this message in our group, but will have to check and get back.

**Jackson Lewis:** We'll get back to you Stakeworks. I did actually take your snapshot yesterday and post it in a thread on us discussing a couple of things. So we'll make sure to get back to you before the end of the week.

**Harry Rook:** All thank you guys. The next call is on the 30th of November discussion points will most likely be around pip 25 and pip 19 as So, if you get time, please have another look through those and Prime yourself. And yeah, I believe this call will coincide with Agra main Network as well. So we'll be discussing that in some more detail. Again as always, guys thank you all for joining. And I'll see you on the next one.

**Meeting ended after 00:14:03 👋**

