
.. include:: ../../../Includes.txt

================================================
2013-03-23 TYPO3 Composer Integration (Wishlist)
================================================

2017-03-23 `Tweet JW <https://twitter.com/JochenWeiland/status/844583199112122368`__, 
`Tweet AS <https://twitter.com/JochenWeiland/status/844583199112122368>`__, 
`Gist AS <https://gist.github.com/alexanderschnitzler/b70680f1878449d4c807127b429bfb71>`__

TYPO3 Composer Integration (Wishlist)

This document gives an overview of the issues I see in the current composer implementation in TYPO3. 
First of all I'll write it in the form of user stories and afterwards write an actual text.

Composer Related Wishes
-----------------------

As a developer I want to:

- ☑ composer init and require typo3/cms 
- ☑ install TYPO3 extensions via composer
- ☐ want the dependencies of the required extensions to be resolved recursively

TYPO3 Related Wishes
--------------------

As an admin/integrator I want to:

- ☐ install extensions via the extension manager (also in composer mode)

As an admin/integrator I don't want to:

- ☑ dump the autoload information on the command line (in non composer mode, fixed in 8.x via install tool)
- ☐ dump the autoload information on the command line (in composer mode)

As a developer I want:

- ☐ To have a fixed satis that doesn't drop the extra part with the "typo3/class-alias-loader" information

As Alexander Schnitzler I want:

- ☐ Extensions to be namespaced (folder structure wise, like in the regular vendor dir) [typo3conf/ext/vendor/extension]
- ☐ The TER to only act as a GUI/Proxy for available extensions.
- ☐ The extension manager to be a GUI for composer itself (being operable only if possible)

My Vision
---------

Let's begin with the most crucial part of this operation. I do believe that composer is the 
package management the TYPO3 environment needs and which should be embraced completely to 
reduce the functionality of the TER to a bare minimum. Why is that so? Because I do believe, 
that TYPO3 does not only want to have the very good working autoloading of composer but also 
the rest of that beautiful composer package being the package management itself and the 
possibility to decentralize the packages. Composer can replace the dependency resolver 
completely (one thing less to take care of) and it can even resolve dependencies recursively. 
As more and more code is packaged into small chunks of useful libraries, you don't want x 
numbers of extension to ship the same libraries in a Resources/Private/PHP folder. It's a 
proper intermediate solution but in the end you don't want to have foreign code in your 
own repository. Besides that, composer enables you to fetch your code from a lot of different 
sources. It's even possible to mix public and private repositories and still get the 
dependencies right.

All this needs a GUI for people who are not familiar with the command line. If you strip 
down the extension manager to a bare minimum, you have the functionality of a `composer 
require` statement. As packages are installed by default if physically loaded, one can 
simply drop the step of having packages with installed/uninstalled states. What is that 
good for anyway? Either you need phpmyadmin or you don't. With composer you can even 
just install packages in dev mode `require-dev` and keep the live version untouched. 
Something that is not possible with the current approach. One can drop the PackageStates.php 
and all `ext_emconf.php` files (just needed for the dependency resolver and displaying the 
title in ext manager anyway). Once you operate your installation in development mode 
(finally a use case for the contexts), extension manager operates with the composer 
`--dev` flag. As there is no pollution of any PackageStates.php file, there is no 
risk to deploy crap to the live server.

So, what about the people that use the extension manager on live systems? Well, screw
them. Why do we even invent our own deployment solution when we accept the thinking 
that one needs to be able to install packages on a live system? That's insane. We are 
living and working in 2017, you do not work on a live server to test something. So,
what's the problem with composer consuming so much RAM and CPU time anyway? Never mind,
it takes place locally and/or on your build server. Even if you deploy via ftp, you 
install/uninstall packages before that. This means, that people need to cope with 
that fact and if they really need to install packages on their live servers, they
should go ask their clients for more money for their own convenience.

What about the namespaces? We adjust the core to search for extensions one folder 
deeper than usual. That way we can namespace our extensions folder wise and do not
have any conflicts with similar extension names. For the developer this gives a lot
more clarity which packages are installed. But the real benefit is, that people do
not need to register extension keys and fight about it. You want to publish your 
own gridelements? Fine, go ahead! And suddenly the TER is a gui like packagist
itself. It can hold meta information about packages but is not responsible for 
the availability of packages themselves. This frees the resources of the server
team and the t3org team can update typo3.org more regularly without the TER 
bound to it.

On top of that, we can drop the satis, installed on composer.typo3.org or 
make it real proxy. I hate installing typo3-ter/foo extensions because it is 
the reason for most issues I have with TYPO3 and composer. It's due to the fact, 
that the composer.json of satis is different from the actual one of the package,
leading to nasty brainfuck situations. Example: compatibility6 has a 
"typo3/class-alias-loader" section which is the key feature – no, it's the 
reason – why that extension exists. You want to register class aliases but 
that information is missing in the composer.json of satis because the way 
satis builds these extensions is dependent on our very own algorithm defined 
in https://github.com/TYPO3/CmsComposerPackageGenerator which noone wants to
maintain. Everyone simply wants to use the original, the pure composer 
integration. Once you face any issue with composer.typo3.org and you are not 
very much into that whole composer topic you start believing that 
composer is shit.

Do we really want to live with the status quo like we do with Extbase? 
It's becoming too many construction sites in just one cms that seem to 
never get finished. I don't think that this split, which is still done 
in many areas, is a benefit to TYPO3. One cannot always get everyone 
aboard but leaving a few behind can make many more join.

Comment by NamelessCoder
========================

NamelessCoder commented 11 hours ago

I whole-heartedly agree with the essence of every argument herein. My two cents 
about pros and cons, some of which Alex already covered nicely:

