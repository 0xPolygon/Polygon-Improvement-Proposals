# Polygon Protocol Governance Call: Transcript

### Attendees

Ankit Singh, Ben Rodriguez, Bitfalls -, Can, Chase Mitchell, David Silverman, Derek Meyer, Dimitri Nikolaros, Fireflies.ai Notetaker Bruno, Fireflies.ai Notetaker Can, Harry Rook, Harry Rook's Presentation, Joonkyo Kim, Kristofer Peterson, Liminal Notetaker, Mariano Conti, Mark Holt, Matt, Michael Mugno, Michel Muniz, Mihailo Bjelic, Mike Brucken, Mirella Guglielmi, Nimit Bavishi, Parvez Shaikh, Paul Drennan, Paul Gebheim, Pete Kim, Sajal Agrawal, Sandeep Sreenath, Sanket Saagar Karan, Simon Dosch, staking 4all, Stream, Vasanti Rode, Vincent Taglia, WebDev StakeWorks, Антон Гаев

#### _This editable transcript was computer generated and might contain errors. People can also change the text after it was created._

Sandeep Sreenath: Alright, so starting with another word hard Fork. as you all know there were a few changes recently because We had initially included EIP-3074 and then we decided against it in favor of eib san02 and obviously it's going to take some time for the specs are almost Frozen button to have a referencing presentation and for certain development. It's going to take some time. That's the reason we are moving ahead with only the remaining changes which is 30 and 36 30 increases the max code size limit to 32 KB from the current 24 KB and

Sandeep Sreenath: To 36, gives the feature to replay failed States in transactions. and also fixes a small fixism but in handling State Singh transactions in the A2 side, which is present currently even and Cholerator when the address is monoi mattress, so those are the two major changes. I think apart from that there's also the 45 which has been proposed recently and

Sandeep Sreenath: there is a possibility that we might add that in another thought still pending discussion. Yeah, thanks, Yeah, I think these are the major updates on Amazon hard Fork.

Sandeep Sreenath: Do also want me to cover the v1.3.4 release today?

Harry Rook: yeah, I mean we can get it to show one final thing on a man about if we're including pip 45, which is we'll go into that in a bit more detail. I think Simon's on the colony. He's the Opera that one but did 45 essentially changes the ticker, but that requires a hard Fork, right? So Yeah, I'm not sure. how the coordination will look between the September 4th migration and also that ticket change that may be included in the emed about hard folks. So, I don't know if we're likely to see a men of our potentially early September or if there's any more thinking around the timelines.

Sandeep Sreenath: Yeah, I think respect to the timelines. It's only pending, the decisions whether we are going ahead with 45. Otherwise, we pretty much have everything in place even 45 and pretty sure Simon and can correct me if I'm wrong that it won't take much time for the implementation or testing. It's probably. operating discussion

Simon Dosch: yeah, no, it's definitely more of political Community question then hard programming issue to solve. It's just renaming a couple of lines.

Simon Dosch: Yes, David.

David Silverman: Yeah key question. I also want to bring up for the community is if we are going to be renaming tickers Beyond just the Actual MRC 20 formatic is it worth us also hard forking to rename wmatic to w pole in the canonical wmatic contract. I would love that one.

Simon Dosch: Yeah, I put that into the pimp for so now it's open for discussion. I'd say In my opinion, it would be very convenient to do this and people I think would expect that to happen and not just thematic. But also the mathematic would just change the name, but of course, this is a significant.

Simon Dosch: Thing to do and to touch that by code via hard Fork so we would only do it if we have a strong consensus there.

Harry Rook: Really? Okay. Thanks Simon. I mean we can come back onto the details and a little bit but that all sounds fine. So yeah, I mean

00:05:00

Harry Rook: So next on the agenda then is the ball release version 1 3 4 Sandeep. I don't know if you have any thoughts on this. I know we just rode it out pretty recently but I don't know if there's any preliminary findings. I think we've got the mites that committee on the course so perhaps they can share some of their findings as well.

