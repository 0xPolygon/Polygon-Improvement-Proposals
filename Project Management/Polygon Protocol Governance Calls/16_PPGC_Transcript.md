# Polygon Protocol Governance Call: Transcript

### Attendees

Alex PFL, Arpit Temani, Ben Rodriguez, Daniel Gretzke, Derek Meyer, Dimitri Nikolaros, Harry Rook, Jackson Lewis, Jerome de Tychey, Joonkyo Kim, Jordi Baylina, Krzysztof Urbański, Liat Cohen, Marcello Ardizzone, Mateusz Rzeszowski, Matt Garnett, Michel Muniz, Mike Brucken, Mirella Guglielmi, Parvez Shaikh, Paul O'Leary, Pratik Patil, Sandeep Sreenath, Tiago - Stakin, Tobias Geiger, Viktor Bunin, Vincent Taglia, Yoav Weiss

#### _This editable transcript was computer generated and might contain errors. People can also change the text after it was created._

Harry Rook: Okay, welcome everyone to PPGC number 16, in terms of the agenda for today's call. two main buckets really, so the first being the Napoli upgrade and Then followed on by some discussion on EIP-3074. To give a bit of context, the Napoli hardfork went live on Mumbai testnet, February 7th.

Harry Rook: Things seem to be looking are they running smoothly right now.  Sandeep, I don't know if you had any updates on that from that you want to discuss.

Sandeep Sreenath: Yeah, I think got it is on the call, Yeah arpit. Do you want to take that?

Arpit Temani: Yeah, so basically as Harry mentioned that last yesterday we were able to successfully release Napoli hardfork on Mumbai testnet and everything seemed fine. So our next plan in terms of Napoli hardfork monitor it for a few days. We are planning to announce mainnet hardfork with the tentative date we are keeping around 28th of Feb. So that's the plan. Yeah, obviously, we will take the call on 14th February whether everything is great. 

Arpit Temani: For Amoy, I'm not very sure that we will be going forward with this as of now. But yeah, it is in progress the discussion side in progress.

Harry Rook: Okay, awesome. yeah kind of in keeping with the general flow soak for one week and then roll out to mainnet. 

Jerome de Tychey: Sorry, I didn't get when is the date of the fork on mainnet? It's the 28th.

Arpit Temani: Their We are planning on 28th of February announcement should be done around mid next week for the community.

Jerome de Tychey: Thank you.

Harry Rook: Okay, awesome.

Harry Rook: Yeah, do we have Vincent on the call? I don't know if the Mumbai testing committee has any feedback as of yet.

Vincent Taglia: Yes, I'm here. So the upgrade went smoothly for us and most of the Mumbai committee Nordic Blue from proton gaming who's working with Docker reported that the upgrade caused their validator node to begin repairing the head State putting them around 66,000 blocks behind the chain head, but no other issues were reported and state growth appears to be normal from our observations.

Harry Rook: Okay, perfect. So, I don't know if I heard you correctly, but was the issue resolved, essentially. With that one note.

Harry Rook: Okay, so just some housekeeping related things. With Napoli going to testnet. PIP- 31,  33. And 16 will all go to last call phase. And then upon reaching mainnet those will move to final so just a heads up to the authors. I think they're on the call. Yeah, so those will need to be updated over the next 24 hours or so. 
Okay, Let's move on next item on the agenda. So this is a proposal to bring EIP 3074 to POS mainnet.

00:05:00

Harry Rook: Jerome I mean we've discussed this a few calls ago and I believe you had some concerns around user safety blind signing issues. and I don't know if you want to kind of go through those in detail, and then we can have a balanced discussion around potential mitigations and then kind of Come to consensus on a path forward with that one. 

Jerome de Tychey: Yep, so it's a long and complicated topic since over the course of ethereum. We had probably five or seven years of discussion around the values implementation of account abstraction. So it's been a long time specifically 3074 has been discussed since late 2020. And is now getting more attention and more and more considerations from the ecosystem, which is great. my initial concern that last time was around the opportunity of pushing that we see a

Jerome de Tychey: giving addresses the capacity of acting as a smart contract wallet comes with similar potential issues that we face with hacks around the ERC 1271 where people get to get pop up to sign something don't know exactly what they sign and the users what it gets drained because it was a auth website that push the specific signature. Can be used elsewhere to move together compatible with the generalized method of recognizing a signature at a contract level.

