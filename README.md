[View on Help Site](https://agilitycms.zendesk.com/hc/en-us/articles/360025465452)

[View on GitHub](https://github.com/agility/ContentDebugger)
# Content Debugger for Agility Websites
Have you published content without error, and the content has not updated on your site yet? Sometimes it can be tricky to figure out why. This tool will help you identify what content has and has not synced to your web server and help troubleshoot the issue. The end goal is to determine whether the issue is based within the application or the sync process itself between Agility and the web server.

![Content Debugger](https://github.com/agility/ContentDebugger/raw/master/content-debbugger.png "Content Debugger")

## Possible causes for content not being updated:
- The web application has additional caching in place and the old, stale content is still cached. Using this tool, you can identify IF the expected content has indeed synced to the webserver. If so, and the content is still not visible on the website, this indicates a caching issue in the application.
- An error occurred during the sync from the Agility Content Server and your Webserver, and not error was reported. If the content has not synced to the web server and the item in question is published in the content manager, this would indicate that it never successfully synced the item. In this case, a support ticket should be opened with support@agilitycms.com for further inspection. You may also try re-syncing the item by publishing it again. However keep in mind that while this may solve the issue, it won't help in resolving the root cause and can often make that more difficult.
- You webserver is not set as an *active* syncing web server in Agility.


## Setup
You'll need to add some code to your MVC application to enable this. The easiest way to do this is to create a page template which executes the code we need.

1. Download/clone the repo (https://github.com/agility/ContentDebugger) and copy the content within the ContentDebugger.cshtml file.
2. Create a new Page Template in Agility called *Content Debugger Template* with no module zones and instruct it to use Inline Code. If using Inline Code, you do NOT need to do a deployment to your website :) If you are unable to use Inline Code, you may add this as a page template file in your MVC project.
3. Paste contents of the ContentDebugger.cshtml file as your Page Template code (either in Inline Code or the MVC project).
4. Create a new page using the new Page Template *Content Debugger Template* you just created and ensure it is published. You should ensure that you mark the page as Visible In Menu/Sitemap to *FALSE* so that this page is not accessed via a search engine. You should also consider adding a robots.txt rule to exclude it and perhaps make the page path unguessable so its not easily accessible by public users.
5. Browse to your page in a browser. For example, if you've created a page called *content-debugger* using the page template in the root of your website, then you should be able to browser to *https://{your-live-site}/content-debugger*.

## Using This Tool
In order to use this tool, you must know the page path on your website to access the debugger (see *Setup*). You should access this in live mode on the production web server. Keep in mind that your content debugger page needs to be published in order to work, this might expose this tool to public users.

Depending on your case, you need to decide what content you want to inspect. This could be content/module or page level. This debugger supports one or the other. 

If your issue is that a page is showing a 404 where it should be published, it is probably a good idea to inspect the page first. Otherwise, if the page is showing, but something is wrong with content then you should inspect the appropriate module or content item/list.

### Debugging Content/Modules
1. Enter the *referenceName* of the content you wish to lookup. This can be a reference name of a content list (New or Shared) or a reference name for a module on a specific page. You can find this by clicking on the *Settings* tab from within the Content Manager when you are on the content list/item or module details.
2. Enter the *languageCode* of the language you wish to inspect.
3. Optionally enter a *filter* to limit the results. For example, you could look for a specific item `ontentID = 123` or `Title like '%something%'`
3. Click *submit*.
5. Inspect the results. On the left-side you'll see the exposed **Content Repository Data**. This represents what content would be returned by an Agility Content Repository query `AgilityContentRepository<T>(referenceName)` and is representative of the data accessible by the MVC application.
On the right-side, you'll see the exposed **Raw Data from Content Files** with latest sync date. This represents the actual content of the synced content file. The content will differ between the two IF any content scheduling is enabled. For example, items that have a release date in the future will be shown in **Raw Data**, however it will NOT be available in the **Content Repository Data**. This is by design since scheduled content is syced to the web server and the filtered out of the result set in **Content Repository**.


### Debugging a Page
1. Enter the *pageID* of the page you wish to lookup. You can find this by clicking on the *Settings* tab from within the Content Manager when you are on the page you wish to inspect.
2. Enter the *languageCode* of the language you wish to inspect.
3. Click *submit*.
3. Inspect the results. On the left-side you'll see the exposed **Page via Content Repository**. This represents what content would be returned by an Agility Content Repository query  `Agility.Web.Data.GetPage(int pageID, string langCode` and is representative of the data accessible by the MVC application.
On the right-side, you'll see the exposed **Raw Data from Page Content File** with latest sync date. This represents the actual content of the synced content file for the page.  The content will differ between the two IF any content scheduling is enabled. For example, a page that has a release date in the future will be shown in **Raw Data from Page Content File**, however it will NOT be available in the **Page via Content Repository**. This is by design since scheduled content is syced to the web server and the filtered out of the results from queries using `Agility.Web` (including routing etc.).

### Are the Content Files and Content via the Content Repository Up-to-date?
If you find that all the appropriate content has synced, but you are still not seeing your update on live, then this indicates an issue in the application outside of *Agility.Web*. 

A common pitfall here is that the application has some additional caching that is keeping old content in cache. One quick way to test this is to reset the application pool on the web server and see if the new content shows. If it does, then this confirms there is additional caching layers keeping the content stale.

### Is the Synced Content still out of date?
If your published content is not yet synced to your web server, this should indicate a web sync issue. 

First, check to ensure that your production web server is set as *active* in *Settings* > *Syncing Web Servers*. If it is set to *deactivated* then this will not sync content (by design) until it is set to *active* or *maintenace mode*.

Next, check the Content Manager for any sync errors in *Reports* > *Details Sync Status*. 

If there are none, check the Web Logs for the production web server in *Settings* > *Web Logs* and look for any errors that could indicate a sync issue.

### Still Stuck?
Please contact support@agilitycms.com for help.








