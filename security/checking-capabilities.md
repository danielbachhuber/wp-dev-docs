Checking Capabilities
=====================

When a user performs an action which alters the database, let it be submitting a settings form, modifying a user’s profile details, or editing post data, we want to check that they have the *capability* to do so. WordPress makes this easier to do through its system of [Roles and Capabilities](https://codex.wordpress.org/Roles_and_Capabilities).

Before we go to much further, let's review. A *role* is a collection of capabilities. WordPress creates roles like Administrator, Editor, and Author by default — they correlate very much with which permissions you'd want to assign in a publishing workflow. And a *capability* is pretty much that: a defined permission. In its default roles, WordPress uses capabilities like `manage_options`, `edit_posts`, and `switch_themes`. When a WordPress user is created, they're assigned a role, and the collection of capabilies associated with the role defines what they can do.

### Capabilities: Verifying Permissions

As you're writing your safe and secure code, you'll want to pay keen attention to which roles should be permitted to perform which action. In this example, we have a helpful function which gives editors a link to trash posts from the front end of their site:

```
function wpdd_frontend_delete_link() {
  $url = add_query_arg(
    array(
      'action'   => 'wpdd_frontend_delete',
      'post'     => get_the_ID();
    ),
    home_url(),
	);
	echo "<a href="{$url}">Delete</a>";
}
```

This function could be used as a template tag within a theme.

Then, we have a function to handle any requests to delete posts:

```
if ( isset( $_REQUEST['action'] ) && $_REQUEST['action'] == 'wpdd_frontend_delete' ) {
	add_action( 'init','wpdd_frontend_delete_post' );
}

function wpdd_frontend_delete_post() {

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

The code above allows any visitor to the site to click on the "Delete" link and trash the post. What's wrong with that scenario?

Well... we only want editors and above to be able to click on the "Delete" link. If *anyone* can do it, chaos will ensue.

As such, we need to make sure the current user can do something only an editor or above can do before performing either action.

When generating the link, it would look something like this:

```
function wpdd_frontend_delete_link() {

  // current_user_can() checks for a specific capability
  // and 'edit_others_posts' is associated with the editor role and above
  if ( ! current_user_can( 'edit_others_posts' ) )
		return;

	$url = add_query_arg(
		array(
			'action'     => 'wpdd_frontend_delete',
			'post'       => get_the_ID();
		),
		home_url(),
	);
	echo "<a href="{$url}">Delete</a>";
}
```

When performing the delete action, it would look something like this:

```
if ( isset( $_REQUEST['action'] ) && $_REQUEST['action'] == 'wpdd_frontend_delete' ) {
	add_action( 'init','wpdd_frontend_delete_post' );
}

function wpdd_frontend_delete_post() {

	if ( ! current_user_can( 'edit_others_posts' ) )
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

In both generating the link and handling the request, we're confirming the current user has the `edit_others_posts` capability before proceeding with the action. If they don't, we don't do anything.
