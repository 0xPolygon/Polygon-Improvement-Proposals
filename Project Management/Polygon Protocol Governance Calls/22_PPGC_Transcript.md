# Polygon Protocol Governance Call: Transcript

### Attendees

Ben Rodriguez, Brian Seong, Can, Chase Mitchell, David Silverman, David Silverman's Presentation, Dhairya Sethi, Dimitri Nikolaros, Fireflies.ai Notetaker Can, Fireflies.ai Notetaker Parth, Harry Rook, Harry Rook's Presentation, Liminal Notetaker, Marcello Ardizzone, Mark Holt, Michel Muniz, Milen Filatov, Mirella Guglielmi, Nimit Bavishi, Parvez Shaikh, Paul O'Leary, Sajal Agrawal, Sandeep Sreenath, Scott Lilliston, Stream, Tiago - Stakin, Tobias Geiger, Vasanti Rode, WebDev StakeWorks

#### _This editable transcript was computer generated and might contain errors. People can also change the text after it was created._

Harry Rook: Okay, I think that's Quickly share the agenda and we can kick things off.

Harry Rook: All Welcome everyone to ppgc number 22. In terms of the agenda for today. We've got quite a pack one. So I'll just quickly run through from a high level. What we are going to cover and then we can get it to some more detail on the specific. So

Harry Rook: yeah, first of all, we're going to just quickly discuss a change to a previous proposal called pip 35. So this was a proposal to introduce a minimum gas limit on the network. So there's been some changes there. That'll go out in the next bore release which is touted as version 1 3 4 Secondly, we have quite a significant upgrades in the pipeline. And today we're kind of happy to be able to share some more kind of concrete specs around that change which will be to do With the handle client, so we've got pip 43 and pip 44 there and Marcello will go for that.

Harry Rook: We'll also discuss some topics around polygon 2.0. So there's been a lot of discussion, especially with the pre-pip around ZK POS around the long-term kind of vision of how POS fits into the ugly but there are some kind of short-term changes to certain contracts and elements of POS right now that will have to change to kind of allow that short long term Vision to take place. So yeah some points to discussion on polygon 2.0 and then finally we'll discussion some kind of final rounding off of some points around the changes to the emission manager. So Simon discuss this on the last call and this is now all been proposed to the protocol Council or be with some slight deviations on what was previously going to be included. There's actually been something taken out of that to narrow the scope. So Harry will be just running us through that quickly. And then if we have time at the end we

Harry Rook: we can do some general Q&A so Yeah with our favorite I think First on the list we have the change in gas. I think this has been taken down to 25 away from the previously discussed very so over to sir.

David Silverman: Yeah, personally today many of the validators and nearly all are enforcing a minimum gas price of 30 and that is through a minimum priority fee that's expressed on the network to kind of make this more uniform the proposals to the initial pip is to move this into the poor client itself just to allow us to keep it more uniform and enforced evenly additionally on the PIP itself where I recommending a change down from the current standard of 30 to 25 part of this is we've seen the long periods of time where the base fee on the network actually is hitting zero Go AI essentially we are artificially keeping the floor up originally this was put into protect against spam transactions. However, we haven't seen as evidence by this floor hitting zero for a pretty long stretches, we're definitely filtering out legitimate transactions and use cases. Honestly, the goal will be to remove this floor entirely.

David Silverman: And have the gas market be entirely free market which is kind of the first step would be decreasing it socially the 25 and in the future. This can be changed directly in more by the validators allowing a much more reactivity to Network conditions.

Harry Rook: Nice. Thanks David.

David Silverman: So, recommending a decrease to 25,…

Harry Rook: I think we have mark from Eragon. But yeah,…

David Silverman: which should lead for,…

Harry Rook: this is I think also in the agrawal client.

David Silverman: to just overall cheaper transactions across the network and…

Harry Rook: There's a similar parameter so I think in order to have this standardized across both clients and…

David Silverman: that again will be expressed through the priority fee as part of a journey to remove this floor entirely and…

Harry Rook: across the network entirely. I think this will need to be reflected within Eragon as well.

David Silverman: let the gas market be again perfectly free market without a floor

