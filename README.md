This is an attempt to document various API endpoints that Iodine provides, in the hope that it may be useful to other developers. Note that this documentation is unofficial; that is, not sanctioned by anyone. All paths are relative to https://iodine.tjhsst.edu/, or to the path to an Iodine installation.

Creating this page was quite an adventure; alas, Iodine is soon to be replaced by its successor, [Ion](https://github.com/tjcsl/ion). Here's to hoping that Ion will have more complete and less hacky API coverage.

# Table of Contents
* [[Authentication|Iodine-API-Documentation#authentication]]
  * [[Authentication error|Iodine-API-Documentation#authentication-error]]
  * [[Login (POST /api/)|Iodine-API-Documentation#login-post-api]]
  * [[Logout (GET /logout)|Iodine-API-Documentation#logout-get-logout]]
* [[Day Schedule|Iodine-API-Documentation#day-schedule]]
  * [[Block|Iodine-API-Documentation#block]]
  * [[Schedule|Iodine-API-Documentation#schedule]]
  * [[GET /ajax/dayschedule/json{?date}|Iodine-API-Documentation#get-ajaxdayschedulejsondate]]
  * [[GET /ajax/dayschedule/json_exp{?start,end}|Iodine-API-Documentation#get-ajaxdayschedulejson_expstartend]]
  * GET /api/dayschedule{?date}
* [[Eighth|Iodine-API-Documentation#eighth]]
  * [[Block|Iodine-API-Documentation#block-1]]
  * [[Activity|Iodine-API-Documentation#activity]]
  * [[GET /api/eighth/list_blocks{/uid}{?start_date,daysforward}|Iodine-API-Documentation#get-apieighthlist_blocksuidstart_datedaysforward]]
  * GET /api/eighth/get_block/{bid}{/uid}
  * GET /api/eighth/list_activities/{bid}
  * [[GET /api/eighth/get_activity/{aid}|Iodine-API-Documentation#get-apieighthget_activityaid]]
  * POST /api/eighth/signup_activity
  * GET /api/eighth/history{/uid}
  * GET /api/eighth/mostoften{/uid}
  * GET /api/eighth/absences{/uid}{?start_date}
  * GET /eighth/vcp_schedule/favorite/uid/{aid}/bids/{bid}
* [[News|Iodine-API-Documentation#news]]
  * [[Post|Iodine-API-Documentation#post]]
  * [[GET /api/news/show/{id}|Iodine-API-Documentation#get-apinewsshowid]]
  * [[GET /api/news/list{?start,end}|Iodine-API-Documentation#get-apinewsliststartend]]
  * [[GET /news/like/{id}|Iodine-API-Documentation#get-newslikeid]]
  * [[GET /news/read/{id}|Iodine-API-Documentation#get-newsreadid]]
  * [[GET /news/unread/{id}|Iodine-API-Documentation#get-newsunreadid]]
  * GET /cliodine/news/old
  * GET /cliodine/news/archived
  * [[GET /feeds/rss|Iodine-API-Documentation#get-feedsrss]]
* [[Student Directory|Iodine-API-Documentation#student-directory]]
  * [[Info|Iodine-API-Documentation#info]]
  * [[GET /api/studentdirectory/info{/uid}|Iodine-API-Documentation#get-apistudentdirectoryinfouid]]
  * [[GET /api/studentdirectory/classes{/uid}|Iodine-API-Documentation#get-apistudentdirectoryclassesuid]]
  * [[GET /api/studentdirectory/class/{sid}|Iodine-API-Documentation#get-apistudentdirectoryclasssid]]
  * [[GET /api/studentdirectory/search{?q}|Iodine-API-Documentation#get-apistudentdirectorysearchq]]
  * [[GET /api/studentdirectory/pictures{/uid}|Iodine-API-Documentation#get-apistudentdirectorypicturesuid]]
