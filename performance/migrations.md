Migrations
==========

As a developer, migrations can be nightmares — or they can be tasks you look forward to. Because you'll likely need to run the migration many times as you make minor corrections, you'll want to optimize for speed. Doing so will make the project much more enjoyable.

## Use WP-CLI as your framework

It goes without saying that you should use [WP-CLI](http://wp-cli.org/) as the framework for your migration script. WP-CLI is easy to extend with [custom commands](https://github.com/wp-cli/wp-cli/wiki/Commands-Cookbook). It also includes many reusable functional and presentational components.

### Put default arguments in wp-cli.local.yml

With commands you'll be using regularly, you can store your standard arguments in your `wp-cli.local.yml` file.

Given a `wp-cli.local.yml` file like:

```
core install:
  title: WordPress Trunk
  url: http://wordpress-trunk.dev
  admin_user: daniel
  admin_password: daniel
  admin_email: daniel@handbuilt.co
```

... a command like this:

`wp core install --title="WordPress Trunk" --url=http://wordpress-trunk.dev --admin_user=daniel --admin_password=daniel --admin_email=daniel@handbuilt.co`

... becomes as easy as this:

`wp core install`

When you want to quickly reset your WordPress instance, it becomes a two-liner: `wp db reset --yes; wp core install`

## Pre-fetch remote assets

If your migration script is downloading assets on each run, you'll be spending too much time on redundant remote requests. Solve this problem by "pre-fetching" your remote assets.

Given a script to loop through all of your remote assets, you can use these helper methods to cache a remote file if the file doesn't exist:

```
	/**
	 * Get the file path for the cached image
	 *
	 * @param string $image_url
	 * @return string
	 */
	private function get_cache_image_path( $image_url ) {

		$upload_dir = wp_upload_dir();
		return $upload_dir['basedir'] . parse_url( $image_url, PHP_URL_PATH );
	}

	/**
	 * Cache a remote image locally
	 *
	 * @param string $remote_image_src
	 * @param string $cached_image_path
	 */
	private function cache_remote_image( $remote_image_src, $cache_remote_image ) {

		// Make sure we aren't killing the server with `wget` usage
		while ( trim( shell_exec( 'ps aux | grep wget | wc -l' ) ) > 20 ) {
			sleep( 1 );
		}

		// Launch a wget process but don't wait for it to complete
		shell_exec( "wget -O {$cache_remote_image} -T 5 {$remote_image_src} > /dev/null 2>/dev/null &" );

	}
```

## Disable thumbnail generation

Especially if you're running the import early in the site rebuild process, you'll want to disable thumbnail generation. Generating multiple sizes of each image has a severe performance penalty.

Thumbnail generation can be disabled with:

```
add_filter( 'intermediate_image_sizes_advanced', '__return_false' );
```

Later on, when you're close to launch and all of the theme's image sizes have been nailed down, you can generate all of your necessary thumbnails with [wp media regenerate](http://wp-cli.org/commands/media/regenerate/).

## Execution Pro Tips

Once you're ready to commit to your migration script, you'll be happy you knew the following tips.

### Run the script using screen

`screen` is an [awesome utility for running your scripts](http://www.mattcutts.com/blog/a-quick-tutorial-on-screen/) even when the terminal window closes. Why does this matter? You probably don't want your script to die when you shut your laptop to go home for the evening. Before starting your script, make sure to launch a screen session.

### Pipe verbosity to a log file

Make sure your script produces output for the changes it's making to the database. WP-CLI has a couple utilities you can use: `WP_CLI::line()` and `WP_CLI::warning()`.

Then, when you're running your script against production data, pipe the verbosity to a log file:

```
wp custom-command my-migration-command &> logfile.txt
```

The log of what happened during the migration can be tremendously useful post-migration, as you debug where your script did the unexpected. [ack](http://beyondgrep.com/) is a useful tool for searching through your log file.
