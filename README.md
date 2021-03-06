HNScraper
=========

This Elixir module scrapes the top 500 posts of HackerNews every hour, and then:

- removes all punctuation except for apostrophes and underscores
- makes all letters lowercase
- removes single-lettered words

from the title of each post, and then these words are put into the DB (Postgres), along with Post IDs and URLs.

The top 500 posts are scraped by ID and any post IDs that already exist in the DB are filtered out. Then, the actual details of the post are retrieved. If the post is NOT a story (i.e. a poll, job or ask), it is filtered out. If the URL of the post is already in the DB, then it is filtered out. Then, the words of the title of the post are put into the DB, along with the post ID and the associated URL.

## Tables
The Postgres tables are described below. I'm sorry if my schemas suck, I'm not incredibly experienced with SQL.

### `Words`

 Column  |         Type          |                     Modifiers                      
 ------- | --------------------- | ---------------------------------------------------
 id      | integer               | not null default nextval('words_id_seq'::regclass)
 post_id | integer               | 
 word    | character varying(50) | 

(although `post_id` and `word` should also be `not null`)

### `Posts`

 Column |  Type   | Modifiers 
 ------ | ------- | ----------
 id     | integer | not null
 url    | text    | 

(where `url` is unique; it should also be `not null`)

### `Counts`

 Column |         Type          |                      Modifiers                      
 ------ | --------------------- | ----------------------------------------------------
 id     | integer               | not null default nextval('counts_id_seq'::regclass)
 word   | character varying(50) | not null
 count  | integer               | 

(where `word` is unique; `count` should be `not null` too...)

## Changing some options
There are options that can be changed:

- how often the scraping happens (default is hourly, max is every minute)
- number of posts scraped (default is 500, max is 500)

Both of these options are passed to `HNScraper.start_scraping(crontime, top_posts_amount)`. `crontime` is a string and `top_posts_amount` is an integer. The format of `crontime` is the standard format for cron jobs. See the [Quantum module](https://github.com/c-rack/quantum-elixir) for all possible configurations.

## License
GPL, I guess.