Harry Rook: Okay a nice smooth upgrade. That's always good news. Thank you very much Vincent, and thanks parvez as Awesome. Yeah, I don't know if anyone else on the cause any points. They want to read the latest roll out. If not, we can probably move on.

Harry Rook: Perfect So third I am in the agenda then is the mike to Paul transition. So yeah, let me link the blog But yeah, I mean, we released some comments on this recently. The date was scheduled for the whole transition is September 4th. I think we've got David on the call who's able to discuss the specific. So that's pit 42 pit 19. And then Simon I think it's gonna go into a bit more detail on pit 45, which is the ticket change. We just mentioned before so Yeah, David over to you, sir.

David Silverman: Yeah, so the tentative date and again looking for consensus on this call is on September 4th to execute this Suite of Pips. The plan would be that on the fourth. We will set in a specific time probably be a new UTC the multi protocol Council will execute and upgrade on the L1 side such that we make staking changes as described and pit 42 which essentially is a fully backwards compatible shift to pull staking what this means is the unified staking preserve will be upgraded to pull if you are using the existing staking methods today that will not change those will still work. Your Matic will be converted to Paul using the migration contract and when you try to withdraw your Rewards or principle, you will still receive mattock also via the migration contract new endpoints will be enable that will allow you to stay poll directly validators and dele.

00:10:00

David Silverman: Leaders can have both pole and mattic stake to them. Again. It's represented through the unified pole token in the back end. This means that existing staking pools lsts are in affected existing exchange Integrations with staking or unaffected. However, it basically has mattock becoming a wrapped form of pole going forward. Additionally. There will be an upgrade executed against the bridge on the POS plasma Bridge, which will make it so that when you attempt withdraw the gas token from POS, you will receive poll instead of Matic effectively changing the gas token of POS to poll. If you try to bridge mattock over this bridge from ethereum to POS. It will auto convert using the upgrade contract to poll and you will receive the exact same gas token. So we would execute those together via the protocol Council and ideally the 43

David Silverman: A hard Fork on the bore client which changes the ticker will be executed around the same time. However, there is no requirement for that to be done.

David Silverman: A couple of side effects to mention for all protocols or could 45. Thank you Simon for all protocols existing inside of POS. There will not be a noticeable change. So all liquidity pools that are currently denominated against wmatic ratic directly will automatically essentially be upgraded to poll as they are using the gas token, which is kind of being a possibly upgraded as well as a lending markets defy Etc. The only thing will be affected again is aetherium D5 and again because mattek is being unaffected on the ethereum side. There's no breaking change on that front. So this is the most backwards compatible way that we can go about this change and the way in which we are, strongly recommending going about it a big thank you to the hundreds of community members that have reached out and spoken to provided feedback on this implementation. So just a big thank you to everyone in the community. Again. The tentative date that we have been talking to Partners about and partners have been collaborators have been suggesting.

David Silverman: Paul's has been September 4th for the mainnet configuration. This configuration already live on a moi and so there's a strong recommendation to test your systems as you can notice a moistan no issues value or still have stake valid are still moving alongside. I don't think any of them actually upgraded to using pull. However, we've tested the poll staking endpoints directly. And so we are pleased with the rollout there. again we leave it to individual staking pools and exchanges to talk about their levels of support however again because it is fully backwards compatible the best of our ability we feel relatively confident with this date as indicated.

David Silverman: Again, it kind of a call out for the community at this point. Is there concerns with that data the September 4th date and if so, I guess Any other changes folks would like to see me to those Pips at this point?

David Silverman: or questions folks have

David Silverman: Yeah, Pete.

David Silverman: I cannot speak to any individual exchange as to their strategy. However, I know exchanges have been in contact with members of the polygonne community and our perceiving accordingly again recommendation is for folks to reach out to individual exchanges. However, as far as we are aware as polygon Labs that there is a level of Readiness that will be achieved.

