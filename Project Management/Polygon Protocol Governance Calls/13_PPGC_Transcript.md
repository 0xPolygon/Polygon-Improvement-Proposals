# Polygon Protocol Governance Call: Transcript

#### Attendees

Christopher Von Hessert, Daniel Gretzke, Fireflies.ai Notetaker Flavian, Harry Rook, Ivan Bjelajac, Jackson Lewis, Jerome de Tychey, Joonkyo Kim, Krzysztof Urbański, Mariano Conti, Mateusz Rzeszowski, Michel Muniz, Mike Brucken, Mirella Guglielmi, Mudit Gupta, Nimit Bavishi, Panagiotis Alexiou (Peter Alexiou), Sandeep Sreenath, Scott Lilliston, Tiago - Stakin, Tobias Geiger, Viktor Bunin, Vincent Taglia, WebDev StakeWorks, Zaki Manian

# Transcript

**This editable transcript was computer generated and might contain errors. People can also change the text after it was created.**

**Harry Rook:** Okay, welcome everyone to PPGC number 13, in terms of the agenda for today. We've got an update on the timelock contract deployment for PIP-29.
We also have a proposal from Mudit, PIP-30 which proposes to increase the code size limit for POS. And then, finally, we're also going to discuss the cadence of these calls. Moving them back to a monthly schedule, which I think would reflect the kind of pipeline of proposals and things moving forward.
So let's kick it off then, yesterday morning the Agra fork went live on Main-net. Massive congratulations to everyone involved on that front. Everything appears to be running smoothly post-folk. There have been reports of significant throughput on the Chain. So some early stress testing there. Which is interesting. 
Let me share today's agenda for everyone.
Okay, cool, so to start it off with PIP-29, which proposes the protocol Council for the 2.0 stack.  Christopher who we have on a call is proposing a change to that proposal that modifies the time lock contracts for a  security parameter. Christopher, do you want to give an overview of how that's going to work and how you're looking at that?

**Christopher Von Hessert:** Yes, sure.
So, hi guys for those they'll know I'm part of the security team together with Mudit. So when we created the time locks, we used OpenZepplin contracts the templates that they provide as part of the deployment of that contract. There was an address that was included as part of one of the rights. So there are different roles in the time lock contract. Who's the admin who can execute and who can cancel. Those have all been properly defined for this Council and specifically for the multisig that this Council governs however as part of the deployment, there is also a zero X address that was added. this was just at it through the UI of OpenZepplin through the contracts. It has no security implications. So it's not like it allows anybody to do anything and the probability of somebody having address zero x0 a lot of zeros and number one is, close to Impossible. It's never impossible, but it's close to Impossible and there isn't really any urgency security. Associated with keeping that there but to make it hygienic and keep it clean. We want to remove that address from the roles of the time lock. Yeah, that would be a timelock just a quick proposal into our multisig to remove that address from the time lock roles.
Yeah, we've done a security assessment internally just to make sure that this is nothing risky that we do not remove any roles that we do not remove any permissions, that there is no implications for the multisig the time lock or the contracts is slots have been verified. We have actually simulated that and also performed the same operation in Goerly where we have the same setup Multisig time lock and contracts just to ensure that if we do that nothing and nothing goes wrong. So yeah, I think that's a quick overview of what we want to do with the time lock if you want more details, I can potentially write them up as well so that you can see exactly where that is for the technical ones.
I guess I'll leave it for questions.

**Mudit Gupta:** Just to add on top of that. It also gives us a good opportunity to do end testing of our multisig. So we'll go through the whole process of making a proposal and getting all signers involved and then we can actually see who responds when and make sure everything is in line.

**Harry Rook:** Thank you very much, guys any questions? from anyone in the call

**Christopher Von Hessert:** No, just very quick. I think that after this call in the next day or something like that. You will receive some new documents that probably will be discussed right now and is those documents you're gonna have all the addresses from the multisig from the time locks and all those type of things so that you can also verify those types of stuff. I apologize that that has been pending for a while, a lot of reviews had to go into a lot of that documentation from legal security all those type of things. But yeah, I think the governance team here will discuss more about those documents in a moment.

**Harry Rook:** Thank you very much. Cool, so no questions from anyone else. Then we can probably wrap this up and move on.
So I the thinking around this was to essentially append PIP-29 and then all of those details you mentioned Christopher we can add as a comment on the Forum and within the GitHub comments as well and kind of just tight up like that essentially.
Awesome, okay. Let's move on then. So next  on the agenda is PIP-30. This is an interesting one, Mudit who we have in the call wrote the proposal to increase the max code size limit on POS. did you want to give an overview of this? I know there's been some discussion on Twitter, which I'll link for everyone. But yeah.

