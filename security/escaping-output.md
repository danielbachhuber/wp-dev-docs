Escaping Output
===============

Every time a post title, post meta value, or some other data from the database is rendered to the user, we need to make sure it’s properly *escaped*. Escaping helps us prevent issues like malformed HTML or the dreaded cross-site scripting attack.

For ease of code review, it's best to escape as late as possible. WordPress' escaping functions have low overhead, so there's no performance penalty to using them as many times as you need to.

### Escaping: Securing Output

WordPress thankfully has a few helper functions we can use for most of what we'll commonly need to do:

`esc_html()` we should use anytime our HTML element encloses a section of data we're outputting.

```
<h4><?php echo esc_html( $title ); ?></h4>
```

`esc_url()` should be used on all URLs, including those in the 'src' and 'href' attributes of an HTML element.

```
<img src="<?php echo esc_url( $great_user_picture_url ); ?>" />
```

`esc_js()` is intended for inline Javascript.

```
<a href="#" onclick="<?php echo esc_js( $custom_js ); ?>">Click me</a>
```

`esc_attr()` can be used on everything else that's printed into an HTML element's attribute.

@todo example for esc_attr()

It's important to note that most WordPress functions properly prepare the data for output, and you don't need to escape again.

```
<h4><?php the_title(); ?></h4>
```

@todo include note of wp_post_kses()

### Conclusion

Whenever you're rendering data from the database, you'll want to make sure it's properly escaped. Escaping helps prevent issues like cross-site scripting.
