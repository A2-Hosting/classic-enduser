Release Notes for 1.0
=====================

Omeka 1.0 was released on June 2, 2009.

What's New in Omeka 1.0?
---------------------------------------------------------------

-   UI Improvements
    -   File metadata page on public themes
    -   Autocomplete for tags
    -   Pagination, filtering and sorting on the users admin panel
    -   Upgrade notification when new version is released
    -   'Remember Me' option when logging in
    -   JSONP now available for JSON outputs

-   Compatibility with 0.10
    -   Minor changes to config.ini
    -   Removed many deprecated helper functions
    -   Changed Dublin Core form inputs

-   API improvements
    -   Plugin API
        -   New plugin API functions for item/file/collection ingest:  `insert_item(), update_item(), insert_files_for_item(), insert_collection(), insert_files_for_item(), insert_element_set()`
-   Theme API
    -   Additional options/arguments for display files including built in display of icons for specific file types
    -   New helpers for file metadata page: `item_file(), show_file_metadata(), link_to_file_metadata().`
    -   Additional options for `show_item_metadata()`
    -   Additional argument for`item()`
    -   Additional argument for `select_item_type(), select_item_type_elements(), select_element(), select_user(), select_entity()`
-   Miscellaneous
    -   release\_object()  to help solve memory leak issues
-   Security fixes
    -   Command Injection with ImageMagick
    -   Default JSON outputs-   Internal changes
    -   Overhaul of File model
    -   Upgraded to Zend Framework 1.7.5
-   List of bug fixes

UI Improvements
--------------------------------------------------------

