# Alexa Entities Demo

This code sample shows how to use [Alexa Entities](https://developer.amazon.com/en-US/docs/alexa/custom-skills/alexa-entities-reference.html) to build a skill using Alexa's knowledge graph.

# ** This repository has been archived **
This repository is still available as a point-in-time reference, but no further updates or support will be prioritized.

## What You Will Need

* [Amazon Developer Account](http://developer.amazon.com/alexa)
* (Optional) [ASK CLI](https://developer.amazon.com/en-US/docs/alexa/smapi/quick-start-alexa-skills-kit-command-line-interface.html)
* (Optional) [Amazon Web Services Account](http://aws.amazon.com/)

## Setting Up the Demo

This JavaScript (Node.js) code sample includes the interaction model and skill code. Directories are structured to make it easy to deploy if you have the ASK CLI already setup (`ask configure`).

1. Open a command-line terminal.
2. Go to the directory where this `README.md` file is located.
3. Enter `ask init` and follow the prompts.
4. Enter `ask deploy`.

If you would like to use the [Developer Console](https://developer.amazon.com/alexa/console/ask), you can follow the steps outlined in the [Hello World](https://github.com/alexa/skill-sample-nodejs-hello-world) example, substituting the [interaction model](./skill-package/interactionModels/custom/en-US.json) and the [skill code](./lambda/index.js) when called for. Don't forget to update your version of npm's [package.json](./lambda/package.json) to specify the dependency on the axios library.

## Running the Demo

> User: Alexa, open knowledge demo.

> Alexa: Welcome, ask me about a country.

> User: Tell me something about Canada.

> Alexa: Canada's political leader is Justin Trudeau. Its capital city is Ottawa.

## Step-by-step Instructions

This section is going to walk you through the implementation of this code sample. Consider you want to build an Alexa skill that provides information about a country.

### Interaction Model

First, define a new intent `CountryKnowledgeIntent` in your interaction model.

```
tell me something about {country}
what can you tell me about {country}
what do you know of {country}
```

Assign the `AMAZON.Country` slot type to the `country` slot. Save the model and rebuild it.

### Intent Handler

Next, implement an intent handler for `CountryKnowledgeIntent`. With Alexa Entities, the `AMAZON.Country` built-in slot type now returns Alexa Entity Identifiers that correspond to the matched slot value. Alexa Entity Identifiers are URLs that can be "dereferenced" to obtain knowledge about them.

Check out the `CountryKnowledgeIntent` handler in `index.js` to see how it is implemented. Here's a high-level overview:

1. Get the API access token from the intent request.
2. Get the Alexa Entity Identifier associated with the `country` slot value.
3. Send a request to the Linked Data API to fetch information associated with entity.
4. If the request is successful, look up the value of the `politicalLeader` and `capital` properties.

Here, we've used the axios library to make HTTPS requests, but any HTTPS client library can be used to call the API. Find out more about the API in the [documentation](https://developer.amazon.com/en-US/docs/alexa/custom-skills/linked-data-api-reference.html).

### Skill Invocation

Let's see what happens when a customer invokes our test skill. For example, they might say:

> Tell me something about Canada.

If "Canada" could be resolved to an entity, the slot results include an Alexa Entity Identifier that looks like this: https://ld.amazonalexa.com/entities/v1/KergWGkJmd9FT9YSvzn3AL.

The intent handler processes the request, gets one of the Alexa Entity Identifier and sends an HTTPS request to the Linked Data API. The API returns a JSON-LD document containing information about the requested entity (Canada). See the API documentation for a [sample response](https://developer.amazon.com/en-US/docs/alexa/custom-skills/linked-data-api-reference.html#response-body).

Finally, the intent handler builds the answer using the information obtained, prompting Alexa to respond:

> Canada's political leader is Justin Trudeau. Its capital city is Ottawa.

## Next Steps

Now edit the code sample and build your own experience to learn more about what Alexa Entities can do. Here are a few ideas of things to try.

* **Use other properties.** Augment the response to mention the country's `humanPopulation`.
* **Traverse the knowledge graph.** Follow entity links and make another API call to obtain the `politicalLeader`'s `birthplace`.
* **Expand to other types.** Update the interaction model and write an intent handler to implement dialogs about `AMAZON.City` entities or other [supported built-in slot types](https://developer.amazon.com/en-US/docs/alexa/custom-skills/alexa-entities-reference.html).
