# News

The News API provides access news items that are posted on Iodine. It corresponds to the *news* module in Iodine.

**All methods in this API require the user to be signed in to an Iodine account,** with the exception of [[/feeds/rss|Iodine-API-Documentation#get-feedsrss]], the publicly available RSS feed.

Most of the source code pertaining to this module can be found in [news.mod.php5](https://github.com/tjcsl/iodine/blob/master/modules/news/news.mod.php5) and [newsitem.class.php5](https://github.com/tjcsl/iodine/blob/master/modules/news/newsitem.class.php5).



## Post

The *Post* resource (XML) represents a single news article.

Note: there are several issues with liking articles; namely, **the `liked` property may not change**. See the like method below for details.


### Properties

- `id` ID of the post
- `title` Post title; may contain HTML
- `text` Post body as raw HTML; within a CDATA block in XML
- `posted` Time at which the post was originally posted (ex. `2015-01-27 15:24:36`)
- `expire` Time at which the post will expire
- `groups` The groups that can view the news article
  - `group`
    - `gid`
    - `name`
- `likedcount` Number of users that have liked this post
- Boolean values (`0` or `1`)
  - `liked` Whether the current user has liked this post
  - `read` Whether the current user has marked the article as read
  - `visible` If false, the post is hidden
  - `public` Whether the post should be posted publicly, for example on Twitter



## GET /api/news/show/{id}

Retrieves the *Post* with the given ID.

The result is an XML representation of the *Post* inside of a `post` tag. This tag is a child of the root `news` tag.


#### Parameters

- `id` (required) ID of the requested *Post* resource (ex. `2757`)


#### Example Result

```xml
<news>
    <post>
        <read>0</read>
        <posted>2015-01-27 15:24:36</posted>
        <expire>3000-01-01 00:00:00</expire>
        <visible>1</visible>
        <public>1</public>
        <groups>
            <group>
                <gid>1</gid>
                <name>all</name>
            </group>
        </groups>
        <id>2757</id>
        <title>Adding Value - Leadership Lunch</title>
        <text>
            <![CDATA[
                You don't follow a leader unless they add value to your life?  Why? <div><br /></div><div>Join our Leadership Lunch discussion in T24 tomorrow at 11:40am.</div><div><br /></div><div>#leadnow</div>
            ]]>
        </text>
        <liked>0</liked>
        <likecount>1</likecount>
    </post>
    <error/>
    <debug/>
</news>
```


## GET /api/news/list{?start,end}

Returns a list of *Post* resources between two positions in the overall feed.

Posts that are expired are not listed. Additionally, if the logged-in user is not in any of the posts' groups, the post will not be listed. However, articles marked as read are still returned.

The result is an XML representation of each *Post*, each of which is inside a `post` tag. All of them are children of the root `news` tag.


#### Parameters

The posts' positions are sorted by time, with the most recent post having position 0.
- `start` (default `0`) Start position in feed
- `end` (default `10`) End position in feed


#### Example Result

```xml
<news>
    <post>
        <read>0</read>
        <posted>2015-01-27 15:28:18</posted>
        <expire>3000-01-01 00:00:00</expire>
        <visible>1</visible>
        <public>1</public>
        <groups>
            <group>
                <gid>1</gid>
                <name>all</name>
            </group>
        </groups>
        <id>2758</id>
        <title>Field Trip Thursday?</title>
        <text>
            <![CDATA[
                <p class="MsoNormal"><span style="font-size:11pt;font-family:Calibri, 'sans-serif';color:#1F497D;">Students taking a field trip on Thursday, January 29, should report to their 4<sup>th</sup> period teacher during 8<sup>th</sup> period on January 28 to take the make-up exam missed on January 27.  Do not report to the regularly scheduled 8<sup>th</sup> period unless you plan to be in attendance all day on January 29 or do not have a 4<sup>th</sup> period exam.</span></p><p></p>
            ]]>
        </text>
        <liked>0</liked>
        <likecount>0</likecount>
    </post>
    <post>
        <read>0</read>
        <posted>2015-01-27 15:24:36</posted>
        <expire>3000-01-01 00:00:00</expire>
        <visible>1</visible>
        <public>1</public>
        <groups>
            <group>
                <gid>1</gid>
                <name>all</name>
            </group>
        </groups>
        <id>2757</id>
        <title>Adding Value - Leadership Lunch</title>
        <text>
            <![CDATA[
                You don't follow a leader unless they add value to your life?  Why? <div><br /></div><div>Join our Leadership Lunch discussion in T24 tomorrow at 11:40am.</div><div><br /></div><div>#leadnow</div>
            ]]>
        </text>
        <liked>0</liked>
        <likecount>1</likecount>
    </post>
    <error/>
    <debug/>
</news>
```



## GET /news/like/{id}

Either "likes" or "unlikes" a *Post*, modifying the `liked` attribute of the *Post* for the logged-in user. The action that occurs is determined by the current state. For example, if `liked` is currently `0`, this will change it to `1`, and vice versa.

Based on basic testing, it appears that **the `liked` attribute does not change** after liking a post, even though the total `likedcount` changes. This has only begun occurring recently (it's probably a bug in Iodine). In addition, even if it were possible to tell whether an article has been liked, this method is inherently subject to race conditions, so do not rely upon its results.

The result is the Iodine news page, located at [/news](https://iodine.tjhsst.edu/news/). Note that the requested article may not even be in the result, so it should be ignored.


#### Parameters

- `id` (required) The id representing the *Post* to like or unlike



## GET /news/read/{id}

Marks a *Post* as read, setting the `read` attribute of the *Post* to `1` for the logged-in user.

The result is the Iodine news page, located at [/news](https://iodine.tjhsst.edu/news/). Note that the requested article may not even be in the result, so it should be ignored.


#### Parameters

- `id` (required) The id representing the *Post* to mark as read



## GET /news/unread/{id}

Marks a *Post* as unread, setting the `read` attribute of the *Post* to `0` for the logged-in user.

The result is the Iodine news page, located at [/news](https://iodine.tjhsst.edu/news/). Note that the requested article may not even be in the result, so it should be ignored.


#### Parameters

- `id` (required) The id representing the *Post* to mark as unread



## GET /feeds/rss

The [RSS feed](https://iodine.tjhsst.edu/feeds/rss) provides public access to Iodine news posts, which can be useful if authentication is not possible. (Snippets are also posted by the [Intranet Twitter account](https://twitter.com/TJIntranet).) However, there are some differences: it is not updated on every request; only public posts are shown; expired posts are not removed; and not as many details are provided.

There is also an [Atom feed](https://iodine.tjhsst.edu/feeds/atom). However, the post body, which is embedded directly and incorrectly labeled as XHTML, is sometimes invalid (thanks Microsoft Office). Trying to parse the Atom feed  will also parse the post body, and will result in parse exceptions. The RSS feed is preferable, as it escapes HTML characters in the post body, rather than trying to embed it directly. When it is parsed, the raw HTML of the post body is produced.

The result is an RSS feed. The structure of this feed differs from that of the standard News API methods.
