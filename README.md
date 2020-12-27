# 🐟 Twitter Carp Data

[@dm4uz3 posted](https://twitter.com/dm4uz3/status/1340108111399559169) a Twitter [Conversational Ad](https://business.twitter.com/en/help/campaign-setup/conversational-ad-formats.html) format Card that was simply a picture of a large carp, with the text `carp`. The Conversational Ad contains a tweet call to action button, that tweets a copy of the card. After being repeatedly shared, in a matter of hours, the card went [viral](https://knowyourmeme.com/memes/carp-tweets).

So far only the original carp tweets here, i'll add scripts and user data later.

## Tweets

`data/carp_card_tweet_ids.zip` contains a text file with 1 tweet id per line, containing original carp tweets that have the conversational card attachment.

## Instructions to reproduce a similar dataset

Running the exact same queries is now impossible, because the recent search endpoint only allows you to go back 7 days. The API will return an error if you try to request something outside the 7 day window. There is a `fullarchive` Premium endpoint that has a paid option, but this is not used here. The new v2 API does not have card information we need, so we have to hydrate the tweet ids with v1.1 API to get it.

Our strategy to retrieve all the carp card tweets is:

1. Get a wide sample of tweets from the Search API that mention `carp` that aren't retweets, quotes, or with links.
2. Hydrate Tweets with v1.1 API to identify the ones with the correct card id.
3. Use the subset of carp card tweets to model spread.

Install searchtweets-v2 to retrieve the dataset:

```
pip install searchtweets-v2
```

This will install a command line tool `search_tweets.py` that makes calling the API easier.

Add your credentials to `twitter_keys.yaml`. It must contain this (replace `A...a` with your own values):

```yaml
search_tweets_api:
  endpoint:  https://api.twitter.com/2/tweets/search/recent
  consumer_key: A...a
  consumer_secret: A...a
  bearer_token: A...a
```

Run the query:

```
search_tweets.py --credential-file twitter_keys.yaml --filename-prefix carp --results-per-call 100 --since-id 1340108111399559169 --until-id 1342229012626763776 --tweet-fields author_id,context_annotations,created_at,id,possibly_sensitive,public_metrics,source,text,withheld --query "carp -is:retweet -is:reply -is:quote -has:links"
```

This will save results to `carp.json`

Extract tweet IDs to hydrate with v1.1:

```
...
```

Hydrate with v1.1 extra `include_card_uri=true` parameter:

```
...
```

Filter out the card tweets only:

```
...
```

`data/carp_card_tweet_ids.zip` is included in this repository, results will be different due to a couple of reasons: You cannot retrieve tweets from beyond 7 days, some tweets will be deleted, or unavailable because users are not private, or suspended.

## Future work

Carp Epidemiology: Modeling the spread of carp tweets over the network of users.
