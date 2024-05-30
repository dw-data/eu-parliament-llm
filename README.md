# EU parliament speeches text analysis

This repository contains the source code and input data for the DW story that used a large-language model fo analyze the speeches of the 9th European Parliament.

## Brief description of the methodology

### 1. Obtaining the verbatim reports of the European Parliament
All the speeches were obtained from the [website of the European Parliament](https://www.europarl.europa.eu/plenary/en/debates-video.html). We extracted all the verbatim reports available from 2004 to 2024, and, later, decided to focus on the speeches from July 2019 to April 2024 – that is, the entirety of the 9th European Parliament. The details of the data collection can be found at the `code/scraper` directory.

### 2. Send queries to GPT-3.5-turbo asking for a series of tags to be applied to each of the individual speeches
Before sending the speeches to the LLM for classification, we made some cleaning and filtering. We removed all the speeches shorter than 280 charcters, in an effort to get rid of prodecural statements such as the president saying "the session is now open'. We also removed two entries that were above 16,000 tokens, which would exceed the context window of the model we used for analysis, GPT-3.5-turbo. We then send the requisitions to the model using the LangChain Python package.

Each one of the speeches was then marked with tags by the model, without expliciting a predetermined set of categories. As a result, we received around 8000 individual classifications. We decided to limit our analysis to the top 100 categories that occured most – which accounted for around 99% of the speeches.

Those 100 categories were then later divided into 9 broader groups (health; war and security; democracy and governance; economy, finance, and infrastructure; environment and climate; human rights, freedoms, and social issues; international relations and diplomacy; parliament issues and procedures; and technology and innovation), also with the usage of a LLM, coupled with manual revision. One speech could be classified into more than one topic.     

The details of the prompt and classification can be found at the `code/llm` directory.

### 3. Extracting country names
In order to count how often each country is mentioned, we decided to focus only in the common name of each country. For example, for Brazil, we counted entries that mentioned explicitly the text string "Brazil", but not "Brazilians" or any other kind of indirect reference.

In order to account for all the 24 official EU languages that were used in parliament, we collected country names from their respective pages in Wikipedia of each language and manually verified the correctness of each entry later. Some manual cleaning had to be done to ensure that all strings were generally comparable. For example, we made sure that we would be using the language equivalent of "South Korea" all of the time, instead of "Korea Republic".

Some caveats apply: sometimes, the name of a country in a language could be a common word in another. "Salvador", for example, is a commonly used name for "El Salvador" on Czech, but it's also the common word for savior in Portuguese and Spanish – languages in which the country would be adressed by the name of "El Salvador".

We checked each one of the highlighted countries to see wether there were similar patterns at play. This was the case for India and Thailand. "Indien", the German word for India, means "if" in dutch. "Tai", the name for Thailand in Estonian, is also a common word in Lithuanian, which could be translated as "this". After removing all the dutch langauge entries, India would still be mentioned often. Thailand without baltic languages, in the other hand, would not appear proeminently, so it was removed from the story.

All the details can be found in the `code/text-extracion` dictionaries.

### 4. Analysis
Finally, from the results, we were able to jump into the analysis.

First, we looked at all speeches that were classified (around 60,000) and counted how many of them had been classified into each one of the groups. Then, we did a similar process to check how many speeches directly mentioned each one of the countries and ranked them.

Finally, to determine which subjects appeared more often when each of the selected countries was mentioned, we compared the percentage of speeches that touched on a subject _within all speeches_ and _only within the speeches that directly mention that country_. 

For example, in the last five years, around 36% of the speeches in parliament were categorized into human rights, freedoms, and social issues. However, if we consider only the speeches that mention Afghanistan, that share rises to 74%. This means that concerns about human rights are highly characteristic of speeches about the country.