David Silverman: Yep, Mirella.

David Silverman: So yeah, so there's no change the rewards flow at all whatsoever. Did the differences again, the master Awards will continue to be accumulating in poll. However, if you continue using your existing endpoints, you'll receive mattock. if you switch to using the Poland points oversee poll under the hood, everything will be admitted and pull there's no change to the amount being distributed nor the AP wire or anything else. All calculations are proceeding as normal. This is exclusively kind of an infrastructure level change.

00:15:00

David Silverman: I think I'll make sure the governance team puts together a metapip similar how we did with Frontier. We'll put together a metapip that kind of combines all of these into some form of a more easy readable document. But as of now, I think the pit numbers are I want to say 19 and a few others 19 and…

Simon Dosch: Yes. Thanks Harry to add to…

David Silverman: 42 are the relevant ones here and…

Simon Dosch: what David was saying.

David Silverman: then 45…

Simon Dosch: We also got 41…

David Silverman: if the community is aligned on the ticker one.

Simon Dosch: which is the last piece of the puzzle there or…

David Silverman: 

Simon Dosch: maybe the second to last which is changing the fact that we're sending mattock instead of Paul still from the mission manager.

David Silverman: Awesome Harry back to you.

Simon Dosch: So that was decided to also do together with all these other upgrades on September 4th. That's possible and that means the stake manager will receive Paul obviously and cinematic from that point on and then let's get to 45 which that is in my opinion the last piece of the puzzle which is missing. Because we upgrading all these staking contracts and the bridge and all these reserves get upgraded. And basically we want to get to a point where people only expect Paul to be used in all the polygon ecosystem. So when I Bridge my poll to the polygonom POS chain, I would expect

Simon Dosch: some token named Paul to show up in my wallet, But because this is a Genesis non-upgradable contract sitting at this 1010 address.

Simon Dosch: 45 I'm suggesting that we would. Upgrade and change these two lines basically so that the name and symbol function would return polygon ecosystem token instead of Matic token and PLL instead of Matic as the ticker.

Simon Dosch: then additionally there's this repatic token, which we could also rename again basically for convenience because it's what people would expect. in the blog post there was also a mention of people basically not having to do anything and they're matching would be converted to Paul I think. Honestly, if that's what we're saying if that's what people are expecting then. At least as a user would expect the ticket to change. So that's why we have this and happy to receive feedback and questions on that.

Simon Dosch: Yeah feels like a brainer. It comes with some risk, of course, So there's probably going to be some front ends some systems that will stumble over this and that might have some functionality relying on the return value of these functions in our opinion, anyone who uses this in some smart contract instead of the contract address. Probably made a bad decisions somewhere. So just changing the name. I think should not have any disastrous conscious consequences for any, smart contracts on that chain, but of course it will have some consequences for other systems. We don't know. Who rode some UI or something which relies?

Simon Dosch: I include the change because it was just another place where we could get rid of the term Matic just to avoid confusing it in the future, but I understand that it is a significant change if you call the transfer Zig function, basically you have to adapt and use this new signature the new domain basically, so If there's a strong opposition to that that's not a hill.

David Silverman: The big one I would say is that permit signatures because we're changing the domain would expire and…

00:20:00

Simon Dosch: I'm dying on that's fine.

David Silverman: need to be redone unless we don't change the domain. that's the one implementation detail. I think that we're still in discussion with several developers about I know that there is a discussion ongoing so contract developers.

Harry Rook: Okay, cool.

David Silverman: Would love to hear from you on this front.

Harry Rook: And then I think one detailed we should kind of Point attention to is 45 being included in a metabod. I suppose that you're formally proposing that it be included at this point Simon or…

David Silverman: Yeah.

Harry Rook: you do not yet feel comfortable enough from?

Simon Dosch: Can you come again there?

Harry Rook: So, the whole idea of pit 45 and the ticketing been included in their metabod for you comfortable proposing that at this point or would you rub away until there's more clarity on some of the risks. So yeah.

