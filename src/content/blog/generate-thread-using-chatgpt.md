---
title: "Generate Twitter/X Thread using ChatGPT"
description: "Using ChatGPT to generate a thread on Twitter/X"
pubDate: "May 27 2024"
heroImage: "/blog-placeholder-2.jpg"
---

I described how I used [Playwright to post a thread on Twitter/X](/blog/thread-using-playwright).
That's an essential part of building self-managed social media account. Our system needs to be able to generate the content and post it. Until now, I covered how to post. In this article I show how to generate the content to post.

Enter, unsurprinsingly, ChatGPT.

I used ChatGPT to give me an idea on how to start building a self-managed social media account.
It suggested to created agents with different roles: `writer`, `engagement`, `trends analyzer`, `spam`, among others.

It also provided me with some code examples using the Twitter API. Since I'll be using Playwright for the interactions, most of the suggested code was discarded. But I kept the concept of using _agents_.

Ideally, I will have a system where I specify a topic and the system starts creating content periodically about that topic.
The topic I chose to post about is _Mental Models_. There are several mental models (wide range) and each one is rich (deep range).

Since these are the first days of the system many things will be manual or hard-coded.

I created my own agent called _planner_. Its task is to choose a mental model and explain it in a series of tweets to be posted in a thread.

```
prompt: Explain the details of the mental model "{model}" in a series of tweets.
```

I'm using [langchain](https://www.langchain.com/) to facilitate the interaction with LLMs. I built the simplest "reasoning" app.
In langchain there the concept of chains. It's like piping processing. There's also the concept of `model`, `templates`, `parsers` among others.
You can chain a template with a model with a parser to build a "reasoning" app.

Let's look at my specific example:

### The chain

```ts
// The template
const taskPrompt = `You manage a twitter account about mental models.
   Your task is to explain a mental model in a series of tweets.
   Each tweet should be no longer than 280 characters.
   Separate tweets with three dashes "---".
  `;
const introPrompt = `Explain the details of the mental model "{model}" in a series of tweets.`;
const promptTemplate = ChatPromptTemplate.fromMessages([
  ["system", taskPrompt],
  ["human", introPrompt],
]);

// The model
const model = new ChatOpenAI({ model: "gpt-4o", temperature: 0.7 });

// The parse
const parser = new StringOutputParser();

// The chain
const chain = promptTemplate.pipe(model).pipe(parser);
```

Now that we have a chain, we can give it an `input` and it will be processed by each part of the chain and provide an output.

```ts
const output = await chain.invoke({ model: "First Principles" });
```

The `output`, as I specified in the `taskPrompt` above, will contain several tweets separated by `---`.

```txt
first tweet
---
second tweet
---
...
---
last tweet
```

I process that `output` and save it to a `jsonl` file. This is the file read by the [Playwright Script that posts the thread](/blog/thread-using-playwright).

Eventually, I will update both systems to interact with each other. One will produce the content and the other one will post it.
For the time being, I execute the script to generate the content manually, move the file and run the script to publish the thread using Playwright.

I hope you don't judge my naive solutions while developing this self-managed social media accounts 😅.

### The code

Find the code of the [planner](https://github.com/michalevids/mental-models-gpt/blob/378db2b3d5fa9d405f72fb03192b1699e80b51ac/src/agents/planner.ts). Thanks for reading!
