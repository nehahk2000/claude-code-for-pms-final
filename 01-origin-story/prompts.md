# 01 · Origin Story — prompts

**Context:** Rook Industries makes software for superheroes and the
people who handle them.

There are two products. Rook Dispatch is the one that gets a
superhero to where they're needed when there's an emergency — it
works out who is close enough and free enough to help, and gets hold
of them. Rook Supply keeps a responder's equipment serviceable and
accounted for, so a handler is never guessing whether the gear will
hold.

You joined two weeks ago as PM on Rook Dispatch. Release 4.2 shipped
on 12 August, shortly before you arrived. Responders have stopped
answering their phones the way they used to, and complaints have
gone up sharply. You were not in the room for any of it.

At the end of the session, type wrap up, and Claude Code saves the
prompts you wrote yourself below — not the starter prompt — updates
CLAUDE.md, and saves your work to GitHub. By Module 6 this file is a
prompt library built from your own questions.

---

### 1.
What can you reach from here? List what's in this folder, what's in Rook's wiki, and what tables are in Rook's database.

### 2.
I just joined Rook Industries as PM for Rook Dispatch. Read everything in 00-rook/company/ and add to the CLAUDE.md at the root of this folder, what you'd need to know to help me do my job here: the products, the people, the vocabulary, where things stand. Leave the session scope block at the top. Keep it under two pages

### 3.
give me the tldr. what is going on with 4.2

### 4.
how many active responders are there and how many callout company gets on daily basis, weekly basis and monthly basis

### 5.
how are pings redirected like who gets the pings

### 6.
give me pre and post 4.2 timelines

### 7.
what problems is 4.2 trying to solve

### 8.
what are the problem statements 4.2 is trying to solve for?

### 9.
organize this into a table

### 10.
before we compare with 4.2, which of the 7 problems did we attempt to address in 4.2?

### 11.
6 and 7 is timeout and availability confidence

### 12.
add this comparison to CLAUDE.md

### 13.
before we move forward, i want to get a read from you, how confident are you in your understanding of the problem statements, the data, and how the data correlates with the problem statements. give me your score on a scale of 0-95, and lets figure out how to improve your confidence together

### 14.
based on everything now you know about Rook, what should I be worried about that nobody has told me?

### 15.
hard to understand ,Re-frame it based on the problem statement we have been working on. Start the statement as "I am a" and then add evidence columns and low, medium and how sure

### 16.
what are the filters?

### 17.
When a handler gets a ping and they accept it, do they still show available after accepting it? Also, when a handler finishes the task, how much time should  they mandatory wait to get another ping?

### 18.
are all these 11 cases very close to the proximity or were far?

### 19.
yes, can we take alook at the cases where the pings went out of the area while other near by was shown available

### 20.
Lets take a step back and align on what I need from you.  In order to best understand how even we arrived at 4.2,I want to better understand the research from customer interviews . Before you share anything, interview me so that you understand what I require as a Product manager at Rook industries responsible for all things Dispatch

### 21.
Within the repo or notion notebook, is there any data around the call out history that has ping information.

### 22.
ok use  the data that is there in pings. Every row is one responder in one week: how many times we pinged them, and how many of those they took. Release 4.2 shipped on 12 August. Tell me what changed after that date. Show me the weekly numbers before and after, and show me the rows you used to get them.

### 23.
what was changed when pings went to the responders. Did handler immediately pinged them and what was the duration when they could accept the ping before and after release

### 24.
what is the last column that says responders pinged?

### 25.
Did anything change in the farlight database or data related to farlight ?

### 26.
pull all the tickets and see if it matches the narrative and the data

### 27.
when someone missed the ping  first time, does it impact the pings that they get even though they are available

### 28.
add this to CLAUDE.md

### 29.
what were the call outs in the area

### 30.
can we callouts column  in the above table for farlight

### 31.
ran the same query who did not go quiet

### 32.
this is interesting, vantage has a home base but also gets pings in different area. Is there a way we can visualize this on some kind of map/visualization to show the blasts of pings pre 4.2 and post 4.2

### 33.
and also who the pings was origniallt send to but who ended up taking it instead and the respective home bases as well to see which heros accepted pings in location outside their homebase

### 34.
add the npbody took it flow and then make this blast maps a sharable artifact

### 35.
looking at the data this way, it almost feels like location no longer matters, was that intentional as part of 4.2 and does it change anything with the analysis we have been putting together

### 36.
publish it as a shareable artifact

### 37.
add the other findings to CLAUDE.md including all visualization and blasts maps

### 38.
Also, tell me what area was least impacted where the pings did not go outside or missed a whole lot. Provide all this information area wise

### 39.
can you explain what does step 7 means

### 40.
can we get more details for Farlight from  offer.py, availability.py , routing.py and history.py

### 41.
tell me if restart happened after 4.2

### 42.
can you tell me looking at the routing.py why the ping was turned down by farlight based on his skills

### 43.
“Did the 4.2 release restart the service, and do acceptance scores survive a restart?. What will happen if you add this as an open question in Claude.md?

### 44.
can you pull the similar data for others like Farlight who stopped getting pings after certain time

### 45.
I want the scores list as you had provided for farlight

### 46.
have these weigtages changed after 4.2 with respect to location, skills etc like .6, .15 and .25. What was it previously?

### 47.
Does that mean , the score went down because the pings were coming outside 20 minutes of where farlight is

### 48.
use all the data sources plus this code walkthrough to help paint a picture for me on what is going on. i want to understand if what was asked to be build was actually built as intended, whether there's any bugs, and what would i tell a developer to do if we decided we want to make any change to prevent heros from silently existing the system because they missed/ignored/rejected a call

### 49.
was an AB testing done when the time was changes from 90s to 60 s and the driving time was reduced from 38 minutes to 19 minutes and responder was penalized?

### 50.
Did Marcus asked a question

### 51.
add this to CLAUDE.md

### 52.
Install news skills
