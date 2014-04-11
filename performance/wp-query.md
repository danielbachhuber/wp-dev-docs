WP_Query performance tips
=========================

A series of performance tips to keep in mind when you're using `WP_Query`

### "posts_per_page" => -1

`WP_Query`, and its sibling functions `get_posts()` and `query_posts()`, is useful in that it allows you to grab an unlimited set of results with `"posts_per_page" => -1`. This feature isn't without its downside, however. Another feature of `WP_Query` is that it will *pre-fetch* all of the post data associated with your query — post meta, taxonomy terms, etc.

When `"posts_per_page" => -1` (or greater than 500), `WP_Query` [won't pre-fetch post data](https://core.trac.wordpress.org/browser/tags/3.8.2/src/wp-includes/query.php#L2927), and instead run a number of queries to fetch the full representation of each post.

If you need a lot of posts, you should instead set a high upper bound like "100".
