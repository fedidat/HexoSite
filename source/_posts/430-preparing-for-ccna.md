---
title: Preparing for CCNA
date: 2018-07-30 16:28:28
tags:
---

I haven't posted for the last two weeks, and I have a good excuse: I was (mostly) preparing for Cisco's famous [CCNA](https://en.wikipedia.org/wiki/CCNA) certification. And I passed it!

[![CCNA certified](/images/430-preparing-for-ccna/ccna.png)](https://www.youracclaim.com/badges/d0483c2b-984b-4b15-971d-6585bc2c447c)

CCNA is considered the entry-level certification for network engineers, although it allows you to do plenty already, and particularly for software engineers like me who rarely see any networking equipment other than their home router. So why did I pass it? Primarily for the certification itself, but also because it shows that I can understand what goes on in the layers beneath my code. In a backend team, a guy who can look at the packets and the ports can be very useful when something looks wrong between the logical layers!

In addition to my totally unrelated background, I passed my test with a decent score of 893/1000 and only after a sloppy week of learning besides the course itself a few months prior. So I'll share my experience and my secrets in detail to make up for the absence.

## Day 1: Getting back to it

Two months had passed since I had attended the CCNA course delivered by my workplace, and to be honest I had forgotten almost everything. I had to start somewhere.

Conventional logic would have you start from the beginning and learning everything again from scratch on Cisco's [Netacad](https://www.netacad.com/). I'll propose doing what I did: taking a couple of questions (and miserably failing most of them) to reminisce as much material as possible within 10 minutes. That will save you a lot time and brain work. While you do that, Google some of the tough concepts.

## Day 2: Re-learn everything

Now that I had a good idea about the problematic subjects and gave my brain a night to process all this stuff, I went over my entire course book, as well as covered pain points on Netacad. But basically, any complete resource will do. By this time, with little effort, you know *all* the concepts, just maybe not thoroughly.

## Days 3 to D-1: ~~Brain dumps~~ Resources

"Brain dumps" designate questions that someone remembered after passing the exam. The word has a negative association as it implies it's cheating and nothing more.

So on the one hand, I will repeat some common Internet wisdom: don't read and memorize brain dumps. You will simply fail your exam and/or your career. On the other hand, I want to address the stigma around even uttering the term "brain dump". It's bullshit. If you are at the stage where you are learning every corner of the material, learning *from* brain dumps is excellent. You can learn all the material using them as pointers. That's a fact, ignore the FUD.

Straight away, paying for 9tut is pointless. And while you will find ENDLESS spam about "free brain dumps [current year]", don't waste your time sifting through them. I did it for days and here's the good stuff:

- [CCNAv6](https://ccnav6.com/cisco-ccna-v3-0-200-125-study-guide-exam-dumps-vcepdf-latest.html): I don't know what the owners of this site are getting from this, but I love them. Free legit brain dumps in a great format. The link goes to 5 parts of a total of 340+ questions with answers which you can either print or solve online. Their labs are less good though. If the site helps you, consider donating to the owner.

- [9tut labs](http://www.9tut.com/category/ccna-lab-challenges): To me, 9tut is not nearly as good as other sites or as Googling some subject. The thousands of comments begging for "the latest dumps" do nothing to help the situation. And again, you will get little out of a premium membership.  
They have one good thing though: the lab challenges. They are very close to the real thing. On each article, click the link and you get the CCNA exam experience. Each troubleshooting simlet has links to the topology, questions under the instructions, and various consoles on the side. Others are basic forms on which you have to enter the right commands (although there is no such thing in the real exam). You could also read their [FAQ & Tips](http://www.9tut.com/ccna-faqs-a-tips).

- Want more questions? You can access the big 9tut collection [here](http://www.9tut.com/ccna-questions-and-answers) or the ["all free dumps"](https://www.allfreedumps.com/200-125-dumps.html) files. But you should rather take a look at [learncisco](https://www.learncisco.net/test-ccna.php?exam=200-125). Every time you access this link, it generates 55 random questions which you can take as a mini exam.

**In short, use the 340 questions of CCNAv6 to know the material really well, using Google when needed, and take all the lab challenges on 9tut to get accustomed to the relevant IOS commands and the interface of the exam. Any extra work is superfluous.**

## Preparing for D-Day

Preparing for the day of the exam, you should also do a couple of things:

### 1. Know the interface

You don't want to fail a question because you don't know how the interface looks. Take a look at [Cisco's videos](www.cisco.com/web/learning/wwtraining/certprog/training/cert_exam_tutorial.html).

### 2. Go over the commands

Print some [packetlife cheat sheets](http://packetlife.net/library/cheat-sheets/) to remember the commands and concepts. Go over them and make sure there's nothing you haven't covered.

### 3. Go over the questions again

Depending on your commute and exam time, you have some extra time to go over more questions. Do it, some questions are unintuitive and you should have a made a note of them. It's time to memorize them. That's right, I do encourage you to memorize *some* questions, and the blame is on Cisco for making some questions unclear.

## The exam

One thing I had trouble finding on the Internet is: how does the exam go? How does it look?

1. You arrive at the place. It starts to get intimidating with the camera in the room, and having to empty your pockets and put your belongings in the next room. The examiner will take your CSCO ID, your name and your picture and eventually enter his login in the Pearson certification software. You select your name and you are invited to start.
2. You have a tutorial presenting the interface, capped at 15 minutes. Nothing surprising. Next-next-next and finish, agree to some Cisco agreement, and press start.
3. The timer starts. You have 1.5 hours or 2 hours depending on whether you speak English natively, starting with question 1/53. You will usually start with the most common type of question: the single-answer with radio buttons. The 53 questions included 6 labs for me.
4. As you go along, you will probably realize that the interface is much better than it looks in [Cisco's videos](www.cisco.com/web/learning/wwtraining/certprog/training/cert_exam_tutorial.html). It's not that different, it's just that the resolution makes it look much more manageable. One notable difference is that you can move the windows around, instead of having to click back and forth like some people claim.
5. Eventually, you complete question 53. In my case, nothing happened, so I left the room and was told the result was in the printer next door. And it's that simple, I was immediately free to go!

## What I got

Time for some spoilers. I had six labs:

- Five were troubleshooting challenges: 
    - ACL and other little things, 
    - RIPv2 ([here](http://www.9tut.com/ripv2-troubleshooting-sim#more-2853)), 
    - EIGRP + GRE + Etherchannel three times ([here](http://www.9tut.com/eigrp-gre-troubleshooting-sim) and [here](http://www.9tut.com/gre-multilink-sim#more-3634) and [here](http://www.9tut.com/ripv2-troubleshooting-sim#more-2853)).
-  One lab needed configuring: VLAN ([here](http://www.9tut.com/vlan-troubleshooting-sim)).

One takeaway here: `show running-config` (AKA `sh ru`) goes a very long way and is used for 90% of lab questions. Coupled with `show cdp neighbors` to match the topology, it is very powerful. It showed almost everything I needed: EIGRP networks, encapsulations, VLANs, interface status, access lists, NTA, DHCP, etherchannels, etc. For the rest, a bit of `show ip protocols`, `show ip interface [int]/brief` completed the required tools.

Another note because I had no idea what to expect from the labs: the five troubleshooting ones had me enter commands on switches and routers to answer questions on the side. The configuration lab required using the IOS command line just as you would in a packet tracer or on real equipment, up to the point you thought the situation should be resolved. The only feedback here is a bunch of logging messages (in my case, VLAN mismatch errors) that stop appearing as you fix the problems.

As for the questions, about 80% were present as-is from the [CCNAv6 dumps](https://ccnav6.com/new-cisco-ccna-200-125-exam-dumps-latest-version-2018-free.html), including #11, #27, #32, #36, #42, #44, #88,#95, #186, #188, #203, #209, #235, #247, #312, #314. And a bunch of questions in the dump covered the material in many of the other questions I had. As you can see, not very scary stuff. The rest of them was sometimes challenging but nothing out of the ordinary if you understand the material from studying with the dumps.

That's it. I hope my experience will help someone out there. Good luck!