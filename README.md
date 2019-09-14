# brainstorming

Ideas for a blog platform simple enough to operate by hand.

Format is not set in stone yet.

## Motivation

The web is a real centralized mess, it would be nice to fix that. As a certain wise person on Twitter put it, "we used to have millions of websites, now we only have four and they're filled with screenshots from the other ones."

Most of the complex solutions today rely on complex protocols and such, in part because those are easier to scale to a trillion users. We don't need that - here, the scalability comes from the internet itself - each blog is its own server. One gets slashdotted, the rest stay just fine.

## Ideas for the solution

### Decentralized

Have people host their own blogs on their own servers. Use RSS for distribution. Perhaps can have servers where bloggers can publish their feeds for discoverability or CDNs that mirror posts. 

### Self-contained posts

A single post should be easy to save and view offline - have a self-contained blog post file format that can be saved offline or quickly mirrored. Something like a `.blog` or a `.post` file format that has all the data needed to read that post offline - all the images referenced and such.

### Flexible

Allow multiple content types - text (tweets, blog posts, novels), photos (photo albums, Instagram posts), audio (songs and podcasts), video (YouTube). 

Perhaps some type of a "reply" format - can just be a field in the meta of the post file.

Focusing on text for now.

### Not annoying

Text posts are HTML. No JavaScript. Bare minimum CSS to allow for simple customizations like changing background colors and fonts. That's it - use HTML as a markup language, not as an app platform.

### Simple

The baseline for simplicity is that somebody can create a blog and publish posts on it purely by hand, no utilities from our side required - meaning that the file format for those blobs and the structure of the blog need to be *incredibly* simple.

### Barebones identity

Have a simple way of verifying that a post comes where it claims to come from - but leave the rest of fancy cryptography out of scope.

## Structure

### Posts

`.post` format needs to be incredibly simple, but a single file. Just make it a `.zip` archive with some files in it:

- `meta.toml` - File describing the post: what the title is, quick description, what content type it is, when it was published, what is the source blog's url, perhaps what it's in reply to, etc.
- `post.html` - Main post file
- `assets/` - Content needed for the post, such as images
- `signature` - Signed hashes of all the other files in the `.post` for verifying authenticity.

For digital signatures, keep it simple. One interesting tool is OpenBSD's `signify`, which can produce a file containing a list of files and their hashes, and the signature for that list. 

This fits with the philosophy of being able to do things by hand - one can perform the necessary checks themselves with just a few bash commands.

Simple usage example of `signify`: https://news.ycombinator.com/item?id=9709843

### Blogs

Rather than requiring some complex API integration for an external reader app to be able to integrate with a blog, have the hypothetical standard require essential parts of the blog be located at specific canonical paths, for example:

- `/feed.xml` or `/feed.json` - the blog's feed
- `/download/postname.post` - path for downloading the `.post` file 
- `/web/postname.post` - web preview of the post, think individual blog posts you'd find on the web today.
- `/posts/postname.post` - canonical path for a post, apps would know to redirect to download, for web browsers it can contain two links to the download and the web preview, and redirect to web preview automatically
- `/archive` - the list of all the posts in the blog
- `/about` - information on the blog's owner
- `/publickey` - or something along those lines, for the blog's public key for post verification

Structure is defined by URLs, so it is possible to just host on a 100% static platform like your own server or GitHub pages.

Of course, would be nice to also have a CMS-type thing that you can push to a VPS and have a page where you can upload `.post` files and have them automatically get added where needed. 

Simple enough, so can do it by hand, a few shell scripts, or a nice GUI CMS.

## Ecosystem

Would need a few components for working with blogs and posts.

### Post packager

Barebones - a script that packages a `.html` provided in the arguments into a proper `.post` file.

The dream - GUI editor. Editing pages should not be too difficult if we stick HTML as a document markup format like in the good old days.

### Blog manager

Barebones - a script that packages a folder full of `.post` files and the user's public key into a blog post structure defined up there

The dream - a CMS to do the same thing visually

### Post reader

Barebones - a script that verifies the integrity of a `.post` file and then opens it in the browser

The dream - a fully-fledged application where you can subscribe to feeds and read posts. Would do the same integrity verification, but also remember the blog's keys and notify the user when something seems off. Would enforce the "no JavaScript and minimum CSS" rule by not even using a full browser engine.

## Future

- Barebones implementation that functions for publishing blog posts on the web
- A standard with all those ideas above about structure and verification set in stone
- Support for the rest of formats and not just blog posts
- Nicer apps for the ecosystem
