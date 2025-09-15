# Polygon Protocol Governance Call: Transcript

### Attendees

Ajay Ponna Venkatesha, Alex PFL, Ankit Singh, Anton Gaev, atlas staking, Ayush Bhadauria, Ben Rodriguez, Bitfalls -, Brian Seong, Can, Chase Mitchell, Christopher Von Hessert, David Silverman, David Silverman's Presentation, Deepanshu Rathor, Dhairya Sethi, Dimitri Nikolaros, Fireflies.ai Notetaker Bruno, Fireflies.ai Notetaker Can, Harry Rook, Jackson Lewis, Jackson Lewis's Presentation, Jerome de Tychey, Joonkyo Kim, Jordan Hagan, Krzysztof Urbański, Liminal Notetaker, Liz Steininger, Manav Darji, Marcello Ardizzone, Mark Holt, Mateusz Rzeszowski, Matt, Michel Muniz, Mihailo Bjelic, Mike Brucken, Milen Filatov, Parvez Shaikh, Paul O'Leary, Pratik Sanjay Patil, Quentin de Beauchesne, Sajal Agrawal, Sandeep Sreenath, Sanket Saagar Karan, Scott Lilliston, Simon Dosch, staking 4all, Stream, Tanisha Katara, Tanisha Katara's Presentation, Tiago - Stakin, Tobias Geiger, Vasanti Rode, Vincent Taglia, WebDev StakeWorks

#### _This editable transcript was computer generated and might contain errors. People can also change the text after it was created._

Harry Rook: Okay, also like everyone. And welcome to ppgc number 21. So in terms of the agenda today is pretty excite. Yeah, we're going to be getting with a discussion As you can Poss phase one. So this phase will involvement connection to taglia through the and very proof and also ideally proposed. consensus proof also discussion or release version one three four and Paul requests 309 and the PIP repository. So this is essentially kind of pulling forward fit 35 from the amount about Falcon. This release was more low level merges from Geoff. So Downstream,

Harry Rook: yeah. then just goes to 26. So this is the transition from Magic to Paula de rewards. So this is actually something discussed in the past and Today we'll kind of just do some final coordination onto an execution of that And then you will finish off with 39, which is a new one from the Santee Jackson. And parvezan Tanisha and this also with validates from missions into the peer Network. So Yeah, without further Ado. Yeah, I hand it over to David and Paul. I think they want to do a high level overview of ZK POS phase one. So yeah over to you guys.

Paul O'Leary: That yeah. Sorry, David got

David Silverman: Does it say political soft?

Paul O'Leary: Yeah, so this is an unusual one for us we decided to offer this basically as a pre-pip because it's a very fairly significant concept. Basically, it's got kind of me to it and a lot of new stuff going on at particularly like its relation to the AG layer.

Paul O'Leary: So in the spirit of kind of getting information getting kind of concept out there as early as possible. We've offered this as a pre-pip to essentially kick off the discussion in the community with this fairly significant change. So just I'll do a tldr basically on kind of what the concept is because there may be some other new Concepts here. So what the AG layer is intended part of what the aguilarious intended to do basically is to kind of generalize the concept of how chains choose to settle while also protecting basically the overall kind of health of all the chains and the Integrity of the assets on the chains as a consequence of that design with as we generalize. This change will be able to have more kind of latitude to choose what their settlement is in some cases as you guys know ZK

Paul O'Leary: Gaevm chain has full execution proofs. So probably what? We hope will be eventually the gold standard of settlement but obviously across the entire kind of ecosystem of all chains and then particularly with POS as you well know is as brutal state. It settles with consensus kind of classic two by three plus one state consensus. It is on the roadmap. It is part of our plan to eventually have possettle as a full DK execution proof, but that'll be a momentous thing. when we get everything to scale to the level of POS and the ZK to scale a good kind of interim solution basically is to just use the existing settlement of POS. And the way that that would be embodied would be with a ZK proof that proves the consensus of the chain and then it's used to settle to the AG layer and then as

