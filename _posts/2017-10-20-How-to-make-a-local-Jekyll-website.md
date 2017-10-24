---
layout: post
title: How to Create A Jekyll Page With KaTeX
date: 2017-10-20 4:00:00
author: Neal Dawson-Elli
---


### Overview:
1. Install [Git Bash](https://git-scm.com/downloads)
2. Find a [Jekyll theme](http://themes.jekyllrc.org/) that you like
3. Clone the repository to a local drive
4. Install [Ruby](https://www.ruby-lang.org/en/downloads/)
5. Add ability to display math with [KaTeX](https://khan.github.io/KaTeX/)
6. Modify the files to suit your needs

## Installing Git Bash
On windows, navitage to the [Git Bash Download Page](https://git-scm.com/downloads) and click the Windows link.  
Follow the instructions for installation.  If you don't know what you're doing, I prefer to keep the settings to default, including
**Use Git from Bash Only**, **OpenSSL Library**,  and **Checkout Windows-style, commit Unix-style line endings**, as well as 
using **MinTTY**.
![image1.png]({{ site.baseurl }}/img/jekyll_katex/git_bash_1.png)

## Next Up, Find a Theme and Clone It
Check out the [Jekyll themes](http://themes.jekyllrc.org/) and find one that you like.  Download it and extract it to a new folder.
It should look something like this: ![image2]({{ site.baseurl }}/img/jekyll_katex/img2.png)

Note the presence of a Gemfile, with no extension, and a _config.yml file.

## Install Ruby
Download the [Ruby installer](https://www.ruby-lang.org/en/downloads/) and follow the prompts.  
You will be promped to install additional things at the end - press `[ENTER]` to install all 3 options.

## Test Out the Website!
To see if your site works, navigate to the window and (in windows 10) right-click without any file selected and choose **Git Bash Here**.
A new window will pop up that will look sort of like a command line terminal.  As per the instructions on [Jekyll's Quick-start guide](https://jekyllrb.com/docs/quickstart/), 
type the following into the command terminal:
`gem install jekyll bundler` - this will install bundler, a gem that manages other Ruby gems
`bundle install` - this will tell Ruby to parse the Gemfile and install any necessary gems.
'bundle exec jekyll serve' - this will launch the site on a local server!  Look at the output of the command window, it will tell you where to point your browser.
![img3]({{ site.baseurl }}/img/jekyll_katex/img3.png)
Unless you've changed it. 127.0.0.1 can be replaced by `localhost`.

## Get Katex to Work For Pretty Math
This was quite the ordeal, but has been figured out largely due to a post by [Xu Cheng](https://xuc.me/blog/katex-and-jekyll/).  The steps are as follows:
1. Add the following lines to your _conf.yml file:

```yaml
markdown: kramdown
kramdown:
   math_engine: mathjax
```
2. Add the following lines to the HTML head file, which will be in the `_includes` folder:

```html
<!-- Load jQuery -->
<script src="//code.jquery.com/jquery-1.11.1.min.js"></script>
<!-- Load KaTeX -->
<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/KaTeX/0.1.1/katex.min.css">
<script src="//cdnjs.cloudflare.com/ajax/libs/KaTeX/0.1.1/katex.min.js"></script>
```

3. Add the following lines to either the footer HTML file, also in the `_includes` folder, or to the bottom of each html file found in `_layouts`:

```html
<script type="text/javascript">
$("script[type='math/tex']").replaceWith(
  function(){
    var tex = $(this).text();
    return "<span class=\"inline-equation\">" + 
           katex.renderToString(tex) +
           "</span>";
});

$("script[type='math/tex; mode=display']").replaceWith(
  function(){
    var tex = $(this).text();
    return "<div class=\"equation\">" + 
           katex.renderToString("\\displaystyle "+tex) +
           "</div>";
});
</script>
```

And now you're ready to display math properly!  Simply embed LaTeX syntax inside a pair of `$$` and you're ready to go! For example, $$ c = \pm\sqrt{a^2 + b^2} $$ would be
typed as `$$c = \pm\sqrt{a^2 + b^2}$$`

# Modify the Files

Now that everything is set up, it's time to get modifying!  The power of Jekyll is turning Markdown files into a website, and this is 
typically done by using the file structure to tell it how to build the websites.  For example, using this theme, the Blog posts are contained in 
`_posts`, which gets its styling from `post.html` inside the `_layouts` folder.  To make a new post, simple add a new file, making sure to include the 
layout information that's present in the file.  For example:
```markdown
---
layout: post
title: How to Create A Jekyll Page With KaTeX
date: 2017-10-20 4:00:00
author: Neal Dawson-Elli
---
```
appears at the top of this page.  Modifying the title or date or author name will modify the information presented at the top.
