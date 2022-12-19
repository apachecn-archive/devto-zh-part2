# 。htaccess for WordPress

> 原文 https://dev.to/gzapico/htaccess-for-wordpress-1b3j

已优化。htaccess for Wordpress，包括 HTTPS 重写+ deflate + headers + expires +删除 ETag

```
##
# .htaccess for Wordpress
# Tweaked by Gustavo Zapico @ Netavanza
# https://netavanza.com
##

# BEGIN WordPress
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]
</IfModule>
# END WordPress

<IfModule mod_deflate.c>
AddOutputFilterByType DEFLATE text/plain
AddOutputFilterByType DEFLATE text/html
AddOutputFilterByType DEFLATE text/xml
AddOutputFilterByType DEFLATE text/css
AddOutputFilterByType DEFLATE application/xml
AddOutputFilterByType DEFLATE application/xhtml+xml
AddOutputFilterByType DEFLATE application/rss+xml
AddOutputFilterByType DEFLATE application/javascript
AddOutputFilterByType DEFLATE application/x-javascript
AddOutputFilterByType DEFLATE application/x-httpd-php
AddOutputFilterByType DEFLATE application/x-httpd-fastphp
AddOutputFilterByType DEFLATE image/svg+xml
SetOutputFilter DEFLATE
</IfModule>

<IfModule mod_headers.c>
<FilesMatch "\.(js|css|xml|gz)$">
Header append Vary: Accept-Encoding
</FilesMatch>
</IfModule>

<IfModule mod_expires.c>
ExpiresActive On
<FilesMatch "\.(gif|jpeg|jpg|png|ico|js|css|swf)$">
ExpiresDefault "access plus 8760 hours"
</FilesMatch>
ExpiresByType text/css "access 1 month"
ExpiresByType text/html "access 1 month"
ExpiresByType image/jpg "access 1 year"
ExpiresByType image/jpeg "access 1 year"
ExpiresByType image/gif "access 1 year"
ExpiresByType image/png "access 1 year"
ExpiresByType image/x-icon "access 1 year"
ExpiresByType application/pdf "access 1 month"
ExpiresByType application/javascript "access 1 month"
ExpiresByType text/x-javascript "access 1 month"
ExpiresByType application/x-shockwave-flash "access 1 month"
ExpiresDefault "access 1 month"
</IfModule>

Header unset Pragma
Header unset ETag
FileETag None 
```