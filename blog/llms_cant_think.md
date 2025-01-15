# LLMs can’t think (yet)

I use ChatGPT almost daily. It assists with anything, but I find it especially helpful in areas I have very little knowledge of. Whenever I have to do something in a field I know nothing about, asking ChatGPT gives a good start on that. Let’s say I want to fix my cars broken headlight; I ask the chatbot and get a list of however many things I need to pay attention to and often that's enough. In many ways, ChatGPT acts like a database in that use case, even the older versions without internet access. Just a faster way to interact with data, a better interface to the vast knowledge the model was trained on. If used for those beginner type questions models work really well. When problems get harder however, AIs can make up facts, they hallucinate. This is getting a lot better and AIs will now rarely make things up. Of course models can still be talked into agreeing with almost anything, if used normally though they usually won’t.

So where is the problem?

AIs are bad at thinking deeply about questions asked. This is nothing new, people noticed this since the very beginning. And while some efforts have been made to change this, in January 2025 large language models (LLMs) are still bad at solving hard or new problems. Today’s systems often struggle with combining concepts in a new way, using multiple steps in a thought process and generalising things.

AI chatbots cannot innovate, their ideas won’t change the world. At least the current systems won’t. In this post I will tell you why that is and what a possible solution looks like.

## Thinking fast and slow
Daniel Kahnemann gives a nice mental model in his famous book “thinking fast and slow”. In the book he explains how humans have roughly 2 modes of thinking, system 1 and system 2. System 1 is handling the intuitive decisions, usually those happen very fast in an almost automatic fashion. This is contrasted by system 2, which handles more complex decisions and thought processes. 

When you are having a drink, system 1 will tell you how to move your hand and fingers to do it, while system 2 might be thinking about how you could save taxes for the purchase of the drink.

When somebody asks you for your name, system 1 is fast to answer, no thinking required.
Current AI chatbots are in many ways analogous to Kahneman’s system 1. In fact, LLMs are so great at emulating system 1, I’d go so far as to say they are better than most humans. This analogy works well for LLMs, they just give word after word, token after token, there is no backtracking or thinking in between. So if the answer wasn’t correct initially, in the fast, intuitive way the LLM answered, it might just be wrong. LLMs cannot think before they talk, an action often recommended to humans.

## Why thinking matters for businesses
After founding a startup, it became second nature to ask “Why does this matter?”. So why does it matter how LLMs think?
Right now, most LLM solutions that already provide value are assistants. Of course agents are being developed, but they haven’t really taken over the world. We use ChatGPT to help write emails, CoPilot to autocomplete our code and generate images using Dall-E (or now ChatGPT). Those are assistants. Of course they make people a lot more productive and have a real impact, but you cannot really give them full tasks, leave them be and come back to a solved problem like you would with a human.

This does not mean replacing humans, but it means simple tasks can be given to the LLM and it will think about how to do it and make sure it’s done. Imagine asking an AI to plan the company Christmas party and it not only looks for a good date, but it also orders food and snacks in line with the theme it gave the party and sends out invitations, all without any oversight needed.

Such a system is often called “Agent” or “Agentic AI”. This sounds good, however, I think the amount of agency an AI system has, is up to the implementation. To really enable those agents to be useful, more needs to be done, LLMs need to get better at thinking.
So while AIs already have some impact, to really change the world, they need to be able to solve harder problems on their own, they need more system 2 type thinking.

## Thinking step-by-step

Lots of people work on enabling LLMs to think. One interesting new idea is test time compute. In a way, this is an attempt to introduce system 2 thinking into the language model by giving the ability to think about the problem before giving an answer. To understand this we need to make a quick sidetrack into how LLMs work. LLMs get text and predict the next word, then the next and so on. For every word they think the same amount of time, they use the same amount of computation. The word “it”, for example, takes the same amount of thinking as a much more complicated word like “relativity”.

The idea of test time compute is to give LLMs the ability to use more compute on the next word or the full answer, allow them “think harder”. And while there are various ways to implement this, the concept is simple - and very related to system 2 type thinking.

Chain of thought (CoT) prompting is one of the techniques to make LLMs “think” about problems. It involves asking the LLM to create a plan and “think step by step” when answering.

Instead of just outputting the solution or answer, the LLM outputs a thought process as well. This does generate better results, which is quite amazing. There are multiple similar techniques, some more sophisticated, like tree of thought and many more. They all work in a similar way as chain of thought.

Let’s for example say we have a simple question, here’s what the answer might be without chain of thought.

![image](./blog_llms_cant_think-strawberry.drawio.svg)

Now a possible chain of thought output:

![image](./blog_llms_cant_think-CoT.drawio.svg)

