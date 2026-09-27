# AI Shopping Assistant Automation

This project contains an n8n workflow for an AI-powered shopping assistant that runs through Telegram. It can handle both text messages and voice messages, transcribe voice notes, search for products, give styling advice, and send the final response back to the user on Telegram.

## What this automation does

The workflow follows this process:

1. A user sends a message or voice note in Telegram.
2. The Telegram Trigger node listens for incoming messages.
3. The workflow checks whether the message contains a voice file.
4. If the message includes voice, it downloads the Telegram voice file.
5. The Python code node fixes the file name by converting .oga to .ogg.
6. The Groq Whisper transcription API converts the voice file into text.
7. The AI Agent receives the user input (either text message or transcribed voice text).
8. The agent decides whether the request is:
   - a product search request, or
   - a styling advice request.
9. If it is a product search, the workflow builds an Amazon India search URL and calls the Scraper API tool.
10. The model extracts relevant product information such as name, price, rating, and product URL.
11. The assistant returns a clean, user-friendly response with top product suggestions.
12. The workflow sends the final answer back to the user using Telegram.

---

## Workflow steps in detail

### 1. Telegram Trigger
- Starts the automation whenever a new Telegram message arrives.
- Watches for updates such as `message`.

### 2. If condition
- Checks whether the user sent a voice message.
- If `message.voice` exists, the workflow continues to the voice-processing branch.
- Otherwise, it sends the message directly to the AI agent.

### 3. Get a file
- Fetches the voice file attached to the Telegram message.
- Uses the file ID from `message.voice.file_id`.

### 4. Code in Python (Beta)
- Adjusts the voice file metadata.
- Replaces `.oga` with `.ogg` so the file can be processed correctly by the transcription API.

### 5. HTTP Request to Groq Whisper
- Sends the converted audio file to:
  - `https://api.groq.com/openai/v1/audio/transcriptions`
- Uses the `whisper-large-v3-turbo` model.
- Sets:
  - `temperature=0`
  - `response_format=verbose_json`
  - `language=en`
- Converts the user voice note into text.

### 6. AI Agent
- Receives the text from the user or the transcribed voice.
- Uses a configured Gemini chat model.
- Follows the assistant instructions to decide if the user wants:
  - shopping/product recommendations, or
  - styling guidance.

### 7. Scraper API tool
- Used only for product shopping requests.
- Builds an Amazon India search URL using the user query.
- Example format:
  - `https://www.amazon.in/s?k=white+sneakers+under+3000`
- Extracts search results and product data from the page.

### 8. Send a text message
- Sends the final generated response to the same Telegram chat.
- Uses the sender’s chat ID from the message metadata.

---

## Required services and credentials

Before importing and running the workflow, make sure you have the following:

1. n8n installed and running
2. Telegram bot token and webhook access
3. Groq API key for speech-to-text transcription
4. Google Gemini API credentials
5. Scraper API key for Amazon product searches

You will need to configure credentials inside n8n for:
- Telegram
- Groq
- Google Gemini
- Scraper API

---

## Setup steps

### 1. Import the workflow
- Open n8n.
- Create a new workflow.
- Import the file `Build Your Own AI Shopping Assistant _ Part 2.json`.

### 2. Configure Telegram credentials
- Create or connect your Telegram bot.
- Set the bot token in the Telegram Trigger and Telegram message nodes.

### 3. Configure Groq API
- Add your Groq API key in the HTTP Request node.
- This is used for voice transcription.

### 4. Configure Gemini
- Add the Google Gemini credentials in the Gemini chat model node.
- This powers the AI assistant logic.

### 5. Configure Scraper API
- Add your Scraper API key in the Scraper API node.
- This allows the assistant to fetch live Amazon product listings.

### 6. Activate the workflow
- Save the workflow.
- Activate it in n8n.
- Make sure the Telegram webhook is correctly set up.

### 7. Test the automation
- Send a normal text message like:
  - `Show me running shoes under 2000`
- Or send a voice message asking for:
  - `Find me a dress for a wedding`
- The assistant should respond in Telegram with product suggestions or styling advice.

---

## Example user flows

### Product search example
User message:
`show me running shoes under 2000`

Automation flow:
1. Telegram Trigger catches the message.
2. AI Agent identifies a shopping request.
3. Search URL is built for Amazon India.
4. Scraper API fetches the results.
5. Top products are summarized and sent back to Telegram.

### Styling advice example
User message:
`What should I wear for a wedding in winter?`

Automation flow:
1. Telegram Trigger catches the message.
2. AI Agent recognizes styling advice intent.
3. It asks clarifying questions if needed.
4. It gives outfit suggestions, color advice, accessories, and styling tips.
5. Final response is sent to Telegram.

### Voice message example
User sends a voice note:
`I want shoes for office under 3000`

Automation flow:
1. Telegram Trigger catches the voice message.
2. File is downloaded.
3. Audio is transcribed with Groq Whisper.
4. AI Agent interprets the text.
5. Product search happens through Scraper API.
6. Results are sent back in Telegram.

---

## Important notes

- The workflow is designed for Telegram messaging automation.
- It combines voice transcription, AI decision-making, web scraping, and message delivery.
- Product search is limited to Amazon India-style search URLs.
- For production use, validate all API credentials and make sure your n8n instance has internet access.

---

## File included

- `Build Your Own AI Shopping Assistant _ Part 2.json` – the exported n8n workflow

## Suggested next improvements

- Add product filtering by budget, brand, or category
- Save shopping history in a database
- Add product links for direct purchase
- Add a fallback response when no results are found
- Add logging for successful and failed requests

---

## Summary

This automation turns Telegram into a shopping assistant that can:
- understand text and voice requests,
- transcribe voice notes,
- search for products,
- provide personal styling advice,
- and reply directly to users in chat.
