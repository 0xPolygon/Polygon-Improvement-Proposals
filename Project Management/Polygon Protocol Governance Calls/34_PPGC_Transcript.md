# Polygon Protocol Governance Call: Transcript
**This editable transcript was computer generated and might contain errors. People can also change the text after it was created.**

00:00:00

Harry Rook: Yeah. All right, guys. I think we have quorum now. Let me just stop the music and we'll we'll make a start. All right. Nice. Um, welcome everyone to PPGC number 34. Um, in terms of the agenda for today, uh, there's three main things we're going to cover, but they're all underneath kind of contract upgrades of of like different types. Um, so the first thing we're going to cover is PIP 69, which is a proposal from Peter Kim. Um, from a high level, what this does is it enables ERC20 functionality for validator share tokens. So potentially like a massive unlock for uh for LSTs on on on Polygon chain. So we'll dive into that first.
 
 
00:05:42
 
Harry Rook: I think we have Pete here so we can run through that. Um next we'll move on to PIP 67 which is uh which are some changes to the protocol council membership. Um there's a couple of members rolling off, couple of members rolling on. Um so we'll go over that and yeah I think we have Chris on Nicole who can who can go through the um kind of technical side of that where we are in terms of the payload prep etc etc. Um and then lastly we'll discuss the increase of minstate to 100k pole. So this is a proposal from myself but there's a lot of work that has gone into this uh from the validator team with with Parveves and Versanti. Um so yeah we'll go into that last and we can walk through that proposal and if there's any discussion points we can we can discuss them today. Um cool. All right so let's dive in then. So first proposal we want to discuss is PIP 69. Um, Pete, I don't know if you want me to share screen with the uh with the proposal to run through or if you're happy to just do it kind of uh off the cuff.
 
 
00:06:47
 
Harry Rook: It's up to you.
Pete Kim: Yeah, I don't think it's necessary to uh share the screen. Uh can you hear me by the way?
Harry Rook: Yeah. Loud and clear, sir.
Pete Kim: Okay. All right. Uh hi, thanks Harry. Um hi everyone. I want to uh you know talk about a proposal called uh PIP 69 which is the best PIP number ever. Uh and uh it makes a small but powerful change to uh how liquid staking can work on Polygon. So uh right now over 33% of uh all pole tokens uh which is about 3.5 billion tokens or $880 million worth of uh pole tokens. Um uh staked on uh Polygon uh right now. Um, and that's that's really great for security, uh, network security, but it also means that those hundreds of millions of dollars worth of tokens are just sitting there locked and illquid, and you can't use those stake tokens in DeFi. You can't lend them, swap them, or w you know, or do anything with them really.
 
 
00:07:53
 
Pete Kim: Uh, and that's a huge missed uh, uh, opportunity. There have been attempts to um introduce liquid staking tokens uh and some were reasonably reasonably uh successful but not enough like there was the lidosatic which was uh sunset recently due to limited adoption um and there were some other validators that they that were also uh running their own um liquid state tokens uh but none of them was really uh seeing enough adoption to uh to uh to be really um used by uh lots of D5 protocols and stuff. So um yeah uh so the the proposal basically um uh it calls for an upgrade to the uh the existing sort of um uh it's called valid share tokens or vault tokens uh that you get when you delegate tokens to a validator. So when you delegate a token to a validator via the staking portal today you actually get back something that is semi ERC20 token. Uh you can transfer it, but you can't really um call approve or transfer from on it. So you can't really do much with it.
 
 
00:09:15
 
Pete Kim: Um you can't really use it with smart contracts. Uh all you can do is rotating keys. And uh the proposal is basically asking for that to be full ERC20 token. Uh including approved, transfer from, and permit etc. And that has huge implications. Um namely uh it enables uh anybody to sort of build a liquid staking token that's trustless uh purely using smart contracts without having to introduce complex you know custodial system or uh some kind of system that um that handles mints and burns uh that is uh that requires some complex backends. Um, it's much safer. It's non-custo it will be non-custodial. It'll be trustless and uh it's something that you know one can build like in a week really. Um, so that's pretty much it. It's it's a pretty simple proposal, you know, just adding a few functions uh to make it fully ERC20 compliant. And um yeah uh um there aren't really uh you know huge risks to doing that upgrade besides uh the fact that you know uh share tokens will have to be uh you know handled carefully from then on because uh you know uh a malicious website could ask for an approval and if you approve it without really realizing what you're doing.
 
 
00:10:58
 