00:05:00

Paul O'Leary: Adjunct to that we have been working closely with the company called succinct. They leverage our plonky3 technology to build this nice infrastructure called SP one that allows you to kind of write General rust code and have it compiled to plonky 3 as the proving system and they've already used this technology to build a plethora of different consensus protocols. They need tenderment and stuff like that. So this is well within the Wheelhouse of what we've already done basically in order to build this consensus,

Paul O'Leary: So that's kind of the tldr of it. Like I said, there's a lot of meat there. So definitely would love I mean again the tip The pre-pip is out there with information and then love to see comments and questions on that, but we can also just open it up to the floor at this point for any kind of immediate reactions or questions from the ecosystem. Yeah. Yeah and David please chime in and add more color if you want.

Paul O'Leary: Good.

Paul O'Leary: Yeah, Absolutely. And again, this is why it's offered as a pre-pip at this point basically because there are a good number of kind of unsettled or technical questions that we have good working assumptions on basically, but would love to hear I mean that this is the point we want feedback from the community. We want at the community to process this as much as possible and kind of help guide the decisions.

David Silverman: on the I can simply call that BLS one essentially today Right, what we would be doing is we'd be doing a full tenderment verification that the concept of the BLS or ice Frost below would be a change to heimdel that says hey in addition to the whole tenderment. let's do a proper BLS signature to verify it's a bit easier to verify and ZK world as opposed to full-tenderment. So I think it's more again a timing thing right fast way to get this is we do tend Efficient and cheapest as we go and do a heimdel upgrade to include this BLS work, but that kind of pushes out the timing of this. So this is some of the considerations for the community to take into account. I think there's other Pips floating around about a broader update to Heimdall. So we might want to link those together. there's different timing paths to be considered. This is more. We wanted to get this out here for the community to start processing.

David Silverman: Hey Jordan.

Jordan Hagan: Hey David, you mentioned it's a more efficient and cheaper just to go for the VLS route rather than the full tenderment consensus route is that you mean computationally if I can engineering effort perspective and where do you see the proofs for this stuff sort of being generated and in this new sort of design?

David Silverman: Paul do you want to take the on the cost? I think it's more of right verifying BLS is this ice frosting is the other one proposed? It's just cheaper than us running the whole tentative improve but

Paul O'Leary: Yeah. Yeah. I think that the cost they were talking ever. Hope we're thinking of is it's the gas cost basically have verifying n number to ccdsa signatures as tender men kind of currently is versus an aggregate signature with BLS.

David Silverman: Yeah chain.

Paul O'Leary: So that's the cost there and then in terms of the proof generation, this is also kind of work in progress and then some back and forth with our partnership or are kind of work with succinct. They are proposing building approver Network that can be hosted and used to generate these ZK proofs have a nice interesting characteristic which is you don't actually have to trust the proving Network because the definition of these groups is that they're succinct. And so when you get approved back if you Outsource your cruise generation to another Network when you get the proof back, you can probably check it yourself to make sure that it's correct. And so

00:10:00

Paul O'Leary: the main attack Vector basically of an Outsource kind of proving Network would be just dos basically like briefing you and denial and denial of service which then you contributely switch to another Network. So it's a very nice model. So we're exploring using succinct to build proofs. We're also exploring kind of maybe doing part of it within polygon. There's a wide open space for other implementation Partners or any other to start to experiment with creating networks as well. So

David Silverman: And just a fly going a bit further on that. There is a section in the PIP here, which would be the introduction of a new role that would be kind of the folks responsible for some of this communication out to the proving system at specifically submission back to L1. I believe at first this is set to being an allow listed address or set of addresses just to kind of let this thing Harden for a bit with the intention of kind of going permissionless, which would allow us to go straight to using a building it somehow into the existing leadership selection and heimdel again, do this is kind of again the community prioritizing.

David Silverman: Completeness cheapness descent full decentralization from launch or…

Anton Gaev: So can ask questions here.

David Silverman: do we want,…

