---
title: "Fixing Wordpress' Gallery"
date: "2009-07-29T16:10:07+00:00"
categories: 
  - "news"
tags: 
  - "coding"
  - "websites"
  - "wordpress"
summary: |-
  It's moderately well known that Wordpress' gallery code, is not as robust as it could be in some cases. These have a tendency to manifest in XHTML / multi-column CSS layouts. There are two main issues;

  1. It inserts inline CSS into the middle of the page, which under XHTML is not good
  2. The gallery layout can do unusual things, including odd line breaks which can create large gaps after the first row, kill the template or other odd things

  It's been bugging me for a while now, so I decided to do a bit of research to find some solutions, and figured it would be useful to post the results.
guid: http://www.longbowslair.co.uk/?p=566
aliases: /2009/07/fixing-wordpress-gallery/
---

It's moderately well known that Wordpress' gallery code, is not as robust as it could be in some cases. These have a tendency to manifest in XHTML / multi-column CSS layouts. There are two main issues;

1. It inserts inline CSS into the middle of the page, which under XHTML is not good
2. The gallery layout can do unusual things, including odd line breaks which can create large gaps after the first row, kill the template or other odd things

It's been bugging me for a while now, so I decided to do a bit of research to find some solutions, and figured it would be useful to post the results.

<!--more-->

#### Solution 1:

Use a plugin, The nicest one out there for a simple XHTML gallery shortcode replacement is [Cleaner Gallery](http://wordpress.org/extend/plugins/cleaner-gallery/) - [Homepage](http://justintadlock.com/archives/2008/04/13/cleaner-wordpress-gallery-plugin)  
You may have to tweak it a bit to get the odd lining up issue fixed, but that's as easy as going to Admin -> Plugins -> Editor and tweaking the CSS in **cleaner-gallery/cleaner-gallery.css**
e.g. In my testing I changed a couple spots (only changes are shown);

```css
.gallery {
//	clear: both;
	margin: 20 auto;
	}
.gallery .gallery-row {
//	clear: both;
	}
.gallery .gallery-item {
	margin-top: 10px;
	}
```

This works well, but you _do_ end up relying on said plugin always working for your version of WP.

#### Solution 2:

Let WordPress (and by extension the WP community) fix it. The inline CSS problem, is difficult, and tied to a couple other issues. As yet there is no one definitive fix for it, although a few patches exist on the WP TRAC [http://core.trac.wordpress.org/search?q=gallery+css](http://core.trac.wordpress.org/search?q=gallery+css)  
A lot of these seem to get closed with 'wontfix' or 'duplicate' but all the .diff changes are still there, if you fancy trying one out.

#### Solution 3:

D.I.Y.ing it. If you don't care about as much about XHMTL and just want to fix the layout, fixing this is fairly trivial, so long as you don't mind doing a little code tweaking.

The first thing you probably want to do, is to stop the gallery function from breaking it's layouts. This needs a bit of code hacking.

Go ahead and pick your preferred method of editing files on your server, be it FTP or in place.  
Find your way to **/path-to-wordpress/wp-includes** and find **media.php**. Now since this is a WordPress system file, there are a couple things to be aware of;

1. You _could_ bork all media related stuff, if you follow this guide and only edit the right spot, you should be fine
2. When you upgrade WordPress next, these changes **will** get reverted, so if they've not been fixed by WordPress, it will be necessary to dive back in again

Now go ahead and create a copy of **media.php** to act as your backup, **media.php.bak** is a good name, since it'll get listed next to the original, but won't be parsed by PHP.

Now to the actual edit;  
Open up the **media.php** file and scroll to around line 600 (depending on your WP version) and look for the start of the gallery section;

```php
/**
 * The Gallery shortcode.
 *
 * This implements the functionality of the Gallery Shortcode for displaying
 * WordPress images on a post.
 *
 * @since 2.5.0
 *
 * @param array $attr Attributes attributed to the shortcode.
 * @return string HTML content to display gallery.
 */
```

Now scroll down a bite further to lines 695 and find this;

```php
				</{$captiontag}>";
		}
		$output .= "</{$itemtag}>";
		if ( $columns > 0 && ++$i % $columns == 0 )
			$output .= '<br style="clear: both" />';
	}

	$output .= "
			<br style='clear: both;' />
		</div>\n";
```

Here you want to change;  
`$output .= '<br style="clear: both" />';`  
so that it reads  
`$output .= '<br />';`  
Note; you should leave the second &lt;br&gt; tag alone otherwise any text after the gallery might slot into any space left in the last row.  
That's it for fixing that particular niggle, easy huh?  
Still looks a bit funky? Scroll up a bit till you reach around line 660 and look for:

```php
		<style type='text/css'>
			#{$selector} {
				margin: auto;
			}
			#{$selector} .gallery-item {
				float: left;
				margin-top: 10px;
				text-align: center;
				width: {$itemwidth}%;
			}
			#{$selector} img {
				border: 2px solid #cfcfcf;
			}
			#{$selector} .gallery-caption {
				margin-left: 0;
			}
		</style>
```

This is the inline CSS that XHTML doesn't like, but since you're not bothered by that, you can go ahead and tweak this as you like.  
One thing I noticed was that the gallery tends to get a little out of shape if you have anything more than the title above it, (i.e. another picture), so I had this;

```php
		<style type='text/css'>
			#{$selector} {
				margin: auto;
				margin-top: 20px;
			}
```

### After all this...

|  | Pros | Cons |
| --- | --- | --- |
| Plugin | 
- Customisable
- Will work after WP upgrade
- Added extras

 | 

- Might not always work with your WP
- May still require tweaks
- Possible bloat (depending on plugin)

 |
| WordPress/Patching | 

- Will be supported by WP
- Less work

 | 

- Could be a while till it arrives, if it arrives
- More work to fix the XHTML issue, should you choose to

 |
| D.I.Y. | 

- Produces the code you want
- Quick fix

 | 

- Won't work after a WP upgrade

 |

None of these are what you could call a perfect fix. Personally, I've switched from the DIY fix to the plugin one, on account that it works, and it's easily editable via the wp-admin. Here's hoping WP will include something like Cleaner Gallery soon, to fix what should be a pretty basic addition.