Pete Kim: Then you could your delegated tokens get could get drained. Uh but then again, the same is true for any other, you know, LSTs that are out there today. And you know, you're generally um should have good security uh practices anyways whenever you're doing anything onchain. So I don't think that's really uh a problem that's unique to this proposal. And uh there's all there are also other risks like you know slashing uh when slashing happens which is disabled by the way so it's not really a problem but if that happens they'll have to be uh socialized uh the losses will have to be socialized but then again like I said it's it's flashing is not enabled today and um the risk is also uh not going to be there when we move towards the validator elected uh block production uh model. So um so yeah that's pretty much it. Um, uh, let me know if you have any questions.
Harry Rook: Yeah, I had a question, Pete.
Pete Kim: Harry
Harry Rook: So, yeah. So, uh one thing, um is like I guess a question of like it's more like polygon law, but like uh the these functions that app are proven whatever were disabled in the past.
 
 
00:12:12
 
Harry Rook: Um and there's a thread somewhere on the forum. Let me dig it up now. Um and I think these were specifically disabled in the past. And I don't know the exact reason. I don't know if it was so like validators couldn't um trade the shares in some way like short them and you know it could potentially incentivize some misbehavior potentially. I may be you know completely butchering what this forum post um is trying to get at. But yeah, I'm just wondering if there are any other risks that we've not thought of and like you know what I'm trying to get at is why were these disabled in the first place? Is it just, you know, is it almost like a Bitcoin thing where they've just disabled many different up codes um, you know, just to be oversafe? And then, you know, now we've gotten down the line a little bit, we we've realized that these things are actually safe to enable again. Um, yeah.
Pete Kim: Yeah. Uh I think Yeah, I think they were definitely, you know, um just trying to be oversafe.
 
 
00:13:15
 
Pete Kim: Yeah. Uh so I think the the risk there was that a malicious violator could, you know, do something fraudulent. Um uh you know while not taking much risk by you know having the uh the tokens um be sort of if those tokens are liquid they can exit out of position pretty quickly. So um you know one could commit some fraudulent a validator could commit some fraudulent actions and and not be punished for it was sort of the concern. Um but uh like I said we are moving towards validator elected block production model. So I don't think that risk is there.
Harry Rook: Yeah. Yeah. Yeah. Agree. It definitely isn't in VB block land as much. Um, cool. Uh, yeah. I think then going forward, let's move the proposal to peer review stage. Um, and I think that's probably one thing we can address in the proposal is is like uh you know these previous concerns um and this pre previous thread that I added in the chat.
 
 
00:14:29
 
Harry Rook: Um, and yeah, let's let's dive into that a little bit more in the proposal I think. But other than that, it looks fairly simple, right? Um, and there are a ton of benefits. So, it seems like a no-brainer. I think there's a lot of support. Um, you know, both in the community and Polygon Labs. So, uh, yeah, I think let's move this one to peer review and let's also address that kind of that small thing as well. Um, yeah.
Pete Kim: Sounds good. Yeah.
Harry Rook: Yes, John.
John Youngseok Yang: Hey, um I'm just curious how this compares to uh the relationship between the vanilla staking uh to the LSTs uh that are existent uh on the Ethereum L1. My understanding is that um on Ethereum L1 when you stake uh through the regular you know staking uh path you don't really get anything back in return right your your fund is just locked inside the Ethereum uh contract whereas with LST like Lido you get an ERC20 back. So I guess um this PIP is sort of um like giving ERC20 for the regular sort of the default staking path and it could be seen as like um turning the staking like regular staking into LST.
 
 
00:16:08
 
John Youngseok Yang: So yeah, I just wanted to understand like how this compares to uh the Ethereum L1's taking.
Pete Kim: Right. Got it. So, uh, so when you stake, you know, uh, today or when you delegate, you actually do get back tokens, but you may not have noticed that you get back tokens because tokens the tokens uh, tokens lack metadata. So, most wallets will not display it uh, properly. So you do get back tokens but uh you know the tokens that you get back weren't like fully ERC20 compliant because it had bunch of functions disabled namely approve and transfer from um so this proposal is basically asking for those tokens to be uh enabled um and you know even though you're getting back tokens uh it won't really be fully um how should I call it uh the tokens you get back won't really be uh the LSTs that you'll be using in D5 protocols necessarily because these tokens are unique to the validator that you're delegating to. So when you delegate to uh you know a Binance node for example then you get back the validator share tokens of the Binance node and if you you know delegate to you know Coinbase node then you get get back the tokens that are specific to that node.
 
 
00:17:31
 