Jerome de Tychey: So my initial concern when I saw  3074 pop up as a potential implementation on Polygon. Was that how easy would it be to spoof a website or what would be the consequences? If spoofing your website to get a signature from now form EOA and I was wasting this concern in the context where more and more people are getting onboarded via.

Jerome de Tychey: Much lighter wallet than we used to and we still see people blind signing on meta mask on Ledger and so on. So what would be the consideration in this context when you get onboarded with an RPC providers in context where our blind sending becomes a default? I didn't want it to hijack the conversation at the stage because for me it was more of a user ux UI and General Security consideration, but overall It feels not really resolved.

Jerome de Tychey: Yeah, they're still a lot of discussion around values implementation of native and obstruction. I'm not sure it's in the best interest of polygon to pursue a 3074 while we still see discussion on going around the 3074. we are seeing new tentative of this approach that a few months ago. November we have now EIP-7560 a new thing that popped up and from what I understand the term Foundation. It seems like EIP-4037 is getting a lot of support from the EF. It's

Jerome de Tychey: apparently there is a pass at DF the results level to make 4037 more native to the protocol and end up with a 4037 being the default the de facto way of account abstraction. my company builds account abstraction Solutions. We are not 4037 compliance so I don't consider myself to bias into this yet.

Jerome de Tychey: I have to say that it's hard to tell whether the EF is going to go push in one of the other direction. It's hard to tell as well. If us a sporting good we should be strongly aligned or some point with what the EF is doing and pushing but my recommendation in this state of uncertainty regarding here which Native abstraction solution will be adopted or will be selected or will gain traction. My recommendation will be to be cautious and wait before rushing into either a 3074 or doing more things in the other way and maybe see what latest attempt like 7560 is getting in terms of traction and discussion and apart from that time very happy that we are doing a 7212 as part of the polygon. That's really good step towards having arbitrary.

00:10:00

Jerome de Tychey: signature logic at the Smart account level. 

Daniel Gretzke: yeah, so I was thinking a lot about risk mitigations in with 3074 like you mentioned for you currently the risks outweigh the benefits and I'm thinking how can we reduce the risk? To a degree that the benefits of weight, I think there are two. main applications towards account abstraction and 3074 the first one being

Daniel Gretzke: Just making life simpler. So any tokens that do not support EIP-2612 with a permit standard, but they could have for example as single transaction flow where you have an approval transaction and then the second transaction kind of batch together into one just overall benefits to ux on chain. The second application is actually onboarding of  I would say non-cryptonative users. Onto polygon in this example. So for example, this could be a video game that wants to onboard users or this could be a portfolio manager that wants to offer users to invest into

Daniel Gretzke: Crypto in a non custodial way but currently, this is just a bad ux because then you need to prefund wallets for approval transactions when you want to trade on their behalf for that second category where you are onboarding brand new users and basically all wallet management is abstracted away from them. I don't actually see these risks because the user usually doesn't have access to the underlying wallet and has more like an application specific wallet where if the game wanted to still ask is they could anyway because you import it, you have to either import your private key into the game or they just generate a wallet for you, So that second part is not where I'm really worried about and I also understand that the thing you're mostly worrying about is existing users that have a crypto wallets to just blindly sign anything.

Daniel Gretzke: And get their funds stolen or even signed over their entire account Here. I have kind of a mitigation path in mind. This could be just working with major wallet providers. To either block. 30 74 South transactions entirely if they don't want to really support it and have an option to turn it on in the security settings. And the second option would be then, So Bribie wallet is for example doing fantastic work in my opinion.

Daniel Gretzke: In the ecosystem where they not only show you what is happening with your transaction, but they also have risk management based on the contracts you're interacting with so when you connect your wallet, it will show you. Hey, is this on some verified list? And also what's the popularity of that if that's more contract of that down how many people are interacting with it? So my Approach would be the following a slow rollout of 3074 in cooperation with major wallet providers. We start building out. Useful wallets that are then verified. So for example, a multi-call that uses 3074 and other contracts that can bring a benefit to the ecosystem and
00:15:00

Daniel Gretzke: the wallet providers can maintain a list of these verified contracts and then for example block and any other 3074 style signature. Where it's unclear what is happening with the funds unless the user goes to the security settings and enables this.

