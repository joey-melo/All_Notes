## Useful Extensions

## .htaccess upload trick

If you can upload `.htaccess` then you can obtain a php shell:
1. Upload an .htaccess with: *AddType application/x-httpd-php .rce*
2. Then upload any file with .rce extension.