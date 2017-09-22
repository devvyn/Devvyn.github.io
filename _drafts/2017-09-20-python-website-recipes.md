---
title:
...

I've already made a personal site with [GitHub Pages],
but I'm still curious about all the other options.
I'm really tempted to see how quickly I could replicate the original site
with alternative solutions.
Of course, if I'm really sincere in my desire to learn,
I'd be well served to write about my experience.

At the moment,
I'm most interested in static site generators,
but I'd also like to try more conventional, "always on" solutions.

## Making Websites

So, the question is,
how does one best deliver one's thoughts to another human
through a web browser?

If the answer is "make a website",
it's time to break it down into smaller pieces,
because there are probably as many ways to make a website
as are ways to open a restaurant.

A hypothetical restauranteur faces numerous choices:

- the choice of menu items,
- the decor,
- the kitchen equipment,
- the behaviour of the wait staff,
- the building location,
- the customer parking,
- locks and alarms,
- and so on.

Similarly, a recipe for a website comprises:

- the selection of content,
- the visual design,
- the user experience design,
- the document handling tools,
- the server's programming languages (if any),
- the HTTP server,
- caching technologies,
- the domain name and URL schemes,
- security necessities such as SSL,
- and so on.

The number of combinations of possibilities is understandably daunting.

## Overview of Website Architecture

The best way to grasp an overview of possible solutions
would be to trace the path of information
flowing between author and reader,
and enumerate the variables potentially affecting that path.
Let's trace upstream from reader to author,
because there's little variability in the final legs of a web page's trip,
and much variability in the middle and earlier.

The first web browser had originally been a simple viewing and navigating layer
between human client and machine server.
Early web servers did little more than to obediently
deliver a file from disk over the network, upon request.
With this simple system, there were few choices:

- Which document editor will you use to write the raw content of your pages?
- Will the document contain embedded functionality
  for the browser to bring to life?
- If extra, dynamic functionality is being created, what tools will do it?
- To which web browser applications and versions will you cater?
- How is the web formatted file produced
  before it's given to the web server machine?
- How will you copy the file to the web server machine?
- What web server software and operating system are on the server machine?
- What web addresses will the site's pages use?

Beginning with this simple foundation, we get all possible combinations of
what would these days be called "static websites".

### Static Websites

For static websites, all content is fully formed in advance,
and the software or person responsible
doesn't stick around to participate in
minute-by-minute delivery to users.
Instead, the pre-created website files go to a web server
that's single-mindedly dedicated to the task of endlessly
listening for requests and fulfilling them one after another
with a transmission of the corresponding file.
By the time any real world user visits a page on a static site,
the original software that generated it is done with this process,
and therefore cannot interact with visitors at all.

When I got started with making websites,
this was the workflow for everyone involved:

1. I write content for later inclusion or conversion into HTML
2. I make web-ready files: HTML (by copy-and-paste, mostly),
   and optionally CSS and JavaScript (if I felt ambitious)
3. Deliver files to server or storage system used by server (probably by FTP)
4. User requests page from browser, browser requests page from server
5. Server delivers file to user's browser, browser displays or downloads file
6. Repeat from step 4

#### Dynamic Features on "Static" Pages

That's not to say dynamism is impossible,
just that there's no participation from
the actual machine that sent the pages.
Dynamic interaction can be achieved in the browser with JavaScript,
and pages can even appear to change and those changes persist
between visits by using local storage such as cookies.
Furthermore, certain HTTP methods that are
typically associated with classic HTML forms
can send data to other machines,
which can in turn do the remembering or computation
your web server or user's browser doesn't do.
For example, [Disqus] can store comments
for any article published on a static site.


#### Static Site Generators

There's a class of web publishing tools that specialize in getting
authors through the first step of making web-ready files
after the necessarily human part is done.
This saves an author the trouble of working through technical matters,
so the author's focus can be on producing content.
Getting the files to a web server is sometimes feature of these systems, too.
In some cases, the tool in question is already on the Internet,
just waiting for your non-HTML content so it can neatly produce
ugly-but-necessary files that look great in the browser.

- [GitHub Pages]

### Server Based Solutions

If you need your website users to be able to interact with your site while using the browser,
some sort of machine needs to be ready and waiting to handle those needs.

- [Flask]
- [Pyramid]
- [Django]

## Choosing

When approaching the problem of
choosing amongst numerous solutions
it's best to start by paring away as many options as possible.

I can narrow down the solutions I'm most interested in
if I take a narrow-minded point of view
by eschewing languages other than those that are necessary.
HTML, CSS, and JavaScript are nearly unavoidable on the web,
unless one strives for utmost simplicity.

## Python

I'm in love with Python
as a language for expressing information for humans.
If I open my mind to include the fondly cherished Python,
I increase my list of fun trials
by a reasonably vast extent,
without going hopelessly overboard.

[Flask]: http://flask.pocoo.org/
[GitHub Pages]: https://pages.github.com/
[Disqus]: https://disqus.com/