David Silverman: get to AG layer faster. Yep.

Anton Gaev: So yeah,…

Paul O'Leary: Yeah, please right.

David Silverman: I think that's the kind of the decision that's being put from the community for many of these considerations.

Anton Gaev: I'm done from Peach people editor. One ask question about this particular design.

David Silverman: So again, you can see here kind of option one and…

Anton Gaev: So by connecting to aguiler what's gonna be the validator's role?

David Silverman: two option one. We've allowed a list instead of addresses with the goal of getting permission lists as the AG layer hardens or we wait to get permissionless immediately building this into a broader heiml upgrade using crime deals leaders selection.

Anton Gaev: So we'll be around this prover itself. or will we just Well send our proof of stake consensus. So well blocks into this layer, which is centralized essentially…

David Silverman: Yeah.

Anton Gaev: because I guess on the one entity runs the proverb. So by these I think our roles are always proof of stake will be diminished a bit. Thus it can scare away our delegators and they can unstake thus making everything very well. But for us that's validators. So yeah was gonna be the one here for keeping everything decentralized.

David Silverman: Yeah.

David Silverman: Yeah, I think it's a great question and I think a key thing I want to stress in that's in this pre-pip is what's not actually changing is the settlement for polygon POS itself snapshots Milestones checkpoints are not going away. Those will still require the full-fledged signature Etc of all the POS validators. We are not requesting or not suggesting a change of block production and how blocks are being produced that is still done by the POS validators. there's no change here. This is discussing the deployment of a brand new bridge contract right FX portal will still be secured by The plasma bridge is still secured by the checkpoint mechanism, this is kind of part of the slow roll out of lizzyk proofs. We are going to kind of deploy the new bridge tested in this setting but even when we complete the zkpos transition validators will still be resp.

00:15:00

David Silverman: For all block production if anything you can view yourself almost as decentralized sequencers in a certain capacity. In fact, that's kind of the architecture. We're currently in the AG layer to be clear. when we're saying that I think pushing on proof generation being centralized. The role as I currently understand it even for ckevm. there is a trusted proverb if the Proverbs respond within a window anyone is able to submit and generate a proof and that's not actually just limited to the validator set. that's open for everyone entirely. anyone can submit any proof to settle the system. kind of giving the trusted role the one that's running a ton of various, larger machines this opportunity to kind of settle immediately mainly…

Anton Gaev: yeah this part I do not only so specifically when you say additional bridge will be added what's going to happen with current plasma operation…

David Silverman: there's an expectation that after users not going to burden themselves with running these large machines unless needed to push through this settlement.

Anton Gaev: where we'll check points go because if there's gonna be another Bridge

David Silverman: in case of some censorship operation by The Trusted sequence or The Trusted proverb. And again, it's only going to affect subset this additional Bridge being added. All the existing systems are PLS or remaining the same today. This is just kind of a net new feature.

David Silverman: Yep.

David Silverman: there remaining the way to think about it is the tokens that are being moved over are going to be the POS portal tokens. This is a collection of about

David Silverman: Things like four to 500 tokens. I don't have the exact number in front of me. if for folks that usually old mapper tool, this is the Old mapper Dot polygon.technology. these are actually currently still under control of a multisig part of the benefit here is by deploying it to this new bridge Alex AI that secured by zkroof so we can safely renounce all of the upgradability over these tokens the plasma Bridge which controls mattock soon to be pole as well as FX portal…

Anton Gaev: So the checkpoints of the Sun so they will still go to ethereum as it did before not to AG layer.

David Silverman: which is how messaging is done as well as most of the newer tokens that have been for instrument will remain unchanged and…

Paul O'Leary: That's right.

David Silverman: subject to checkpoints and checkpoints. By the way Etc. This is all part of the consensus mechanism that the lxlr bridge will be kind of looking at to respect so, Alexis I still gonna require the functioning,…

Anton Gaev: yes, so it seems Okay,…

David Silverman: the validator networking continue to function in order to move forward.

