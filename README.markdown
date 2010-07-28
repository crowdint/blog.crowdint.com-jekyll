# blog.crowdint.com

The tech blog for Crowd Interactive

## Requirements & Setup

Clone the repo

    git clone git@github.com:crowdint/blog.crowdint.com.git

Bundler, install the gem:

    gem install bundler

Then use bundler to install everything else:

    bundle install

You might want to install [Pygments](http://wiki.github.com/mojombo/jekyll/install) to configure your syntax highlighting, so you can try this (depending on your OS):

On OSX with MacPorts:

    sudo port install python25 py25-pygments

Alternatively on OS X Leopard:

    sudo easy_install Pygments

On Archlinux:

    sudo pacman -S pygments

On Ubuntu:

    sudo apt-get install python-pygments

On Gentoo:

    sudo emerge -av dev-python/pygments

On Windows(?):

    Are you using Windows???... Seriously.

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

## Common issues

### Segmentation Fault

If you get something like this:

  ..../rdiscount-1.6.5/lib/rdiscount.bundle: [BUG] Segmentation fault

You will have to reinstall rdiscount gem manually, it will compile it with the correct platform

### pygmentize command not found

Install python syntax highlighter

  sudo easy_install Pygments