Mark Holt: And yeah, hi, so I guess we just need a PR. to do this

Harry Rook: Okay, yeah.

Mark Holt: I'll look at organizing. That is somebody I need to coordinate with or should I just bought a man out and

00:05:00

Sandeep Sreenath: Yeah, so mark that there's already a craft beer. That we have raised but we were waiting for the ppgc and each consensus then we probably turn it to a normal PR. for you to review

Mark Holt: Yeah, I mean I presume we'll put it into whatever the next so we're releasing patches to 2.60 fairly regularly at the moment. So if we can just coordinate and get that in it should be pretty straightforward. I think.

Harry Rook: Perfect.

Harry Rook: Okay, cool. If there's no further comments there, I think we can. Agree that that's the best limit for now and move on.

Harry Rook: Thanks, Okay moving on that so next on the agenda is it's handled V2. So I will just quickly link the Tips in the chat, but these are fairly substantive. It's Essentially an entire refactor of a farm. Don't bring it up to date with the latest kind of libraries that will be pointing to and I think Marcello. You're going to just quickly run through those from a high level and then we can get into more detail if needed.

Marcello Ardizzone: Yes, thanks So yeah, as you anticipated we are working on the complete refactoring of handle to publish your version P2 which basically Replaced tender means we be newer Comet BFD consensus engine and this is compatible only with the cosmos SDK architecture going from 050. We are also based the new version of involvement on 050x from Cosmos SDK.

Marcello Ardizzone: And we have three still different Pap stuff 33 is basically the one talking about replacement of pendulance with Comet BFD and the primary motivation. There is basically, to remove the tech tapped that anchors enter to this old version of tender meat. Then the wind is basically an archived repo as of now and the release that we are using was from about four years ago. So, kind of updated from the time that we basically launched the POS Network and despite we made of course some great throughout the years comment bft really be beneficial for the entire network for many actors. First of all, there's a new blockchain interface, which is an announcement of the previous one called ABC A+ Plus Which introduces new types A more flexible way of the execution for transaction and blocks.

Marcello Ardizzone: And also some advanced functionalities in terms of state things, for example. And also we will Leverage The Volt extension, which is this new feature coming with a VCR plus which is basically we will be using that to basically replace the concept of sites transaction which is basically the main driver behind the working attended means I'm years ago. And as you know this side transaction name that are basically transaction queries external data, like checkpoints or state sync that add, as root dash of War chain or event data from L1 ethereum, right and product extension will be basically an arbitrary sequence of bytes that gets added to the block click on meet.

Marcello Ardizzone: And then it's going to be responsibility all time as an ABCI application to Define an interpret this data and in our case, there will be used as a replacement as of this site transactions that version that we are targeting for committee is deal 38. where we basically are amazing all the changes from peppermint on top of it. And the process for the upgrade is going to be quite a delegate one because we need Earth to every days a permitting changes on top of comet phds set. But then we need to embed this comment into the newer version of rental to be compatible with customer specific 8050. Then of course, it will go through a phase of testing and auditing and possibly working the code based on the results.

Marcello Ardizzone: Until a stable release is available for our forecast PFT eventually in the future. The goal is also to remove this Fork of comic bft and just to use your dream version by back parting some of the changes that we are, in everything from peppermint. And as you might understand this upgrade this non Backward Compatible with the current temperature chain, so we will need to launch a brand new chain in everyday. Which also includes changes in the management of the cryptographic piece. And so for that reason we will have a meticulous face of testing and performance review. But as of now the migration is just going to be one-to-one migration. So we will try to keep all the handle capabilities functionalities and utilities intact.

00:10:00

Marcello Ardizzone: And all the functionalities that come with the new ABCI and the new Comet we have the library are going to be available and leveraged in the future. This is it for VIP 43 Comet upgrade. I will stop here for just some second in case you have any questions.

Marcello Ardizzone: okay, then next one is EIP 44 which is basically, coming along with that one and it's the upgrade of Cosmos SDK, which is even bigger in terms of technical impact on the polygon PS changes specifically for Handle, because we are migrating from 37. To version 0.50 which means around four years of development. So we are migrating from Cosmo sub version 1 to version 4 which as you might imagine carry on a lot of new features functionalities and the new architecture in Cosmos SDK, which will be beneficial for POS Network again.

