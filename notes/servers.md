# Servers

*Note*: for security, as this document is freely accessible, servers have
not been named. In general no compromising details have been included.


## Scope

These notes cover *the WebFaction servers*, namely:
* how they relate to the software stack;
* how they are setout, organised & managed;
* how they are deployed.

* *Note*: preferably, eventually, we will move away from WebFaction, since
  while it does some things well e.g. the UI and its Dashboard is useful, it
  is not ideal, for example the web servers are shared which is undesirable.


## The basics

* WebFaction is an internet service. We rent two machines off them as:
  * a production server (identified by the number <redacted, ask SB>);
  * a test server (ditto by <redacted, ask SB>).

* Each is ~ 1GB (in RAM).

* We can interact with these severs either via:
  * the in-browser UI i.e. by logging into the WebFaction site online;
  * the CLI, by `ssh` (for those users with `ssh` access configured, i.e.
    those on the `ssh users` list for the WebFaction account).


## The details

* When you login via the WebFaction UI, you get to a dashboard page. From
  there, you can navigate through the various tabs, as outlined in the
  sections below.

* ESGF platforms: will interface with that in that `cf-python` (we were
  co-hosting, but now it has been moved). [?]

* We can see processes on the servers via the logs etc. For example, we
  sometimes get automated emails from WebFaction informing us that the
  memory usage is over the limit for our purchased account plan. If we
  check the logs & pick out the processes that are using the bulk of memory
  we can manage them appropriately. There are two main offenders for
  memory usage at the moment, namely the:
  * `prod_ws_url_rewriter_fi` service;
  * `pid_sync_handler.py` script.


### Databases & storage

* We use five different databases (DB) in total (see 'Databases' tab in the
  UI):
  * a MySQL DB used for the WordPress site;
  * two PostgreSQL DB for each server, each of the pair being for:
    * the errata;
    * the documentation.

* We rarely, if at all, need to touch the MySQL Wordpress-related DB, and
  we should not have to touch the PostgreSQL databases apart from during
  deployment (at least for the moment, we are trying to get that automated).

* The PostgreSQL databases are "fairly well normalised" [?].

* Some of the documentation itself is stored on GitHub in specific
  repositories, because it totals ~8.6 MB so is quite small & on GitHub can
  be kept compact & quick-to-access. But a documentation DB (per
  server) is also used. Notably:
  * the CIM documents are all stored in the `ES-DOC/esdoc-archive` repo on
    GitHub;
  * but all errata is stored in the databases.

* We have pointers to the GitHub repositories so we can pull down any
  required content, in either JSON, HTML or XML format.

* There are two consumers of the databases: 
  * `owner`: has full access, including deletion privileges & the ability
    to create new tables;
  * `user`: despite the UI stating they have "full permissions", they can
    only read & write to & from existing tables.


### Emails

* There are various email accounts associated with the WebFaction account.

* There are also a numebr of email lists: you can use WebMail to create lists
  of relevant people & we use it for example to blanket email all of the
  project liasons with updates.

* We don't use the Webmail service provided.


### Security

* SSL certificates have been setup and are managed by CNRS (Le Centre
  National de la Recherche Scientifique). They are renewed annually.


### Domains & websites

* The 'Domains and Websites' tab is the most important one in practice.

* There are two key entities:
  * domains, i.e. URLs;
  * websites, i.e. applications.

* The domains are mapped onto the websites to produce the physical overall
  website.


#### The domains/URLs

* The root domain is `es-doc.org`, & the sub-domains have various prefixes,
  where the resulting URL formed is `<prefix name>.es-doc.org` and we call
  that the domain.

* For example, the sub-domain `api-pyessv` produces `api-pyessv.es-doc.org`.
  That particular domain has some basic JSON & it e.g. enables comparison
  between the CMIP5 & CMIP6 components/versions [?] of ES-DOC.

* It was decided to use sub-(sub-)domains rather than second-level (& third
  etc.) domains i.e. `es-doc.org.<component name>` (for the example
  above that would be `es-doc.api-pyessv.org`), to bind the
  components to the root ES-DOC domain, because the 'slashing' required
  for second-level domains would add complications.

