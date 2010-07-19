# blog.crowdint.com

The tech blog for Crowd Interactive

## Requirements & Setup

Bundler, install the gem:

    gem install bundler

Then use bundler to install everything else:

    bundle install

## Write something

Create a file in the _post folder following the permalink convention YYYY-MM-DD-title-of-the-post.markdown or YYYY-MM-DD-title-of-the-post.textile

Yau can use either [Markdown](http://daringfireball.net/projects/markdown/) or [Textile](http://www.textism.com/tools/textile/) to write the posts. Just specify the format as the extension of the file.

## Header

The following header must be included at the very top of your post.

    ---
    layout: post
    title: About Crowd Interactive
    author: The Author Name
    email: author@crowdint.com
    avatar: d32b52ec6403614b1adf3e648cbbe584
    short_date: Jun 01
    ---

### Avatar

You can create your avatar using [Gravatar](http://www.gravatar.com). Then go to the "Check this Gravatar" section and copy the Email Hash and include it on your header:

    avatar: a59cda5de705716cbd18195a4dd68a56

## Check it out

To test how your post looks, you can run the jekyll server locally:

    jekyll --server --auto
    
And then, go to http://127.0.0.1:4000 and check how the blog will look.

The _--auto_ option will make jekyll update the server automatically every time a file on the blog changes.

## Commit

Commit the changes, preferably in your own branch and then, let the blog maintainers know that you're ready to be published.