**Mudit Gupta:** For sure, so for background EIP-170 introduced a size limit on contracts of around 24 KB. You can't deploy contracts bigger than that on Ethereum right now. It was fine for the time when the EIP was introduced 2016 or 17, people weren't really building complex contracts. But since then the ecosystem has matured a lot. There are now much more complex contracts being built out there, and developers are hitting this limit again and again and again and again, it has become a very painful ux perspective.
So, from that perspective, it's time to revisit this limit. So this limit was introduced as a quick fix to the underlying problem of they're not being a cost for fetching the contract source code. It costs the same amount of gas. No matter if you call a contract of one kilobyte size or 24 kilobyte, even though for the 24 kilobyte the system has to fetch a larger data and do more processing.
So between 1 and 24 practically there's not much difference. So we said this quick fix worked, but obviously, without a limit if someone was to upload let's say 10 megabyte contract then that would become a DDOS vector fetching 10 by 10 megabyte of data for every transaction would be not great without extra cost. So that's why this limit was introduced. where there are two ways we can go about this one is we just nudge the limit up a little bit to 32 kilobytes. That's what this proposal is. The reasoning behind that is that even a nudge to 32 kilobytes will give everyone lots of headroom, that is 33% increase in the size. So people would have lots of Headroom to work with and
the other reason for increasing this is that the hardware has gotten much better. Now it takes less time to fetch 32 kilobytes of data than it took to fetch 24 kilobytes of data when this EIP was introduced on average. So we are just keeping up with improvements in hardware and bumping up this limit. and as Jerome said, yes gas would prevent 10-megabyte contracts, but it won't prevent maybe 50 60 kilobytes of contract so I don't remember the number from top of my head, but you can pause probably build a contract which is 100 kilobytes in size 120 by just keeping the innate code small and then in the init code just returning a large amount of data.
So there is going to be a limit. But the limit that is put by gas is not enough. That's why the 24 kilobyte limit was introduced. So yeah, that's a proposal to increase it to 32. It's a short-term proposal in the longer term the idea would be to rework the gas pricing for doing transactions on a contract. So people pay less cash for smaller contracts and big larger amount of gas for larger contracts. So that's a bigger exercise. It's in the research phase right now both on Ethereum side and Polygon, but this is more of a short Gap solution to elevate some of the pressure that developers are Will open it up to questions.

**Jerome de Tychey:** Thank you, Mudit for the comments. We have a technical limit for the contract size imposed by the block size. And that's great. I know also comments on my end.

**Mudit Gupta:** Yep, along This change will also be increasing the max init size. It is defined as twice the max code size and EIP 3,000 something by Martin, and we are gonna keep it in line with being twice the code size. So it's gonna go up from 48 kilobytes to 64 kilobytes.

**Harry Rook:** Change could cause fragmentation. I don't know if you've thought about discussing this on the RollCall or anything like that, but Yeah. Wondering what your thoughts are there.

**Mudit Gupta:** Yes, so for me l2's are the playing Ground of ethereum. That's where you can try out new features and advancements. So for me an L2 must be backward compatible with Ethereum, but it can freely built on top of Ethereum. It can add more features and bells and whistles that developers might want if an L2 is just a clone of Ethereum. There's no benefit of having these 15 different l2's that same thing. So it is true that these contracts will not work on Tell the other networks adopt this change, but that's why we have l2's and that's where possibly Go ahead Daniel.

**Daniel Gretzke:** I also feel like it's not taking away anything So if you have a contract that is deployed on Ethereum, you can still just deploy the same one on polygon. So it just gives an added benefit to someone who's exclusively building on polygon. It's not taking away from other protocols or anything else.

**Mudit Gupta:** Correct.

**Harry Rook:** Nice Okay, anyone else have any thoughts feedback?
Okay, we can wrap up then and move on to the final agenda point. So. For the last four months or so these calls have been run in on two weeks intervals. I think reviewing the pipeline of things potentially coming on POS. And for 2.0 as well, I think it makes sense to move these calls back to a monthly Cadence. So I'm proposing to hold the next ppgc on December 21st. So that's in three weeks.
and then the one after that would be on January 11th. So three weeks after again and then after the January 11th call, we would move to a fully monthly Cadence. The problem with got in moving to monthly right now is that there's the Christmas, New Year's period which kind of Falls an awkward time if we to do it a month from today, so two free weekly cadences and then back to monthly. So if everyone on the calls agree that that's suitable then I can get that formalized.

