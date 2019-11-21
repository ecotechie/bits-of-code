# Bits of Code
Collection of code snippets to make command line life a bit easier...


# WordPress: 
# Enable updates when asked for FTP credentials
_This happens when file/folder write permissions are set securely_

_Add this code to wp-config.php_

_From [Stack Overflow](https://stackoverflow.com/a/9401709/6041422)_
```php
define('FS_METHOD', 'direct');
```
## I love all you, precious plugins, slowing everything to a crawl, phoning home for fresh ads to show, so much.
_From [Twitter @Rarst](https://twitter.com/Rarst/status/1083041219494793216)_
```php
define( 'WP_HTTP_BLOCK_EXTERNAL', true );
define( 'WP_ACCESSIBLE_HOSTS', '*.wordpress.org' );
```
## Stop WP embed script from loading
```php
// Remove WP embed script
function ecotechie_stop_loading_wp_embed() {
    if ( ! is_admin() ) {
        wp_deregister_script( 'wp-embed' );
    }
}
add_action( 'init', 'ecotechie_stop_loading_wp_embed' );
```
```php
/**
 * Conditionally change menus.
 */
function ecotechie_wp_nav_menu_args( $args = '' ) {
	// change the menu in the Header menu position.
	if ( 'top' === $args['theme_location'] && is_page( '138' ) ) {
		$args['menu'] = '4'; // 4 is the ID of the menu to use here
	}
	return $args;
}
add_filter( 'wp_nav_menu_args', 'ecotechie_wp_nav_menu_args' );
```


# WP-CLI:

## Convert all database tables to Innodb with WP-CLI
```php
wp @ALIAS db query "SELECT CONCAT('ALTER TABLE ', TABLE_SCHEMA,'.', TABLE_NAME, ' ENGINE=InnoDB;') FROM information_schema.TABLES WHERE ENGINE = 'MyISAM'" --skip-column-names | wp @ALIAS db query
```


# PHP:

## Quick PHP debug with wp_die()
_Replace ... with variables to display on screen_
```php
wp_die( '<pre>' . print_r( array( ... ), 1 ) . '</pre>' );
```

# .htaccess:

## Attempt to load files from production if they're not in our local version
```apache
<IfModule mod_rewrite.c>
  RewriteEngine on
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteRule wp-content/uploads/(.*) \
    http://{PROD}/wp-content/uploads/$1 [NC,L]
</IfModule>
```


# Bash Scripting:

## Qick and dirty timer
_From [Unix Stack Exchange](https://unix.stackexchange.com/a/52347/274236)_
```bash
start=`date +%s`
# Code to be timed...
end=`date +%s`

runtime=$((end-start))
```
or
```bash
start_time=$SECONDS
# Code to be timed...
elapsed_time=$(($SECONDS - $start_time))
```

## “Yes/No” Menu
_From [Tec Admin](https://tecadmin.net/bash-script-prompt-to-confirm-yes-no/)_

```bash
while true
do
 read -r -p "Is $this_variable correct? [Y/n] " input
 
 case $input in
     [yY][eE][sS]|[yY])
 break;;
     [nN][oO]|[nN])
 read -p "Enter variable again:" this_variable;;
     *)
 echo "Invalid input..."
 ;;
 esac
done
```


# Bash Commands:

## TTL value of a DNS record
_From [ShellHacks](https://www.shellhacks.com/dns-ttl-lookup/)_

**Remaining**
```bash
dig +noall +answer domain.com
```

**Configured**
```bash
DOMAIN=domain.com; dig +noall +answer $DOMAIN @$(dig NS $DOMAIN +short|head -n1)
```

## Website response time
_From [ShellHacks][1]_
```bash
curl -s -w '\nLookup time:\t%{time_namelookup}\nConnect time:\t%{time_connect}\nAppCon time:\t%{time_appconnect}\nRedirect time:\t%{time_redirect}\nPreXfer time:\t%{time_pretransfer}\nStartXfer time:\t%{time_starttransfer}\n\nTotal time:\t%{time_total}\n' -o /dev/null domain.com
```

## Print the public IP
```bash
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```

## Compress PDF

```bash
ps2pdf in.pdf out.pdf
```

## Search recursesively
**Files modified on Jan 11 at 4am**

```bash
find . -type f -ls | grep 'Jan 11 04'
```

**Files edited in date range**
```bash
find . -type f -newermt 2018-01-10 ! -newermt 2018-01-11
```

## chmod recursevly
**CAREFUL WITH THESE!!!**

**f = files, d = directories**
```bash
find . -type f ! -perm 0644 -print0 | xargs -0 chmod 644
```
```bash
find . -type d ! -perm 755 -print0 | xargs -0 chmod 755
```

## Find files and directories not owned by GROUPNAME, excluding some paths
```bash
find . ! -group GROUPNAME ! -path './APATH/' ! -path './ANOTHERPATH'
```

[1]: https://www.shellhacks.com/