Marcello Ardizzone: besides the new set of functionalities that we will leverage again in a second moment because the migration is just gonna be a one-to-one migration. We will have new modules in the core application that will announce eventually the user experience of the users. The architecture itself is very simplified and more robust with the new version of Cosmos SDK offering new services like the protobuf and grpc query and the auto CLI. We will eventually merge also the two clis that we currently have in on the demon and the CLI in one package. And as cosmos SDK leverages the capability of commit BFD. Basically the upgrade is going to be kind of going together with each other. And this also will allow us, to have more frequent Upstream merges the same way. We do formatting for poor, right and this means more functionalities to leverage and for sure a higher level of security.

Marcello Ardizzone: The process here I try to be quick but it's basically a cosmos SDK based artwork for the migration which is not a synonym of based artwork. So this means that the whole network will be stopped at the coordinated height that has been previously upgraded upon. And since we stopped the application database. It's gonna be the same content across the notes. We will export that at a Json file.

Marcello Ardizzone: And then Malay this Json file in a way that we will make it compatible with the new architecture and the new block structure from commit BST. And then after this downtime in the V2 will be spun up with a new chain maybe and we chose this part because basically there were just two different ways of Performing such a great one with the other fork and the other another base migration, but the database migration was harder to do because of the incompatibility blocks structure compared to comment PFT. So in this way, we have no less effort to adapt the data, we will go not going, of me and particles these method is used by Cosmos SDK for the upgrade from B3 to before yeah, that's complex or prone.

Marcello Ardizzone: But yeah, we have some drawbacks that I told you before the system needs to be out for several hours. We can give a rough estimate. Yeah good.

Marcello Ardizzone: Yeah.

Marcello Ardizzone: All right. Yeah, I'm referring here to handle chain. So of course, as board is dependent or involvement it seems but yeah, we will need to adapt the code and other tooling mechanism in order to let board run independently for rental. And in the meantime, we will perform the upgrade forever, right the Star Gate upgrade. For example for customer service around six hours just to give you a rough timeline. But yeah, they were using some tools and some modules like the crisis and great model for Cosmos SDK which were performing some funny check that we need to do manually. But yeah, we will, publish all the tooling around this process for the node operators to perform the update in the best way possible and then we will have simulation of this migration on that notes on that as well and then a final coordinated upgrade for mainnet that will involvements one for

00:15:00

Marcello Ardizzone: I am Gallina at work for board. So yeah again not Backward Compatible. with the current chain and a lot of security considerations to be done. And yeah, we will make the whole Community aware of all the tooling and the process in advance in order to have a smooth process.

Harry Rook: Nice and in terms of how ball functions independently while harm does down do we have a mechanism? In mind yet. Just thinking we should probably. Draft a pip around that at some point.

Marcello Ardizzone: Yeah, we discussing several ideas internally there could be, third-party service or the possibility to change the algorithm internal in war to go backwards. I think the last proposal but yeah, we're trying to come up with the best solution and try to be of course the centralized. So yeah, once we read internally we come up with the people and we can interact for that's

Harry Rook: okay guys any thoughts if not, yeah, I'll leave you guys to browse The Forum but Yes, okay, if there's no other comments we can move on so next on the agenda then pip 42 and pip 19. So these are as I spoke about before some of the kind of short-term changes. We need to make to kind of facilitate the future integration with Agra and transition to DK POS. So David, I don't know if you want to give a run through of pit 42 kind of how it fits into the larger picture of things and also 19 as well.

David Silverman: Yeah, I can run through. So earlier in the year. We'd be as a community greed on pip 18, which was Frontier. And this is the first stage of polygon 2.0 which was the launch of the poll token and the migration of the new tokenomics as well as the switchover of polygon P OST using poll instead of Matic. We did the first half which was launching the pull token setting up those migration contracts setting up the inflation and everything appears to be moving great. We are now proposing the migration over of the POS chain itself. And so we have Pip 19 which specifies how the gas token of POS will upgrade from Matic to poll and we have Pip 42, which is a new pip which will specify how POS staking is going to change to use poll really want to thank many community members for their feedback on this especially the validators be a great session earlier in the week with a number of them and