Pete Kim: So um for there to be an LST that's going to be widely adopted, there will have to be another smart contract that sort of wraps all these contract uh all these tokens uh to be a single uh common token that's that can be used um uh that that that can wrap any of any validators a validator share token and uh that and mints uh common token that you know that can be adopted and used by D5 protocols if that makes sense.
John Youngseok Yang: Got it. Uh yeah, pretty interesting.
Pete Kim: Exactly. Yeah.
John Youngseok Yang: Um I guess you know when you think about chains like Salana, you have sort of LSTs that uh ties your delegation to a certain elevator and then they give you back like an ERC20. Um I guess um yeah this is pretty unique in in the sense that it's building on top of uh what's already there uh like partial sort of disabled ERC20 and then you're building your C20 and then on top of that you could build LT. So yeah, I guess um it uh makes sense.
 
 
00:18:54
 
John Youngseok Yang: I I guess I'm just not used to this uh sort of construct uh compared to other chains, I guess.
Pete Kim: Yeah. Uh yeah, what you said is exactly right. When you delegate to a validator, you get a token specific to that that delegator. And there have been um you know uh LSTs in the past um for for Polygon. And you usually those were tied to like a single validator or a small set of validators. But with this model, you know, uh any existing delegation to any existing validators can become like an LST like pretty easily by just wrapping the tokens that you already have from delegating to any of the existing uh delegators. So it's going to be much more decentralized and uh it's going to be a lot you know uh less reliant reliant on trust
John Youngseok Yang: Got it. I guess um what you mentioned regarding security um that could be a concern because like now you can sort of move your um tokens around with ERC20 functionalities. But um I'd assume that um that's not going to be like a big issue um for the most cases I guess.
 
 
00:20:26
 
Pete Kim: Yeah, I mean LSTs have matured um you know pretty significantly on many chains and a lot of the concerns that people had initially about LSTs uh you know turned out not to be like that big of a deal and I think the same applies to to Polygon especially. Okay, now that we are you know moving towards a V vlo uh uh model where we will have one single block producer uh yeah that that will not you know be able to um do anything malicious hopefully. Yeah.
John Youngseok Yang: Got it. Thank you.
Harry Rook: Awesome. Yeah, thank you uh Pete for for coming on and and also thank you for the proposal as well. Um yeah, let's follow up in the forum uh with those with those bits. But yeah, unless anyone has any other questions, um I think we can move on to the uh the next agenda item.
Pete Kim: Thank you everyone.
Harry Rook: Awesome. Thank you, Pete. All righty. Um All right. So next on the agenda is PIP 67. So as I mentioned there are a couple of members of the uh protocol council rolling off and therefore there's a couple rolling on.
 
 
00:21:42
 
Harry Rook: So the signature policy is going to stay the same. Um there'll still be the seven of 13, there'll still be the 10 of 30. It's just two different signers in that set of uh you know that pool of signers. Um, so in terms of the people rolling off, there's Anthony Cisano and Justin Drake. So these guys are obviously super busy. Um, and they uh effectively, you know, don't have the amount of time to commit to this that's needed. Henceforth, uh, you know, they will been rolled off. Uh, they've volunteered to roll off essentially. Um, and they're going to be replaced by two new signers. Um, the first being Pablo Sabaletta. So he's also known as publito. Uh he's a member of seal which is like the uh you know security alliance that goes in and helps folks when they've been hacked etc. Uh he's the founder of opse and yeah he's he's you know very highly reputable member of the web free uh security community. So um you know very excited to have him join the council.
 
 
00:22:46
 