Simon Dosch: I mean We've done some research and we think. there's some risk involvement. I would argue that it's worth taking it. so I'm definitely suggesting that to be included and in the best case scenario implemented, together with all the other changes in September, so At least from my side IPI we can see that.

Harry Rook: awesome

Simon Dosch: Yeah.

Simon Dosch: That's definitely true. So. if you've already cached the Ticker ones and your system, doesn't always which makes sense update the ticker or read it from the blockchain at all anymore. Then probably you'd have to somehow update it or people would have to

Simon Dosch: Reset the RPC in their wallets Etc. I think there was also mentioned in the blog post even.

Simon Dosch: Yeah. Yeah, there was basically the main reason we changed it.

Simon Dosch: yes it

Pete Kim: Yeah, we could potentially also accept both the mains in the pyramid function if we were to upgrade the implementation.

Simon Dosch: So both the Old and the new one you mean?

David Silverman: As I am aware.

Pete Kim: Yeah. It might actually be an option…

David Silverman: There's going to be a big push from dabble in the other devrel teams in the ecosystem to work with applications.

Pete Kim: but I haven't thought of the security implications of that.

David Silverman: And the other kind of long tail changes need to be made to be updated. So definitely something that I know folks are aware of Derek and things out that are dressing also addressing Pete Kim's comment in chat on updating domain definitely agree due to the Dynamics. Yeah.

Harry Rook: Okay, perfect. Yeah, I think that wraps up pick 45 any of the comments. Feel free to leave them in the Forum post for pick 45. I think Simon's gonna be dropping in a few hours or So yeah keep you out for that one.

Harry Rook: Okay moving on then final point is the polanium grades and the review feature requests David. I don't know if you want to take this one. I'm happy to share screen and walk through all.

David Silverman: Yeah, definitely something we should have some of the security team members across the ecosystem look into. great suggestions

Harry Rook: Yeah.

David Silverman: Yeah.

David Silverman: Yeah, yeah, you want to share screen. the two there's a couple big ones and a lot are being addressed via some of the other Pips such as connection to the AG layer of yellow Excel. Why? Pete's here. Okay your hand raised anything else you wanted to hit on that one. no. Good. So yeah, I'm planning the polygon is gonna be the hard Fork after I metabod and a lot of quietly the suggestions that we saw in here was connection to the AG layer updates to the cosmos modules to bring some compliance with a modern comic bft Etc. And I think these are In progress and covered by other Pips. So I know those earn progress several of the other ones that were on here was a ed25519 key scrolled a bit more. Yeah. It was the ed2559.

00:25:00

David Silverman: 825519 precompile that came from some of the developers over at Walla connect as well as bringing certain things like path DB which I believe is coming with a pbss over on the bore client two interesting ones to flag that we definitely love to see Community feedback first was a request for partial unstaking I know that the team is looking into this as the next staking level upgrade coming post this migration. So definitely on the next validate around table of things going to be a discussion of what that implementation looks like from. Some folks are looking into it when that's been a very large request and something that definitely we want to ensure gets brought in especially in conjunction with expansion of the validator set as well as I believe there's been

David Silverman: Session of increasing minimum stake. So those are all I would say that that's definitely a prerequirement. The other one has been a request that has come in from several different defy teams to discuss staking elements of the bridge. This is in line with things that we've seen across the ecosystem particularly pioneered by both the ZK evm and blast. This is definitely something that has a decent amount of risk potentially being introduced and so very much strongly encourage folks to read the proposals from teams such as yarn and angle, I believe a few others have posted on here and centrifuge. Definitely something we want to hear Community feedback from I think there is a few teams working on a proper pip a general call in general just to remind folks that

David Silverman: please please suggest new features here in pilani this planning thread. They do not need to be full formal Pips. These can be simply ideas and changes you want to see on the POS Network broadly.

