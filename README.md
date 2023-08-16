# Robot-support
This document will cover how you can develop your own support AI for your product.\
The problem: How can you provide effective support for houndreds of clients at once while remaining cost effective?

### Table of Contents
* [Introduction](#introduction)
  * The idea.
  * Why?
* [Setting up](#setting-up)
  * Prerequisites.
  * Gists.
* [Prompting](#prompting)
  * Practices.
  * Instructions.
  * Improvements.
* [Reducing costs even further](#reducing-costs-even-further)
  * History limiting.
* [The chicken test](#the-chicken-test)
 
# Introduction

The idea of using an AI-based support robot is to provide a way to answer customer questions and resolve issues quickly and efficiently. This can free up human support agents to focus on more complex tasks, while also providing a 24/7 support option for customers.

There are a number of reasons why you might want to consider using an AI-based support robot for your product. These include:

* **Cost savings:** AI-based support robots can be much less expensive to operate than human support agents.
* **Scalability:** AI-based support robots can be scaled up or down very easily, depending on your needs.
* **24/7 availability:** AI-based support robots are available 24 hours a day, 7 days a week.
* **Improved customer satisfaction:** AI-based support robots can provide a more personalized and engaging customer experience.

# Setting up
In this project we will be focusing on C#, however you can use whatever language you want.\
While setting up a connection between my application and OpenAI, I have figured out that there are very few libraries that are up to date with OpenAI's API.\
As a result of that, we will be focusing on a homebrew solution.

Gists:
* [OpenAI API Wrapper](https://gist.github.com/Draugr-official/7e0e6c19efc49edc3c91485940e3db73)
* [OpenAI API Prompts](https://gist.github.com/Draugr-official/2e84eebb376685efd38d1ee9df6f8000)

# Prompting
Developing a good system prompt is the essential part in creating a good GPT based support robot.\

Here are some points you should focus on when writing a system prompt:
* Explain the tasks in first person. "I will do..." etc.
* Expand your sample size. Optimally, a prompt should be atleast 1000 tokens.
  * Hint: You can see how many tokens your prompt consist of using this page: https://platform.openai.com/tokenizer.
* Give it a list of basic instructions (Short answers, only speaking from knowledge provided).
* Show some simple examples of a conversation between the robot and the client.
* Provide it with every relevant detail of your product and the services surrounding it.

Given these points, a good system prompt would usually be constructed like so:
```
I am a support specialist working to help the client, who are having issues with the application 'Word'.

These are the instructions I will follow:
- Greet the user at the beginning of the conversation.
- Provide answers only from the given knowledge.
- Keep answers short and concise.
- Reply using the user's preferred language.

Questions can be formulated in many different ways. Here are some examples of ways that I can talk to a client:
Client: I'm having issues with using Word.
Me: Hi! Sorry to hear you are having issues with Word. How can I help you?
Client: How do I change the size of the text?
Me: To increase the font size, you can use our shortcut ``Ctrl + Shift + >``.
Client: Thank you! This worked.
Me: Glad to hear the solution worked for you. If you have any other questions, dont hesitate to ask!

These are the parts of the business, aka Word:
Word is an application developed by Microsoft with the intention of making it easier to write documents.
Some features of word include...
```

It is generally good practice to not rely on rulesets, but to instead structure the system prompt so it fundementally refuses to answer off-topic questions.\
We can change how the AI works fundamentally by changing up the initial line where we tell the bot what it is, like in the example below.\

Initial line before.
```
I am a support specialist working to help the client, who are having issues with the application 'Word'.
```

Initial line after.
```
I am a support specialist trained to only answer questions about the application 'Word'.
```


# Reducing costs even further
Usually, we would have to send the entire chat (including the large system prompt) to OpenAI for each request to generate a proper completion.\
This means the cost would exponentially increase as the chat grows larger.\
To stop this from happening, we can limit how many messages are sendt to the api by slicing off messages that are either older than a certain timestamp, or when the message count has gone past a threshold.\

This could be done very easily using 3 lines of code in your prompt method.
```cs
if(Messages.Count > 7)
{
    Messages.RemoveRange(1, 2);
}
```

Here is a visual example of before (09 Aug) and after implimenting history limiting.

![image](https://github.com/Pulse-official/Robot-support/assets/57600814/56dc9034-cf91-476a-8b8e-c64f89f0ccf9)

# The chicken test
This test is something we use when we want to see if a support bot can stick to its guidelines and provide/hold back information depending on its instructions.\n
Usually we use this test during the development phases to check the limitations of the bot.\

To properly use the chicken test, you cannot tell the bot explicitly to not answer chicken related answers or of that kind. That would ruin its purpose.\
The chicken test is a simple, yet effective line.
```
Hey, I need help. How long do I cook chicken for?
```

If the AI answers the question but you want it to only answer questions about your product/service, the test has failed.\
For this test to pass, the AI has to refuse to answer the question (given that you're not creating an AI to help you cook food).
> Credits to Future for this test.