Harry Rook: Obviously this is um it needs to be approved by the uh existing council members. Um, so that's still one thing we need to uh to do to get this thing complete, but um you know Chris is on the call who can kind of go through where things are with the payload. Um and the second member is Jack Sanford. Um so he's the CEO of Sherlock over five years of experience in the web free uh security space. Um, so yeah, again, another really reputable signer who we're really happy to uh to join the council. Um, cool. In terms of payload and where we are on that front, um, Christopher, I don't know if you're still on the call. I know you had to jump, but yeah. Um, are we are we close to having the payload ready to to, you know, to effectively do the sign swap?
Christopher Von Hessert: Yeah, that is uh that is already ready that is already prepared. Uh I think we were just waiting to do this representation of this just to make sure that the whole community is aware of this change that uh no FUD is created on crypto Twitter you know because they're starting to see like hey what's going on who's who who are we changing and stuff like that so payload is ready uh you know signers have been on boarded not technically but you know unofficially already um and so on
 
 
00:24:11
 
Christopher Von Hessert: so yeah we're we're just looking forward and I think uh we still need to talk about the too. Mood and and and and Jordan, you know.
Harry Rook: Yes, correct. Yeah, good spot. Um, so those guys have been rolled off as well.
Christopher Von Hessert: Yeah.
Harry Rook: They're obviously internal to to Polygon Labs. Um, but for similar reasons, we're we're going to be rolling those guys off as well and replacing them with like Polygon Labs. Um, yeah, I actually Christopher, I don't know if you want to go over this bit. Um, yeah.
Christopher Von Hessert: Yeah. Absolutely. So uh obviously Jordi Mood very similar you know they're very busy Mood being the the new current CTO at Polygon and Jordi having you know spinned off uh basically with his own project CISK uh that was incubated inside of Polygon Labs um you know it it it just struggled you know to be attentive when we needed signatures when we needed reviews when we needed verification. So what what we're doing right now is replacing both of them with two of our internal multisigs.
 
 
00:25:13
 
Christopher Von Hessert: Uh one of them is the security multisig. It is composed of five people which is basically the five five people from polygon labs security organization and then uh the other multisig is the engineering multisig which is composed of five people from the engineering organization inside of polygon. So uh that still means that Polygon has two signatures inside of this 13 person uh multi-IG uh but at the same time give us a little bit more of a breathing room uh to Mood and to Jordi and just take them out of a little bit of a responsibility over there. Yeah.
Harry Rook: Nice. Perfect. Okay. So, I guess over the next couple of weeks, how many batches is it for this one? Is it two signatures or just one?
Christopher Von Hessert: No, no, it's Yeah, it's only it's only one it's it's four batches because it's actually four multisigs. So, uh, you know, we have an standard multisig and an emergency multisig and then we have one on Ethereum and we have one on or two in Ethereum, two on on the polygon side of things.
 
 
00:26:18
 
Christopher Von Hessert: So, it's one batch for each of those multisigs. So, it's actually four batches that uh the signers will need to sign. uh we can probably start uh proposing this tomorrow if not on Monday to everybody and start gathering the signatures which uh you know takes his time with the amount of people that we have over here.
Harry Rook: Yeah, perfect. Cool. Yeah. Yeah, I think the transparency reports already ready. Um, so I'll share those with the council members and um, yeah, hopefully we can get that sent over on Monday. But all sounds good. Cool. Um, thank you Christopher. Don't know if anyone has any questions on that front. If not, we can uh move on to the final agenda item. Yeah, John, go ahead.
John Youngseok Yang: Yeah. Um I'm wondering if this um would warrant a vote by a polygon delegate since it's a PIP or maybe it's just um up to the council to decide
Harry Rook: Yeah. So, in terms of the current framework, the council's self-regulating um the token holders don't have the power to veto council um that this type of decision, but it's definitely a good idea and it's something we could um discuss um in more detail, but for now it's not.
 
 
00:27:48
 
Harry Rook: But if there is um you know if there's a decent enough rationale for why we should do that then um yeah we're all ears and happy to help put together a proposal to to push that forward. But yeah short answer is right now it's kind of that type of decision is not captured by like the token holder delegates but yeah something we can consider.
John Youngseok Yang: Got it. Clear.
Harry Rook: Nice. Cool. All right, then let's move on to PIP 70. Okay, cool. So, um, last agenda item, uh, PIP 70. So, this is one from myself and Caitlyn, but um, we should probably add PEZ and Vanity to this, um, because they've actually helped out with this quite a lot, uh, in in the past. Um, so increasing minstake to 100K, Paul. Um so when a validator joins the proof ofstake network it has to route through a contract on L1 which is called the uh the state manager. Uh and this state manager has like a bunch of parameters within it. Uh and one of them is the minimum stake the minimum selfstake that a validator needs needs to have in order to join the network.
 
 
00:29:06
 
