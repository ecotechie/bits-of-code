# Bits of Code
Collection of code snippets to make command line life a bit easier...

# Table of Contents:
* [WordPress](#wordpress)
* [WP-CLI](#wp-cli)
* [PHP](#php)
* [.htaccess](#htaccess)
* [Bash Commands](#bash-commands)
* [Bash Scripting](#bash-scripting)
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

## Set database option to not autoload
```php
wp db query "UPDATE $(wp db prefix)options SET autoload='no' WHERE option_name='OPTION_NAME'"
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
## Add www. and https
```apache
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteCond %{HTTPS} off
  RewriteCond %{HTTP_HOST} !^www.
  RewriteRule ^(.*)$ https://www.%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
</IfModule>
```
## Strip www. add https DOESN'T WORK!!!!!
```apache
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteCond %{HTTPS} off
  RewriteCond %{HTTP_HOST} ^www\.(.*)$ [NC]
  RewriteRule ^(.*)$ https://%1%{REQUEST_URI} [L,R=301]
<IfModule>
```
## This does:
```apache
<IfModule mod_rewrite.c>
  RewriteEngine on
  RewriteCond %{HTTP_HOST} ^www. [NC,OR]
  RewriteCond %{HTTPS} off
  RewriteRule ^(.*)$ https://DOMAIN.com/$1 [L,R=301]
<IfModule>
```

# Bash Commands:

## Generate public/private rsa key pair with (YOUR_EMAIL) as note, then output keys to screen
```bash
ssh-keygen -C YOUR_EMAIL -m PEM -f ./id_rsa && echo && cat id_rsa && echo && cat id_rsa.pub
```

### SSH tunnel into a MySQL shell:
```bash
ssh -i ./id_rsa -p 22 user@domain.com -L 65000:127.0.0.1:3306 -N
```

## Display new information as it is being written to a file (FILE_NAME)
```bash
tail --follow FILE_NAME
```

## Display disk space information
```bash
df -hT
```

## List directories sorted by total number of files recursively, starting with (.)
_From [StackExchange](https://superuser.com/a/325174)_
```bash
find . -type d | while read dir; do echo "$dir" : $(find "$dir" -type f | wc -l); done | sort -k2 -t ':' -n;
```

## List directories and files sorted by size
_From [StackOverflow](https://stackoverflow.com/a/14749369)_
```bash
du -a -h --max-depth=1 | sort -h
```

## Show total size of files of type (.jpg) recursively in a directory (.)
_From [StackExchange](https://unix.stackexchange.com/a/41552)_
```bash
find . -type f -name '*.jpg' -exec du -ch {} + | grep total$
```

## Show total size of a directory (.)
_From [StackExchange](https://unix.stackexchange.com/a/185765)_
```bash
du -sh .
```

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

## “Validate Input” Function
_Inspired by [Tec Admin](https://tecadmin.net/bash-script-prompt-to-confirm-yes-no/)_

```bash
validate_input () {
  read -r -p "$*" input 
  while true
  do
    read -r -p "Is $input correct? [Y/n] " maybe

    case $maybe in
      [yY][eE][sS]|[yY]|"")
      echo "$input"
      break
      ;;
      [nN][oO]|[nN])
      read -p "Enter variable again:" $input
      ;;
      *)
      echo "Invalid input..."
      echo
      ;;
    esac
  done
}
```

## "Yes/No" Function

```bash
yes_no () {
  echo
  while true
  do
    read -r -p "$* [Y/n] " maybe
    
    case $maybe in
      #Return 0, a non failure status.
      [yY][eE][sS]|[yY]|"")
      return 0
      break
      ;;
      # Return 101, a non-zero (failure) status.
      [nN][oO]|[nN])
      return 101
      break
      ;;
      *)
      echo "Invalid input..."
      ;;
    esac
  done
}
```

## Quick Yes/No logic

```bash
read -r -p "What's your question? [y/N] " response
[[ "$response" =~ ^([yY][eE][sS]|[yY])$ ]] && $do_something || $do_something_else
```

[1]: https://www.shellhacks.com/
