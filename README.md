# iodine-api-docs

This is an attempt to document various API endpoints that Iodine provides, in the hope that it may be useful to other developers. Note that this documentation is unofficial; that is, not sanctioned by anyone. All paths are relative to https://iodine.tjhsst.edu/, or to the path to an Iodine installation.

Creating this page was quite an adventure; alas, Iodine is soon to be replaced by its successor, [Ion](https://github.com/tjcsl/ion). Here's to hoping that Ion will have more complete and less hacky API coverage.



# Table of Contents

* [Authentication](Authentication.md)
  * [Authentication error](Authentication.md#authentication-error)
  * [Login (POST /api/)](Authentication.md#login-post-api)
  * [Logout (GET /logout)](Authentication.md#logout-get-logout)


* [Day Schedule](Day-Schedule.md)
  * [Block](Day-Schedule.md#block)
  * [Schedule](Day-Schedule.md#schedule)
  * [GET /ajax/dayschedule/json{?date}](Day-Schedule.md#get-ajaxdayschedulejsondate)
  * [GET /ajax/dayschedule/json_exp{?start,end}](Day-Schedule.md#get-ajaxdayschedulejson_expstartend)
  * GET /api/dayschedule{?date}


* [Eighth](Eighth.md)
  * [Block](Eighth.md#block)
  * [Activity](Eighth.md#activity)
  * [GET /api/eighth/list_blocks{/uid}{?start_date,daysforward}](Eighth.md#get-apieighthlist_blocksuidstart_datedaysforward)
  * GET /api/eighth/get_block/{bid}{/uid}
  * GET /api/eighth/list_activities/{bid}
  * [GET /api/eighth/get_activity/{aid}](Eighth.md#get-apieighthget_activityaid)
  * POST /api/eighth/signup_activity
  * GET /api/eighth/history{/uid}
  * GET /api/eighth/mostoften{/uid}
  * GET /api/eighth/absences{/uid}{?start_date}
  * GET /eighth/vcp_schedule/favorite/uid/{aid}/bids/{bid}


* [News](News.md)
  * [Post](News.md#post)
  * [GET /api/news/show/{id}](News.md#get-apinewsshowid)
  * [GET /api/news/list{?start,end}](News.md#get-apinewsliststartend)
  * [GET /news/like/{id}](News.md#get-newslikeid)
  * [GET /news/read/{id}](News.md#get-newsreadid)
  * [GET /news/unread/{id}](News.md#get-newsunreadid)
  * GET /cliodine/news/old
  * GET /cliodine/news/archived
  * [GET /feeds/rss](News.md#get-feedsrss)


* [Student Directory](Student-Directory.md)
  * [Info](Student-Directory.md#info)
  * [GET /ajax/studentdirectory/info{/uid}](Student-Directory.md#get-ajaxstudentdirectoryinfouid)
  * [GET /api/studentdirectory/info{/uid}](Student-Directory.md#get-apistudentdirectoryinfouid)
  * [GET /api/studentdirectory/classes{/uid}](Student-Directory.md#get-apistudentdirectoryclassesuid)
  * [GET /api/studentdirectory/class/{sid}](Student-Directory.md#get-apistudentdirectoryclasssid)
  * [GET /api/studentdirectory/search{?q}](Student-Directory.md#get-apistudentdirectorysearchq)
  * [GET /api/studentdirectory/pictures{/uid}](Student-Directory.md#get-apistudentdirectorypicturesuid)