* The sub-domains are (re-)routed to the required endpoint, e.g.
  `further_info_url` is routed to the `explorer` sub-domain.

* There are some core prefixes: for the domain names:
  * `test_`: all test server domains are prefixed with this;
  * `stats_`: these are domains configued to collect analytics (mainly
    towards IS-ENES3 reports, otherwise we don't use these analytics much).

* Domains are easy to set-up, but the important thing is how they are
  structured. Thought has been put into the structure as it is.


#### The websites/applications

* One advantage of using WebFaction is that it initialises your
  applications for you.

* We use WordPress (`WordPress`) & static (`static`) applications, but most
  are custom (`custom`), listening on a dedicated port.

* All of our web services are developed in Python using the Tornado
  web framework. We execute a shell script to launch a web service, & that
  allocates a port via WebFaction.

* The websites are organised so that they may have one of several prefixes,
  subprefixes, & suffixes:
  * **prefixes**:
    *`test_` or `prod_` to specify the applicable server;
  * **sub-prefixes** (optional):
    * `fe_`: implies *just a Javascipt application* i.e. it has no associated
      HTML, such that all the content is created via JS using Vue.js
      (previously Backbone.js was used, but we moved to Vue);
    * `ws_`: a webservice, which uses Python & Tornado (see above);
    * `wordpress_`: the wordpress sites;
  * **suffixes** (optional, i.e. the primary domain has no suffix):
    *`_http`: (all run via SSL but) this re-routes from `http` to the
      corresponding `https` domain. This enables us to always route to the
      encrypted i.e. HTTPS equivalent, for security.
    * `_stats`: for cases where we are collecting statistics/analytics.

* Overall, this gives a full application name (optional elements are given
  in square brackets which do not form part of the name) of
  `<prefix name>[<sub prefix name>]<web-service name>[<suffix name>]`, for
  example `prod_ws_errata`, or `prod_fe_explore_slink`.

* Cross reference these with the GitHub repositories, noting that the repo
  names also include many of the prefix & suffix keywords as outlined for
  the sub-domains. For example, the `prod_fe_errata` application is directly
  related to the `esdoc-errata-fe` repository. One general naming difference
  is that the GitHub repos all have `esdoc-` as a general prefix (& use `-`
  not `_` as a separator) to the application & sub-application components
  that follow.

* We will never need to touch applications with the suffixes, as above.


#### Mapping domains to applications

* Domains are routed to applications. This mapping is set up by WebFaction:
  it specifies that certain applications should serve certain domains.
  [? order wrong in above sentence?]
  
* Mappings can be setup or amending via  the UI: go to the 'Contents'
  sub-tab & then to 'Add an Application'.
  
* We are using custom certificates, as provided by IPSL. It is valid until
  October 2020 and we will renew them. We shouldn't generally have to touch
  them, except for creating new ones or deprecating old ones.


### Development environment


#### SSH access

* Only users who have been added to the list permitting ssh access will be
  able to `ssh` into the servers.

* A designated ES-DOC development environment has been designed and setup.
  All core developers should use it to facilitate collaboration.

* As part of this, there are helper commands set up for ease & consistency
  of operations. For example, there is a ready-made script for SSH'ing into
  either server, so use those. Taking the production server as an example:

   ```
   $ ssh-prod
   <prompts for credentials, log in>
   $ ls
   <displays list of directories on the production server>
   ```
* Of the directories listed from the above command, there are some that we
  are unlikely to, & others that we expect to, need to edit:
  * (probably) never edit: `bin`, `lib`, `temp`, `hosts`, `.csv`;
  * likely to need to edit: all of form `web*`, namely:
    * `webapps` [note: one of the two most important, see below];
    * `weblibs` [note: the other that is most important, see below];
    * `webcerts`;
    * `webenvs`.


#### `webapps` directory and 

* This is the first of the two important directories. It holds all of the
  applications shown on WordPress, e.g. there are the sub-directories
  `prod_fe_explore_slink` & `prod_fe_errata`, each specific to the
  corresponding application (website).


#### `weblibs` directory

The other important root-level directory on the server is `weblibs`:

```
$ cd weblibs
$ ls
<listing of weblibs sub-directories>
```

* The sub-directories as shown from the above include two archives:
  * `esdoc-archive`: maps back to the GitHub repo under `ES-DOC` of the same
    name;
  * `esdoc-cdf2cim-archive`: stores the raw inputs (JSON blobs) that
    ultimately get converted to the CIM documents.

* Note that inside `esdoc-archive`, the `docs_XX` files e.g. `docs_aa` are
  compressed files & we have dedicated scripts to compress & uncompress them.

* Some of the libraries of note under `weblibs` are:
  * `esdoc-docs`: this is the static documentation, which doesn't get used
    much;
  * `esdoc-py-client`: AKA `pyesdoc`;
  * `esdoc-shell`: see section below;
  * `esdoc-web-explorer`: application of pure JavaScript, such that all of
    the HTML is dynamically rendered through JS, using the Vue.js framework
    via Node.js, & calling various APIs.
  * ... [& more yet/not to be discussed]

* To set up the exact ES-DOC development environment using `esdoc-shell`,
  such that any developers will gain an identical environment, run:
  ```
  $ git clone https://github.com/ES-DOC/esdoc-shell.git
  $ . activate  # or 'source activate'
  $ . install
  ```

* Regarding `esdoc-web-explorer`, note:
  * the `.js.map` files are not important to us.
  * via the following, all of the JavaScript gets compressed into a `dist.js`
    which incorporates the relevant files (most of the ES-DOC stack is
    converted in a similar way:

    ```
    $ git clone
    $ cd node-modules
    $ init node init
    ```

#### `esdoc-shell` & interfacing with the GitHub repositories

* The overarching philosophy is that we should not have to do anything
  manually. Therefore there are a large number of scripts that have been
  setup and aliased to intuitive command names for ease of use. All
  of these scripts have the same pattern ("like a design pattern")
  including a similar naming structure.

* There is a `sh/activate` script that sets everything up by:
  * exporting & aliasing as relevant;
  * [most importantly] creating an `ops` directory.

* The `ops/` directory that is created as above:
  * can be considered to be the "dev ops" directory;
  * has the purpose of storing any content required whenever any of the
    `esdoc-shell` commands are run, and remaining specific to the given
    installation.
  * is included in the `.gitignore` so it is never committed to the repo.

* Note each web-service has its own dedicated virtual environment under a
  `venv` directory. There are also `conf`, `daemon` & `logs` directories.

* By means of the above, & more, everything is structured & self-contained.

* In the beginning, the directories were populated via running a `git clone`
  for the appropriate repo inside them, e.g:

  ```
  $ cd webapps/prod_fe_errata
  $ git clone https://github.com/ES-DOC/esdoc-errata-fe.git
  ```

* Then, to (taking `webapps/prod_fe_errata` again as an example) to update
  the server to a changed state of the GitHub repository, we use a mixture
  of standard `git` commands & custom scripts aliased in the development
  environment. For the static websites, this is quite simple:

  ```
  $ git pull
  $ errata-ws-daemon-reload
  $ errata-ws-<tab-tab>
  <This will display the various scripts setup.>
  ```

* Web services (containing `ws` in their name, e.g. `prod_ws_errata`), which
  are non-static, were also initialised with a `git clone` command, but
  there are extra steps for updating them relative to the steps for static
  sites as illustrated above, namely the web-services need to be re-run after
  so that they pick up on the updated directory state after a `git pull`.

* Note *scripts are mapped to all repositories*, so the equivalent commands
  (to those in the example above) exist for other repos. Look at the `sh/`
  sub-repo for any repo to see these shell scripts.

* There are also commands to manage the instsitutional repositories, for
  example to push new content after its creation by an institute.


### Misc.

* We don't use the 'Affiliate Program', an opt-in scheme to potentially earn
  money based on site traffic.

* We rarely have need to use the official support from WebFaction (see
  the 'Support' tab in the UI). MG [as of a Telco on 13/02/20] has only had
  use the support services ~4 times.
