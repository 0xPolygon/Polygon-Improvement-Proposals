# Polygon Protocol Governance Call: Transcript

### Attendees

Adam Dossa, Andries StakeWorks, Angel Valkov, Carlos Juarez, Christopher Von Hessert, Dimitri Nikolaros, Fireflies.ai Notetaker Bruno, Harry Rook, Harry Rook's Presentation, Joonkyo Kim, Kaitlin Beegle, Krzysztof Urbański, Liminal Notetaker, Marcello Ardizzone, Mariano Conti, Mark Holt, Mena's Notetaker, Mirella Guglielmi, Nikhil Chaturvedi, Pratik Sanjay Patil, Sandeep Sreenath, Stream, Tiago - Stakin, Traversi Normandi

# Transcript

#### This editable transcript was computer generated and might contain errors. People can also change the text after it was created.

Harry Rook: So, welcome everyone to PPDC number 31. in terms of the agenda today, there are two main buckets really. So, for a little bit of wider context, there's a lot of things in flight at the minute. both on the B and Heel side. So, there's a pretty big refactoring of Heindel that we discussed today. that's been in the works for some months now. So, we've got some updates with regards to that.  I think he's an at audit stage. so we'll discuss the playbook for the actual migration itself to handle V2 which is quite a complicated migration involving some downtime of the bridge. So yeah we have Marcelo on the call who's going to do a high level run through of that and then there'll be some more details to follow on the forum. and as well as that we have an upcoming hard fork on board so it'll be called Bilai. I hope I pronounced that correctly Sandy.

Harry Rook: but that's going to include a lot of the compatible pectra inclusions. and then as well as that some other kind of gas improvements and optimizations as well that are kind of more specific to Polygon POS.  So, yeah, without further ado, I know you're in the airport right now, Marcelo, but I'm going to hand it over to you, and if you want to do a run of Handle V2, I'm happy to share screen and we can run through some of the details there.

Marcello Ardizzone: Thank you Yeah, as I said, apologies for the background noise. I'm at the airport. So, I hope you enjoy the music at least. So, yeah, we got a quick update from the protocol team right on the MW2 migration.  So at the moment the development is completed and we are currently working on adapting bore so it can run without depending on end which means that your ball will continue producing blocks even while angle is being upgraded and also we're making bore compatible with both version of handle so it won't experience any pose during the transition phase and same of course with solo forgon soon after and in parallel the handle V2

Marcello Ardizzone: So then its dependencies like Cosmos and Comet are undergoing the security audit which we expect to wrap up end of April. Yeah. And to support the approval as Sud mentioned we have developed a migration script and the runbook and the purpose of the script is basically to walk the operator step by step through the upgrade process and it includes stopping and even though it should be stopped already because we are leveraging the H type in the binary itself.  Then it will export the states from handle v1 u backing everything up and handle v2 with all the migrated configuration and then patching the system to be ready for v2 in it. it also includes some roll back capabilities safety check validation at each stage.

Marcello Ardizzone: and we have a roll back strategy under development in case something goes wrong it will be another E1 version without basically the hot tight restriction which will basically force V1 to keep running after the temporary stop. and we also written this detailed guide for operators who may want to follow the process manually because maybe on custom environments the script may not be usable out of the box like anti Docker or Windows even though by some estimation we should be supporting 85% of validators currently.

Marcello Ardizzone: and yeah a quick note on the downtime right during the migration as already mentioned the handle bridge will go kind of offline which means that no intrachain messaging will happen during that window however this is important bend and as I said before will continue to run so the polygon chain will not experience any downtime or halt in block productions only the crosschain operation will be posted that we're doing our best to ensure the downtime is minimal at the moment we are testing the scripts and the process on nets. Soon we will do the same on Mumbai which we have internalized for this purpose because the huge state it hit old is a great test for the migration and then we're focusing on optimization streamlining the step making the process as efficient as possible basically. the script will be signed and checked for your information.

Marcello Ardizzone: So every node operators can verify if they're using bumper proof version and we will be using some official channels for communication still to be defined but yeah it's going to be I guess a combination of following post and the variable itself and maybe one final point to note is that once the migration is complete MV1 blocks history will be gone so unless someone like RPC providers explicitly keeps it around for our panel purposes basically

00:05:00

Marcello Ardizzone: yeah with regards to timeline by end of April as I said we should have an audit completion we are testing on deet and we will be performing a Mumbai migration test by end of May if everything goes fine we are planning for ammo and then a tentative for June end of June I think for main unless delays surface during this testing or audit response time yeah that is basically  it from my site at P2 to take any question.

Harry Rook: Thanks, Marcelo. I just wanted to add, so for those that aren't aware, there's an offsite for a lot of the Polygon developers next week. so in terms of when the runbook's going to be published, I'm assuming it'll be in a couple of weeks time, Because it's a pretty beefy document and I'm sure there's probably still some dependencies we're not fully we don't have in hand yet. So yeah, just wondering a couple of weeks, is that a reasonable timeline or will it be a little bit kind of further down the road?

Marcello Ardizzone: It also depends on the audit reports right I would say two weeks is reasonable but we need to wait for the end of April reports from the auditing company in case we need to make any adjustment.

Harry Rook: Okay, so yeah, that will probably be going out on the forum.

Harry Rook: so yeah, keep your eyes peeled for Any validators listening. and if you have any questions, yeah, feel free to jump in on the forum you can comment under also under the handle V2 PIP as well. So yeah, if there's no questions on that, I think we can move on. so there are a couple of things on the bore side. let me share this tab. So pip 63 this kind of specs out the inclusions. a lot of it's downstream from So the Petra upgrade that's scheduled on Ethereum. so there's a lot of kind of compatible and incompatible things within that upgrade.

