# PodQuery GPT

### What it does
Podcasts are a good medium for passive learning, especially during dead times such as driving or commuting. However, I noticed that finding a high-quality episode on a niche technical topic often requires manually sifting through many irrelevant content or "fluff."

So I made a custom GPT to automate this discovery process. It transforms a user intent (e.g., "I want to learn about X") into a micro-curriculum of specific podcast episodes. It is made to filter out casual banter and surface only the most informative episodes, hopefully saving a good chunk of research time.

### How does it works
- Before calling the API, it converts the user's natural language prompt into exact phrase matches to formulate a query suitable for Listen Notes.
- Executes a GET /search request to the Listen Notes API (with a few constraints).
- Parses the JSON payload to analyse the resulting episode descriptions and filters them to select the three highest-signal episodes.
- It arranges them into a structured order, justifying their selection and providing links for listening.

## Table of Contents

- [Project Tree]()
- [AAA]()

## Setup guide
### 1. Obtain your Listen Notes API Key
Go to the [Listen Notes API Page](https://www.listennotes.com/api/) and subscribe to the free plan. You will get 50 requests per month (the landing page says 300, but you'll see it is actually 50), which I think is more than enough for this tool.

Listen Notes will ask you to provide context about how and for which platforms you're going to use their service. They do it primarily to prevent scraping. You can copy and paste these descriptions:

- **Use case**: _I am developing an AI-powered educational curation agent built as an OpenAI Custom GPT. The tool utilizes the /search endpoint to transform natural language prompts into structured "Micro-Curricula." It fetches raw JSON metadata, applies LLM reasoning to filter for educational value, and returns a ranked list of episodes. The GPT operates statelessly within OpenAI's infrastructure. It does not pre-fetch, cache, index, or store any data on a back-end server._
- **Platforms**: _The application is accessible via the Web, iOS, and Android. As an OpenAI Custom GPT, it is not a standalone downloadable app but operates entirely within the official OpenAI ecosystem. It is accessible to users simultaneously through the ChatGPT web interface and the official ChatGPT mobile applications for iOS and Android._

Within minutes you should receive a confirmation email and be able to navigate to your [Listen Notes API Dashboard](https://www.listennotes.com/api/dashboard/) where you can copy your API Key (make sure you keep it secure, do not share or publish it anywhere).

#### Why Listen Notes and not Spotify?
Primarily:

- Spotify’s API does not support duration filtering in the search query; you would have to fetch irrelevant 2-minute trailers and filter them out afterwards, wasting tokens and processing power.
- Similarly, you can instruct Listen Notes to search only the title and description, ignoring authors and audio transcripts. Spotify searches all metadata fields simultaneously which would flood results with irrelevant hits.
- Listen Notes indexes the entire open podcast ecosystem (RSS feeds from everywhere, including Apple Podcasts), Spotify is more of a closed garden that (understandably) prioritises its own content.

### 2. Initialise the custom GPT
- Create a new GPT using the _Configure_ tab.
- Disable _Web Search, Canvas, Image Generation and Code Interpreter & Data Analysis_: leaving these enabled can cause the model to get "distracted" and, for instance, attempt web searches instead of strictly using your API.
- I recommend selecting _GPT-5.2 Instant_ as the default model: this tool does not require deep problem-solving capabilities; it simply needs adherence to a YAML schema. We also don't want a 10 to 30-seconds delay or the model to second-guess our instructions.

### 3. Define the instructions
Paste this block of text into the _Instructions_ field.

#### Brief explanation on the instructions I gave
- **Step 1: Query Reformulation & Optimisation**:  