Daniel Gretzke: And this is kind of my train of thought right now.

Jerome de Tychey: Yeah, and again to clarify my point of view initially. I had a reservation about 3074 but from a ux UI your perspective but that shouldn't be considered a worthwhile consideration. And to block it do it anything else like at the end of the day it matters most that the applications and the providers care about these security elements.

Jerome de Tychey: My main concern right now is that we should pick our battle and Implement and do what we think is most likely to be adopted a widely at the Ethereum ecosystem. Hence why I mentioned for me. It's difficult to tell whether the best solution that will be widely accepted that they're at the ATM level would be 3074 or something else like any other native I can abstraction solution. It seems like you have in the chat saying a lot of things about 7560. my main concern will be like if we divert too much from official geth support and official maintenance support in the midterm to long term. It could pose a problem and difficulty to reconcile later on and so

Jerome de Tychey: if we were doing 3074 we will block us to evolve later to a 7560. I honestly don't know but that would be one of my concern and if yes potentially shouldn't be wait later on to see what kind of consensus emerged between the core devs around the 7560 and see whether we should move forward with a 3074 or continue the discussion on the 7560 and maybe you have if you want to react

Yoav Weiss: So so regarding a 7560 it actually not them mutually exclusive with the 3074 because a thirty seventy four  adds capabilities to the eoa basically execution abstraction. You can do a fancy things you execution. Whereas a 7560 is for a full account obstruction. However, I would say that the 5858  in a 3074 only are in a way usually exclusive because it doesn't make sense to support both and both of them add similar capabilities to eoa execution. both of them would allow you to do something that is similar to a delegate call from the eoa. And to it which is useful for things like batching.

Yoav Weiss: The difference is that in a 3074 once you sign a commit once and it can be replayed in the future. And if you need to prevent replay it has to be prevented by the contract Itself by the invoker. Whereas with a 5806. it's one transaction. You signed it transaction, which does the delegation and It's not a signed message that is like a permit that can be replayed. Is only used there right now for this transaction aside from that. I think the two proposals are similar.

Yoav Weiss: So I think from security perspective I would prefer the latter but regardless I would say that it's best to have the same solution for polygon and for ethereum because since let's say if a film goes with a 5806. It's unlikely that 3074 will also include alongside and then if polygon does the opposite we're going to end up with the wallets having to support different delegation methods for each chain. So I think it will be best to have one solution. It is compatible with both and then in the future for Native account obstruction, it's something that we're still exploring.

Daniel Gretzke: So I have to agree on one point with the deviation from the ethereum protocol when it comes to solidity right solidity needs to be patched to support off and off call and this not reach wider adoption. We would have to maintain separate solidity versions with support for often off call. I'm personally not in favor of 4337. There is a lot of overhead, separate mempool.
00:20:00

Daniel Gretzke: You still need to deploy a 4337 wallet. I think that generally speaking for 4337 was so prominent because it's smart contract only solution that could just be deployed and people could start experimenting with it. So it was quicker to gain adoption and I think that

Daniel Gretzke: someone kind of has to start with things through 3074 for others to follow and see how it works. So there is some risk involvement. I also agree on the drawbacks.

Daniel Gretzke: I see that replay protection is now. integrated but just because Control over to Smart contract. You don't actually know what it does. So perhaps we should also consider. 5806 as well and maybe compare those to each other.

Daniel Gretzke: Yeah.

Daniel Gretzke: Are there any other thoughts?

Paul O'Leary: Yeah, this is just kind of thinking out loud that I mean, obviously the overall strategy with Ethereum and…

Paul O'Leary: compatibility with the ethereum itself. And then the etherium ecosystem is crucial but I'm wondering if anybody the concern I have about that is for the question I have there is what would be a realistic time frame for instance for some of these primitive level account abstractive mechanisms landing on Mainnet that as everyone knows kind of the part of the imperative before 3037 was that it was done in a way that had no protocol changes basically and that was thought to be for the benefit of the safety basically of the main net but part of the whole kind of rip thinking basically and roll call is that roll ups and change like polygon basically should be able to have more design space basically and push forward because we don't have to have the same.

