eZ JSCore LS for Exponential
============================

ezjscore is the script and style backbone of Exponential: it packs your
JavaScript and CSS into single cached files, loads jQuery, and lets JavaScript
call PHP functions on the server through
one simple endpoint.

**1.4.0 brings jQuery 4.** `ezjsc::jquery` loads jQuery 4.0.0 together with
jQuery Migrate 4.0.2, and `ezjsc::jqueryUI` loads jQuery UI 1.14.2. Older jQuery
code keeps working, and the jQuery 3 files are still shipped for templates that
name them directly.

This is the same ezjscore that Exponential 6 ships in its kernel, published here
so it can be installed, followed and contributed to on its own.

What it gives you
-----------------

| | |
|---|---|
| **Packing** | `{ezscript_require( array( 'my.js', 'other.js' ) )}` and `{ezcss_require( 'my.css' )}` collect files for the page head; the pagelayout writes them as one packed, minified, cached file each. `ezscript()` / `ezcss()` write them in place, `ezscript_load()` / `ezcss_load()` print what was collected, `ezscriptfiles()` / `ezcssfiles()` list the files |
| **Libraries** | packer keys `ezjsc::jquery`, `ezjsc::jqueryUI`, `ezjsc::jqueryio`, local copies or a CDN (`LoadFromCDN`); YUI and its keys are removed as of 1.5.0 |
| **Server calls** | PHP functions JavaScript can call through `ezjscore/call/<group>::<function>::<arg>…`, answering JSON, XML or text; groups and permissions in `ezjscore.ini` `[ezjscServer_<group>]`. Ships `ezjsc` (time, search), `ezjscnode` (subtree, load, priorities), `ezjsctemplate` (render a template), `ezpublishingqueue` (asynchronous publishing status), `ezajaxuploader` |
| **Encoding** | template operators `json_encode`, `xml_encode`, `node_encode` |
| **Access checks** | `has_access_to_limitation` in templates |

What is new in 1.4.0
--------------------

- **jQuery 4.0.0** for `ezjsc::jquery`, followed by **jQuery Migrate 4.0.2**:
  `jquery()` loads the new `jqueryMigrate` file when it is set. The quiet
  Migrate build is the default. It restores what jQuery 4 removed so older code
  keeps working, without filling the console. For a list of every old call your
  pages make, set `LocalScripts[jqueryMigrate]=jquery-migrate-4.0.2.js`, the
  reporting build. Empty it to load jQuery 4 alone.
- **jQuery UI 1.14.2** for `ezjsc::jqueryUI`, which supports jQuery 4. The
  previous jQuery UI was 1.10.3, from 2013.
- **The CDN entries name the same releases** as the local files. They still
  pointed at jQuery 1.10.2 and jQuery UI 1.10.3, so `LoadFromCDN=enabled` loaded
  far older libraries than the local copies.
- `extension.xml` and the new `ezinfo.php` carry the version and list the
  bundled libraries.

The jQuery 3.7.1, Migrate 3.4.1 and jQuery UI 1.10.3 files stay in
`design/standard/javascript/`, for templates that name them directly.

Install
-------

**With Exponential 6** there is nothing to install: ezjscore is part of the
kernel, in `extension/ezjscore`.

**As a package** (into an installation's `extension/` directory):

```sh
composer require se7enxweb/ezjscore:^1.4
```

If your Composer does not find the package, add this repository to your
project's `composer.json` once and run the command again:

```json
"repositories": [ { "type": "vcs", "url": "https://github.com/se7enxweb/ezjscore" } ]
```

Then make sure it is active and refresh:

```ini
# settings/override/site.ini.append.php
[ExtensionSettings]
ActiveExtensions[]=ezjscore
```

```sh
php bin/php/ezpgenerateautoloads.php --extension
php bin/php/ezcache.php --clear-all
```

When you clear the packer cache (`--clear-id=ezjscore-packer`), clear the
template block cache with it (`--clear-id=template-block`): cached page heads
name the packed files, and they would otherwise point to files that no longer
exist.

Upgrading from jQuery 3
-----------------------

Most sites have nothing to do: Migrate keeps older calls working. Two calls
were removed before jQuery 4 and are not restored by Migrate, so code that
uses them fails:
- `.size()` (removed in jQuery 3): use `.length`;
- `$.browser` (removed in jQuery 1.9): use feature detection.

To find them:

```sh
grep -rnE "\.size\(\)|\\\$\.browser" extension/*/design design --include=*.js --include=*.tpl
```

The [jQuery 4 upgrade guide](https://jquery.com/upgrade-guide/4.0/) lists every
change. For a step-by-step guide to moving Exponential code from YUI and
jQuery 3 to jQuery 4, see
[Exponential UI's converting guide](https://github.com/se7enxweb/expui/blob/v1.0.0.0/doc/CONVERTING_YUI_to_EXPUI.md).

Works well with
---------------

[Exponential UI](https://github.com/se7enxweb/expui) (`expui`): jQuery 4 plus
the `Exp` API, which replaces YUI in Exponential feature by feature. Listed
before ezjscore in `ActiveExtensions`, it shares this jQuery 4 and switches on
the reporting Migrate build.

Documentation
-------------

- `doc/INSTALL` and `doc/FAQ`: installation and questions
- `settings/ezjscore.ini`: every setting, with its explanation
- `CHANGELOG`: the history

License
-------

GNU General Public License v2.0 or later (see `LICENSE`). Copyright eZ Systems
AS, and [7x](https://se7enx.com) for the Exponential versions. The bundled
libraries keep their own licences: jQuery, jQuery Migrate and jQuery UI (MIT,
`LICENSE-jquery*.txt`).
