# Polygon Protocol Governance Call: Transcript

### Attendees
Arpit Temani, atlas staking, Chase Mitchell, David Silverman, Dhairya Sethi, Dimitri Nikolaros, Fireflies.ai Notetaker Parth, Harry Rook, Jerome de Tychey, Joonkyo Kim, Jordan Hagan, Krzysztof Urbański, Manav Darji, Mark Holt, Matt, Milen Filatov, Mirella Guglielmi, Nimit Bavishi, Parvez Shaikh, Rahil's MeetGeek Notetaker, read.ai meeting notes, Sajal Agrawal, Sandeep Sreenath, Scott Lilliston, stream, Tiago - Stakin, Vasanti Rode, Viktor Bunin, WebDev StakeWorks

#### _This editable transcript was computer generated and might contain errors. People can also change the text after it was created._

Harry Rook: Welcome everyone to ppgc number 20 in terms of the agenda for today. It's actually a pretty brief one. So I don't anticipate this call to be overly long. We're going to have another call in two weeks. So yeah, there's quite some topics to discuss around the 2.0 transition ZK POS and the staking migration, And we'll be having an over call in two weeks. This call will primarily focus around Amenabad hardfork so There's been some developments on the L1 side around 3074. Obviously last call, we spent some time on holding in on the spec for we're kind of subject to changes on L1. There's been developments there. So we're going to feed that through.

Harry Rook: in today's call So yeah Ahmedabad will be the main focus. There's also some points to cover around pip 25, so we discussed this some time ago. I believe it's October last year and PPGC number nine so pretty trivial upgrade to the

Harry Rook: POL migrator contracts basically calling a burn function to previously burn MATIC via EIP-1559. So we're ready to execute that. So we're just going to cover some of the final points on today's call and then last of all we'll cover the Heimdall release version v1.0.6 which went out last week. So PIP-37 that defines the amount about spec like I was saying before there's been some developments on L1 with regards to 3074 and there's also been some developments on our side of things which I think mean that we're going to have to push this. perhaps a couple of weeks or maybe a bit longer but Yeah, Sandeep. I don't know if you want to.

Harry Rook: If you have any further updates on that front.

David Silverman: I can jump in Sandeep if you're not audible right now. We can't hear you.

Sandeep Sreenath: Here and I already will know.

Harry Rook: Yes, yeah.

Sandeep Sreenath: Thanks I think as Harry briefly touched upon it. So in the last ppgc we were trying to come up with or finalize the scope of Ahmedabad Hartford and we were aiming to release it probably sometime in early June but a lot of things have changed since then so there's a new EIP-7702 sure a few might have already gone through it. yeah, which is with changes the Dynamics a bit and we are also what waiting and seeing what happens in ethereum. So it looks like many people are in favor of 7702 and I think the decision has been made already.

Sandeep Sreenath: Or ethereum for the L1 that they will be dropping through 0.4. Knowing failure of sons a little too. So that's a big change because the Ahmedabad hard Fork had one of the main changes was 3074. Yeah, so with that, not going to even we feel that, even to its better we go with sons and you probably can add more about it. But yeah apart from

Sandeep Sreenath: Apart from this. We also had a few other Pips pip 30, which was increasing the max code size 36 that's change in the state receiver Genesis contract which fixes a buck in the states in mechanism and also gives an option to read right some of the failed States in transactions. and we also had 35 which basically puts 30 gray fixes 30 way in the paper call or in the client because of non uniform ux that's currently there are due to differences in conflict and validated level.

00:05:00

Sandeep Sreenath: yeah, so I think this is a larger point. I think it's also an open, question. So do we proceed with hard food without 30749, including the astrology changes.

Sandeep Sreenath: Yeah, I think apart from this. Maybe I'll quickly cover sync issues also, which we are facing in both since the last couple of weeks. So there were few reports where notes would sing till the tip of the chain and suddenly they stop thinking and only after restarting, or in a lot of cases after a few minutes suddenly starts thinking so this is something that's not very widespread. We have also encountered it on a few of our notes. But yeah and a few Partners have actually encountered the same issue. So the investigation is still going on. We are trying to figure out and once we know that I think the fix Wheels we place more but we have some

