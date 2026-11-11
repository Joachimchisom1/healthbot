# Zatti Health Bot

A lightweight web-based **health-care chatbot** built with Python and Flask. Users chat with the bot in a browser, describe how they feel, and the bot replies with simple, everyday guidance about common conditions and symptoms (colds, cough, fever, asthma, chicken pox, and more). It can also tell the time and date, and when it doesn't know an answer it can hand the question off to a Google search.

> ⚠️ **Disclaimer:** This project is for educational and demonstration purposes only. It is **not** a medical device and does **not** provide professional medical advice. Always consult a qualified doctor for real health concerns.

---

## Why this project was built

Many people reach for the internet the moment they feel unwell, but general search results can be overwhelming and hard to trust. This project was built to explore a simpler idea: a friendly, conversational assistant that answers common health questions in plain language.

The goals were to:

- **Make basic health information approachable** through a chat interface instead of long articles.
- **Demonstrate a chatbot that needs no heavy machine-learning stack** — it learns entirely from a plain CSV file that anyone can edit.
- **Serve as a teaching project** for Flask, request handling, and simple text-matching techniques.

Because the "knowledge" lives in a spreadsheet-style file, you can grow or change what the bot knows without touching the Python code.

---

## How it works

The bot does **not** use a neural network or external AI API. Instead it uses **string-similarity matching**:

1. Every question/answer pair lives in `data/chatbot.csv` (column 1 = what a human might say, column 2 = how the bot should reply).
2. When a user sends a message, the bot compares it against every known question using Python's `difflib.SequenceMatcher` similarity score.
3. If a line scores above the **confidence level** you set, it becomes a candidate answer. The bot picks the best (or a random close) match and replies.
4. Special reply codes trigger built-in behavior:
   - `getTIME` → returns the current time
   - `getDATE` → returns today's date
   - `IDKnull` → a "I don't know" style response, optionally followed by a Google search link.
5. Every exchange is logged to `BotLog.csv`.

### Project layout

The application lives inside the `Python-LiveChat-master/` folder:

| File / folder            | Purpose                                                                 |
| ------------------------ | ----------------------------------------------------------------------- |
| `chat.py`                | Main Flask application — run this to start the bot.                      |
| `botConfig.py`           | Settings: bot name, avatar, background, time zone, confidence level.    |
| `botRespond.py`          | The matching engine that picks a reply from the knowledge base.         |
| `dateTime.py`            | Returns the current time and date in the configured time zone.          |
| `data/chatbot.csv`       | The bot's knowledge base — edit this to teach it new things.            |
| `avatar.py`              | Helper to download and set a new bot avatar image.                      |
| `templates/index.html`   | The chat web page.                                                       |
| `static/`                | CSS, images, sounds, and other front-end assets.                        |
| `BotLog.csv`             | Auto-generated log of every conversation.                               |
| `requirements.txt`       | Python package dependencies.                                            |

---

## Requirements

- **Python 3** (developed with Python 3.10)
- pip

---

## Setup & installation

1. **Download or clone** this repository, then open a terminal.

2. **Navigate into the application folder** (the code is in the `Python-LiveChat-master` subfolder):

   ```bash
   cd Python-LiveChat-master
   ```

3. **(Recommended) Create and activate a virtual environment:**

   On Windows:
   ```bash
   python -m venv venv
   venv\Scripts\activate
   ```

   On macOS / Linux:
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```

4. **Install the required packages:**

   ```bash
   pip install -r requirements.txt
   ```

   This installs **Flask**, **Pillow**, **pytz**, and **pyperclip**.

---

## Running the bot

From inside the `Python-LiveChat-master` folder, start the app:

```bash
python chat.py
```

The server starts on **all interfaces on port 50**, so open your browser to:

```
http://localhost:50/
```

or `http://<your-computer-ip>:50/` from another device on the same network.

> **Tip:** If port 50 is unavailable or blocked on your machine, change the last line of `chat.py` to use a different port, e.g. `application.run(host='0.0.0.0', port=5000)`, then visit `http://localhost:5000/`.

Type a message such as *"I have a cough"* or *"What time is it?"* and the bot will respond.

---

## Customizing the bot

Open `Python-LiveChat-master/botConfig.py` to change how the bot looks and behaves:

```python
myBotName       = "Zatti Health Bot"   # The name shown in the chat window
botTimeZone     = "Africa/Lagos"       # Used for time/date answers
botAvatar       = "/static/zatti.png"  # Avatar image (static file or URL)
chatBG          = "/static/care.webp"  # Chat background (static file or URL)
useGoogle       = "yes"                # Offer a Google link when the bot doesn't know
confidenceLevel = 0.80                 # Match threshold between 0.00 and 1.00
```

- **Teach it new answers:** add rows to `data/chatbot.csv` in the form `question,answer`. No retraining step is required — the bot reads the file live.
- **Tune accuracy:** a higher `confidenceLevel` makes the bot stricter (fewer but more precise matches); a lower value makes it answer more loosely.
- **Change the avatar:** run `python avatar.py` and paste an image URL when prompted.

---

## License & attribution

This is an educational project. Review and adapt it freely for learning purposes. It is not intended for clinical or production use.
