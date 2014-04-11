## Part Three: Plugin Security

Your code works, but is it safe? Will it make your users love your plugin or theme, or hate it when it lets their site get hacked? The best WordPress projects keep their users safe.

When writing code to run across hundreds if not thousands of websites, you should be extra cautious of how your code is written. In particular, when building a settings page for your theme, creating and manipulating shortcodes, or saving and rendering extra data associated with a post.

You're in luck though! There are common patterns you can follow to ensure the security of your code. We’ll break them into three key ideas:

**[Securing Input](securing-input.md):** Every time a user submits data to WordPress, or data is ingested from an external feed, or data comes into WordPress, you should make sure it's safe to handle. You can do so by validating and sanitizing the data.

**[Escaping Output](escaping-output.md):** Every time a post title, post meta value, or some other data from the database is rendered to the user, we need to make sure it’s properly escaped. This helps prevent issues like cross-site scripting.

**Ensuring Users Are Who They Say They Are:** To keep sneaky evildoers from changing your plugin's settings or performing actions they shouldn’t be doing, it's important to always check to see if they are who they say they are. WordPress offers two ways: nonces and capability checks.

Ready to make your plugin secure? Let’s get started.
