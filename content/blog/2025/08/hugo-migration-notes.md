---
title: "Hugo migration notes"
date: "2025-08-27T20:19:00+01:00"
categories: 
  - "news"
tags: 
  - "websites"
showtoc: true
---

As promised (threatened?) [previously](/blog/2025/07/long-time-no-post/) here's the follow up post about the migration to [Hugo](https://gohugo.io). There's a multitude of posts [^1] [^2] [^3] covering the why and how of swapping, so I won't rehash all of that. Instead I want to cover some of the workarounds and processes I went through converting this site.

Some extra context, in the theme of docker-ing all the things, I run my two hugo dev sites and VSCode editor in dockers (potentially worth a post on it's own, this one's long enough), so the compose files differ a little from stock.

With that all said, here follows a vague attempt at organising a mad collection of notes made as I went about the swap.

### Running hugo

As mentioned I use docker to host a container for dev work. Luckily [hugomods](https://hugomods.com/) have an automated [docker image](https://docker.hugomods.com/) that should stay up to date for as long as hugo/github is a thing.

This is my adapted docker compose;

```yaml
services:
  hugo-main:
    container_name: hugo-main
    hostname: hugo-main
    image: hugomods/hugo:exts-non-root
    user: 1026:100
    ports:
      - 1313:1313
    volumes:
      - /<path-to-repo-dir>/web:/src
      - /<path-to-docker-storage>/hugo/hugo_cache:/tmp/hugo_cache
    network_mode: bridge
    command: server --baseURL http://<server-name>/ --cleanDestinationDir --gc -DEF -N
      -s /src/kenjie20.co.uk
    restart: on-failure
```

- `user: 1026:100`  
Run as user 1026 and group 100 for file ownerships (1026 being my uid)
- `--baseURL http://<server-name>/`  
When running hugo in server mode it modifies urls from the `hugo.yaml` `baseURL` setting to `localhost`, adding this fixes links so you can access the test site from another PC
- `--cleanDestinationDir`  
This makes sure the generated output is cleared on container restart, handy when doing branch swapping or failed runs where bits get left over
- `--gc`  
remove unused cache files after the build
- `- /<path-to-repo-dir>/web:/src`  
`-s /src/kenjie20.co.uk`  
I keep a few sites in the `/<path-to-repo-dir>/web` directory, as well as a few utilities I want to access inside the hugo container, so adding the `-s` switch runs hugo on the correct site

### VSCode docker tweaks

I have VSCode in a docker, and use a custom dockerfile to build that image. To make it so that docker image can talk to the hugo docker, I need to give it docker-outside-of-docker (again, as previous mentioned, potentially worth a post on it's own), this is a quick overview of the changes;

First add `docker.io` to the `apt-get install` in the dockerfile and add `/var/run/docker.sock:/var/run/docker.sock` to the volumes on the compose file.  
Then to VSCode on the desktop add the following to settings.json, this adds a new terminal profile that connects to the named docker container directly, rather than having to connect to it manually each time;

```json
    "terminal.integrated.profiles.linux": {
        "docker hugo": {
            "path": "sudo",
            "args": ["docker",
            "exec",
            "-it",
            "<docker container name>",
            "/bin/sh"]
        }
    },
```

Also, this set up allows running `sudo docker restart hugo` on the regular VSCode terminal to quickly restart the hugo container from within VSCode in case of errors.

### Running hugo migration tools

This took a fair bit of experimenting before I settled on something that worked. There are a [fair number](https://gohugo.io/tools/migrations/) of migration tools for hugo, and they all to some degree or another do enough to get the job done, but I'm nothing if not a stickler for the little things, and it'd drive me mad to miss some data in the swap. After a lot of reading [^1] [^4] [^5] [^6] and trying a few, I landed on a combo of both [wp2hugo](https://github.com/ashishb/wp2hugo) and [wordpress-export-to-markdown](https://github.com/lonekorean/wordpress-export-to-markdown)

`wp2hugo` builds a full hugo config using papermod, has extra metadata nothing else migrated, has shortcodes for some common WordPress plugins and builds an auto nginx conf.

`wordpress-export-to-markdown` builds a cleaner page/posts directories with content only, ideal for use as base files to pull missing data into.

However, I did hit a slight snag, `hugomods` docker image uses sh, and wp2hugo needs bash, and `wordpress-export-to-markdown` needs npm/npx, and node from the VSCode docker doesn't have npm. Fortunately the `peaceiris/hugo` docker image had both bash and npm/npx, and since these are run-once type things, we can run them like this;

#### wp2hugo

- `sudo docker run -it -u 1026:100 -v /<path-to-repo-dir>/web:/src  --entrypoint /bin/bash peaceiris/hugo:latest-full`  
- `-u 1026:100` Run as user 1026 and group 100 for file ownerships (1026 being my uid)  
- `./wp2hugo --source ../<Wordpress-export>.xml --output convert --download-media`

#### wordpress-export-to-markdown

- `sudo docker run -it -v /<path-to-repo-dir>/web:/src  --entrypoint /bin/bash peaceiris/hugo:latest-full`
- `npx wordpress-export-to-markdown`
- Ran with these settings

```text
? Path to output folder? output
? Create year folders? Yes
? Create month folders? Yes
? Create a folder for each post? Yes
? Prefix post folders/files with date? No
? Save images attached to posts? Yes
? Save images scraped from post body content? Yes
? Include custom post types and pages? Yes
```

- `chown -Rv 1026:100 output` chown to my user

### Managing code

A couple notes here I made along the way of building out the site;

1. Many posts about Hugo talk up the versatility and control, but it took my a while to come to terms with one essential aspect, theme templates are **NOT** gospel, and you can easily tweak a theme by using overrides, just by placing a file with the same name in the local source. [^7] [^8]
2. ~~Use branches for themes, with content on main~~  
Use uncommitted hugo modules to swap & test different themes without polluting the git feed. Commit changes for the theming and tweaks on the `theme` branch and merge to `main`  
To swap around between branches while you've got uncommitted changes, use `git stash push -u -- <file list>`. [^9]

### Migration process

For most, the exports from wp2hugo or wordpress-export-to-markdown is fine, but as I mentioned before some formatting and metadata differ or go missing from both exports. Therefore this is the workflow I settled on to merge it all into how _I_ wanted, and the file structure I wanted.

I used wordpress-export-to-markdown as a base, then copied extra meta from wp2hugo, such as a full length `date` timestamp, the `categories` and `tags`, the `guid` for the posts unique original url, setting the `aliases` from the post's slug and adding `lastmod` from `wp:post_modified` for when posts were edited.

wp2hugo also produces slightly different markdown formatting, which helps spot where export's parsing isn't right

Next up I manually worked through the wordpress site, using the admin post/page lists checking revisions to validate dates, and md layout, making sure I got the right formatting, and filling in complex bits, such as;

- Where I had a newline but not a new paragraph, I needed to manually end a line with a double space `  `, which markdown turns into `<br>`
- Find replacing smart quotes across all markdown files [^10]
- For some of the more complex posts, markdown doesn't cope with nested elements so great, for this I had to wrap missing html in `rawhtml` shortcodes

Some wordpress shortcodes have equivalents in Hugo (youtube notably) so next was going through and replacing YT embed's and WP shortcodes, with Hugo shortcodes.

For some of the old private marked content I still have on the site, but don't want indexed in the lists, then I can make them pseudo private by adding this to the front-matter;

```yaml
build:
  list: never
```

With the bulk of the markdown in a fit state, I can go back through to do a final correction of file layout;

- `git mv` folders around into content
- Rename certain `index` to `_index`, mostly for old WP pages that were acting as subindexes
- Rename single pages from `index` to the folder name  
i.e. `/2009/01/hello-world/index.md` to `/2009/01/hello-world.md`

Finally check for and retrieve missing files not grabbed and go back through for a final correct of image urls, and links

### Theme considerations

There were a few things I was looking for from a theme, however, after the earlier realisation (theme templates are **NOT** gospel and can be overridden), I started looking at ways to tweak the bits I was missing from PaperMod.

#### Post edit timestamps

This one was pretty straightforward, a simple theme edit to one of the PaperMod templates, namely [post_meta](https://www.jacksonlucky.net/posts/use-lastmod-with-papermod/)

#### Right aligned images

There were a few ways to tackle this, at first I looked at [Markdown Attributes](https://gohugo.io/content-management/markdown-attributes/), this didn't quite work, but did give some additional bit of utility for other things, namely for my usage, list styles.

PaperMod let's you drop partial css files to get bundled into the final css, this is ideal for this. A little [css file](https://github.com/KenjiE20/kenjie20.co.uk/blob/main/assets/css/extended/figure-custom.css), and adding `class="figure-right"` to figure shortcodes does the job.

#### Extra shortcodes

[parsiya/Hugo-Shortcodes](https://github.com/parsiya/Hugo-Shortcodes) has a set of turnkey common shortcodes. I grabbed abbr to add that html tag to markdown, and blockquote to add citations to blockquotes.

### Image galleries

I have a bunch of static image galleries that I want separate from the big photo gallery running on Piwigo, and as it turns out there are lots of versions of how to achieve this in Hugo. After another bout of reading [^11] [^12] [^13], I ended up using a hybrid of techniques;

- Add a loading shortcode in the page header so it only loads lightbox when the main image shortcodes gets used.
- The main gallery shortcode, takes a few ideas and combines them;
  - The basic sub-folder/page-resources gallery builder from [christianspecht.de](https://christianspecht.de/2020/08/10/creating-an-image-gallery-with-hugo-and-lightbox2/)
  - Inspired by point 1 from [vanwerkhoven.org](https://www.vanwerkhoven.org/blog/2021/responsive_images_in_hugo_theme/), use responsive grids.  
  I built a simpler implementation using [w3's documentation](https://www.w3schools.com/howto/howto_css_image_grid_responsive.asp)
  - Borrowed a bit of [mfg's](https://github.com/mfg92/hugo-shortcode-gallery) code for loading sidecar's
- Then to get lightbox acting consistently across all larger images, take the gallery shortcode and tweak it for a lightbox single shortcode. It basically mimics the figure shortcode with lightbox triggers.

### Automation

Most of this topic has been covered plenty, but there were a tweaks along the way, again mostly previously documented.

- When testing the build against Lighthouse, split up the configs for different purposes (merge checks vs deployed follow up tests)
- [benhoskins.dev](https://benhoskins.dev/github-actions-deploy-with-rsync/) for pointers on using GitHub actions to run rsync for deployment
- [robb.sh](https://robb.sh/posts/check-links-in-hugo-with-htmltest/) for pointers on using htmltest to sanity check links and HTML elements. It needs a few tweaks to help it along with CI [^14]

### Analytics

For analytics, I’m now using [GoatCounter](https://goatcounter.com). It’s privacy-friendly and lightweight, which is a nice improvement over the usual suspects. It's also way easier to parse basic stats, which is all I really want.

### Comments

Figuring out comments was another deep dive of reading [^15] [^16] [^17] [^18], and there are a lot of ways to leverage them into a static site. I wanted something with ease of use and potentially guest/no registration posting.

Disqus is built in, but a nightmare, [^19] so that's out. [Webmentions](https://indieweb.org/Webmention) never quite got traction, so they're out. Staticman and others like it are nice, and keep comments in the source control for portability, but need a service running somewhere, which detracts from the nicety of a static site, and can potentially really pollute the git commit feed. I looked at using newer social media systems to handle comments, either [mastodon](https://github.com/oom-components/mastodon-comments) or [bluesky](https://github.com/czue/bluesky-comments), but it kinda undermines ditching discus on privacy stands, if I'm then pulling comments from another stream isn't obvious they're being displayed elsewhere.

In the end I went for [Giscus](https://giscus.app/), which is based on [Utterances](https://utteranc.es/), and uses GitHub discussions or issues, respectively. I lose guest posting, but nothing needs to be hosted, and the comments get stored alongside the source, so I makes a decent compromise of features.

[^1]: Shoutout [muffn.io How I Migrated From WordPress to Hugo](https://blog.muffn.io/posts/how-i-migrated-from-wordpress-to-hugo/), ended up being very helpful
[^2]: Notable mention; [Static site generators: Hugo vs Jekyll vs Gatsby vs 11ty](https://tomhazledine.com/eleventy-static-site-generator/)
[^3]: There were many more posts, I've just lost them as I didn't note them / couldn't re-find them
[^4]: Notable mention; [Migrating from WordPress to Hugo](https://bjornjohansen.com/wp2hugo/)
[^5]: Notable mention; [Migrating from WordPress to Hugo](https://davegill.io/blog/migrating-from-wordpress-to-hugo/)
[^6]: Again, many more posts, these are just the helpful/notable ones
[^7]: [Hugo Docs: Lookup Order](https://gohugo.io/templates/lookup-order/)
[^8]: For example my overrides for the ananke theme to give it dark mode [GitHub](https://github.com/KenjiE20/gohugo-theme-ananke)
[^9]: Alternatively the [Git Graph](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph) VSCode extension had a nice UI for stash commands
[^10]: Using [Smart Quotes Normalizer](https://marketplace.visualstudio.com/items?itemName=pinebeecreative.smart-quotes-normalizer) makes this easier
[^11]: Notable mention; [Lightbox Gallery with Go Templates in Hugo](https://zietlow.io/posts/hugo-photo-gallery/)
[^12]: Notable mention; [Adding lightbox to Hugo](https://julianstier.com/posts/2020/03/hugo-and-lightbox/)
[^13]: Notable mention; [Darthagnon/hugo-easy-gallery - load-photoswipe](https://github.com/Darthagnon/hugo-easy-gallery/blob/production/layouts/shortcodes/load-photoswipe.html)
[^14]: [Jesse Wei](https://jessewei.dev/blog/2023/papermod/#ci) htmltest and other tweaks
[^15]: Notable mention; [A Blog Without Comments Is Not a Blog](https://blog.codinghorror.com/a-blog-without-comments-is-not-a-blog/)
[^16]: Notable mention; [Various ways to include comments on your static site](https://darekkay.com/blog/static-site-comments/)
[^17]: Notable mention; [How to add commenting functionality to your Hugo Blog using Utterances](https://www.softwarecraftsperson.com/posts/2024-02-04-blog-comments-using-utterances/)
[^18]: Notable mention; [Using giscus for comments in Hugo](https://cdwilson.dev/articles/using-giscus-for-comments-in-hugo/)
[^19]: [Replacing Disqus with Github Comments](https://donw.io/post/github-comments/)
