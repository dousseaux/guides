# Python RESTful

Pedro Dousseau - February 16th, 2018

## Overview

A guide to create RESTful APIs using Apache and Python CGI. Also, it will show how to create a client Python script to make RESTful API calls.

I assume the reader already have experience with Apache and Python.

## RESTful API

Apache web server will handle the requests and execute Python Scripts for those requests through CGI.

#### Enable CGI and Rewrite on Apache

```
$ sudo a2enmod cgi
$ sudo a2enmod rewrite
```

#### Setup Apache Site Configuration File

Make all files in a cgi folder be executed:

````
<Directory /srv/www/yoursite/public_html/cgi-bin>
        Options ExecCGI
        SetHandler cgi-script
        AllowOverride All
</Directory>
````

Or allow .py files to be executed as scripts in a particular folder:

```
<Directory /srv/www/yoursite/public_html>
    Options +ExecCGI
    AddHandler cgi-script .py
    AllowOverride All
</Directory>
```

#### Setup Apache .htaccess

Inside of the script folder, add a .htaccess folder with the following content:

```
RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME}\.py -f
RewriteRule ^(.*)$ $1.py
```

It will no require .py extension on URI to execute the file.

#### Python Script

Add python executable to the top of script:

```
#!/usr/bin/python
```

As a HTTP response, the script must print a HTTP response header before anything else. A simple response header will only include the content type followed by a empty line, like following:

```
print("Content-Type: text/html;charset=utf-8\r\n")
print("I am the body of the response.")
```

Some options of content type:

- application/javascript
- application/json
- application/x-www-form-urlencoded
- application/xml
- application/zip
- application/pdf
- application/sql
- application/graphql
- application/ld+json
- application/msword (.doc)
- application/vnd.openxmlformats-officedocument.wordprocessingml.document(.docx)
- application/vnd.ms-excel (.xls)
- application/vnd.openxmlformats-officedocument.spreadsheetml.sheet (.xlsx)
- application/vnd.ms-powerpoint (.ppt)
- application/vnd.openxmlformats-officedocument.presentationml.presentation (.pptx)
- application/vnd.oasis.opendocument.text (.odt)
- audio/mpeg
- audio/vorbis
- multipart/form-data
- text/css
- text/html
- text/csv
- text/plain
- image/png
- image/jpeg
- image/gif

If you want the response to be downloaded as a file instead, use the following header:

```
print("Content-Disposition: attachment; filename=my-file-name.dat\r\n")
print("I am the file content.")
```

Do the following to get GET parameters and POST data?

```
import cgi

post_get_arguments = cgi.FieldStorage()
raw_post_data = sys.stdin.read() 

print(post_get_arguments.getvalue("param_name")
```

#### Enable Script Debugging

Add the following to the script:

```
import cgitb
cgitb.enable()
```

#### Example of Apache Site Configuration File

```
<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	ServerName api.my-site.com
	
	<Directory /var/www/api.my-site.com>
		Options +ExecCGI
		DirectoryIndex index.py
		AddHandler cgi-script .py
		AllowOverride All
	</Directory>
        
	DocumentRoot /var/www/api.my-site.com	
	ErrorLog  /var/log/apache2/api.my-site.error.log
	CustomLog /var/log/apache2/api.my-site.access.log combined
</VirtualHost>
```

## REST Client

REST Client scripts can be done using the Requests Python Module. See how to do it in its [documentation](http://docs.python-requests.org/en/master/user/quickstart/#make-a-request).