David Silverman: If there's any of the folks want to raise on this call directly happy to talk about them. another one that I know has come up. I would say the interface…

Harry Rook: session

David Silverman: interface I don't know…

David Silverman: interface suggestions. I think a bit out of scope of what this call is for. This call is for changes to the various contracts validator clients Etc explicitly for POS as for an interface, I would say that and I can dress that for there's already been Integrations if it's explicitly on the story but it's been heard several times in feedback is a methods on reducing reorgs. We instituted Milestones was our first attempt at reducing that and adding some form of additional finality post the heimdel 2.0 upgrade. I know there's active lines of research on fully removing reorg by moving sequencing to heimdel entirely which would provide I think pretty close to two second finality if we're able to move it down to single slot. However, that's still an open line of research. I think in the next ppgc we're going to have a report in from some of the teams looking into that. But again, if there's any features or proposals the folks wish to raise at this point definitely would love to open the floor. On the ethereum side with all of the major Dex aggregators. So if you were to use services like house swap or one inch or Fiber swap or zero X, they all have the migration contract directly coded in as a route. And so this is me speaking as an individual and not my capacity is polygon Labs. I would definitely suggest generally using Dex aggregators as they will give you best execution and that will look at Is there a potential Arab through liquidity pools or is the best path directly through the migration contract. So there's no Direct eui.

David Silverman: interface…

David Silverman: 

David Silverman: That polygon Labs is offering as far as I am aware.

David Silverman: most of the upgrades are going to happen passively. So by moving mattock into the bridge, you're automatically being migrated over again, the unified Staker service migrating over those two alone as part of the September 4th upgrade which we should probably put a name to I'm gonna call it the poll migration that should migrate in excess of 50% of the supply and we expect it to continue to go pretty strongly down that path as exchanges and custody Partners upgrade as well. I think we have a yeah.

00:30:00

David Silverman: Again, I can't speak to any individual exchange. I know the ones that hold the largest amount are going to be supporting a migration. But I defer to them for their own timelines and what that support looks like Etc don't want to speak to anyone strategy airplanes.

David Silverman: It Derek appears you just threw in there. This is rewards destination address. Is this the idea that you would like to clean your stake rewards at a different address than the address you staked from?

David Silverman: Got It definitely something I will add into the research guilds info

David Silverman: Partial Awards ESP. I think we spoke about that that's been a huge request. I think it's the next item that the validation folks are gonna be this next valuator upgrade. We're gonna be looking at post migration migration is taking a ton of bandwidth. any other suggestions folks want to raise I guess we have this. spot on the call Or go to the Forum. I understand it's definitely a intimidating speaking is always difficult. I prefer the form myself. As it's a requirement for a lot of other things that I think have been kicking around in the community.

David Silverman: 

David Silverman: 

David Silverman: Actually just a quickly. Dig on this further is the saying partial withdrawal for the Rewards or for the principle.

David Silverman: As I know we've request for partial withdraw the principal. for the reward so it's like you have If I'm a delegator and I've earned I don't know. Let's say a thousand pole. I should be able to specify that. I don't want the full thousand. I just want A hundred or…

Harry Rook: Okay, awesome.

David Silverman: Got it. Thank you. Definitely something we'll add Into the list as that should be able to be done alongside.

Harry Rook: Yeah, I never

David Silverman: Yeah, that's a great suggestion.

Harry Rook: Okay, cool.

David Silverman: All right Harry back to you.

Harry Rook: Awesome, Yeah, I mean that wraps up the agenda. I don't know if anyone has any other I never threads they want to bring up. If not, we can probably wrap it up finished.

Harry Rook: next call would be August 22nd. So we'll be giving up very much for the migration and also potentially the amount about heart folks. So, yeah, hopefully see you guys there. Thank you all for tuning in and over on some different time zones, and it's a lot of efforts to join this call So, yeah. Thank you all very much and I'll see you on the next call guys.

Meeting ended after 00:34:00 👋