Sandeep Sreenath: Leads that the team is working on. Yeah, I think this is what I wanted to cover and open to questions on hard for and might even those Communications.

Sandeep Sreenath: I'm not sure. if this is related to the sink issue, but yeah, I think is there a GitHub issue already against this?

Dimitri Nikolaros: Sorry, what did you ask me?

Sandeep Sreenath: I mean this has been raised as an issue in GitHub already or has it been communicated?

Dimitri Nikolaros: No. I think we spoke some of the people in the Okay,…

Sajal Agrawal: Yep. I can.

Dimitri Nikolaros: I think part of his responded. Yeah.

Sajal Agrawal: So basically this was reported internally and these appeared to be a communication issue between bore and Heimdall since I mean, all these settings were fixed and we restarted to ensure that this doesn't happen again and I think Dimitri test working fine at the moment, correct.

Dimitri Nikolaros: Everything is working fine. This only happened last week on the validator first and…

Sajal Agrawal: Yeah.

Sajal Agrawal: Yeah.

Dimitri Nikolaros: then two later on one and then a few days later on another Century on a different DC. These are really strong bare metal servers. It's never happened before so There's nothing obvious in the logs. It's very weird. I just rewind it to about 65,000 blocks. Instantly and…

Sajal Agrawal: Yes.

Dimitri Nikolaros: and there was no restart needed it just and then you just resynced on its own. I spoke to other validators some have had decisions in the past. It only happened to them once. So s one of those it's a rare thing. I'm not sure so we're just monitoring for now.

Sajal Agrawal: Yeah, okay.

Sandeep Sreenath: Yeah, maybe sajal if it happens again do let us know and Thanks.

Sajal Agrawal: Yeah, yeah, definitely.

Harry Rook: Okay, Thank you guys. yeah, I mean, but I meant about I guess we're faced with a couple options. So

Harry Rook: we can either deploy sooner without 7702 and yeah, I don't know what The appetite for the option is 7702. Looking at some of the recent PRS and I've been spoken to manava about it. It seems like it could be fairly premature to kind of wait on a timeline from that front. So yeah, I don't know. If it would be wise to wait for the move on L1.

00:10:00

Harry Rook: Yeah as we can.

Sandeep Sreenath: Yeah, I would say let's wait for some more time because it's still very decent stages. So yeah, having the eib was actually

Sandeep Sreenath: less than a month ago, so

Sandeep Sreenath: I guess David was saying that the specs are also still being finalized. I'm not sure if you have an update on that.

David Silverman: Yeah, I propose that we wait until the speck around 7702 is finalized from the last ACD it seemed as if it was going to be one to two more weeks originally wait for the next ACD but I know that polygon Labs Engineers as well as several other ecosystem teams are obviously a broader ethereum Community. Once we have an implementation, we'll make sure it gets into before and Aragon polygon and be able to consider moving forward then yeah, Our recommendation is definitely waiting to include this in the hard Fork as Mark key feature.

Harry Rook: The next goes in two weeks. So hopefully, those dates line up nicely.

Harry Rook: Okay.

Harry Rook: I never points on I meant about guys. If not, we can move on. Yeah, manav's just links the latest PR in the chat there if you want to take a look.

Harry Rook: Okay, cool, so moving on then next point in the agenda is PIP 25, so yeah as I mentioned before this is something there was proposed October of last year fairly trivial change. So it just brings the pole Supply in line with the mic that was burned previously under Erp 1559. So there's a burn function within that migrator contract to burn the amount of poll which was confirmed in an earlier hard folk David. I don't know if there's any updates on that front. I know we've kind of discussed this quite a few times now which ready for execution. But yeah, it's one if there's any final thoughts and

David Silverman: Yeah, just I guess walk through a reminder of what this pip is doing is the migrator contract to let you go from attic to pull what's set up an initialized at the full original the full original supply of manic, which was 10 billion. There is a number of Matic that has been burned primarily going to addresses 0x0000 dead as well as just the token address itself. This pip is proposing to burn that amount of mattock. what that number of Matic is of worth of poll out of the migrator contract representing what the actual Final kind of supply of Matic ended up being so just kind of initial equalizing the two the two.