Harry Rook: if you check out pip 61, it just has all the compatible things that we'll be integrating within our own client. and it also has the other two pips as well. I don't know Sandep if you want to do a high level run through of this kind of timelines and how things are looking there as well. and then we can go into the change in pip 60 afterwards.

Sandeep Sreenath: Yeah, hey everyone. So, yeah, Bilai hard fork. like Harry was saying, this is the next board hard fork and we plan to release, all the changes, PCRAPs or most of the PCRAPs that we've been working on. so this is still under development currently where we are getting the changes from merging the code from upstream kit and there are a few inclusions I think you can Harry if you can go into the 61 just if we want to cover the inclusions. Yeah.

Sandeep Sreenath: So yeah, we'll be enabling EIP 2537 that's ompiled for VLS 12381 curve operations 29357623 and the biggest one 7702. the others the second section you see that they have been included but they will not be activated or supported within the working client implementation. that includes CIP 7549, 7840 and 7691. and yeah, the third section which you see is it's not really relevant to Polygon POS because those are mostly to do with the consensus here of Ethereum. so yeah if you can go back Harry. Yeah so those are the spectra IPs.

00:10:00

Sandeep Sreenath: the other PIP pip that we have which will go live with the BI hard fork is pip 58 where we are adjusting one of the EIP59 parameters to smoothen the gas spikes when there is very high network activity. so currently I think around two years or three years ago after we implemented EIP1559 on POSOS there was one hard fork where we also increase the base fee change denominator from 8 to 16. So yeah this is already a well tested out change.

Sandeep Sreenath: so there's not too much u work to be done on that so we are actually increasing it to 64 now so that the rate of change of base fee is lesser when the blocks are full or even when the blocks are empty.  So basically what this does is the increase also becomes slower and the rate of decrease also becomes slower. So yeah that's a doublesided thing. So currently I think it increases by 6.25% and after this change it's slightly over one and a half%. So because it's four times right the denominator.

Sandeep Sreenath: So yeah that's the major change. U I think this would give a lot of relief to some of the DAPs which have been affected when there is very high network activity. so it's not going to affect the users too much. it takes a much longer time to probably become 2x or 4x so on.

Sandeep Sreenath: but I think earlier it used to within a few hundred blocks we could see thousand% increase right so it won't be as drastic as it is currently yeah I think if you can go back Harry so yeah the last one which is included is pip 60 so we are basically just some background on this. we have been working on a lot of impactful changes over the last year or year and a half. I think all of you are also aware of the blockm or parallel EVM work that we have done and also PBSS the part-based storage scheme which I think most of the validators have already rolled out and some of them are still in the process.

Sandeep Sreenath: yeah and we also added a few features which did not require a hard work like commit interrupt which ensures that transactions which are taking too long are preempted and the block is propagated so that we maintain the block time of 2 seconds.  So with all of these improvements that we have made over last couple of years we also did some experimentation with increasing the gas limit and we are confident that we can easily increase it by 50% without putting too much load on the network or on the infra. so yeah that's the big change here. so we are increasing the block as node from 30 million to 45 million.

Sandeep Sreenath: yeah I guess this number was more conservative. I guess we had initially proposed it to be 40 million but we are confident enough now to make it even higher by can increase it by 50% more. yeah those were the big changes. any questions so far?

Harry Rook: No questions from my side. I'm not sure if you mentioned some kind of rough timelines for when we look to deploying a moy. I know some of the picture things might be contingent on Ethereum L1.

Harry Rook: But yeah, I don't know if we have any kind of rough timelines we can look at.

Sandeep Sreenath: Yeah so I think on get most of the changes are already in and…

00:15:00

Sandeep Sreenath: because they also rolled it out on the test net right so in holleski so we should be pretty good there but like I said we are still getting  the upscreen changes. So we are merging one version at a time because there are too many changes right since the last merge that we took from upstream. so I think we are currently at 1.14.11 which has already been released and it's like we have released a stable tag 1.14.13 from gith is being tested right now and hopefully will be released very soon maybe in the next week. and we are also going to start working on the 1.15.x

Sandeep Sreenath: X versions of G which actually contain the Petra EIP. So yeah, that's going to take a couple of weeks at least and a few more weeks of testing because they actually include all the EIPS from Petra.  So yeah probably I think we are looking at around mid to end of May when we could release or roll out the BI hard fork on a test net and maybe a few weeks later on mainet if everything goes

Harry Rook: Thanks, So, it sounds like it's going to be a busy summer. yeah, like I was saying before, there's a lot in flight. I think after this coming off site, yeah, it'll be time to start kind of, executing on all of this stuff as we get into the summer. So, I'm expecting these calls to probably start ramping up a little bit towards then. I know they've been a little bit quiet over the last couple calls. So, yeah, appreciate that, Sandep.  And thank you as well, Marcelo, for running through the handle V2 things. I don't know if anyone else has any agenda points they want to discuss quickly before we wrap it up. but if not, give you guys a little bit of your day back. All right. Awesome. thank you all for joining.

Harry Rook: Next call will be Currently tentatively scheduled for May 1. So yeah, hope to see you guys there and enjoy the rest of your day, guys. Thank you for joining.

Sandeep Sreenath: Thanks everyone.

Meeting ended after 00:18:05 👋

This editable transcript was computer generated and might contain errors. People can also change the text after it was created.
