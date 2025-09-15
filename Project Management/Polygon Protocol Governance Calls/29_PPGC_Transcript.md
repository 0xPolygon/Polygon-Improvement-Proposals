# Polygon Protocol Governance Call: Transcript

### Attendees
Andries Jansen, Angel Valkov, Ben Rodriguez, Christopher Von Hessert, Dimitri Nikolaros, Fireflies.ai Notetaker Bruno, Harry Rook, Harry Rook's Presentation, Joonkyo Kim, Liminal Notetaker, Marcello Ardizzone, Mateusz Rzeszowski, Michel Muniz, Nikhil Chaturvedi, Parvez Shaikh, Raneet Debnath, Sajal Agrawal, Sanket Saagar Karan, Simon Dosch, stream, Traversi Normandi, Vasanti Rode, WebDev StakeWorks

#### _This editable transcript was computer generated and might contain errors. People can also change the text after it was created._

Harry Rook: Okay, welcome everyone to PPGC number 29. in terms of the agenda for today, we've got a couple of buckets. So, the first being some upgrades to Polygon POS's system smart contracts.  So layer one contracts on Ethereum being pip 54 and also pip 57 and then we also have some points to cover on the Heimdoll side as well. So on the last couple calls we've been discussing There's a follow-up hard fork that we need to go over today called Daw and then the contents of that hard fork being pit 55 as well.

Harry Rook: So those are the two main buckets and then at the end we'll probably just briefly re recap on the STB followup which was the idea to introduce polygon feature request. So yeah we'll probably finish off with that. So without further ado first thing on the agenda system smart contract stuff. so we discussed on the last call about pit 54. Chris kind of went through the spec as well as Matias as well. so that's progressing kind of on the development side and we're now at a point where we could start to discuss a little bit more like the execution plan and how things are looking in terms of timeline as well. So yeah Chris, I don't know if you want to dive in there if you've got any updates.

Harry Rook: Yeah.

Christopher Von Hessert: Thanks Harry.

Christopher Von Hessert: Hi everybody. So as we discussed just to recap now this is regarding moving the current polygon posig that has been there for I don't know how many years definitely before I started at polygon into changing those ownerships to the new protocol council. that is a much needed change in order to improve the security, the stability and obviously the decentralization of the whole chain. we discussed a little bit about what this encompasses. It is actually a very simple change because the only thing that we really have to do is basically call a function to change the ownership of those contracts to be either the time lock or the emergency council.

Christopher Von Hessert: No, in the PIP itself, we have mapped all the different contracts and where do they have to go or who are their new owners. So, we have done all that in research work already. And right now, what we're going to start doing is preparing the payload to actually get this done. So, we plan to start preparing the payload next week for all the changes. No, it's a lot of changes, but it's always the same simple change. No.  So the mistake is going to be more on the operational side of things. if it ever happens, we're going to make sure it doesn't than on the complexity of the transaction itself. so our plan for doing this without making any mistakes is actually we're going to prepare the payload. Then we're actually going to create a fork. we're going to apply the payload to that fork of mainet.

Christopher Von Hessert: And then we're going to actually send the battery of huge amount of tests. So we're going to actually try to simulate mainet just if it was going. So everything that is going on in mainlet at the same time we're going to actually apply that to what is happening in the simulation in the fork that we created. And therefore with that trying to bring a little bit of reality to that fork and trying to understand hey has anything broken? Is there any problem?  Have we missed anything? No. that should probably run for a couple of days if not a couple of weeks to ensure that nothing is wrong. And if everything works out perfectly well, then we will actually start moving into the deployment or the proposing of the payload with the previous council and with the new council that needs to accept the roles. this will all be patched.

Christopher Von Hessert: So just I expect that this is only going to be one transaction for the protocol council that will include all the acceptance of the ownerships let's say like that and it's not going to have to be multiple ones but we will actually know that better when we have the payload prepared. yeah, we've also diagrammed a couple of the worst case scenarios or what could go wrong or what could be missing. And we feel that the only thing that could be missing is that we have missed ownership or a contract role. some were hidden in some of the contracts.

00:05:00

Christopher Von Hessert: We've absolutely checked everything but there could be something that is missing over there and obviously in case that happens we will always retain ownership of the previous multisig. the previous council will still be here. Part of the people that are in that council are in the new council. so it's more or less the same people and that multisig is not going to disappear. We're not going to close it.  is going to remain as it is in case that we need to make use because there is something that was missed or one role or transaction that was not included. So again, we still have a couple of days if not a couple or a couple of weeks actually until we actually will be executing this transaction but we want to start moving fast from now now on and really just get this done. it's a much needed upgrade to the security to centralization of the whole network and of Polygon itself.

Christopher Von Hessert: I don't know if anybody has any questions. Be happy to answer them.

Harry Rook: So, yeah, awesome. Thank you for the update, Christopher. I think one thing it might be worth touching on is that this upgrade basically bolts the POS chain into the new governance framework. So, the whole pillar two system smart contracts framework which involves token voting using delegates and dynamic quorums.  So yeah, that's another kind of angle to this. so it bolts our new governance framework into the POS chain which is obviously nice. so yeah, another thing to be aware of there. I don't know if anyone else has any thoughts they want to add. If not, we can move on to 57.

Harry Rook: Okay, yeah, pretty recent PIP. adds a very handy function to the migration contracts. I don't know if we have Simon on the call who's can walk us through kind of the spec a little bit, but yeah, I can see you're on, Simon. So, over to you, sir.