-   485, adds a file metadata page that will display on every public theme. This displays Dublin Core and other element metadata for the files that are attached to items. This can be accessed at the following relative URL in any Omeka installation: 'files/show/id', where ID is the unique numeric ID for the file (similar to items). See Theme API improvements for more details.
-   352, adds pagination capabilities to table classes and controllers. Omeka admin panel now shows pagination for lists of collections and users. Plugins can also use these improvements to implement pagination in a clean and simple fashion.
- 65, implements autocomplete for adding tags via the admin interface.
- 489, adds filtering/sorting criteria for users. Site administrators can now sort by username, first/last name, institution name, role, status (active/inactive). Can filter by role or status.
- 459, adds an upgrade notification to the admin dashboard to tell users when a new version of Omeka is available (with a corresponding helper function, `get_latest_omeka_version()`
-   158, enables JSONP for auto-serialized JSON requests. The javascript callback can be provided via the optional 'callback' parameter for any JSON request (output=json). JSONP responses are given the 'text/javascript' MIME type. This is different from the regular JSON MIME type ('application/json').
-   154, adds a 'Remember Me' option to the login form. This will maintain a user's session for 2 weeks from the moment of login. If 'Remember' is not checked, the session will expire when the browser session terminates.
    -   This also adds a 'session.name' field to the config.ini file and removes the pre-existing 'login.expire' setting. See Compatibility Issues for more details. If session name is not provided in the config file, the default session name will be automatically generated from the WEB_ROOT of the installation. A common use case for customizing the session name is so that session data could be shared across Omeka installations or with other applications running on the same server.
    -   Also added an 'initializeSession' phase to Omeka_Core, so that the session name could be properly configured prior to requests.

Compatibility issues with 0.10
--------------------------------------------------------------

-   'login.expire' setting was removed from the config.ini file. If you rely on this setting for determining the length of user sessions, tell your users instead to check 'Remember Me' when logging in through the admin. That is the same as setting login.expire to 2 weeks, and having the option of not extending the session is more secure on public computers.
-   Many deprecated helper functions have been removed from the 1.0 release of Omeka. Most of these were non-working as of 0.10, but some may or may not have functioned correctly. In the interests of clarifying the API, these functions will not be available as of 1.0. Using any of these functions within themes or plugins will now cause a fatal error:
`       generate_url($options, $name)
       dublin_core($type) {
       metatext_form($item, $input="textarea",$metafields=null)
       items_rss_header()
       get_month($date)
       get_day($date)
       get_year($date)
       item_metadata($item, $field, $escape=true)
       type($id=null)
       types(array $params = array())
       tags(array $params = array())
       items(array $params = array())
       users(array $params = array())
       institutions(array $params = array())
       metafields(array $params = array())
       people(array $params = array())
       has_collection($item, $name=null) {
       has_files($item) {
       item_types()
       collections(array $params = array())
       entities(array $params = array())
       has_collectors($collection) {
       has_tags($item, array $tags=array()) {
       has_thumbnail($item) {
       has_type($item, $name=null) {
       display_empty($val, $alternative="[Empty]") {
       get_base_url($use_relative_uri=false)
       is_current($link, $req = null) {
       link_to_thumbnail($item, $props=array(), $action='show', $random=false)
       link_to_fullsize($item, $props=array(), $action='show', $random=false)
       link_to_square_thumbnail($item, $props=array(), $action='show', $random=false)
       _link_to_archive_image($item, $props=array(), $action='show', $random=false, $imageType = 'thumbnail')
       items_rss_uri($params=array())
       item_permalink_url($item)
       define_metafield($name, $description, $type=null)
       add_theme_pages($dir, $theme='both')
       add_controllers($dir='controllers')
       add_data_feed($format, $options=array())
       add_navigation($text, $link, $type='main', $permissions=null)`
       

-   3572 All Dublin Core elements on the items form are now plain text. The 'Language' and 'Date' elements are the ones affected by this change. The logic behind this change is that the previous form inputs (drop menu for language and YYYY/MM/DD date field) did not allow the full range of data entry that is implied by unqualified Dublin Core. In certain situations, data ingested from other systems may not have been possible to edit using Omeka. For example, if a date field containing 'May 10, 1932' was not converted to 1932-05-10 when ingested into the Omeka database, it would not be editable using the Omeka administrative interface.
-   The value in 'path_to_convert' is changed from previous versions. This will not affect users with an up-to-date database. See Security Fixes for more details.

API Improvements
-----------------------------------------------------

### Plugin API

-   Some helper functions have been added to simplify the task of ingesting data into Omeka from other systems. For more information about these helpers, see the codex or inline code documentation:
    -   `insert_item(\$metadata = array(), $elementTexts = array(), $fileMetadata = array()) 
    -   update\_item(\$item, \$metadata = array(), \$elementTexts = array(), \$fileMetadata = array())
    -   insert\_collection(\$metadata = array())
    -  488, `insert\_element\_set(\$elementSet, array \$elements
        = array())`
    -  648, `insert\_files\_for\_item(\$item, \$transferStrategy,
        \$files, \$options = array())`
-   3710 release_object($var): Can be used in plugins to avoid memory leaks. This encapsulates a workaround for PHP's built in     garbage collection, which causes memory leaks when objects contain circular references. It does this by explicitly destroying an object instead of relying on PHP's garbage collection. Currently this only has a noticeable impact with record instances (classes extending Omeka_Record), though it may be extended to other classes in the future as needed.
    -   Caveats: This will destroy an object before automatic garbage collection would normally take over, so there may still be broken references to that object elsewhere in the application. This could cause problems when two plugins conflict with one another. For example, one plugin could store a reference to an Item record for future use, and a second plugin might release that record using release\_object(). In this case, the first plugin would cease to work because of the second plugin's behavior. For this reason, release\_object() should be avoided unless a memory leak crashes the plugin or otherwise interferes with its function.
    -   Example: `$item = insert_item(array('public'=>true), array()); release_object($item);`

### <span id="Theme_API" class="mw-headline"> Theme API </span>

-   \#479, a series of enhancements to
    [display\_files()](Theme_API/display_files.html "Theme API/display files"):
    -   adds 'linkAttributes' and 'imgAttributes' options
        to display\_files(). These are arrays that represent the XHTML
        attributes in the &lt;a&gt; and &lt;img&gt; tags, if applicable.
    -   adds support for displaying icons using display\_files(). The
        simplest way to receive high quality essay papers is to come to
        the greatest [essay writing
        services](http://essaysprofessors.com). I really know that some
        academic writing organizations provide reasonable prices. Users
        can now pass the 'icons' option as a keyed array of MIME types
        and paths to the respective icons. Here is a usage example from
        the code:
    -   changes the 3rd argument of display files from a string (class
        attribute for the wrapping div) to an array (all attributes for
        the wrapping div). NOTE: This will break themes that relied on
        the previous behavior, but is easy to fix.
    -   the div that wraps each file now contains a class corresponding
        to the hyphenated MIME type of the file, i.e.
        <div class="image-jpeg">

        </div>

    -   learned 'linkToMetadata' option for the 'image', 'icon' and
        default callbacks. This is a boolean value that allows the theme
        writer to choose whether or not to link to the file's
        metadata page. This overrides the 'linkToFile' option and
        defaults to false.
    -   Here is an example of some of the new changes to
        display\_files():

 

           echo display_files_for_item(array(
               'showFilename'=>false,
               'linkToFile'=>false,
               'linkAttributes'=>array('rel'=>'lightbox'),
               'filenameAttributes'=>array('class'=>'random-class'),
               'imgAttributes'=>array('class'=>'icon-image'),
               'icons' => array('audio/mpeg'=>img('audio.gif'))));
          

-   \#641, enhancements to
    [show\_item\_metadata()](Theme_API/show_item_metadata.html "Theme API/show item metadata"):
    -   adds a 'show\_element\_sets' option. This allows a theme writer
        to choose to show specific element sets for an item. The theme
        writer can also choose to show all item type metadata for the
        item by putting 'Item Type Metadata' in the list (or the more
        specific name for certain item types, like 'Document Item
        Type Metadata').
    -   adds a 'return\_type' option. This option can be either 'array'
        or 'html' (default is 'html'). It allows a theme writer to
        retrieve the output from show\_item\_metadata() as an array to
        more easily enable custom markup.

 

-   [item()](Theme_API/item.html "Theme API/item"):
    -   Adds a fourth \$item argument to be able to display element
        metadata for an arbitrary Item record.
    -   Adds 'Permalink' metadata retrieval. Example: echo
        item('Permalink'); --&gt;
        <http://absolute.uri.to/omeka/items/show/1>

 

-   \#485,
    [item\_file()](Theme_API/item_file.html "Theme API/item file"),
    [show\_file\_metadata()](Theme_API/show_file_metadata.html "Theme API/show file metadata"),
    and the file metadata page:
    -   There is now a files/show page that can be displayed on
        every theme. By default, this page is located in the shared view
        scripts directory, application/views/scripts/files/show.php. If
        you would like to customize the markup for this file within your
        theme, copy this file from the shared directory to a files/
        directory in your public theme and modify accordingly.
    -   item\_file() helper has been added to facilitate display of
        metadata associated with files. It behaves similarly to item()
        when displaying Dublin Core or other element sets, though the
        arbitrary metadata associated with files is different than it is
        for files. The following metadata can be displayed for files:
        -   'Id'
        -   'Archive Filename'
        -   'Original Filename'
        -   'Size'
        -   'Mime Type'
        -   'Date Added'
        -   'Date Modified'
        -   'Authentication'
        -   'Mime Type OS'
        -   'File Type OS'
        -   'Uri'
        -   'Fullsize Uri'
        -   'Thumbnail Uri'
        -   'Square Thumbnail Uri'
        -   'Permalink'
    -   show\_file\_metadata() helper has been added to facilitate
        listing file metadata in the same vein as show\_item\_metadata()
        for items. The main difference between this
        and show\_item\_metadata() is that it can display the 'MIME Type
        Metadata' element set, which displays all the metadata that was
        programmatically extracted from the file when it was first
        uploaded to Omeka.
    -   [link\_to\_file\_metadata()](http://omeka.org/c/index.php?title=Theme_API/link_to_file_metadata&action=edit&redlink=1 "Theme API/link to file metadata (page does not exist)")
        helper has been added to facilitate linking to the new file
        metadata page within themes.

<span id="Security_Fixes" class="mw-headline"> Security Fixes </span>
---------------------------------------------------------------------

-   \[3706\] Fixed a security hole where the 'path\_to\_convert' admin
    setting could be replaced with an arbitrary command that would be
    executed when derivative images were created.
    -   The solution was to change the 'path\_to\_convert' setting so
        that it only represents the directory containing the ImageMagick
        binaries (not the binary itself). This should be fixed
        automatically when upgrading existing databases to 1.0.

 

-   \[3587\] Fixed a potential security hole involving JSON outputs. It
    was discovered that the default JSON output for users included the
    user's password hash. JSON outputs are protected by the ACL, so the
    password hash would only be available to super users, who can change
    passwords arbitrarily anyway. Still, this could have become a
    vulnerability in the future.
    -   The solution was to remove all default non-HTML outputs
        (including JSON) from controllers, thus forcing controller
        writers to enable alternative output formats explicitly using
        Zend Framework's ContextSwitch action helper. This reduces the
        chances of accidentally exposing sensitive data to the end user.
        The down side is that it may break plugins that rely on the
        existence of default 'JSON' outputs.

<span id="Internal_Changes" class="mw-headline"> Internal Changes </span>
-------------------------------------------------------------------------

-   Upgrades Zend Framework to 1.7.5.

 

-   \#651, completely overhauled the File model. Prior to 1.0, the File
    model handled everything to do with files, including uploading
    files, extracting metadata from the files, and creating
    derivative images. This splits those components into separate
    classes (Omeka\_File\_Ingest, Omeka\_File\_Info, and
    Omeka\_File\_Derivative\_Image, respectively). The File model is now
    represented by the following 4 classes:
    -   Omeka\_File\_Ingest -- In charge of ingesting files into Omeka,
        either through direct uploads (form POSTs), urls, or
        filesystem paths. This can also be extended to allow additional
        methods of file transfer by passing instances of classes that
        adhere to Omeka\_File\_Transfer\_Adapter\_Interface.
    -   Omeka\_File\_Info -- Extracts additional metadata from files.
        This extracts the same metadata as in 0.10. It has the most room
        for improvement of all the new file classes. Of particular
        interest in future development would be to revamp the algorithm
        that it uses to determine what metadata fields to populate.
    -   Omeka\_File\_Derivative\_Image -- Creates derivative images when
        given the path to the original file. Like Omeka\_File\_Info,
        this will run automatically when a file is inserted into the
        Omeka database. This is not meant to be invoked by
        plugin writers.

<span id="List_of_Bugs_Fixed_in_1.0" class="mw-headline"> List of Bugs Fixed in 1.0 </span>
-------------------------------------------------------------------------------------------

-   Fixes \#626 Images for items show have alt attribute for the
    display\_files function.

 

-   Fixes \#623 Make list tab on admin/items/browse appear selected when
    first going to the page.

 

-   Fixes \#622 Alert on forgot password page breaks layout, adds images
    for submit-small and submit-extran small submit inputs.

 

-   Fixes \#632, plugins should be able to add view scripts without
    adding controllers or a controllers directory.

 

-   Fixes \#630, class autoloader needs to check entire include path.

 

-   Fixes \#650, ElementSet::addElements() overwrites existing elements.
    Now, the order is relative to the other elements being added, which
    prevents overwriting.

 

-   Fixes \#634, Dublin Core elements should be text areas, adds
    a migration.

 

-   Fixes \#652, session namespace error using Zend Framework 1.7.4.

 

-   Fixes a potential security issue where the database object was
    partially serialized into JSON for Omeka\_Record objects.

 

-   Fixes a potential security hole involving default JSON outputs
    for controllers.
    -   This removes the default action contexts from
        Omeka\_Controller\_Action and adds them to each
        relevant controller. The default action contexts were 'json',
        'rss2' and 'dc' for 'browse' actions, and 'json' and 'dc' for
        'show' actions. This not make sense for most controllers, since
        RSS and Dublin Core are only for the ItemsController. It also
        opened up a security hole where sensitive data could have been
        passed to an attacker through the automatically serialized
        JSONP output.
    -   Note that this breaks backwards compatibility for any plugin controllers that extend off Omeka\_Controller\_Action, but only if those controllers rely on the default existence of the 'json' output for 'browse' and 'show' actions.
-   Refs \#485, fixes a filter so that a plugin writer can do
    array('Display', 'File', ...) to filter element text displayed for files.
-   Refs \#485, fixes a bug where filtering the element text so that it is blank should show the '\[no text\]' value for the     RecordMetadataList helper.

 

-   Refs \#485, fixes a bug in RecordMetadata helper where filters were
    not being properly invoked for arbitrary fields like 'Archive
    Filename' or 'Date Added'.

 

-   Fixes styling on install form, adds closing tag to dashboard.

 

-   Fixes \#613, query string vars not sent when redirecting users to
    the admin panel.

 

-   Fixes \#647, Item view helper needs to be able to be called
    inside filters.
    -   Now item() can be called inside a filter that normally runs
        inside of another call to item(), so the API can be used to
        filter one element based on the value of another element.

 

-   Fixes \#640 Themes admin panel layout busted with lots of themes
    in directory.

 

-   Fixes \#657 Make uninstall button on plugins list red.

 

-   Adds border above Save Item/Delete item on items/edit form, hides
    the Elements header on item-type/add if there are no element
    associated with an item-type.

 

-   Fixes \#653 Hide the 'remove' link from single dublin core fields on
    item/add form.

 

-   Fixes \#516 remove element button on item-types/edit doesn't prompt
    for confirm.

 

-   Fixes \#665 'Use HTML' checkbox on items form was broken in IE7.

 

-   Fixes \#661 Link to Omeka archive in installer broken on subdomains.

 

-   Fixed a bug in display\_files() where GIF and BMP were not
    displaying derivative images \[3722, 3724\].