While this is a simple example it shows how laying out the process avoids mistakes.
This, however does not really solve the problem completely. Users have very little control over how hard the model should think. Additionally, imagine the LLM is starting to solve a problem and makes a mistake. There is no way for it to go back and fix that. It will continue and step-by-step get to the wrong solution. CoT is certainly an improvement, but it does not solve the underlying problem with LLMs.

## Think until you’re right

Even with newer methods like chain-of-thought prompting, AIs still often produce wrong output. When using ChatGPT I often simply ask the bot to try again and give it some more information.

That often works and I get the answer I’m looking for. So how could we automate that? This is where verification comes in.
One way to improve is to build a more static, non-LLM system around the LLM. This can be as simple as the following program:

![image](./blog_llms_cant_think-verifier.drawio.svg)

With this simple program, the LLM can try again and again until it eventually comes up with the correct solution. When wrong, it’s reasonable to give some hint that the previous solution was tried and wrong, but that can be in a condensed form, enough to make the LLM not try the same thing again but no more than that. This is exactly what we already do manually when ChatGPT is wrong.

Consider the following example:
Let’s say I want to buy a car, do that as cheap as possible and ask the LLM to do that for me.
The process would go as follows:
* Step 1: LLM suggests an Audi from 2010 on eBay.
* Step 2: Verifier checks the listing actually exists and is the cheapest.

-> Turns out, this listing does not exist. Go back to step 1 and try again.
* Step 1 (2nd try): LLM suggest a VW from 2001 on Ebay
* Step 2 (2nd try): Verifier checks the listing actually exists and is the cheapest.

-> The listing exists and is the cheapest.

I can now go buy this car if I want to. Clearly giving the LLM a second try made the answer better and more useful to the user. 
Of course, this has two problems. The first one is a classic in computer science. How do we know the LLM finds the solution at all? What if it just loops through the program forever? Well, maybe it won’t. But this isn’t a practical thing, a simple max loop count will deal with this. This can even be an advantage, we can for example provide an ”I don’t know” as the output if the LLM doesn’t manage to solve the problem in 5 tries.

The second issue is a bit harder to deal with. It has to do with step 2, the verification. How does that work? What problems do we have verifiers for? How can we make new ones?

## How GoatSwitch AI used verification 

In this part I want to go on a bit of a tangent and tell you about a startup I used to have, GoatSwitch AI. While it didn’t work out in the end, we had some nice insights into LLMs while trying to modernise code. What our software did was take existing, working code and translate it into a modernised version, maybe even a different language. This is an ideal use case for the verifier-based System 2 thinking LLM. There are multiple things that make it such a great use case.
1. Unittests are standard practice in software development.
2. When modernising, functionality should be kept the same. No new functionality is added.


These two points enable LLMs to perform far better than on most problems. The key step is to take the unit tests and only accept the modernised code if the tests still pass. This means we could generate 5, 10 or 100 tries to modernise code with the LLM and only take the single on passing the tests. For easy modernisation problems, the first try often already worked, especially after GPT4 was available. For harder problems, with loads of deprecated code, dead libraries and syntax changes it would often take 8 or more tries.

I haven’t seen a use case like this yet that leverages LLMs just as well, but I’m sure they will come.

## A way to verify anything
At this point you should be convinced that verifiers make the current LLMs much stronger and enable them to solve real problems. But how to get those verifiers? What problems do we have verifiers for (except coding)? What problems can we even make verifiers for?

Unfortunately, most things are not as easy to verify as code is. The one exception might be math, but most people do not solve high-level math problems in their day-to-day.
Most tasks are not that easy to verify, often it’s hard to even say what a correct solution might look like. Let’s say I want my AI to design a roadmap for a new product my company wants to develop. There are a million ways to do this, and any of them might be acceptable to me. In this case it’s even harder to build a verifier, because multiple correct solutions exist!

While this is not a solved problem, quite a bit of progress was made on it and (allegedly) that progress enables OpenAI’s new o1 and o3 models.
The solution is to use more LLMs and introduce a “verifier-LLM”. This model then judges if the solution and the process the llm came up with are correct. Obviously this is not a perfect system, but it is better than nothing. It’s easier to judge if something is good than to come up with something good, so you get an improvement. Right now, to my knowledge, this is mostly used to generate training data for LLMs, but it can just as well be used to iterate on difficult problems and provide better solutions.

## A future of thinking agents
AIs cannot really think yet, but they are certainly moving in the right direction. Startups are already working in integrating agents for any task imaginable and soon the LLMs will be good enough to actually enable those agents to work well. And when everything works, your life will be different. You will do different things, and half of what annoyed you before will be solved by an AI agent for just that problem. That future isn’t quite here, but it will be when LLMs have learnt to think.

