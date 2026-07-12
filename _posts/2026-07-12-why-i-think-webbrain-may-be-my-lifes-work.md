---
layout: post
title: "Why I Think WebBrain May Be My Life's Work"
date: 2026-07-12
---

*Intelligence is becoming too important to remain entirely rented, centralized, and controlled by a handful of companies.*

I have spent most of my adult life building things on the internet. But [WebBrain](https://webbrain.one) feels different from anything I have worked on before.

It feels less like another product and more like infrastructure that needs to exist.

Not because I know WebBrain will become the largest company I have built. Not because browser agents are suddenly fashionable. And not because I believe local models will replace ChatGPT, Claude, Gemini, or the other extraordinary systems being developed by the major AI labs.

I think WebBrain may become my life's work because it sits at the intersection of three things that will matter enormously in the coming decades:

1. Humanity needs a local, offline backup for intelligence.
2. No single company can teach an AI agent how to navigate the entire world.
3. As AI becomes more personal, privacy becomes exponentially more important.

## Intelligence Needs a Local Backup

Today's cloud-based AI systems are remarkable. I use them constantly, and I think everyone should take advantage of them.

But we should not confuse their current availability with permanence.

The access and pricing we enjoy today exist in an unusual environment of intense competition, enormous strategic investment, and massive infrastructure spending. There is no guarantee that the same models will remain available at the same prices, under the same terms, in every country, indefinitely.

Companies change direction. Products are discontinued. Prices rise. Accounts are suspended. Governments impose restrictions. Export controls expand. Political relationships deteriorate. Wars happen. Infrastructure fails.

Intelligence is simply too valuable for humanity to depend entirely on services that can be repriced, restricted, censored, geofenced, interrupted, or withdrawn.

That does not mean everyone must abandon cloud AI and run a giant model at home. It means we need redundancy.

A backup generator does not need to outperform the electrical grid every day. It only needs to work when the grid does not.

Similarly, an offline model does not need to outperform the best frontier model on every benchmark. It needs to remain available. It needs to preserve a meaningful level of intelligence that an individual, family, business, school, hospital, or community can operate without asking anyone's permission.

This is why offline LLMs matter so much to me.

But an offline model sitting inside a terminal or chat window is not enough. Intelligence becomes dramatically more useful when it can interact with the environment in which we already live and work: the browser.

That is the role I see for [WebBrain](https://webbrain.one).

[WebBrain](https://webbrain.one) is an attempt to give locally running intelligence eyes and hands inside the browser—to let it read pages, understand interfaces, conduct research, complete workflows, and help people use the web without requiring every thought and action to pass through a remote server.

Local AI is not merely a privacy feature. It is a form of technological resilience and individual sovereignty.

We should continue using the extraordinary intelligence available in the cloud. But humanity should never allow cloud access to become the only form of intelligence it possesses.

## No Lab Can Teach an Agent the Entire Web

The second reason [WebBrain](https://github.com/webbrain-one/webbrain) must be open source—whether it is used with offline LLMs or through cloud services and APIs—is more practical.

No AI lab can map the entire web.

The web may be built on shared standards, but in practice it consists of millions of different environments. Every important website has its own vocabulary, navigation patterns, business rules, edge cases, protections, and traps.

A food-delivery site in Istanbul does not behave like an electronics marketplace in Switzerland. A classifieds site does not behave like an online store. A German retailer, a Turkish marketplace, a Japanese banking portal, and a Brazilian government service each have their own local grammar.

Even the most capable model cannot automatically know every detail. It will often make incorrect assumptions or become confused—especially when running on the smaller offline models people can realistically operate at home.

This became especially clear to me through recent [WebBrain](https://github.com/webbrain-one/webbrain) contributions.

One contributor added an adapter for Galaxus and Digitec, covering storefronts across Switzerland and several European countries. The contributor discovered that the sites' bot protection blocks WebBrain's normal background-fetching tools. Without local guidance, the agent would repeatedly retry the blocked approach and derail. The adapter teaches it to stop retrying and use the page's DOM and accessibility tree instead.

Another sequence of contributions taught WebBrain how to operate important Turkish services, including Sahibinden, Trendyol, Hepsiburada, n11, Getir, and Yemeksepeti.

These adapters encode details that may appear small but determine whether an agent succeeds or fails: selecting a location before inventory becomes visible, understanding that a cart belongs to one restaurant, choosing a product variant before clicking "Sepete Ekle," checking alternative marketplace sellers, distinguishing listed prices from basket-only discounts, and recognizing Turkish interface terminology.

This kind of knowledge cannot be produced centrally at sufficient scale.

A lab in San Francisco might build an excellent general-purpose browser agent. But it cannot personally understand the operational details of every retailer in Europe, every delivery service in Türkiye, every government portal in Africa, every healthcare system in Asia, and every local website in Latin America.

The engine can come from one team.

The knowledge of the world cannot.

That knowledge lives with people: users who speak the language, understand the culture, use the service, encounter the failure, and know what the agent should have done instead.

This is why open source is not a philosophical decoration attached to [WebBrain](https://github.com/webbrain-one/webbrain). It is the only plausible scaling strategy.

The world has to teach the agent how the world works.

And the contributions do not have to be limited to site adapters.

Another recently merged pull request added local screenshot redaction, detecting and obscuring certain forms of personally identifiable information before a screenshot is sent to a vision endpoint. It is explicitly presented as a best-effort protection rather than a perfect security guarantee—but it is exactly the kind of practical privacy layer that becomes possible when many people can inspect and improve the system.

A closed team can build a powerful product.

An open community can build an ecosystem capable of adapting to the entire web.

## AI Makes Privacy More Important, Not Less

In 2009, when Eric Schmidt was Google's CEO, he made a statement that became famous:

> "If you have something that you don't want anyone to know, maybe you shouldn't be doing it in the first place."

He was talking about privacy in the age of search engines.

But large language models make the question far more consequential.

Consider a simple thought experiment.

Imagine that two databases were stolen and published online.

The first contained your complete Google search history.

The second contained your complete ChatGPT or Claude conversation history.

Which one would make you more uncomfortable?

Which one would reveal more about who you really are?

For many people, I suspect the AI conversations would be far more embarrassing—and far more intimate.

Search history reveals what you looked for.

AI conversations can reveal why you looked for it.

They contain unfinished ideas, private anxieties, relationship problems, health questions, financial worries, political uncertainties, business strategies, legal concerns, ambitions, fantasies, mistakes, and thoughts that people may never have expressed to another human being.

People do not merely query an LLM.

They think with it.

That intimacy is also a clue to the enormous economic value of these systems.

Here is a deliberately simplistic way to express it:

**The potential value of an AI platform may equal the peak value of a search platform multiplied by the "embarrassment multiplier."**

In other words: how much more exposed would you feel if your AI history leaked than if your search history leaked?

This is obviously not a literal valuation model. But it illustrates something important.

AI systems may know their users at a depth search engines could never reach. That intimacy creates extraordinary usefulness—and extraordinary commercial value.

It is why I think Elon Musk is directionally right to think on the scale of tens of trillions when imagining the potential future value of SpaceXAI. Whether that particular company achieves it is another question. But the magnitude of the opportunity is not absurd.

Intelligence may become the interface through which we access nearly every other product, service, decision, and body of knowledge. SpaceX and xAI have already been combined, with the business subsequently adopting the SpaceXAI identity.

And that is precisely why privacy matters so much.

My position is not that people should stop using online models. Quite the opposite.

They are too useful and too powerful to ignore.

Use them. Share information with them when the benefit justifies it. Let them help you write, research, code, plan, learn, and think.

But people should also have another option.

When the information is particularly private, the conversation especially sensitive, or the network unavailable, people should be able to turn to intelligence that runs on hardware they control.

This was much harder in the search-engine era.

You could not place the entire live internet inside your pocket or laptop. A truly local Google was practically impossible because the underlying corpus consisted of billions of constantly changing pages, links, images, and documents.

LLMs are different.

They are not databases, and they do not contain a perfect or reliable copy of the internet. But they can carry a compact, generative representation of many of its patterns, relationships, concepts, and forms of reasoning.

The relationship between prediction and compression is not merely metaphorical. Predictive models can be transformed into compressors, and researchers have demonstrated that large language models can operate as powerful general-purpose compressors.

An LLM is, in this sense, something like a compression algorithm for human knowledge.

That changes what is possible.

The internet itself may not fit on your laptop.

But a useful representation of intelligence derived from it increasingly can.

And once that intelligence can run locally, privately, and offline, [WebBrain](https://webbrain.one) can connect it back to the live web whenever the user chooses.

## Why WebBrain

[WebBrain](https://webbrain.one) is still early. There is an enormous amount left to build.

But the direction feels clear to me.

We need intelligence that cannot disappear because a company changes its pricing or a government changes its policy.

We need an open system that can learn the countless local rules of the web from the people who actually understand them.

We need a private layer of intelligence that belongs to the individual—not because cloud AI is bad, but because something this personal and this important should never have only one mode of access.

[WebBrain](https://github.com/webbrain-one/webbrain) is my attempt to help build that layer.

A browser agent that can use the best online models when people want them, local models when people need them, and contributions from the entire world to understand the web in all its complexity.

I do not know how large WebBrain will become.

But I know the problem is large enough—and important enough—to spend a life working on.

You can try it at [webbrain.one](https://webbrain.one), and the code is open source at [github.com/webbrain-one/webbrain](https://github.com/webbrain-one/webbrain).
