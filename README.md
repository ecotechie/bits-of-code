# Bits of Code
Collection of code snippets to make command line life a bit easier...


<details>
  <summary>
    
# WordPress: 
  </summary>

## Enable updates when asked for FTP credentials
_This happens when file/folder write permissions are set securely_

_Add this code to wp-config.php_

_From [Stack Overflow](https://stackoverflow.com/a/9401709/6041422)_
```javascript
define('FS_METHOD', 'direct');
```
## I love all you, precious plugins, slowing everything to a crawl, phoning home for fresh ads to show, so much.
_From [Twitter @Rarst](https://twitter.com/Rarst/status/1083041219494793216)_
```javascript
define( 'WP_HTTP_BLOCK_EXTERNAL', true );
define( 'WP_ACCESSIBLE_HOSTS', '*.wordpress.org' );
```
## Stop WP embed script from loading
```javascript
// Remove WP embed script
function ecotechie_stop_loading_wp_embed() {
    if ( ! is_admin() ) {
        wp_deregister_script( 'wp-embed' );
    }
}
add_action( 'init', 'ecotechie_stop_loading_wp_embed' );
```
```javascript
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
</details>


<details>
  <summary>
    
# WP-CLI:
  </summary>
  
## Convert all database tables to Innodb with WP-CLI
```javascript
wp @ALIAS db query "SELECT CONCAT('ALTER TABLE ', TABLE_SCHEMA,'.', TABLE_NAME, ' ENGINE=InnoDB;') FROM information_schema.TABLES WHERE ENGINE = 'MyISAM'" --skip-column-names | wp @ALIAS db query
```
</details>


<details>
  <summary>
    
# PHP:
  </summary

## Quick PHP debug with wp_die()
_Replace ... with variables to display on screen_
```javascript
wp_die( '<pre>' . print_r( array( ... ), 1 ) . '</pre>' );
```
</details>

<details>
  <summary>
    
# .htaccess:
  </summary>
  
## Attempt to load files from production if they're not in our local version
```javascript
<IfModule mod_rewrite.c>
  RewriteEngine on
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteRule wp-content/uploads/(.*) \
    http://{PROD}/wp-content/uploads/$1 [NC,L]
</IfModule>
```
</details>


<details>
  <summary>
    
# Bash Scripting:
  </summary>
  
## Qick and dirty timer
_From [Unix Stack Exchange](https://unix.stackexchange.com/a/52347/274236)_
```javascript
start=`date +%s`
# Code to be timed...
end=`date +%s`

runtime=$((end-start))
```

## “Yes/No” Menu
_From [Tec Admin](https://tecadmin.net/bash-script-prompt-to-confirm-yes-no/)_

```javascript
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
</details>


<details>
  <summary>
    
# Bash Commands:
  </summary>
  
## TTL value of a DNS record
_From [ShellHacks](https://www.shellhacks.com/dns-ttl-lookup/)_

**Remaining**
```
dig +noall +answer domain.com
```

**Configured**
```
DOMAIN=domain.com; dig +noall +answer $DOMAIN @$(dig NS $DOMAIN +short|head -n1)
```

## Website response time
_From [ShellHacks][1]_
```
curl -s -w '\nLookup time:\t%{time_namelookup}\nConnect time:\t%{time_connect}\nAppCon time:\t%{time_appconnect}\nRedirect time:\t%{time_redirect}\nPreXfer time:\t%{time_pretransfer}\nStartXfer time:\t%{time_starttransfer}\n\nTotal time:\t%{time_total}\n' -o /dev/null domain.com
```

## Print the public IP
```
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```

## Compress PDF

```
ps2pdf in.pdf out.pdf
```

## Search recursesively
**Files modified on Jan 11 at 4am**

```
find . -type f -ls | grep 'Jan 11 04'
```

**Files edited in date range**
```
find . -type f -newermt 2018-01-10 ! -newermt 2018-01-11
```

## chmod recursevly
**CAREFUL WITH THESE!!!**

**f = files, d = directories**
```
find . -type f ! -perm 0644 -print0 | xargs -0 chmod 644
```
```
find . -type d ! -perm 755 -print0 | xargs -0 chmod 755
```

## Find files and directories not owned by GROUPNAME, excluding some paths
```
find . ! -group GROUPNAME ! -path './APATH/' ! -path './ANOTHERPATH'
```

[1]: https://www.shellhacks.com/