Pros
----

- Less custom inventions in TYPO3 to handle extensions
- Less TYPO3 infrastructure to maintain
- Less requirements put on extension authors regarding metadata; no more ext_emconf.php updates.
- No requirement to initialise projects with a huge composer.json boilerplate, able to 
  build entire base project using a few key composer require commands
- Obvious benefits to deployment; compatible with standard deployments of composer packages
- Packagist is orders of magnitude faster than TYPO3 satis
- Obvious extension publisher benefits by reducing the need to 
  1. double-publish and 
  2. package dependencies in ways composer does not agree with
  
Cons
----

- Drastic change unless EM is also turned into a GUI for composer
- Controversial change to make EM require presence of composer (that said, 
  I agree with all the arguments Alex made in this regard)
- Is a serious amount of work
- Requires even more work following that, to make obvious improvements to 
  things like extension folders using vendor parent folders

The way I see it, the two biggest hurdles this would face, has to be the rather
drastic implicit consequence that EM no longer works without composer (again,
I agree, we should do this). That's quite controversial given the many (cheap)
hosting providers that do not provide access to composer. In the end this is 
a political decision and one that is influenced by user requirements - and 
acting unilaterally here would probably hurt more than it would benefit 
TYPO3. But, I refrain from getting too involved in the whole debate about 
politics towards end users - I stick to the technical arguments as much as 
possible.

The second big hurdle is the amount of dedicated manpower this would require; 
involving not just the TYPO3 core developers but also the server team and I
expect some T3A decision whether or not to fund it. Enough said that it's 
probably not the optimal timing to call for that manpower this year (and 
there are other problems that can come from attempting to crowd-fund something
this integral involving this many participants).

Even though I'm pessimistic about overcoming those two hurdles at the current 
time I highly encourage the core developers, members of the server team and 
T3A to consider the points made above. I know plans are being thought out, 
but given the scale of this, we need as many people as possible to be on 
board with the idea as a whole and be willing to argue to the next person,
and the next, etc. that this is in fact the right direction to take. Not 
just on the technical level but also in the broader scale of being a PHP 
application in a PHP ecosystem ;)


Comment by helhum
=================

helhum commented 8 hours ago

Thanks for the writeup. For me it does not have any surprising things in it and 
I agree with most of what you and Claus have been written. I disagree with some 
and have different opinions on some technical details.

What surprises me a bit are two things.

1. Much things on the wishlist are already possible with little configuration and 
   the help of TYPO3 Console

2. The focus on what we don't have instead of the celebration of what we achieved 
   already.Let's look at what is possible already first:

..

   one can simply drop the step of having packages with installed/uninstalled states
   
typo3cms install:generatepackagestate exists for years now and was constantly improved. 
I'm now close to generate it on a regular composer install run without any other 
scripts config to be required. The only thing that holds me back is backwards 
compatiblity. Once I have this resolved, when using TYPO3 Console it will just 
work like you describe. It already works like that if you add one line to your 
composer scripts.

   I hate installing typo3-ter/foo extensions because it is the reason for most
   issues I have with TYPO3 and composer

We officially recommended to extension authors to publish their code to Github 
and a little later recommended to register them on packagist. Many extensions 
(including compat6 you complain about) are available on packagist, resolving all 
issues you have with composer.typo3.org. Just use extension packages from packagist
and be done. If you need an extension on packagist, talk to the authors, help them
to get this done. It makes more sense to invest the time in that, than working
on a legacy layer (composer.typo3.org), which will obsolete itself anyway much 
faster than we can provide things like a composer UI. In a recent TYPO3 8 project,
I don't even need this composer repo any more.

Now to the focus on the negative

   Do we really want to live with the status quo

What makes you think that this is the case? Do you think things are moving too slow?
I disagree. We are in a transition phase. Sometimes this hurts a bit, but it is 
important to finally heal. We can't move faster than our developers. With that 
I mean developers in agencies, extension developer and also core developers.
What works great for you, needs time to sink in for others. This is really nothing 
to worry about.

Are we really moving slow? Look at the amount of extensions on Packagist, or the 
traffic in the Slack #composer channel. Consider the #continuous-delivery channel 
popping up. Not only technical implementation or limitations should be considered, 
but the social dynamics, shift in mindset that is constantly happening. I consider
these things much more important than getting rid of the ext_emconf.php file,
which does not really matter much, is not much hassle to handle but a huge amount 
of work to get rid of. Same is true for the boilerplate currently necessary in the 
composer.json file. It is so much easier to copy that around or take a minute to 
explain that to newbies, than to change things in that area without breaking 
existing (composer) workflows for a bunch of users.

I'm convinced that a big budget, political decisions, loads of development hours 
cannot significantly mitigate the pain some of us feel in this transition phase. 
We are all learning and adopting new workflows. What would have a greater impact 
is to educate ourselves and help others. The technical things will follow 
naturally when the is there.

Look at TYPO3 Console for a last time. When I created it, people questioned the 
project for years. Now it has become a natural choice for composer based but also 
non composer based projects. Although I recommend it to be used with composer, I 
take the (sometimes not so small) effort of also providing it as extension, 
because 

a) I think it is important to not cut off a significant user base and 
b) I'm convinced that people using the console in non composer mode will adopt 
   a certain mindset that will help to adopt composer based workflows lateron.

Conclusion
----------

Wishes are fine as is challenging the status quo. However in the end, time is 
better spent on educating and helping others. This will already improve the 
status quo and potentially gets more people on board to work on moving forward. 
Transitions are hard. Get help and help others. You still think more needs to 
be done? Fork! Publish! If your work is convincing, people will adopt. TYPO3 
will adopt.

Welcome to OSS. We all love it (and hate it some times)!