David Silverman's Presentation: You can.

David Silverman: Essentially the way this is going to operate as we are going to do an upgrade to the validator share contract as well as the stake manager and it will be a new implementation that does kind of these three following things first. We are going to take all of thematic that is inside the existing stake manager and convert it to poll using the existing migration contract. If you call the buy voucher and sell voucher contracts after this migration date, you will receive a poll or it will pull from your wallet to do staking behaviors. So all new stakin in requests will be denominated in poll will be adding in a series of Legacy functions. This would be force on stake Legacy transfer funds Legacy delegation deposit Legacy. Pretty much all of the existing functions will just have a underscore Legacy and this will allow you to use your existing perfect functions as they exist today.

00:20:00

David Silverman: and in Matic and it will still work. So if you were to call for example buy voucher Legacy, it will pull Matic out of your wallet and then using the migration contract stake you under the hood with pole and if you were to call cell voucher Legacy, you would still receive Matic from your staking reward from your kind of initial principle. And so from your existing perspective and interface is nothing's changing but behind the scenes it allows us to use one single stake asset in the form of poll again, all of these Legacy functions will maintain their existing interfaces as they have today. So if you have a system that is kind of like a reliant the first kind of minimum upgrade you have to do is just change your target from your existing function to the Legacy equivalent and then longer term add support for whole additionally.

David Silverman: To be clear this half of the upgrade will not change any contracts on the polygon Poss Network and no other properties about staking the reward rate on bonding period slashing Etc is being changed.

Harry Rook: I think you covered it perfectly. So there's no questions David.

David Silverman: We are purely changing the way in which the stake token operates in the most backwards compatible way that we see fit to move forward to assist with this in the PIP. we released the release candidate for the implementation contracts on sepulia. They have been verified. This will give you guys an opportunity to take a look at the code and documentation will be released shortly as well. I've linked those in the Forum here.

David Silverman: as always if there's any questions, please you can ask them on the call reach out to the validator support team. Definitely want to hear from various folks the target for this upgrade. We hope to do it on testnet after each CC and Late July and on mainnet the date we are currently hearing from folks is as acceptable is August 15th. The goal is August 15th to execute the stake as well as the next upgrade. I'm going to go over which is the bridge upgrade before I go to the bridge upgrade any high level questions on staking.

David Silverman: Just real quick to be clear as an existing stake or delegator. You don't have to do anything on the 15th. If you wish to stake more Matic or unstaken receive your Awards asmatic as you do today, you just need to use Legacy functions. Otherwise, you will see mostly upgrade to poll.

David Silverman: questions comments concerns

David Silverman: Yeah. Once again big thank you to very the exchanges as well as many of the delegators validators and lsts for providing feedback. We hope this is a much more simpler mechanism than what was originally proposed in PIP 18, which was a full migration to it is taking contract.

00:25:00

David Silverman: All right for pip 19. I'm gonna pull that one up, it was specified and we had some consensus on it a while ago, but definitely I want to review it. Now that we have the code and we likewise will be deploying a sample implementation contract or at least candidate implementation contract to sapoleia early next week, but we will be upgrading the native token of polygonquis as a reminder the native token currently today. On POS is by.

David Silverman: By standard we call it Matic. However, it is a gas token that we can be when redeemed across the native plasma Bridge.

Harry Rook: Thanks, David.

David Silverman: Matic is pulled out of the bridge. The intention is to upgrade what that native token is redeemed for on the ethereum side over to Paul. This means that from the perspective of the POS chain chain, there is no upgrade occurring that means all user balances will automatically convert to poll their gas tokens right are going to remain on change. They can still pay gas all of theirmatic instantly upgrades because Matic and polar fixed one to one, there's no kind of risk there. It just means that with drawing back over the bridge you will end up with a different token than you may have placed into it initially. It also means we don't need to kind of do a really really painful upgrade on PLS of deploying some new token switching it and now users don't necessarily have tokens for gas. Or repeat this again the timelines that we're hoping based off of some of the consents we received from community members is post ECC for the sapolia amoi migration.