**Ivan Bjelajac:** I think it makes sense. My only question will be that maybe it makes sense. Also to have some sort of either questionnaire or something that goes in circulates in between so we can maybe do something synchronously outside of the call. Also,

**Harry Rook:** Yeah, yeah, that's a good point Thank You Ivan. I think so one thing we've been discussing internally is about utilizing the Discord channel for things of that nature a little bit more so Yeah, what I can do is I can reshare the invite To discord and then we can do asynchronous updates there until the next call. That sounds good. Okay. Kristoff

**Krzysztof Urbański:** Yeah, sorry because it just came to my mind regarding this Max code size limit increase. Did you consider what are the consequences in terms of the tools that are for example block explorers like What are we going to break after after all? What could we potentially break after increasing this block size?

**Mudit Gupta:** Yeah, so as far as tooling goes not much should break because most of the tooling is built to be generic and within testing you can still increase limits lots of time people when they're building on tracks increase the limit because if your instrumenting the contract measuring code coverage, then it increases the size of the contract anyway, so all of the development tools at least can handle it very easily all of the clients can handle it as far as block Explorer go. blog explorers also accept at least etherscan accepts a single file upload for source code which obviously takes the file size to be quite large and in the backend they use salty for compilation. So I'll see has no problems compiling any size contracts. It will be happily come pile and deploy for The error only happens at the blockchain level. So we don't know I don't think there is going to be anything that breaks with this obviously if anything breaks we will work with the partners to fix it and will give enough time to everyone to report if it's gonna be an issue for them hope but hopefully That thing is gonna break. I can't imagine any tooling braking the biggest setback on negative point of this change is really that it makes it harder for us to do proofs on.
On POS because the complexity of proofs is directly proportional to the contract size. So increasing 24 kilobyte to 32 kilobyte means polygon zero team is gonna have to work that much harder to make it more efficient. But those guys are geniuses they can pull it off. Other than that. I think there are no significant disadvantages.

**Krzysztof Urbański:** Thank you.

**Harry Rook:** One other thing that came to my mind Is there any plans on Ethereum mainnet to kind of amend this in any way that could cause future incompatibility issues?

**Mudit Gupta:** So ethereum folks are researching on different ways to do it right now. so, this is a stop Gap solution as I mentioned. The long-term solution would be repricing. So we charge charge more for bigger. Once we introduce that change then we'll basically have another hard Fork depending on what that changes either. We will remove the PIP this change or we'll build on top of it, but it won't bring any incompatibilities. It's just going to change the pricing mechanism and already nobody should be depending on gas prices for certain things. As long as that assumption is held everything should be fine.

**Harry Rook:** Thank you very much.

**Ivan Bjelajac:** that maybe just a quick question, but we can work together to pick this at some point, tools such as Tenderdly and the number of other ones that do gas price simulations Etc, would do to get affected but some of these things but obviously it's on us to fix I actually had another classroom and you mentioned other Forks, after Shanghai terrium updated in terms that it switched from four ation, using timestamps to two timestamps instead of block numbers. Do you think that we plan to do that with polygon at any point?

**Mudit Gupta:** Not planned right now, but it's not related to this. It doesn't affect the stage.
Right now as far as Diverging away from block numbers from hard Focus not planned on POS, but we do have Sandeep on the call if he has more insights.

**Sandeep Sreenath:** yeah, in fact we had to Implement of a change for us to switch back to the block based, hard folks. because on Ethereum, I guess they dependent on the on the consensus not on the other chain the be contain basically for the launch time. But since that required a lot of changes  we didn't go ahead with that and we had to kind of favorite some of those changes and ensure that we stick to the block-based..
Did that answer your question?

**Ivan Bjelajac:** Thank I understand the reasoning I know this sound problems with us after the arcade. So we have to be fixing anything. so I wanted to ask why we are not doing the same thing with ethereum, but I understand That's the case.

**Sandeep Sreenath:** right

**Harry Rook:** Thank Yes, so I  next call will be on December 21st. I'll be sending out a revised invite the one after that would be January 11th, and then monthly Cadence from then on out. So yeah, as always guys. Thank you for joining and enjoy the rest of your day.

**Meeting ended after 00:23:30 👋**

