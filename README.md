# Polarizer — Sentiment Analysis for Twitter

Polarizer is a small Python tool that fetches recent tweets matching a search
query, cleans them, and classifies each tweet's sentiment as **positive**,
**neutral**, or **negative**. It then reports the overall positive/negative
percentages and prints a sample of tweets from each category.

It uses the Twitter API (via [Tweepy](https://www.tweepy.org/)) to retrieve
tweets and [TextBlob](https://textblob.readthedocs.io/) for sentiment scoring.

## How it works

The logic lives in `twitter.py` in a single `TwitterClient` class:

1. **Authentication** — On construction, `TwitterClient` creates a Tweepy
   `OAuthHandler` with your Twitter consumer key/secret and access
   token/secret, then builds a `tweepy.API` object used to fetch tweets.
2. **Fetching tweets** — `get_tweets(query, count)` calls the Twitter search
   API for tweets matching `query` and parses each result into a dictionary
   holding the tweet text and its sentiment. Retweets are de-duplicated so the
   same parsed tweet is not counted twice.
3. **Cleaning** — `clean_tweet(tweet)` strips mentions, URLs, the `RT` marker,
   and non-alphanumeric characters using a regular expression.
4. **Sentiment scoring** — `get_tweet_sentiment(tweet)` builds a `TextBlob`
   from the cleaned text and uses its polarity:
   - polarity `> 0` → `positive`
   - polarity `== 0` → `neutral`
   - polarity `< 0` → `negative`
5. **Output** — `main()` fetches tweets for a query, computes the percentage of
   positive and negative tweets, and prints up to 10 example tweets from each
   category.

## Prerequisites

- Python 3
- A Twitter Developer account with API credentials:
  - Consumer key
  - Consumer secret
  - Access token
  - Access token secret

> **Important — credentials handling**
>
> The current `twitter.py` contains placeholder credentials assigned directly
> in the source. **Do not commit real Twitter API keys or tokens to source
> control.** Instead, read them from environment variables (or a local,
> git-ignored config file) and keep them out of the repository. For example:
>
> ```python
> import os
> consumer_key = os.environ["TWITTER_CONSUMER_KEY"]
> consumer_secret = os.environ["TWITTER_CONSUMER_SECRET"]
> access_token_key = os.environ["TWITTER_ACCESS_TOKEN"]
> access_token_secret = os.environ["TWITTER_ACCESS_TOKEN_SECRET"]
> ```
>
> If any real credentials have ever been committed, rotate (regenerate) them in
> the Twitter Developer Console.

## Dependencies

- [`tweepy`](https://pypi.org/project/tweepy/) — Twitter API client
- [`textblob`](https://pypi.org/project/textblob/) — sentiment analysis

Install them with:

```bash
pip install -r requirements.txt
```

TextBlob may require a one-time corpora download:

```bash
python -m textblob.download_corpora
```

## How to run

1. Install dependencies (see above).
2. Provide your Twitter API credentials (see the credentials note above).
3. Run the script:

   ```bash
   python twitter.py
   ```

By default `main()` searches for the query `"Donald Trump"` with a count of
`200`. Edit the `query` and `count` arguments in `main()` to analyze a
different topic.

## Usage / output

Running the script prints:

- The percentage of positive tweets
- The percentage of negative tweets
- Up to 10 example positive tweets
- Up to 10 example negative tweets

Example shape of the output:

```
Positive tweets percentage: 42.0 %
Negative tweets percentage: 18.0 %


Positive tweets:
<tweet text>
...


Negative tweets:
<tweet text>
...
```

## Tech stack

- **Language:** Python
- **Twitter access:** Tweepy (`tweepy`, `OAuthHandler`, `tweepy.API`)
- **NLP / sentiment:** TextBlob
- **Text processing:** Python `re` (regular expressions)

## Project structure

```
.
├── twitter.py            # Main script: TwitterClient class + main()
├── requirements.txt      # Python dependencies
├── README.md             # This file
├── LICENSE               # Apache License 2.0
├── cmp_project_ppt.pptx  # Project presentation
├── cmp_project_ppt.pdf   # Project presentation (PDF)
└── Screenshot (199).png  # Sample output screenshot
```

## License

This project is licensed under the Apache License 2.0. See `LICENSE` for
details.
