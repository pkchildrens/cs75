# PHP

### Apache

- One of the most popular web servers is Apache; the A in LAMP stack (linux, apache, mysql, php)
- Configuration file is called httpd.conf, apache.conf, apache2.conf
- In this file features can be implemented like virtual hosting
- Virtual hosting is the idea that a web server can use multiple websites on the same ip address

### httpd.conf port 80

- <VirtualHost *:80> - tells a web server to define a vhost on port 80 of any ip address this server has; the asterisk is a wild that means it doesn't matter what ip address the request comes in on, go ahead and listen on port 80 of any of those IPs
- CustomLog logs/www.cs75.net-access_log - where to put custom logs
- DocumentRoot /home/cscie75/srv/www/www.cs75.net/html - tells vhost where html, php, etc files for www.cs75.net live specifically here
- ErrorLog logs/www.cs75.net-error_log - specifies where to put error logs
- ServerAdmin help@cs75.net - in case there are errors, given to the public
- ServerAlias cs75.net - another name for server name to have both work. In addition to this, even though the alias is here, a DNS record, an A record, with the correct ip address needs to be listed with this alias. The server name, www.cs75.net, needs an A record as well, or CNAME.
- ServerName www.cs75.net - defines the vhosts name
- You cannot make a CNAME (alias or canonical name) for the root of a domain
- So here, cs75.net cannot be a CNAME for www.cs75.net. But something with a host name www.cs75.net can be a CNAME.

### httpd.conf port 443

- Mostly a duplicate of above, but adds ssl stuff. SSL requires configuration of a few files. SSL stands for secure sockets layer, protocol websites use to communicate securely with browsers. In order to use SSL on the website, a certificate needs to be distributed.
- If a browser says a website can't be trusted, it's probably because it's certificate wasn't signed by a certificate authority. Or the certificate is expired.
- There is a public key and a private key. To send infomation, the browser can use the public key of cs75.net to encrypt whatever info it wants to send, which can only be decrypted by the private key.
- After creating the certificates, you get two files: a key and a certificate. A private key and a public key.
- SSLCertificateChainFile /path/to/gd_bundle.crt - some SSL providers aren't in the browser's trusted list, so this is an additional certificate to add to the chain of trust to keep the chain intact
- SSLCertificateFile /path/to/cs75.crt - location of public key, what you are paying for from verisign or whatever certificate authority
- SSLCertificateKeyFile /path/to/cs75.key - location of private key, not in a public place
- SSLEngine on
- Running SSL on your website requires a unique IP address. (28:00) This is because everything inside the virtual envelope is encrypted, including the host header

### mod_rewrite

- Module rewriting lets you rewrite urls
- RewriteEngine On - turns on
- RewriteCond %{HTTP_HOST} !^www\.cs75\.net [NC] - if else condition, if the http host variable exists (equivalent to the host line in an http request), then if it does not match the following (because of !), then do the rewrite rule. The (^) indicates the beginning of the variables value must start with www... etc. The (\.) is an escape for the dot. The [NC] means no case, meaning case insensitive. 
- RewriteRule (.*) https://www.cs75.net/$1 [R=301,L] - This is the then part of the expression. If the first case evaluated to true, then this states to redirect the user to https://www.cs75.net/$1. The (.*) means zero or more characters, capture them (parens). By convention it is then stored in the $1. [R=301, L] means send a 301 redirect response. The L means last, meaning don't process anymore rewrite rules after this one.
- Why would a user reach this website and not already be at www.cs75.net? The situation of davidnews.com could be avoided by using a rewrite rule. It is also useful to standardize urls, so someone arrive at cs75.net will go to www.cs75.net.
- For practical reasons, it is helpful so that cookies can be isolated. This means cookies can be isolated to www.cs75.net or cs.cs75.net instead of global to all of cs75.net.
- For security reasons, it can help in forcing users to stay on https:
- RewriteCond %{HTTPS} !=on
- RewriteRule - redirect to https version

### GET and POST

- GET request has state change in the url itself, POST does not
- POST allows for large file uploads, a file could in theory be encoded in base 64 and thrown into the url but there's often a browser limit and this limit is not standardized
- Sending sensitive information should not be sent in a GET as it ends up in the url. If it is over ssl, it would be encrypted, but it is not encrypted from anyone on the same home network or on the same computer with access to browser history. Also GET requests are logged in logs. 
- We could just use POST all the time instead, but you can't always copy and paste the url. So not useful for any situation where you would want to save state.

### PHP

- PHP is an interpreted language. A compiled language needs to be run through a compiler which outputs object or byte code. An interpreted language instead goes through an interpreter reads the source code top to bottom and left to right and does what the code says to do step by step. 
- Because it is interpreted line by line, it tends to be less performant. There exists php compilers called opcode caches which do the interpration once, convert the code to an intermediate format called opcode, and save that opcode. 
- Facebook open sourced HPHP, which compiles php to c++ which compiles to object code.
- Web servers should never be run as the root user. Common usernames are 'nobody', 'www', 'apache', 'httpd', etc as long as it's not root.
- suPHP is substitute user php. Customer A's code is executed by username A. B's code is executed by B. The webserver transforms itself into A when it's time to run A's code, and B when it's time to run B's code.

### PHP Language Features

- Variables start with $
- Loosely typed language, meaning there are types but they are not readily enforced. So is javascript. The type is not declared, it is inferred from the value you put inside of it. e.g. $x = 123; would type x as integer.
- If you try and use a string in an if statement where it's expecting a boolean, there will be some implicit conversions.
- Superglobals there are special global variables that are in scope everywhere. $_COOKIE, $_ENV, $_GET, etc 
- $_GET returns an associative array with all the information the user submitted in a form 
- $_SESSION will take any values or keys or whatever and store it, cookies, isbns, etc. If the cookie hasn't expired the data will still be here.

### Permissions

- The google.html file placed in the public_html folder was 403 forbidden when we tried to access it through the browser through apache
- To see permissions on a file, run the command code(ls -l)
- There are three types of permissions: -rw-rw-r--, here we can split it to - | rw- | rw- |r--. This can be gotten here by chmod 664.
- The first character indicates if it's a directory, a d indicates directory, a - indicates a file
- Then the three groups are owner, group, and world. So here the owner and group can read and write to the file, the world can read it only.
- This is actually an ok permissions configuration, the problem is the world needs to have access to the directory
- When you do code(ls -al) you will see the permissions of code(.) and code(..). code(.) indicates the current directory and code(..) indicates the directory above.
- The 'x' permission on a file means it is executable, but on a directory an 'x' means someone can get into it (cd into it)
- Static files like html, css, javascript, images, etc need to be readable by the world to work on this server. PHP files only need to be read by the web server, not by the world. 