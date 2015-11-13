# Using Photon in local development

Automattic's [Photon image resizing service](https://developer.wordpress.com/docs/photon/) is open source, so you can use it locally if you want to roll your sleeves up a bit.

1. Check out the Photon codebase to a location served by your VIP Quickstart or Salty WordPress Vagrant: `svn co https://code.svn.wordpress.org/photon/ photon.dev`. You will probably need to edit `/etc/hosts` on your local machine too.
1. You probably don't have Gmagick installed on your machine. You'll need to perform some operations as sudo to make this happen.
 1. `sudo su` - Inside your Vagrant instance, switch to the super user.
 1. `apt-get install libgraphicsmagick1-dev php5-dev make` - Install Graphicsmagick dev tools. You may need to `apt-get update`
 1. `cd /root; wget https://pecl.php.net/get/gmagick-1.1.7RC2.tgz` - Grab Gmagick for compiling.
 1. `tar -xvf gmagick-1.1.7RC2.tgz; cd gmagick-1.1.7RC2` - Get ready for compiling!
 1. `phpize` - Get ready for compiling again!
 1. `./configure; make; make install` - Compile for your system.
 1. `echo "extension=gmagick.so" > /etc/php5/mods-available/gmagick.ini` - Make the PHP module available.
 1. `ln -s /etc/php5/mods-available/gmagick.ini /etc/php5/fpm/conf.d/20-gmagick.ini` - Enable the module.
 1. `service php5-fpm restart` - Restart PHP-FPM to enable the module.
1. Use Jetpack 3.4.2 or later to [ensure my filter is applied](https://github.com/Automattic/jetpack/pull/1936).
1. Add this code snippet to a local MU plugin:

```
add_filter( 'jetpack_photon_domain', function( $domain ){
	return 'http://photon.dev';
});
```

And voila! Local images served from Photon.