Anton Gaev: so is my following a statement Then our role will be slightly diminished because part of our responsibilities will go out as validators responsibilities go to a glare and…

David Silverman: correct checkpoints all go to ethereum this is basically think of this as what we are posing is there is a different bridge that bridge has its own settlement mechanism POS is settlement mechanism,…

Anton Gaev: in time they will get more and more responsibilities. more breached tokens and…

David Silverman: which is checkpoints is remaining unchanged.

Anton Gaev: the total weight of the POS chain,…

David Silverman: That makes you part of the CK migration. Yeah.

Anton Gaev: if you allow me such a terminology to be used maybe you can replace it to be correct. Yes.

David Silverman: I'd push back on that just like those tokens are still secured by the consensus proof, which is generated by which ultimately they can sense is coming from pollinators. I think just a shifting responsibilities Paul if you want to jump in here.

Paul O'Leary: Yeah, I don't see how this diminishes the role of the aggregators or the delegate because effectively all that the sp1 proof or the proof will do in this case is essentially just effectively I mean, it uses the same logic of two by two plus one basically of the validator set in the delegates and it kind of just repurposing that and leveraging it for additional functionality to settle onto the bridge so I'll definitely think about it. It's an interesting point. I don't see how it diminishes the role. It kind of borrows the existing role and then I guess there could be an interesting question there about okay, we're borrowing that but then maybe in a way that's not directly.

Anton Gaev: Hopefully everything is exactly the same as you say,…

Paul O'Leary: Accruing value to the validator set in the short term,…

Anton Gaev: but what I really want to emphasize for polygon to make announcements properly.

Paul O'Leary: but that would probably be part of the planning of the decentralization of the AG layer going forward basically, so we're far away from settling on an economic model basically for the agenda particular one with the centralized notes.

Anton Gaev: Let's call this way not like you've done it badly previously, but they're not to scare off our applicators or…

Paul O'Leary: The tldr be there. Basically it's not clear to me that it diminishes the role of the validated.

Anton Gaev: clients because they may think that you try to diminish their own role.

Paul O'Leary: It's a borrows the value and validators for other purposes right now basically,…

00:20:00

Anton Gaev: Mighty can no longer is available token or…

Paul O'Leary: but it doesn't in my view diminish the world.

Anton Gaev: something like that. just

Anton Gaev: Please double check when you're gonna properly make this implementations that proof of stake still works as it intended to work and actually not nothing bad is happening. It only improves the system something like that.

Anton Gaev: I'm not sure if I take a lot of time, but can I keep asking questions? I have a couple more.

David Silverman: Of course.

Harry Rook: Yeah, go for it.

Paul O'Leary: Yeah, of…

Anton Gaev: Okay, I think initially last year maybe there was some plans of changing proof of stake validators or…

Paul O'Leary: of course. I mean, It's Not Unusual thing here that this pre-pip, but that's also I think because we understand that it's a significant change. So ideally if you can do me a favor maybe kind of capture that concern of that some of that feedback basically in this preparing so we can kind of make it part of the discussion…

Anton Gaev: adding a new role of data availability layer to them for ckv Amazon like that. Maybe I'm wrong.

Paul O'Leary: because it's a very very interesting point or…

Anton Gaev: Maybe those were just some rumors and…

Paul O'Leary: very useful point. Thank you.

Anton Gaev: thoughts out loud and not something official but is it still Within These plan maybe after this particular creepy?

David Silverman: So I can speak a bit to this. Zke evm that was not as far as I'm aware that has not been brought up in the cards aren't any discussion if that's something validators want to do again suggest making a post on The Forum to move CK VM from a roll up to a validium using the POS validator set as a DA layer. That could be an option. I think it's something we've discussed prior. I think what has been discussed is