Simon Dosch: So this is quite a simple addition to the polygon migration contract. you can already use the normal migrate function to convert matic to pole here. we didn't have this in the initial implementation because we thought it might be used to scam people or have them somehow sign this function. so we thought it might be easier at the beginning to just have the migrate function where you can really only migrate matic to yourself. but unsurprisingly we got a lot of requests from some exchanges and from some other platforms to have this function as a convenience in the migration contract.

Simon Dosch: So with this you would be able to send matic and then the poll would be received by a different address than yourself and the recipient you can see in this function here. So that is pretty much it. I took the liberty of writing this into the bib and we're proposing to add this function into the polygon migration contract by upgrading it and bumping the version to it would be 1.2 I think.  That also means the event gets a little update to include the recipient and that is it. So pretty easy stuff but a nice addition to the migration contract I believe.

Harry Rook: Yeah, thanks yeah, I don't know if anyone has any questions. Just one thing I wanted to add there is that, it will be the protocol council that will need to ultimately execute this. So, if we have any council members on the call, yeah, we'll appraise you a little bit more down the line, but yeah, as Simon was saying, it's pretty lightweight but effective feature. So, yeah, haven't seen any push back today. yeah, thank you moving on then to the second bucket which is everything on the handle side. So yeah, on the previous call we kind of discussed this in some length about the joic hard fork which went out on a moy last year.

00:10:00

Harry Rook: so there were some issues observed on a mo so effectively some of the nodes were falling out of sync. and this requires an other upgrade to kind of rectify the issue that needs to be forked in again. So another hard fork. so yeah kind of want to clarify that there's two hard forks right? There's Jik and there's Jovic is the kind of seed randomness upgrade and then the day upgrade is a follow-up one to patch a kind of bug that was created there. so yeah, day will probably rolled out in a couple of weeks.

Harry Rook: I'll have more details on that after Renit's kind of gone through pip 55. But yeah, I just wanted to clarify that when these two go out on mainet they will be kind of labeled the JIC upgrades and not the DLO JIC upgrades. yeah, just to make that clear. it's just kind of because of the way the code needs to be labeled, we need to create two namings for the fork. So yeah, that's a little bit of context on Daw. I don't know if we have Ren on who's able to walk us through PIP 55, but yeah, if so, then over to you, Rene. Yeah.

Raneet Debnath: Yeah, Thanks, Harry, could you go to the PIP once?

Harry Rook: Do you want me to share screen on 55?

Raneet Debnath: Yeah. No, you can just directly go to the PIP.

Harry Rook: Okay.

Raneet Debnath: Yeah. Yeah, thanks for the intro, so I think we briefly touched upon this on the last edition of the PPGC.

Raneet Debnath: so to recap essentially the YIC hard fork was rolled out in Amoy and we saw a couple of issues occurring on some external nodes where we found the root cause to be a slight nondeterministic bug in the span committing logic to be precise and that was because so with the YOIC hardwork amended the parameters required to propose a span being a range of board blocks which would be produced eventually.

Raneet Debnath: and the seed essentially of that span message would be used to essentially determine the list of validators who would be the producers for those range of blocks. and that seed would be a bore block essentially.  So when committing a span on one node, if that node's bore process fell out of sync or were to crash or something were to happen, something non-deterministic were to happen, it would fail to validate that span and would essentially produce an inconsistent application state and hence I mean it would break the consensus.

Raneet Debnath: So we figured out the fix would be rather simple and that would be just amending the span message transaction type to also include the seed author which is needed at one stage of committing the span and we could validate that statelessly.  So without having to commit to the state database, we could validate the seed and the seed authorizing the block producer of that block which is being used as the seed.

Raneet Debnath: So yeah, that's essentially the core of this 55 which is a hard fork inducing change which will go with the day in law and yeah on ammo we are planning to roll it out exactly one week from today.  So that would be 23rd of Jan and some point in midFeb I think so as Harry already mentioned earlier yvic and dane law on mainet would go on the same block.

00:15:00

Raneet Debnath: So these both changes would be activated on the same block on mainet but on a moy yeah as your hardpoke has been activated we would have to do this on a higher block number for den law. So yeah that's mostly it. I'm open for questions if any. Thank you.

Harry Rook: Yeah, thank you just looking on the forum for the ips. here we go. Yes. So, if anyone has any longer form thoughts, particularly validators, then feel free to take a look and let us know what you think. if there's no questions there, then, we are 90% through the agenda, so it's going to be a fairly quick call. so yeah, the next thing on the agenda is just a small item. obviously we touched on the last call about the whole STB situation and then a piece of feedback that was received from that the name prepip makes it almost feel as though it's on some pre-ordained path to being executed at some point.

Harry Rook: which we kind of felt was a bit of a mismatch to what the authors intended with that proposal. So yeah what we've done is basically followed up with something we're calling polygon feature requests.  So in the future if anyone has any things they want to suggest from a highle perspective about the polygon possal things low-level things or just feature requests then yeah this provides a template and just a little bit of guidance that you can either take or leave to try and help formulate your thoughts

Harry Rook: and yeah, just kind of assist you of in doing that type of thing. So yeah, that's out on the forum. So yeah, if you're interested, then feel free to take a look. that's all I have on the agenda for today. I don't know if anyone else has any updates they want to add. yeah, anyone on the POS team, contracts team, security team?  If there's anything else, then yeah, I guess we've got a little bit of time, then feel free to jump in. But if not, we can probably wrap it up. All in that case, thank you everyone for joining. the next call is on February the 13th. So, yeah, all be well. I'll see you guys there.

Harry Rook: Thanks again for joining the call.

Meeting ended after 00:18:42 👋

This editable transcript was computer generated and might contain errors. People can also change the text after it was created.
