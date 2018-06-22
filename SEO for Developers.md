# Search  Engine Optimization for Developers

Pedro Dousseau - March 21st, 2018

## Content

Page content must be optimized for a keyword in order to rank higher on search engines.

So, the keyword must be present in:

- Page title
- URL
- Meta Description
- Header Tags
- Images alt

Repeat but don't too much. Focus the content in the keyword, add links and go deep.

[MOZ Tips](https://moz.com/blog/4-graphics-to-help-illustrate-onpage-optimization)


## Code Structure

#### HTML

First time page rendering must be fast and not rely on loading of stylesheets and javascript.

Make only header and footer visible and render its css inside of style tag in the head. In the bottom, javascript and css tags should be added and loaded only after html is rendered. After all style and javascript is loaded, make rest of content visible through javascript.

Example of html page layout in haml for ruby on rails:

```
<!DOCTYPE html>
%html(lang="pt-br" dir="ltr")
	%head
		%meta{:charset => "utf-8"}
		%title= @title.truncate(70)
		%meta{:name => "description", :content => @description.truncate(315)}
		%meta{:name => "viewport", :content => "width=device-width, initial-scale=1.0"}
		%meta{:name => "author", :content => "Pedro Dousseau"}
		= favicon_link_tag "favicon.png"
		= csrf_meta_tags
		
		-############ INLINE CORE STYLE
		%style(type="text/css")
			= Rails.application.assets_manifest.find_sources('core.css').first.to_s.html_safe
		
	-############ PAGE CONTENT
	%body
		= render 'header'
		= yield
		= render 'footer'
		
		-############ CSS
		%noscript#deferred-styles
			= stylesheet_link_tag 'bootstrap.min.css', media: nil, type: 'text/css'
			= stylesheet_link_tag 'font-awesome.min.css', media: nil, type: 'text/css'
			= stylesheet_link_tag 'application', media: nil, type: 'text/css'

		-############ GOOGLE ANALYTICS
		:javascript
			-# Analytics code here
		
		-############ JAVASCRIPT
		= javascript_include_tag 'jquery3.js', defer: true
		= javascript_include_tag 'bootstrap.js', defer: true
		= javascript_include_tag 'application.js', defer: true
	
		-############ INIT DEFERRED CSS LOADING
		:javascript
			var load_deferred_styles = function(){
				setTimeout(function(){
					var add_style_node = document.getElementById("deferred-styles");
					var replacement = document.createElement("div");
					replacement.innerHTML = add_style_node.textContent;
					document.body.appendChild(replacement)
					add_style_node.parentElement.removeChild(add_style_node);
				}, 50);
			}
				
			var raf =	window.requestAnimationFrame ||
						window.mozRequestAnimationFrame ||
						window.webkitRequestAnimationFrame ||
						window.msRequestAnimationFrame;
		
			if(raf){
				raf(function(){
					window.setTimeout(load_deferred_styles, 10);
				});
			}else{
				window.addEventListener('load', load_deferred_styles);
			}
```


#### Stylesheets

Stylesheets loading must not interfere on first time page rendering, thus core style should be inside style tag and stylesheets loading deferred and loaded through javascript.

So, core style for rendering header and footer must be inside of style tag in the head.

Stylesheets must be linked inside of a noscript tag in the bottom of the body and loaded through javascript like:

```
var load_deferred_styles = function(){
	setTimeout(function(){
		var add_style_node = document.getElementById("deferred-styles");
		var replacement = document.createElement("div");
		replacement.innerHTML = add_style_node.textContent;
		document.body.appendChild(replacement)
		add_style_node.parentElement.removeChild(add_style_node);
	}, 50);
}
	
var raf =	window.requestAnimationFrame ||
			window.mozRequestAnimationFrame ||
			window.webkitRequestAnimationFrame ||
			window.msRequestAnimationFrame;
	
if(raf){
	raf(function(){
		window.setTimeout(load_deferred_styles, 10);
	});
}else{
	window.addEventListener('load', load_deferred_styles);
}
```

#### Javascripts

As stylesheets, scripts file loading must not interfere on first time page rendering. So, they must be in the bottom of the body and the defer property should be added to the tag.

Core script for first time page rendering must be inside of html, be short, and not rely on libraries, like jQuery.


## Image Optimzation

Image optmization is done through [Image Magick](https://www.imagemagick.org/script/index.php).

Bash script to optimize all PNGs in all subfolders of a folder:

```
for file in some/folder/*/*.png
	do convert $file -resize '128x>' -strip $file
done
```

Bash script to optimize all JPGs in all subfolders of a folder:


```
for file in some/other/folder/*/*.jpg
	do convert $file -resize '600x>' -sampling-factor 4:2:0 -strip -quality 85 -interlace JPEG -colorspace RGB  $file
done
```
## Server Configuration

Besides standard web server configuration, it must also set Cache-Control headers to tell browsers to perform file caching.

In apache, you need to enable mod_headers:

```
sudo a2enmod headers
```

Example of https and file caching apache configuration file:

```
<IfModule mod_ssl.c>
    <VirtualHost *:443>
        ServerAdmin webmaster@localhost
        ServerName files.sigmasensors.com.br
        DocumentRoot /var/www/files.sigmasensors.com.br/
        
        <Directory /var/www/files.sigmasensors.com.br/>
            Options Indexes FollowSymLinks
            AllowOverride all
            Require all granted
            Order allow,deny
            allow from all
        </Directory>
        
        <FilesMatch "\.(ico|pdf|flv|jpg|jpeg|png|gif|js|css|swf)$">
            Header set Cache-Control "max-age=700000, public"
        </FilesMatch>
        
        ErrorLog  /var/log/apache2/files.sigmasensors.com.br.error.log
        CustomLog /var/log/apache2/files.sigmasensors.com.br.access.log combined
        
        SSLCertificateFile /etc/letsencrypt/live/files.sigmasensors.com.br/fullchain.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/files.sigmasensors.com.br/privkey.pem
        Include /etc/letsencrypt/options-ssl-apache.conf
    </VirtualHost>
</IfModule>
```