David Silverman: when we talk about zkpos and moving POs to being a zero knowledge secured L2. We would not be doing a formal rollup. I think was the initial proposal that basically what would occur is by the nature of the 100 plus validators of POS being the decentralized sequencing Network. They would also be maintaining the data to be available. And so they would be the da and therefore we would classify it as a validium. But to be clear it is no change in the role of what a validator needs to do today. It is more of just giving you guys probably giving values the proper technical name of what would be doing down there. In this case. They would be acting as the da be clear the kind of already act as the da today for PLS.

David Silverman: So again, I think it's more of us. Just moving from speaking in kind of sidechain speak to academic I think of L2 beats on the call here, please correct this if we're using any terms and properly and we thank you all for keeping us honest. I think it made a note here at your questions for your research team. Please feel free to reach out. We're happy to jump on with them and ensure using the right words.

Anton Gaev: Okay, that's nice. And that's helpful. Thank you. one last question about the plural and yes right now, it's kind of someone can step up if the current proverb is down and there are many within polygon City key there are many chains. Not all of them connected to a glare but still will implementing some probability centralization techniques, or is it out of the question for this year and 4,025

David Silverman: Yeah again, I can jump in here in the sense of saying I think one of the things provers is long-term. I think we do want to get rid of The Trusted proof of role in cdk and to be clear. that's a setting I just don't think any of the cdk changes have turned it off to be clear when we Decentralized prover. I think it's again technically according to the way the protocol works today. There's a window upon which this kind of privileged prover is able to post if it doesn't post then it's open to anyone decentralized proving in that regard right anyone can generate a proof and submit it.

David Silverman: I think that we are working on making infrastructure easier for that. One of the key things that is coming is I think the next release of the provers are stateless. Meaning that anyone is operating approver for one of the cdk chains can technically operate it for all of the chains as they don't need to have kind of any chain specific code inside the prover and that there will be a ton of latent prover capacity in case any of these kind of primary provers fail.

David Silverman: Decentralized proving I think is a bit more of an open topic again. if the desire here and this is one of the things that can be brought up in this question here is if the desires hey, we don't want to use this. We don't want to give kind of this a Singh to network and they have their own prover Network, which I would encourage them to post about the decentralized capabilities of that. But for the proven Network, we don't want to give that a privilege role we want to say anyone can submit a proof at any time that can be one of the options. So again, this is why we're kind of submitting the pre-pip is for everyone to discuss and submit out, concerns they have and things you like to see in the design so that the various Engineers both inside polygon over it's sayington in many of the other development Partners. We work with topics where Etc can adjust according to the community's feedback.

00:25:00

David Silverman: Yeah.

Harry Rook: Maybe do like zkbm related things async we can yeah,…

David Silverman: Yeah.

Harry Rook: we can start off right in the Discord.

Harry Rook: All right, cool. I never thought Scott's guys. I never questions. indeed great questions.

Harry Rook: Okay.

David Silverman: Just for the timing side of this we'd really love to get. feedback as soon as possible probably like a couple more ppgc Cycles just so we can kind of really start shaping with that. Final pip is going to look like just given where we are in the form of development. So at this point, I think we're in a pretty good place being very improved in a pretty good place with some of the other proof mechanics. I think it's more of this protocol slot. Yeah. That we want to make sure we're good for so please provide your feedback so we can adjust the dev resources as needed and the wording of the final pip as we give everyone plenty of time for you.

Harry Rook: All right, also, thank you guys. Moving on then so second point in the agenda is ball release v134 and pull request 309 in the depository.

Harry Rook: Yeah, I don't know if we have pratik on the call.

Pratik Sanjay Patil: Yeah anyways.

Harry Rook: And if you want to do a high level overview of the release and we can get into pit 35 as all.

Pratik Sanjay Patil: Not sure. So yeah, we have two major updates. The first one is about Ahmedabad hard work and the second one is about related to this only which is the next board. Is that going to have which is 1.3.4? So as we know EIP 3 0 7 4 which was part of and about hardcore was in place by EIP 7702.

