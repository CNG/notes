# Shell miscellaneous notes

This is an assortment of things I had to look up at one point and thought I might need to again, or did, again and again… anyway, I am using flashcards now, so this is no longer updated (much).

## get latest files

You can run the commands below to find the most recently created file (ignoring the annoying `.DS_Store` files) in the backup directory, and then copy all the attachments that are newer than that file into the `to_sort` folder. 

    SOURCE_DIR=~/Library/Messages/Attachments
    BACKUP_DIR=~/Desktop/messages_backup/sorted
    DEST_DIR=~/Desktop/messages_backup/to_sort
    NEWEST=$(find $BACKUP_DIR -type f ! -iname ".*" -print0 | xargs -0 gstat --format '%Y :%y:%n' | sort -nr | cut -d: -f5- | head -1)
    find $SOURCE_DIR -type f ! -iname ".*" -newer $NEWEST -exec cp -a {} $DEST_DIR  \;

## `bel`

    for i in $(seq 1 10); do tput bel; sleep 0.2; done

## `dd`, `mkfile`

    mkfile 10m 10.txt
    dd if=/dev/zero of=10.txt  bs=1024  count=10240
    dd if=/dev/zero of=10.txt  bs=1M  count=10
    
## `for` loops

    for i in /etc/*.conf; do cp $i /backup; done
    for((i=1;i<=20;i+=2)); do mkfile ${i}m ${i}.txt; done
    for i in {1..5}; do COMMAND-HERE; done

## symbolic links

    ln -sn /var/www/domain.com/httpdocs www

## New linux server

make mysql autostart:

    update-rc.d mysql defaults

Ignore errors with Find:

    find . 2>/dev/null

## Using `tar`

Compress: `tar -zcvf $FILE.tar.gz $PATH`
Extract: `tar -zxvf $FILE.tar.gz`
Extract to directory: `tar -zxvf $FILE.tar.gz -C $DEST_PATH`

## `rsync`

`--info=progress2`

## `vim`

Cut and paste:

1. Position the cursor where you want to begin cutting.
1. Press <kbd>v</kbd> (or <kbd>V</kbd> to cut whole lines).
1. Move the cursor to the end of what you want to cut.
1. Press <kbd>d</kbd>.
1. Move to where you would like to paste.
1. Press <kbd>P</kbd> to paste before the cursor, or <kbd>p</kbd> to paste after. 

Copy and paste can be performed with the same steps, only pressing <kbd>y</kbd> instead of <kbd>d</kbd> in step 4.

The name of the mark used is related to the operation (<kbd>d</kbd>:delete or <kbd>y</kbd>:yank).

## `screen`

Start or resume session by name

    screen -xRR custom_name

Create new shells within session: `CTRL+a` `c`

Advance to next existing shell within session: `CTRL+a` `CTRL+n`

Regress to previous existing shell within session: `CTRL+a` `CTRL+p`

Exit but leave running: `CTRL+a` `CTRL+d`

Exit and close all shells: `CTRL+a` `\`

List screen sessions: `screen -ls`

Help: `CTRL+a` `?`



## `sed`

Replace text in a file:

    sed -i 's/OLD/NEW/g' pl.sql

Replace text in all found files:

    find /var/www -type f -name "*.html" -print0 | xargs -0 sed -i 's/OLD/NEW/g'

Truncate file by keeping first 100 lines

    sed -i '100,$ d' filename
    
Count PHP errors in Apache web log

    time cat /var/log/apache2/error.log | sed -n 's/.*client 127.0.0.1]\s\(PHP .*\)/\1/p' | sort | uniq -c | sort -rn**

To delete all lines containing particular string/text use following command:

    sed '/SEARCH/d' filename 

## `find`

Files modified before and after certain date:

    find . -type f -name "*" -newermt 2014-03-09 ! -newermt 2014-03-12

Files modified less than one day ago:

    find . -type f -mtime -1

Files owned by root:

    find . -uid 0

Change root owned files to www-data:

    sudo find . -uid 0 -exec chown www-data:www-data {} \;

## SSH tunnel

Add to local `.ssh/config`:

    Host votecharlie.com me
      HostName votecharlie.com
      User username
      Port XXXX
      LocalForward 7022 domain.com:22
      LocalForward 7023 dev.domain.com:22
    Host A_tunnel
      HostName localhost
      User username
      Port 7022
    Host Adev_tunnel
      HostName localhost
      User username
      Port 7023

Then connect via:

    ssh -f me -N
    ssh A_tunnel

## Miscellaneous

http://machiine.com/2013/easy-way-to-give-user-permission-to-edit-and-add-files-in-varwww/

	SetEnvIfNoCase User-Agent "bot|spider" bad_bot=1 
	<Directory "/usr/lib/cgi-bin"> 
	deny from env=bad_bot
	</Directory>
	
	
	ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
	<Directory "/usr/lib/cgi-bin">
	        AllowOverride None
	        Options +ExecCGI -MultiViews +FollowSymLinks
	        Order allow,deny
	        Allow from all
	</Directory>
	    
	SetEnvIfNoCase User-Agent "bot" bad_bot=1
	SetEnvIfNoCase User-Agent "spider" bad_bot=1
	ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
	<Directory "/usr/lib/cgi-bin">
	        AllowOverride None
	        Options +ExecCGI -MultiViews +FollowSymLinks
	        Order allow,deny
	        Allow from all
	        Deny from env=bad_bot
	</Directory>

Count all errors by day:

    cat /var/log/apache2/error.log | sed -r -n 's/^\[(\w)\w+ (\w+ \w+).+/\2 \1/p' | uniq -c

Count PHP errors by message:

    cd /var/log/apache2/; cat error.log | sed -n 's/.*client 127.0.0.1]\s\(PHP .*\)/\1/p' | sed -r -n 's/(.*)(, referer.*)/\1/p' | sort | uniq -c | sort -rn > error.log.php.counts

Count all errors by message:

    cd /var/log/apache2/; cat error.log | sed -n 's/.*client 127.0.0.1]\s\(.*\)/\1/p' | sed -r -n 's/(.*)(, referer.*)/\1/p' | sort | uniq -c | sort -rn > error.log.counts
    
## SSL certificate request

Generate a new private key and Certificate Signing Request

    openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key

Generate a self-signed certificate (see How to Create and Install an Apache Self Signed Certificate for more info)

    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt

Generate a certificate signing request (CSR) for an existing private key

    openssl req -out CSR.csr -key privateKey.key -new

Generate a certificate signing request based on an existing certificate

    openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key

Remove a passphrase from a private key

    openssl rsa -in privateKey.pem -out newPrivateKey.pem
