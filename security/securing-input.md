Securing Input
==============

Each time a user submits data to WordPress, or data is ingested from an external feed, or data generally comes from an external source, you should make sure it’s safe to handle. You want to make sure the data is safe for a variety of reasons; to help prevent [XSS](http://en.wikipedia.org/wiki/Cross-site_scripting) if the data is improperly escaped on output, and to ensure your code is executing how you expect are two good reasons. You can make sure this data is safe to use by *validating* and *sanitizing*.

If you learn anything from this document, pay attention to you're handling those `$_GET` and `$_POST` variables!

## Validating: Checking User Input

Validating is ensuring the data you're receiving from a user matches what you expect to receive. Let's take a look at an example.

Say we have an input area in our form like this:

```
<input type="text" id="my-zipcode" name="my-zipcode" maxlength="5" />
```

We're telling the browser to only allow up to five characters of input, but there's no limitation on what characters they can input. They could enter "11221" or "eval(". We want to make sure we're only processing zip codes from the form.

This is where validation plays a role. When processing the form, we'll write code to check each field for its proper data type. If it's not of the proper data type, we'll discard it. For instance, to check "my-zipcode" field, we might do something like this:

```
$safe_zipcode = intval( $_POST['my-zipcode'] );
if ( ! $safe_zipcode )
	$safe_zipcode = '';

if ( strlen( $safe_zipcode ) > 5 )
	$safe_zipcode = substr( $safe_zipcode, 0, 5 );

update_post_meta( $post->ID, 'my_zipcode', $safe_zipcode );
```

Since the `maxlength` attribute on our input field is only enforced by the browser, we still need to validate the length of the input on the server. If we don't, an attacker could cleverly submit a form with a longer value.

The intval() function casts user input as an integer, and defaults to zero if the input was a non-numeric value. We then check to see if the value ended up as zero. If it did, we'll save an empty value to the database. Otherwise, we'll save the properly validated zipcode.

This style of validation most closely follows WordPress' whitelist philosophy: only allow the user to input what you're expecting.

@todo show another whitelist example where the input is validated against a known set of values

## Sanitizing: Cleaning User Input

Sanitizing is a bit more liberal of an approach to accepting user data. We can fall back to using these methods when there's a range of acceptable input.

For instance, if we had a form field like this:

```
<input type="text" id="title" name="title" />
```

We could sanitize the data with the `sanitize_text_field()` function:

```
$title = sanitize_text_field( $_POST['title'] );
update_post_meta( $post->ID, 'title', $title );
```

Behinds the scenes, `sanitize_text_field()` does the following:

* Checks for invalid UTF-8.
* Converts single characters to entity.
* Strips all tags.
* Remove line breaks, tabs and extra white space.
* Strip octets.

The `sanitize_*()` class of helper functions are super nice for us, as they ensure we're ending up with safe data and require minimal effort on our part:

* `sanitize_email()`
* `sanitize_file_name()`
* `sanitize_html_class()`
* `sanitize_key()`
* `sanitize_meta()`
* `sanitize_mime_type()`
* `sanitize_option()`
* `sanitize_sql_orderby()`
* `sanitize_text_field()`
* `sanitize_title()`
* `sanitize_title_for_query()`
* `sanitize_title_with_dashes()`
* `sanitize_user()`
* `esc_url_raw()`
* `wp_filter_post_kses()`
* `wp_filter_nohtml_kses()`

## Conclusion

Any time you're using potentially unsafe data, it never hurts to validate and sanitize it. Validating is confirming the data is what you expect it to be. Sanitization is a more liberal approach to cleaning your data.