Pratik Sanjay Patil: And this has pushed the development timelines and the timelines and about hard work a bit further back. and there was one more change in Ahmedabad hardcore, which is 35 and this 25 is basically to increase the minimum gas to 30 gray to basically improve the US of the stream and this if 35 you didn't require a hard Fork, so it was because of the delaying and a lot hard for this 35 was getting delayed. So we have removed 35 out of and the world are fork, and we're going to include that in the next board 1.3.4 release. And apart from this there are two major changes in 1.3.4. The first one is the get Upstream. So we have taken Upstream of that version 1.13.6.

Pratik Sanjay Patil: And the second change in the next release will be as you will the users are partners that rewarding some sync and peering issues on board. So there were a few recent changes in P2P. Which also we think we're related to this problem. So we have those commits in the P2P module and we have included those in the next release that as well, which is one dot three dot four. So yeah, we have started testing of that release internally and soon there will be a bitter release to be deployed on a more.

Pratik Sanjay Patil: And yeah, that was about one dot three dot four and four folks who don't know today. We released more version 1.3.3 which includes much awaited feature which is ancient block running. So if you want to try it out, please go ahead and give it a try. And yeah, this is a big creature where the external contributors Community aim forward and work on this so you would shout out to them as well as Mano from our team who like from start to the end. And also shout out to you guys for approving it.

00:30:00

Pratik Sanjay Patil: That was my quick update. Thank you.

Harry Rook: Thank you ik. any questions guys?

Harry Rook: Okay, awesome.

Harry Rook: All Okay. Do we have Simon on the call?

Simon Dosch: Yes, I'm here.

Harry Rook: Yeah, so just to give a little bit of background context before I hand it over. There's pick 26 is effectively lists out the emission schedule for Paul which changes annually. and so as part of that and The anniversary is coming up towards the end of June. And so we're going to need to upgrade the emissions manager contracts. So yes Simon. I don't know if you want to give a quick overview of the code changes and any specifics around the security Audits and that type of thing.

Simon Dosch: yes, I'm not gonna go into too much detail. Basically. We're going to lower the overall emission rate from 3% to 2.5 percent that means instead of 2% going to the stake manager and thereby to validators. We will have 1.5% going to them.

Simon Dosch: And that's been in the end a small code change that has been audited by now and we want to get this proposed to the multisig very soon so that we can punctually execute this update on June 30th, when it was supposed to happen in conjunction with this update. We have two other small things. One thing is that we will stop sending this money in that we stopped converting Paul to Matic before we send it to the state manager because soon we will also be updating the state manager and since

Simon Dosch: that's going to happen. We will actually use this time here and stop converting to mattock and just be directly sending Paul to the segmenter and then lastly because we will have a new community treasury board. Also very soon.

Simon Dosch: before this update, even we will Change the address of the treasury in the emission manager contract and that is something that will happen. And in the initialization of the new contract,…

Harry Rook: And yeah, that's so David.

Simon Dosch: so I think it's important to get this out there when somebody looks at the payload that is intentional.

Harry Rook: Go ahead.

David Silverman: Sort of just to provide an important part of whole point of clarification this emissions decrease follows from the Genesis Matic schedule.

Simon Dosch: We want to change the address of the perjury to a new address…

David Silverman: This was one of the scheduled decreases That Was Then ratified last year as well as part of the transition to pull…

Simon Dosch: which is going to be a dow where grants are going to be approved for the community. And that will be the update.

David Silverman: where we would keep the mattock Genesis emissions schedule until it ran out and then we would switch to the pole white papers 2% So just to be clear where this is coming from

00:35:00

Harry Rook: Yeah, that's right.

Harry Rook: Thank you Simon. So I think we targeting tomorrow to send the payload to the council. I don't know if we have any of them on the call. But yeah.

Simon Dosch: Believe the 17 is where it was. First decided right?

Simon Dosch: Thank you everybody.

Harry Rook: Yeah, I mean we can discuss ync. if there's no. Yeah, I mean the council members would be inform when the payload is ready. So whether it's Friday or next week, we'll get the notices circulated.

Harry Rook: Okay, awesome Right Moving on then to the third point Sorry the fourth which is pit 39. So this is a new pair that was poor yesterday from vasanti paveers Jackson and Tanisha.