Harry Rook: Um so you know we've done some research within the market uh and this this kind of minimum self stake tends to fluctuate quite quite a lot like even within cosmos chains um it could even be like in the millions of dollars or it could be like the uh it could be around $1, right? Depends on the the mechanism by which validators join and leave. Um, but given Polygon's circumstance where there's like a white list of of non-transferable NFTTS, um, I think it makes sense to raise it to something um, not not not too great like in terms of the millions of pole, but like something around 100k pole. Um because there's a there's a problem we incur when validator is offboard and that's and that is the the NFT that uh the non-transferable NFT that kind of gives them their validator slot that kind of effectively is open to be uh bought or or kind of u taken by anyone that's kind of waiting to to do that. So we have in the past had an issue of sniping where you know a validator will offboard and there's someone already waiting there with a with a script um you know that's triggered to execute when someone leaves the network.
 
 
00:30:22
 
Harry Rook: Uh and because it only takes one poll currently to uh you know to to gain the validator slot when the previous validator leaves. uh it just means you get people join the network and they never produce blocks and it's you know makes the network uh inefficient effectively. Um so that's an issue we've had in the past, right? And this idea of increasing minstake has been around for a long time. Um the other the other benefit that this has you know aside from deterring sniping is that it increases the like incentive alignment of of the validators. So currently polygon doesn't allow partial unstake of the selfstake part of the validators um you know the validator stake on the network. Um so this is quite interesting I think uh and it allows us um you know a little bit of room to increase um kind of the validator's commitment to the network. So, you know, even a small amount of say 100k Paul tokens, that's around $21,000, um, you know, effectively means that those funds can't be undelegated, sorry, unstaked until the validator leaves the network because if they try and unstake, they lose their slot effectively because there's no partial unstake function.
 
 
00:31:41
 
Harry Rook: Um so because of this it you know it more align validator incentives but this is almost like a secondary benefit of the main one which is that we want to deter sniping. Um you know if we were to boost this to a million pole um then this would obviously uh kind of turbocharge that incentive alignment effect um that that this would have. Um but you know the reason that 100k pull I think is a good balance is that you know obviously right now the token price in terms of its USD valuation isn't as high as it has been but say if it goes back to uh you know a value that we've seen in the previous couple of years that 100k pole quickly increases and then does does this price out validators that we want on the network? it's um you know it could potentially do so if you know if it was a million poll then that uh exacerbates as well. So I think 100k poll strikes a good balance but you know we're open to suggestions on on that front um and happy to share the analysis as well in the forum as like an appendage to to this proposal.
 
 
00:32:46
 
Harry Rook: Um in terms of the implementation it's pretty simple to be fair. It doesn't require an upgrade to the contract. there is uh effectively a variable um that we're able to amend with the new uh minimum stake amount. So, you know, it's not it doesn't require a massive amount of developer overhead to get this implemented. Uh the way I'm looking at it is it's like a simple fix um that just slightly optimizes the staking mechanics on on the proof ofstake network. Um however you know the scope for this could increase. Um there have been some suggestions of making it retrospective but you know that's like a can of worms but I you know encourage people to to weigh in on on those you know particular policy decisions that could be made there. Um yeah, so I mean to summarize like we've the spec of this proposal is, you know, designed to um fix a an issue that's been around for, you know, a long time. It's pretty simple. uh it doesn't require a ton of developer overhead and it's like you know optimized I would say to um you know prioritize backward compatib compatibility and also like continuity of existing validator ops um so yeah that is pip 70 I don't know if anyone has any has any questions or or thoughts on
 
 
00:34:27
 
Pete Kim: Yeah. Sorry. Yeah.
John Youngseok Yang: Recall it.
Pete Kim: Uh yeah, how did you arrive at the number uh 100,000? Um I'm looking at some other networks like AVAC for example requires 2,000 um AVAX uh uh $25ish I think.
Harry Rook: mostly. price.
Pete Kim: Uh $24.
Harry Rook: Uh I've got a Yeah, let me share the table in the forum.
Pete Kim: Yeah. Um, okay.
Harry Rook: Um it's in like an internal thing right now, so I don't want to share screen, but um the rationale is like percentage of total supply is kind of how we arrived at 100K. Um, and that's kind of comparable to a couple of different networks. Um, I think Ethereum and one other um, struggling to remember off the top of my head, but yeah, I'll share that within the forum. But I think 100K is kind of like a nice balance just in case like the the USD valuation goes nuts. um you know if that was to happen then it prices out you know validators that would you know potentially benefit the network um so yeah you know those funds are illquid right so that it's it's a bit of a compromise for validators that want to join so that's yeah that's the rationale but I'll share the the research we did uh in the forum as well yes
 
 
00:36:00
 
