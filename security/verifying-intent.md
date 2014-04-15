Verifying Intent
================

When a user performs an action which alters the database, let it be submitting a settings form, modifying a user’s profile details, or editing a post's bdoy, we want to confirm it was actually them who initiated the action. WordPress packages a nice system called nonces ("Numbers used Once") to help you out.

Because nonces have a set expiration, and can be generated to be unique to a given action, they're easy for WordPress to verify and difficult for the baddies to fake. Without nonces, your code can be susceptible to [cross-site request forgery](http://en.wikipedia.org/wiki/Cross-site_request_forgery).

Back when nonces were added to WordPress, Mark Jaquith [did a nice write-up](http://markjaquith.wordpress.com/2006/06/02/wordpress-203-nonces/). If the introduction is a bit confusing, you should go read that link and then check back.

### Verifying Intent: Nonces

Building upon our [capability example](checking-capabilities.md), when we generate the action link, we'll want to use `wp_create_nonce()` to add a nonce to the link:

```
function wpdd_frontend_delete_link() {

	if ( ! current_user_can( 'edit_others_posts' ) )
		return;

	$url = add_query_arg(
		array(
			'action'   => 'wpdd_frontend_delete',
			'post'     => get_the_ID();
			'nonce'    => wp_create_nonce( 'wpdd_frontend_delete' ),
		),
		home_url(),
	);
	echo "<a href="{$url}">Delete</a>";
}
```

The `wpdd_frontend_delete` argument we pass to the function ensures that the nonce we're creating is unique to our action. Nonces can also be included in a form with the `wp_nonce_field()` helper function.

Then, when we're processing a request to delete a post, we check that the nonce is what we expect it to be:

```
if ( isset($_REQUEST['action'] ) && $_REQUEST['action'] == 'wpdd_frontend_delete' ) {
	add_action('init','wpdd_frontend_delete_post');
}

function wpdd_frontend_delete_post() {

	if ( ! current_user_can( 'edit_others_posts' ) )
		return;

	if ( ! wp_verify_nonce( $_REQUEST['nonce'], 'wpdd_frontend_delete' ) )
		return;

	// Get the ID of the post.
	$post_id = ( isset( $_REQUEST['post'] ) ?  get_post( (int) $_REQUEST['post'] ) : false;

	// No post? Oh well..
	if ( empty($post_id) )
		return;

	// Delete post
	wp_trash_post( $post_id );

	// Redirect to admin page
	$redirect = admin_url('edit.php');
	wp_safe_redirect( $redirect );
	exit;
}
```

Nonces keep WordPress secure because they verify the user intended to perform the action. Because nonces have an expiration time, and can be generated to be unique to the form or link, it's very difficult for the baddies to forge them.