David Silverman: that calculation is in the pimp and it basically looks at the mattock balances of those two addresses 0x0000 dead and the mattock token contract itself and then proposes burning that number of pull out of the migrator.

Harry Rook: Yeah, So to clarify that poll isn't accessible right unless it's upgraded or something of that nature. So for that reason, it's a fairly tal. upgrade in terms of security or to economics or anything of that nature, so

Harry Rook: Yes more of a hygiene type upgrades. Yeah, I don't know if there's any updates in terms of the transaction payload when executed or sorry been prepared.

Harry Rook: Yeah, I think we're pretty much ready to go on that from.

Harry Rook: Okay, no problem. Yeah, so in terms of next steps here then so. the payload has been produced I think security just kind of doing a funnel once over on that one. Like I was saying before it's a pretty trivial upgrade. So In terms of next steps. This will be put forward to the protocol council members. So put forward probably by modit who's on the council will then execute the initial phase and push the upgrade into the time lock period so that's 10 days. in which if there's significant pushback then the council can essentially opt to not execute it at the end of the time right so

00:15:00

Harry Rook: Those are the next steps in terms of a final formality. There'll be a transparency report on the Forum like the wars with the previous transaction, which kind of details kind of all the individual moving parts and how they're going to be affected by the payload itself.

Harry Rook: Yeah, but aside for that. I think we're good to go. Unless there's only any other. Points or feedback or anyone have any reservations then? Yeah, we should be good to go.

Harry Rook: Okay, I can only be a good thing. Next on the agenda then so Handel really spursion 106 this was a book fix emergency roll out. Which happened late last week? I don't know if we have arpit on the call to how we do to just do a quick run through kind of what happened there. And yeah how things are looking post the upgrade.

Arpit Temani: Sure. So yeah, really got one of the reports on the Arabic boundary program. Why one of the hackers? And so according to the report. In a certain scenario, Milestone could be posted as a message on instead of for checkpoint. So that was the bug. So basically after investigation by the team we found out that this is possible, but that was a very very rare case scenario when certain conditions like you need to make sure that the start block of my stone and start block of checkpoint.

Arpit Temani: Needs to coincide and they should be no other Milestone proposed at that particular point of time and certain conditions where basically we need to match the boards in ID Etc. And also the Milestone ideal needs to be spoiled in such a way that it resembles the checkpoint message. So that was the kind of work which a malicious validator could send on the L1 and get consensus on it. So that was the bug. So when we did not test some playing around with David and realize that this is possible, but in a very very case scenario, so we kind of fixed it via removing that particular message which can be so basically what happens is that there is a function called get site signed by switch is signed.

Arpit Temani: Every validator on a milestone message and that particular message can be spoofed. So we kind of removed that particular message which no longer will be signed by anyone and we cannot get any kind of spoofing or any kind of signatures on a particular message. So yeah, that was the fix we made we also kind of put some hard checks around the Milestone ID…

Harry Rook: Thank you Cool. Yeah, any other thoughts on that guys?

00:20:00

Arpit Temani: because in the bug Dimitri could be spooked not in the order not in the format which we were using. So yeah, we put some heart attacks around that so to make sure that nobody…

Harry Rook: Okay, cool.

Arpit Temani: who can send random my son ID, it should be generated by you again proposal format. So yeah, that was the bug fix which we made a released and since release we didn't see any shoes with anything and we have also done some internal testing to make sure that this does not like this is no longer the case and…

Harry Rook: Yeah, so that wraps up the agenda like I was saying earlier in the course, I'm pretty sure one this week,…

Arpit Temani: it can spoof the checkpoint message and…

Harry Rook: but there's another scheduled on June the 13th. So in two weeks time, that one I think will be pretty packed.

Arpit Temani: pass in my stream data into that.

Harry Rook: There'll be a lot of focus around 2.0 and…

Arpit Temani: So yeah, everything looks good.

Harry Rook: zkPOS, so it should be Pretty stacked agenda. So yeah pretty excited for that one. since the agenda is done. Yeah, is there any other questions or comments guys? If not, we can wrap it up.

Harry Rook: Okay, appreciate you guys taking the time to join We're on different time zone. So yeah, appreciate you guys just taking the time and yeah, I'll see you guys on the next call. Thank you very much.

Meeting ended after 00:22:07 👋