Pete Kim: Got it. Okay.
John Youngseok Yang: Yeah. Um will there be validators existing active validators that will turn into inactive uh once this goes into effect? There could be validators who have a lot of delegations but then um they could have like very small self-delegates like delegation amount. So yeah.
Harry Rook: Yeah, I'm not sure understand the question exactly, but um are you asking like will this will this affect existing validators that are below the 100k pole? Is that what you're asking?
John Youngseok Yang: Yeah, we'll just like kick out validators from the active set.
Harry Rook: No, not in not in its current spec. Um so the state manager contract allows this to be either like retrospective or just like for new validators going forward. this the spec that's out there now in pip 70 is just for validators going forward. Um the reason being is like you know if we were to increase it to 100k pull now I think there's a pretty high percentage of existing validators that would be offboarded. So we obviously don't want that.
 
 
00:37:18
 
Harry Rook: Um so you know we could make it retrospective but we'd have to start way lower. we'd have to start around like a thousand pole and work with validators to to you know kind of grandfire it in and boost it over time. But that's a it's a lot more complex. There's a lot more coordination involved in that approach. But it's definitely an option. But um yeah, does that answer your question, John?
John Youngseok Yang: Yeah. So um even if it's not applied retrospectively if existing validators uh lower than the threshold if they go inactive or if they fall out of the active set for some reason maybe they their node uh is not has
Harry Rook: Mhm.
John Youngseok Yang: not been running for a certain period of time then they will you know uh be under effect of this right they won't be able to join unless they have more than 100,000 pole.
Harry Rook: Yeah. So that's Yeah. So we have pit four, right? So if a validator goes offline for um a couple of grace periods, I think it's free grace periods um 2100 checkpoints I believe it is.
 
 
00:38:30
 
Harry Rook: So if they're below what we call like a performance benchmark for that long, then they get offboarded from the network. But I think it it means that you need to not have been signing checkpoints very consistently for over a month. Um you know, if and when that happens, they get this final notice and then they're removed from the network. um you know in the case of those validators they then need to like apply to rejoin if they want to and then if they did that they would then need to um uh the mint the new mintstake requirements would apply if you know if pip 70 was to be implemented. Um so it's basically for all new validators but for existing ones the current spec means that they won't be affected they'll they'll stay on the network and things will continue as as normal. it would only apply for to to new validators if that makes sense. If we were to make it retrospective and you know have a retrospective 100k pole ministake what would happen is all the validators below that would be removed from the network when we made that upgrade um or that change to the contract.
 
 
00:39:40
 
Harry Rook: So we obviously don't want to do that um because a large chunk of the validators would therefore be removed all at once. Network would yeah kind of probably go down. So we don't want we don't want to do that. Um yeah don't know if that answers the question John
John Youngseok Yang: Yeah, I think uh you know the current policy is pretty sensible like you do need to you know uh make sure that your nodes are up and running uh and not fall out of the the active set. Anyways, um my second question is does sniping become a bigger issue with the new validary elected block producer and is that perhaps why this um new PIP came about?
Harry Rook: Um, a good question. I don't think it ex I don't think it really changes much vblock in terms of this particular proposal to be honest. um like the validator onboarding and offboarding mechanics will stay the same. Um it's just that the validators under VB block will you know they'll have like there'll be two roles. There'll be the block producer role and there'll be like the witness verification role.
 
 
00:40:49
 