Harry Rook: and yeah, this effectively specifies the validator onboarding framework, so

Harry Rook: Yeah, I think Jackson you wanted to take this one and do an overview of how that's going to work.

Jackson Lewis: Yeah sure thing. Thanks I will just share the Pips so that it comes up.

Harry Rook: And welcome back by the way Jackson.

Jackson Lewis: Thank Nice to be here again with you guys. this slot usually clashes exactly with an important meetings. So I haven't been able to make it for a while but much appreciated. Can you guys see the tip screen now?

Harry Rook: Yes.

Jackson Lewis: Yeah, so this pip basically outlines the admissions process into the POS Network which prior to this process being created. Only existed in I guess you could say an organic format. So back in PIP 4 one of the first Pips that was releasing this manner. Part C kind of outlined the path in which validated selection for the network would be taken forward.

Jackson Lewis: And there was kind of three sections the part was where we were currently at that point. And that entrance into the network was kind of a solely centralized decision. And then that we would lead the community down to a point in which it was automated and more decentralized than it was currently. So we started to build this process both Tanisha and I ever since Sunday have been working on it for many months. and effectively this admissions process does exactly that it takes us a step away from where we were presently at that time people fall, and we've built a

Jackson Lewis: Semi-automated process for applicants to put forward an application to join the network aspiring validators and that application form is a set of questions generally about the entity itself and then a questionnaire which goes over the three main pillars of what we deemed as being important parts of the application process. So

Jackson Lewis: Basically to Define what we thought was important. We looked at three main verticals and they were stake or financial skin in the game. So to say experience as it suggests here a number of years operating a validating node on a POS Network and then expertise which is kind of a zoomed in defined expertise range or score based on their technical knowledge of nerd operations, the POS Network in general and how it operates as a protocol and then across those three verticals the application form then scores and ranks everyone that goes through it and obviously, ranked high to low high. It's quite easy then for the people that are managing that process to see at that stage where an applicant sits in strength of ability to operate in the network.

00:40:00

Jackson Lewis: So the score on the assessment test as I mentioned Tanisha will dive a little bit further into how we built this process and a little bit of the maths behind it and some of the scoring mechanisms, but effectively this application process means that the automated score and ranking of those validators then puts higher score validators in a position where they can be

Jackson Lewis: Looked at for further considerations the network in which that application then goes through to an interview where the person conducting that interview and this is now being taken up by privas and disanti that interview can go deeper into the information. They provided provide in the application and can wise be verified. So the interviews kind of like the second gate so to say again where all of that automated test information is verified and the operator of that interview can go a bit deeper into this entity and their experience and then from that point if all is good,…

Tanisha Katara: Thank you Jackson. I will also share my screen.

Jackson Lewis: no red flags and the information checks out then and internal Committee of peers and leads internally can decide upon Whether that applicant is able to join the network.

Tanisha Katara: Hello My name is Tanisha.

Jackson Lewis: I will briefly stop there that one last thing would be that basically with this has been operating for.

Tanisha Katara: I work as the governance Innovation specialist at polygon. We're here to share the insights on the methodology to determine the weightages for the validated admissions scoring.

Jackson Lewis: Nylon, six months now, maybe leave a little bit longer closer to eight months. and as Tanisha will describe there's been I think a range of 10 or…

Tanisha Katara: So to achieve a fair and representative scoring logic.

Jackson Lewis: 12 validators that have come through the submissions process,…

Tanisha Katara: We essentially embarked on two Key activities first.

Jackson Lewis: and we've also been tracking the

Tanisha Katara: We conducted a survey of high performing validators measuring the correlation between stake experience expertise and…

Jackson Lewis: the ability of this application process to provide strong validators, so I'll stop there but I'll let Tanisha continue on some of the in-depth information