Paul O'Leary: security kind of mindset basically that may not have and let's put it this way. I'm not sure. Like I said, what's the realistic if we're looking at five eight or six or three zero seven four basically as particular landing on mainnet at some point. What's a realistic time frame for that? and doesn't make sense for Roll-Ups for the lte's basically to kind of wait for that time frame to push ahead. The other thing I guess is maybe regrettable is that I think there is a bit of a chicken and egg problem. I don't see that. I mean 3074. Opens up it I think there needs to be basically comprehensive wallet support in order to Leverage 3074 to provide.

Paul O'Leary: A better security for end users. and now realize it's incredible that we didn't try to reach out for instance Sam from metamask to get their perspective because I know he's sorry.

Daniel Gretzke: I reached out to metamask Rabbi and trustworld so far and…

Paul O'Leary: Because I think there's this chicken and…

Daniel Gretzke: reaching out to see what we can do there.

Paul O'Leary: egg problem. Right? there has to be support for these Primitives in order for the wallets basically to be able to try to work on their support as well. And that's just again. We're just throwing out the thought that there's and obviously existing wallet providers, metamask are gonna have a hard time getting behind a protocol or a protocol change. Basically, that the drastically changes the way that they're kind of wallets currently have to work which obviously is the benefit of resource 3074 basically is it kind of like kids slipstream better into existing wallets, and obviously that picks up a lot more kind of Some risk as well, basically. Anyway, those are just kind of random thoughts.
00:25:00

Daniel Gretzke: Of course and ultimately, I think everyone also agrees that EOS are not going to the future of how we handle things and that some form of account abstraction will come sooner or later. And I totally agree with Paul's point that it will take a long time until we see it on Mainnet and seeing it being successfully implemented on another chain will definitely influence that decision going further because less experimentation needs to be done and risk can be assessed on a completely different level.

Alex PFL: up

Paul O'Leary: Yeah.

Alex PFL: from the vaseline's perspective. We are big supporters of both 3074 and 4337 I think. in my mind, I view 4337 as very infrastructure adjacent and that significant portion of that pec. I mean, if there's this way better than I talked about a few times but I think a significant portion of that spec goes towards. Preventing or mitigating DDOS vectors at the Mev layer. We greatly appreciate that. I think that's something that we would potentially have to deal with a lot. So I do strongly support 4337 especially the more, infrastructure aligned parts of it, but for us 3074 also unlocks quite a few really neat functionalities.

Alex PFL: Inside of the Mev layer, especially for solder interactions and things of that nature. So from fastlane's point of view, we really like both and we hope that both are implemented sooner rather than later.

Paul O'Leary: Lightclient your comment about 3074 versus five six. I mean, I think the community would really appreciate any if you want to expand on that at all.

Matt Garnett: Are you referring to the comment about the mainnet making a decision done?

Paul O'Leary: Yeah, yeah.

Matt Garnett: Yeah, I mean I think.

Matt Garnett: Or doves are very aware that the users want better ux on mainnet and we're sort of sitting on our hands at this point because it's very difficult to choose between several different possibilities. I mean, these are two options. There's another five pretty good options and then there's the possibility of what about a fully feature full accounts abstraction proposal that's going to come one day in the future and it's very difficult for court to Be able to balance between the different options and choose, the best one and usually that's what they're trying to do. They're trying to choose the best option.

Matt Garnett: And so that's kind of like why I think that we haven't had a lot of progress on mainnet is that there hasn't been any experimentation with these things and developers. I mean as we saw with this whole tea loads of radical unless it's actually scheduled to go into a fork. No one really cares or pays too much attention. And so all the sudden people were getting excited about tea low just a couple weeks ago because it was going live on testnuts and I think the same is true for these proposals to improve ux unless we see them on a chain that has actually users actual money being used successfully. It's probably not going to happen for mainnet. it's very difficult at this point to Make Arguments for proposals to the execution layer. Where you without a doubt prove that it's a good thing to do on the execution layer is what we're seeing unless it fixes a security vulnerability.

Matt Garnett: Or it's something that furthers a specific aspect of the chain interoperability between the consensus layer and the chain. It's very difficult. So I would love to see somebody add support for 3074 50 608 something just so we can try and progress pain that.

