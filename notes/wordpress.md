# WordPress

## Scope

These notes cover *the WordPress-based user-facing website*, namely:
* how it is organised;
* the maintenance of it; &
* how it relates to the wider ES-DOC ecosystem.

*Note*: the notes are specific to the ES-DOC WordPress site. There are lots of
tutorials & documentation available online to cover WordPress in general.


## The basics

* The ES-DOC ecosystem is mapped to the WordPress site as a front-end. You
  can see the specific mappings via the 'Domains and Websites' tab on the
  [WebFaction UI](./servers.md).


##  Our use of the WordPress UI

* We rarely use the WordPress Dashboard.

* We use a few plugins, though, & so it is good to check the 'Updates' page
  every now & again to see if the plugins we use need updating.
  * the most important plugin we use is 'Conditional Menus' which changes
    the content of the menu bar element depending on where the user is in
    the site page tree.

* We are using one of the WordPress themes and are happy with it, so there
  is no particular need to change at least for now.

* There is a 'Posts' tab that we currently [13/02/20] do not use, but it is
  what we will use when we start an ES-DOC blog.

* MG usually does not touch the 'Settings' tab, either.

* 'Users' WordPress site tab:
  * MG & SB have been setup as admins;
  * some others are listed to just receive emails etc.

* There are media libraries to hold graphics for the site.
  * The "1, 2, 3, 4" graphic (the one found at the foot of
    [this page](https://es-doc.org/cmip6/)) is "probably the most important
    graphic on the site".
  * There are some techincal screencasts are on there too.

* The CMIP6 "microsite" is the main part of the whole site at present.

* Note that, regarding the menu on the WordPress site:
  * the 'compare' currently goes to the old, i.e. CMIP5, comparator;
  * 'view' is synonymous for 'explore' (i.e. pertains to the explorer
     application).


## Maintenance


### Notes

* The process for adding a new page depends on whether or not the page is a
  static one:
  * if it is static, then simply use the 'Pages -> New Page' button. The
    'slug' item is the text that forms the resource path of the rendered page
    URL, i.e. `es-doc.org/<slug>` (e.g. `slug=about` would be mapped onto
    `es-doc.org/about`);
  * if not static, then there is a further step [need to clarify what this is].

* Theming: we can edit/customise the style theme, where we currently use
  the '2017' variant of the theme we like & see no reason to change to a newer
  version (they update it each year).


### Current maintenance TODOs

* Some current, minor, maintenance TODOs:
  * [ ] check for any dead links there [SB: could we use a plugin for this?];
  * [ ] update the default CSS stylesheet file so there is no padding against
        the text when viewed on a desktop;
  * [ ] review the content of the 'technical' section, since some of it is
        no longer relevant & can be removed.