Tanisha Katara: Second we engaged in a percentile deep dive to essentially Define the parameters of what do we exactly mean by experience expertise and state and how each of these definitions correlate to high performance? So we utilize something called as the Pearson correlation coefficient, which is what you see on the screen where we Quantified the relationship between performance expertise take an experience.

Tanisha Katara: So this statistical measure ranges from minus 1 to 1 and as you can see, it showed us that the correlation between POS experience and performance was moderately High which allowed us then to go deeper into how exactly do we Define experience and the correlation jumped up when we defined experience as equal or greater than three years. So this was one people finding in the second exercise. We utilize the validator percentile analysis, and we found two other parameters that were also deemed significant one is expertise, which is gay which is gauged through a rigorous validator assessment quiz, and the second is take which means that you would, meeting the networks minimum staking requirement now both the survey and the correlation Matrix helped us determine the final

Tanisha Katara: just which is right here.

Tanisha Katara: and as you can see, the expertise is divided into three parts the governance assessment and outsourced operations to avoid Foul Play. We've also introduced additional security mechanisms, which is all questions are randomized and unpredictable. The questions are timed and you need to be within the average limit for time and the third is that the answer options are also obviously a highly scored validator across these three pillars. Like Jackson says can be considered eligible. However, it's important to know that an eligible score does not guarantee admission. It is obviously subject to verification and making sure that what is claimed is in fact the reality and provable through on chain data.

Tanisha Katara: Just as a closing remark, I think it was important for us throughout this exercise to stay aligned with our values on being transparent and inclusive no longer do we rely solely on stake instead? We consider a multitude of factors through the admission process to recognize the invaluable contributions of our validators accurately and…

00:45:00

Parvez Shaikh: Yeah, Thanks Jackson. I think you guys have pretty much covered everything that had to be covered in this pip. I would say.

Tanisha Katara: I hope this continues to help prospective validators coming from all backgrounds of life.

Parvez Shaikh: The only thing that I would want to speak about is with we having more hands-on experience and…

Tanisha Katara: I'm going to hand it over to parvez…

Parvez Shaikh: conducting these interviews.

Tanisha Katara: who leads efforts on validator it engagement to add more color to this.

Parvez Shaikh: We'll be more than happy to take in any Community feedback or…

Tanisha Katara: Thank you.

Parvez Shaikh: suggestions, pretty much me or vasanti. I think she's also on this call any feedback, please feel free to share it with us.

Parvez Shaikh: I think Harry

Harry Rook: Also, yeah, thank you parvez. So I mean as it's a bit right? It's still a kind of an evolving process. So Forever on a call and everyone listening in on the recording feel free to weigh in and post your opinions on the Forum.

Harry Rook: there's any questions on that guys any thoughts or feedback?

Harry Rook: All right, we've got 10 minutes left and we've covered all of the agenda items. So yeah, I'll leave it open if anyone's got any other final closing thoughts. They want to add then feel free. If not, That we can wrap it up a little bit early.

Harry Rook: Yeah, David.

David Silverman: I just want to fogging update. I know there was a lot of requests from folks regarding an update on the whole migration timelines. We've been continuing to receive a ton of feedback from various parties and hoping to provide an update on the next ppgc. So just wanted to keep Folks up to you on there.

Harry Rook: awesome Yeah, so the next call being two weeks guys. as it's scheduled right now. We may move it. But in terms of things that area marks pit 38, which I've seen some feedback on from a few of the validators. And then they'll probably also be some updates on the amount of upgrade as well. So yeah, be sure to tune in.

Harry Rook: And yeah, I think that's everything guys. So Jordan do you have a question? I was a finger slip.

Jordan Hagan: Just trying to clap and say bye.

Harry Rook: All thank you guys for tuning in and overall on different time zones. So yeah, appreciate you guys taking the time to join and I'll see you on the next call.

Parvez Shaikh: Thanks, Harry.

Jordan Hagan: Bye everyone.

Paul O'Leary: Yeah, thank you.

Simon Dosch: Thank you. Bye.

Paul O'Leary: Thank you.

Meeting ended after 00:48:03 👋