David Silverman: And then August 15th is the tentative date we want to go with for the mainnet migration, …

David Silverman: So this is the kind of the cleanest upgrade path.

David Silverman: 

David Silverman: migration, but this is obviously all subject to consensus by everyone on this call. all this means is on aetherium. When you try to redeem the gas token the native token of polygon POS, you will receive a poll when you withdraw you can deposit either pull or…

Harry Rook: Yeah, that makes sense. So would be executed kind of with 42 right at the same time.

David Silverman: Matic into the bridge and you will receive whole or the native token over on the POS side Matic that is deposited at the bridge will be instantly converted our upgraded into poll between this and the stake migration. This will mean about 65% of the supply of Matic will have fully upgraded to pull. kind of and functionally Matic at this point would become a wrapper around Paul for those that needed for any Legacy systems medical still always be an operational ER on ethereum throughout this going forward. Yeah, 42 and 19 would occur on the same time and there's no change to heimdel or bore acquired. This would exclusively be the protocol Council executing a multi-sig transaction plus time lock at a certain block and that is the intention on that front for LST stakers Etc. We may recommend that hey you might want to pause your system for an hour before and after this migration just to make sure that there's not too much activity along the boundary.

David Silverman: 

Harry Rook: Okay, okay.

David Silverman: There's nothing that we can kind of Do on that front.

Harry Rook: Yeah, I appreciate it to everyone. Listen. There's a lot of different moving parts that were discussing.

David Silverman: It's an immutable contract. However, basically after this upgraded…

Harry Rook: So we do actually have a pip that specifies everything from a highly high level …

David Silverman: which again tentatively is being scheduled for August 15th. This will kind of Mark the end of most use cases of Matic in the polygon ecosystem as we move all utility over to poll.

Harry Rook: how things are going to progress so

Harry Rook: yeah, the frontier pit I think we should probably revisit that at some point and kind of Interlink it with all of these different moving parts to so people can more easily reference what's going on…

David Silverman: This might affect those.

Harry Rook: because…

David Silverman: Yeah, this affects most bridges anyone,…

Harry Rook: yeah, like I said before there are A lot of different moving parts.

David Silverman: solvers that are using the bridge Etc and…

Harry Rook: So yeah, appreciate that David.

David Silverman: exchanges that may have Integrations of Matic deposits on both POS and…

Harry Rook: I don't know if anyone Nicole has any thoughts feedback or any kind of push back on that date?

David Silverman: I believe most have spoken out to the various technical teams involvement in this.

Harry Rook: Yeah, feel free to jump in.

David Silverman: If not, please reach out to myself Harry or anyone we're happy to make sure you guys get technical documentation. And again the release candidate for the new implementation of the bridge will be deployed on some polia next week.

Harry Rook: Okay, Let's move on so next on the agenda and the final kind of big topic is the kind of protocol console execution of the changes to the emission manager. So obviously we discuss this on the last call Simon darsh. Kindly run through those changes. Yeah, I think deharia there's been some updates something at least pick 41. There was excluded from that previous payload. So don't know if you just want to quickly run through those changes and the rationale the

Dhairya Sethi: That's true. Yeah, sounds good. So the essence of the changes are that we are decoupling 41 from Pep 26 and 40 but it essentially means is that originally what was proposed is that the default emission manager was going to be upgraded to and the emission rate for the validator shares was going to be reduced from 1.52 to 1.5 percent. That The other part of this was then validator rewards. We're going to be back converted to Paul and then stake manager will be upgraded received routes rewards involvement what David just talked about a while back this change like David just mentioned will be pushed later on. So the change from the last papers essentially that we're separating the back conversion of Matic to Paul in a separate pip, which is called 41. So everything else. I mean, it's the same and essentially the default emission manager upgrade right now entails only two things one is that the validator reward emission is reduced from 1.5 to 2.

00:30:00

Dhairya Sethi: It is still being back converted to Matic.

Meeting ended after 00:36:49 👋
