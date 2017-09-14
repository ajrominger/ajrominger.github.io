# LHD

Personal website of Laurent Hébert-Dufresne : http://laurenthebertdufresne.github.io

### Requirements

This is a [Jekyll](http://jekyllrb.com) site hosted on [GitHub Pages](http://pages.github.com).

Make sure you have Ruby 2 or above ``ruby -v`` or install it ``sudo apt-get install ruby``

Install Bundler using RubyGems : ``sudo gem install bundler``

Install rest of dependencies using Bundler : ``bundle``

### Updating the website

After cloning the website, simply ``cd`` to the project folder and use Jekyll to serve the website on your local computer : ``jekyll serve``

The website will be available at the following url : [http://localhost:4000](http://localhost:4000)

The majority of contents are written in [Markdown](https://daringfireball.net/projects/markdown/).

#### Changing the site name or other settings

Most settings are located in the ``_config.yml`` file.

#### Changing the homepage

The homepage is located in ``index.html`` file at the root of the project. This is the only HTML file.

#### Page contents

All pages are located in the ``_pages`` folder.

Adding a page will result in a menu item being added to the header.

#### Adding a new post

All pages are located in the ``_posts`` folder.

Copy paste an existing post and change the file name to reflect the date and the name of the post.

### Deploying the changes

Commit all changes using ``git add --all && git commit -m "Updating the website"``

Push to Github using ``git push``

Snice : http://laurenthebertdufresne.github.io