Paul O'Leary: Yeah, I guess I have similar thinking too basically which is just even with 437. you can look at it on paper basically and I had kind of questions about some of the complexity and stuff like that. And then I mean some of the stuff is unknowable until you see it actually play out in the wild, right and then what I'm seeing right now, it's worth it to seven is that I mean, I love the on chain contract basically as the wallet, but some of my concerns about some of the complexity basically and the trade-offs that they made have proven to be true as well basically and then it's a really hard chicken and egg problem because that you're just saying it's like some of these things are unknowable until you put them out into the wild basically and obviously we need to do that in the safest way possible and not actually introduce, Massive Attack vectors basically, but it's really tough to know.
00:30:00

Paul O'Leary: and also the other thing like I said, that's part of the chicken and egg problem is that we really need to see how the wallet providers basically react to certain things and it's hard to them to invest basically in protocols that don't exist or actually aren't out in the while so

Paul O'Leary: Yeah, keep in mind too, I guess and I'm not trying to understand or under State that I mean, the only proximate decision here basically would probably be to roll something out on Mumbai and we started looking at 3074 and had preliminary implementations of it. Probably more than a year ago. He'd go look at the fork and my perspective back then was that the way I thought about it is we could be aggressive getting it on Mumbai and then very unaggressive about getting it on Main debt basically and see if we can get some people basically to get some wall providers to experiment and show what the implementation might look like in the trade-offs might be basically in kind of using it basically but also in a way that they can build guardrails and kind of protectors basically into the wallet itself. So putting out on mainnet might kind of Unblock that just the way that we're now hearing people are very excited about 7212 basically being on Mumbai right now so they can start to experiment on a

Paul O'Leary: substantial chain, basically with more kind of volume and stuff she

Harry Rook: Unless there's any other thoughts I think we should move on to Forging a path ahead for this one. So yeah, if there's no more thoughts, I think we can. start looking further forward.

Harry Rook: Okay, what I've got in mind and this is based on Dan's comment in the chat, so

Harry Rook: I think having more discussion with wallet providers is definitely a good avenue and the suggestion around a report with a  comparisons between 3074 and 3806. so I think if this sounds good to you all as well. Let's spin up a sub Channel within the discords and then whenever the report is ready we can share that in there. I'll share the Discord link with everyone on this call so we can do more synchronous discussion on there after this and then we'll kind of reconvene on March 7th, and we can dive back into this. so yeah, if that sounds good, we can agree on that strategy going forward.

Paul O'Leary: Yeah, here still have the coming Napoli hard Fork to get through and then what we had started to I mean, obviously we were batting around time frames and we could look at 3074 for the next hard Fork but that's once and months basically, so we have a lot of time for more discussion and feedback.

Harry Rook: awesome

Harry Rook: Okay, That concludes all the agenda items. I believe there was one final point that we're going to come back from Derek

Harry Rook: So Derek, I don't know if you want to kind of expand on your message a little bit more with regards to peering on mainnet, feel free.

Derek Meyer: Yeah, I think there are prior discussion about getting a list of different IPS for validators and Sentry nodes so that we can kind of enforce peering and just making sure that we have healthy peers on all of our nodes. And we've been noticing on our side that we've had some missed checkpoints and I'd love to get ourselves back to that 100% quality. And this was something that we feel like probably help us get that.

Sandeep Sreenath: Yeah, sure dick, I think. You might be talking to parvez. or maybe sahil? I think we've had some good success, when we were peering different centuries to the validator notes. And we would want to continue right? I'm not sure where that activity is. Currently the last I heard was that we were still compiling the list, but I can check and get back on that.
00:35:00

Derek Meyer: Okay, Yeah, I mean I think even the partial list might be some improvement on our side…

Sandeep Sreenath: Yeah.

Derek Meyer: but yeah definitely interested in

Sandeep Sreenath: Yeah, Can you tell me what's the value heater ID that you run?

Derek Meyer: 144

Sandeep Sreenath: Okay. Thanks.

Harry Rook: That runs off the agenda points. I don't know if anyone has any closing thoughts. If not, we can wrap it up.

Harry Rook: So then I think this school's been super constructive. So yeah, I've shared the dev Discord Link In the chat, feel free to join that and we'll continue this until the next call in that space. Thank you very much everyone and I'll see you on the next Corner 7th of next month.

Paul O'Leary: Okay. Thanks.

Tiago - Stakin: Thank you.

Sandeep Sreenath: Thanks, everyone.

Meeting ended after 00:36:54 👋