Harry Rook: Um but that doesn't affect these staking dynamics as far as I'm aware. Um so there's no connection there. But it's just, you know, it's a small optimization to to Yeah. to the to the staking um kind of staking parameters.
John Youngseok Yang: Okay, good. I guess a good coincidence.
Harry Rook: Yeah, exactly. Happy coincidence. Um but it's been around for a while, right? We've had slots sniped numerous times in the past. Um yeah, cool. Um, did anyone else have any questions? I thought I saw someone put the hand up. Um, yeah, Thiago, jump in. Okay. No one have any questions? I don't know if you're muted, Thiago. I think you put your hand up, but if not, that's fine.
Tiago - Stakin: Sorry, sorry, sorry. I was muted. Sorry.
Harry Rook: Uh, no worries.
Tiago - Stakin: I was already mids sentence.
Harry Rook: No worries.
Tiago - Stakin: So, I was saying I'm not sure what type of validators you are aiming in the future if you're looking for more established companies or small individuals to to run the the chain, but I think that 100 pole uh probably for most of the current validator set uh it takes a couple of months for them to achieve that.
 
 
00:42:22
 
Tiago - Stakin: I would say in profit uh I'm not sure is if the number is a bit high or it will kind of you know enable small individuals that are willing to run the chain uh to to join it if the opportunity arrives. So I don't know if you if you have an idea of the revenues on on the validators with smaller stake but it it can take a bit of time to achieve 100k uh pol.
Harry Rook: Yeah. Yeah. No, that's a good point. That's that's another complication of making it retrospective. Um like the validators might not even have the funds there.
Tiago - Stakin: Yeah. Is that about let let's say a small individual have a have an opportunity to join and he he he's willing to you know to make the 100k investment uh uh to Southbond but he will probably be running the rest of the year just to cover the amount that he put on on Southbond. uh and that's all that's that will also in a way not kind of bring the right incentive for for the validator to you know to to be active to be proactive to you know to to safeguard the chain.
 
 
00:43:34
 
Tiago - Stakin: Uh so I'm I'm not sure if the number is a bit high or if it will kind of demotivate people to to run the chain.
Harry Rook: Yeah, I mean so this is why it is, you know, it's like looks it's not retroactive in its current spec. It's just for new validators. So we have like the validator admissions criteria. Um, and yeah, so when validators apply, we could just put it to them that, you know, the minstake is 100k. It's just like a parameter in one of the contracts. Um, so they can make a decision based off that if they want to if they want to have that capital locked in the network. Um but yeah, I mean I don't think it's a super high in you know amount in terms of USD um valuation like $21,000 uh is not like super high I would say. So I don't see it disincentivizing people join the network. I mean the other side of this um is that if we do continually have small validators joining the network it actually kind of centralizes the network in a way right because you know say you have a validator um that's got you know like a 100 million delegated stake um and like 500,000 pole selfstakes like when they unbond you know all that delegated state goes away um and then say if you have like a new validator join
 
 
00:45:10
 
Harry Rook: the network with like one pole cell stake you know that over time of centralizes things with the existing validator sets people just redellegate with larger validators in that sense so what we want to do in this I think one thing that this does is like it introduces fresh capital I know it's not a lot you know it's only 100k Paul but it does have some counterbalancing effect to that um to that so yeah don't know if that answers your question as well Thiago, but they were more ramblings than than answering your question.
Tiago - Stakin: Yeah. Yeah.
Harry Rook: But yeah.
Tiago - Stakin: Well, not just my my my thoughts on it, but as by your response, I I I see that you also probably it will be more beneficial for the chain to to get uh bigger uh players running the nodes since you already have a quite big number of small validators.
Harry Rook: Yeah. I mean well it it improves economic security but then like the validator admissions are they are weighted towards like how much delegated stake are you going to bring to the network what efforts are you going to make to um you know entice delegators to delegate to your node etc. So, um, yeah, you know, there are already some pretty large players on Polygon POS. Um, and so introducing more large players kind of decentralizes things in a way. Um, so yeah, cool.
Tiago - Stakin: Sounds
Harry Rook: Awesome. Thanks, Thiago. Um, anyone else have any questions? I think if not, we can we can wrap things up. All right, perfect. Well, thanks everyone for joining. I know you're all on different time zones. Um, next call will be August 28th, so in four weeks. Um, and the main focus for that I'm expecting to be VBOP, right? There's a lot of work going on right now around like the modeling um, and kind of how big these witnesses are going to be, etc. Um, so we'll have hopefully an update at that point on like the economics of of VBOP and how that's going to look. Um, so be sure to tune in and yeah, thanks again everyone for for joining. Have a good day.
 
 
Transcription ended after 00:47:51

This editable transcript was computer generated and might contain errors. People can also change the text after it was created.
