Migrations
==========

As a developer, migrations can be nightmares — or they can be tasks you look forward to. Because you'll likely need to run the migration many times as you make minor corrections, you'll want to optimize for speed. Doing so will make the project much more enjoyable.

## Use WP-CLI as your framework

It goes without saying that you should use [WP-CLI](http://wp-cli.org/) as the framework for your migration script. WP-CLI is easy to extend with [custom commands](https://github.com/wp-cli/wp-cli/wiki/Commands-Cookbook). It also includes many reusable functional and presentational components.

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
