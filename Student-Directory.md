# Student Directory

The Student Directory API provides access to information on students and staff at TJ, which is restricted based on each user's privacy settings, and the access privileges of the logged-in user. It also provides information on classes.

**All methods in this API require the user to be signed in to an Iodine account.** Even when signed in, the information available depends on the access privileges of the logged-in user.



## Info

The *Info* resource (XML) represents information about a student or staff member at TJ. Note that many of these fields will often not be provided, due to access restrictions.


### Properties

Many of the properties are from LDAP, which means they are sourced from the school administration and FCPS. They can be updated only by consulting the school administration. This has several consequences; for example, non-binary genders are not supported.


#### Name (LDAP)

Note that the web interface displays full names in the format "William (Bill) Jefferson Clinton", which is none of the options below. Wikipedia displays the same name as "William Jefferson 'Bill' Clinton".

- `sn` Surname/Last name (ex. `Clinton`)
- `givenname` Given name (ex. `William`)
- `middlename` Middle name (ex. `Jefferson`)
- `nickname` Nickname (ex. `Bill`)
- `cn` Common name (ex. `William Clinton`)
- `displayname` Display name (ex. `William Jefferson Clinton`)
- `title` Title (ex. `Mr.`)


#### Identity (LDAP)

Note that `tjhsstStudentId` denotes the user's FCPS ID, also known as the student ID (however it also exists for teachers). It is the username used to sign into the FCPS domain on Windows computers, or to sign into [fcpsschools.net](http://fcpsschools.net). For students it is a seven-digit numeric code. Normally, only your own ID is visible, but other users' IDs can be found by searching the fcpsschools.net directory [using the Directory API](https://developers.google.com/admin-sdk/directory/v1/guides/search-users) or by using the "Directory" tab in Google Contacts directly.

- `objectclass` (`tjhsstStudent`, `tjhsstTeacher`, ?)
- `iodineuid` Iodine/TJ username (ex. `awilliam`)
- `iodineuidnumber` Iodine UID number, not FCPS ID (ex. `8889`)
- `tjhsstStudentId` FCPS ID number
- `birthday` Date of birth (ex. `20000101`)
- `graduationyear` Year in which the user will graduate (ex. `2017`)
- `gender` Gender (`M`, `F`)
- `homephone` Home phone number (ex. `7035555555`)


#### Address (LDAP)

- `street` Street address (ex. `1600 Pennsylvania Ave NW`)
- `l` City (ex. `Washington`)
- `st` State (ex. `DC`)
- `postalcode` Postal/zip code (ex. `20500`)


#### Contact info (Iodine)

- `mobile` Cell phone number

For the following properties, it is possible to specify multiple values for each field. Normally, each tag contains text. With multiple values, each tag will contain child nodes, which will have tag names equivalent to the regular tag name without the last character. (This was probably intended to strip off an "s".)

Example (XML):

```xml
<!-- Single email address -->
<mail>example@example.com</mail>

<!-- Multiple email addresses -->
<mail>
  <mai>one@example.com</mai>
  <mai>two@example.com</mai>
</mail>
```

Example (JSON):

```js
// Single email address
"mail": "one@example.com"

// Multiple email addresses
"mail": [
    "one@example.com",
    "two@example.com"
]
```


- `telephonenumber` Other phone number
- `mail` Email address
- `webpage` Webpage
- Usernames for messaging services:
  - `aim`
  - `yahoo`
  - `msn`
  - `icq`
  - `googletalk`
  - `jabber`
  - `xfire`
  - `skype`


#### Permissions

Permissions indicate the user's privacy settings. For students, there are two sets of permissions. One of them indicates what their parents have approved to be shown on Iodine (ex. `perm-showaddress`) and the other indicates what they have selected in Iodine's settings (ex. `perm-showaddress-self`). Both must be `TRUE` for the information to be shown. For staff members, only the latter settings exist.

Each of these is either `TRUE` or `FALSE`, if present.

- `perm-showaddress(-self)` Address; affects `street`, `l`, `st`, `postalcode` proerties
- `perm-showbirthday(-self)` Birthday; affects `birthday` property
- `perm-showeighth(-self)` 8th period schedule; affects Eighth API
- `perm-showlocker(-self)` Locker number; affects `locker` proeprty
- `perm-showmap(-self)`
- `perm-showpictures(-self)` Pictures; affects the `/pictures` endpoint
- `perm-showschedule(-self)` Enrolled classes; affects `enrolledclass` property and Student Directory API
- `perm-showtelephone(-self)` Home phone; affects `homephone` property


#### Other

- `locker` Presumably the user's locker number, if one is assigned (default `0`)
- `eighthoffice-comments` Comments the 8th period office has made about this user ;)
- `counselor` Iodine UID of the student's counselor
- `is_admin` Whether the user is a sysadmin (`TRUE` or not present)


#### Classes

All classes are inside the `enrolledclass` tag, which contains more `enrolledclass` tags, which contain comma-separated name/value pairs (presumably some sort of an LDAP reference). The only important one is `tjhsstSectionId`.

Example (XML):

```xml
<enrolledclass>
  <enrolledclass>tjhsstSectionId=740500-01,ou=schedule,dc=tjhsst,dc=edu</enrolledclass>
  <enrolledclass>tjhsstSectionId=001562-10,ou=schedule,dc=tjhsst,dc=edu</enrolledclass>
  <!-- and so on -->
</enrolledclass>
```

Example (JSON):

```js
"enrolledclass": [
    "tjhsstSectionId=740500-01,ou=schedule,dc=tjhsst,dc=edu",
    "tjhsstSectionId=001562-10,ou=schedule,dc=tjhsst,dc=edu",
    // ...and so on
]
```


- `tjhsstSectionId` Section ID of a *Section* resource



## Section

The *Section* resource (XML) identifies a section of a course. The terminology is confusing and inconsistent, so instead of the ambiguous term *Class*, these two terms will be used:

* **Course**: What students sign up to take. Each course comprises a specific set of subject material and curriculum that is shared between sections. Courses are listed in the [TJHSST Course Guide](https://fcps.tjhsst.edu/coursemgmt/courses/300/), along with their course ID numbers.
* **Section**: A specific instance of a course. Each section comprises a group of students who are taught together in the same period. Generally, the location and teacher are also the same. While students sign up for courses, the section that they are assigned is determined by scheduling constraints. Each section is identified by the course ID and a section number.

Each course is generally taught in multiple sections, by multiple teachers accross multiple periods. For example, let's say I'm taking Artificial Intelligence 1. The course ID, as seen on the course guide, is `319966`. There are [six sections](https://iodine.tjhsst.edu/studentdirectory/section/319966) of this course, taught by Mr. Stueben and Dr. Torbert during different periods. I am in Mr. Stueben's 6th period section, which has a section number of `04`. So the *Class* resource that I am associated with has a section ID of `31966-04`.

Note that the data provided by this API is not always accurate, and that the data may be fairly screwed up for certain courses, especially PE.


### Properties

Note that there are some differences between the properties of the *Section* resources returned in the body of the `class` method and those found elsewhere. **In particular, `numstudents` and `students` are only retrievable with `class`.**


#### Intrinsic to the course

- `classid` Course ID, identifies the course that this *Class* resource teaches (ex. `319966`)
- `name` An abbreviated title of the course (ex. `Artificial Intell1`)


#### Specific to each section

- `sectionid` Section ID, comprised of the course ID and the section number (ex. `319966-04`)
- `quarters` Quarters during which this section takes place
  - `quarter` (`1`, `2`, `3`, `4`)
- `room` The room where the section is located (ex. `200`)
- `period` Period during which the section takes place (ex. `6`)
- `teacher` An *Info* resource representing the primary teacher of the section
- `numstudents` Number of items in `students`, not total number of students enrolled *(not always available)*
- `students` The students enrolled in this class who have their schedule visible *(not always available)*
  - `student` An *Info* resource representing each student



## GET /ajax/studentdirectory/info{/uid}

Returns the *Info* resource corresponding to a specific user.

The result is a JSON representation of the *Info* resource. The XML tags that would be in the `info` tag are instead properties of the root JSON object.


#### Parameters

- `uid` (default: current user) The Iodine UID or UID number of the user to retrieve (ex. `awilliam`, `8889`)


#### Example Result

```js
{
    "objectclass": "tjhsstStudent",
    "iodineuid": "awilliam",
    "iodineuidnumber": "8889",
    "cn": "Angela William",
    "sn": "William",
    "header": "TRUE",
    "givenname": "Angela",
    "graduationyear": "2017",
    "mail": "ahamilto@tjhsst.edu",
    "style": "i3-light",
    "boxcolor": "0063ff",
    "mailentries": "-1",
    "perm-showaddress-self": "FALSE",
    "perm-showtelephone-self": "FALSE",
    "perm-showbirthday-self": "FALSE",
    "perm-showschedule-self": "FALSE",
    "perm-showeighth-self": "FALSE",
    "perm-showmap-self": "FALSE",
    "perm-showpictures-self": "FALSE",
    "perm-showlocker-self": "FALSE",
    "startpage": "news",
    "chrome": "TRUE",
    "eighthagreement": "TRUE",
    "allset": true,
    "__nulls": []
}
```



## GET /api/studentdirectory/info{/uid}

Returns the *Info* resource corresponding to a specific user.

The result is an XML representation of the *Info* resource inside of an `info` tag. This tag is a child of the root `studentdirectory` tag.


#### Parameters

- `uid` (default: current user) The Iodine UID or UID number of the user to retrieve (ex. `awilliam`, `8889`)


#### Example Result

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE studentdirectory [

<!ELEMENT studentdirectory (body,error,debug)>
<!ELEMENT body (#PCDATA)>
<!ELEMENT error (#PCDATA)>
<!ELEMENT debug (#PCDATA)>
]>
<studentdirectory>
  <info>
    <objectclass>tjhsstStudent</objectclass>
    <iodineuid>awilliam</iodineuid>
    <iodineuidnumber>8889</iodineuidnumber>
    <cn>Angela William</cn>
    <sn>William</sn>
    <header>TRUE</header>
    <givenname>Angela</givenname>
    <graduationyear>2017</graduationyear>
    <mail>ahamilto@tjhsst.edu</mail>
    <style>i3-light</style>
    <boxcolor>0063ff</boxcolor>
    <mailentries>-1</mailentries>
    <perm-showaddress-self>FALSE</perm-showaddress-self>
    <perm-showtelephone-self>FALSE</perm-showtelephone-self>
    <perm-showbirthday-self>FALSE</perm-showbirthday-self>
    <perm-showschedule-self>FALSE</perm-showschedule-self>
    <perm-showeighth-self>FALSE</perm-showeighth-self>
    <perm-showmap-self>FALSE</perm-showmap-self>
    <perm-showpictures-self>FALSE</perm-showpictures-self>
    <perm-showlocker-self>FALSE</perm-showlocker-self>
    <startpage>news</startpage>
    <chrome>TRUE</chrome>
    <eighthagreement>TRUE</eighthagreement>
    <allset>1</allset>
  </info>
  <error></error>
  <debug></debug>
</studentdirectory>
```



## GET /api/studentdirectory/classes{/uid}

Returns all *Section* resources corresponding to a specific user. In other words, this returns their class schedule.

As noted above, the *Section* resources returned from this method will NOT include the `num_students` or `students` fields.

The result is an XML representation of each *Section*, each of which is inside an `class` tag. All of them are children of the `classes` tag, which is a child of the root `studentdirectory` tag.


#### Parameters

- `uid` (default: current user) The Iodine UID or UID number of the user to retrieve (ex. `awilliam`, `8889`)


#### Example Result

Note that only two *Section* resources are shown; the rest have been omitted.

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE studentdirectory [

<!ELEMENT studentdirectory (body,error,debug)>
<!ELEMENT body (#PCDATA)>
<!ELEMENT error (#PCDATA)>
<!ELEMENT debug (#PCDATA)>
]>
<studentdirectory>
  <classes>
    <class>
      <sectionid>319966-04</sectionid>
      <classid>319966</classid>
      <quarters>
        <quarter>1</quarter>
        <quarter>2</quarter>
      </quarters>
      <room>200</room>
      <name>Artificial Intell1</name>
      <period>6</period>
      <teacher>
        <objectclass>tjhsstTeacher</objectclass>
        <cn>Michael Stueben</cn>
        <!-- Teacher details omitted -->
      </teacher>
    </class>
    <class>
      <sectionid>001562-10</sectionid>
      <classid>001562</classid>
      <quarters>
        <quarter>1</quarter>
        <quarter>2</quarter>
        <quarter>3</quarter>
        <quarter>4</quarter>
      </quarters>
      <room>000</room>
      <name>Homeroom Grade 10</name>
      <period>8</period>
      <teacher>
        <objectclass>tjhsstTeacher</objectclass>
        <cn>Joan Burch</cn>
        <!-- Teacher details omitted -->
      </teacher>
    </class>
    <!-- More sections omitted -->

    <error>Warning: Undefined variable: v&#13;
&lt;br /&gt;Error number: 8&#13;
&lt;br /&gt;File: &lt;a href='https://iodine.tjhsst.edu/highlight/368/var/www/iodine/modules/studentdirectory/studentdirectory.mod.php5#368'&gt;/var/www/iodine/modules/studentdirectory/studentdirectory.mod.php5:368&lt;/a&gt;</error>
    <debug></debug>
  </classes>
</studentdirectory>
```



## GET /api/studentdirectory/class/{sid}

Returns the *Section* resource corresponding to a section ID.

The result is an XML representation of the *Section* resource inside of an `class` tag. This tag is a child of the root `studentdirectory` tag.


#### Parameters

- `sid` (required) Section ID of the section to retrieve (ex. `319966-04`)


#### Example Result

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE studentdirectory [

<!ELEMENT studentdirectory (body,error,debug)>
<!ELEMENT body (#PCDATA)>
<!ELEMENT error (#PCDATA)>
<!ELEMENT debug (#PCDATA)>
]>
<studentdirectory>
  <class>
    <numstudents>12</numstudents>
    <sectionid>319966-04</sectionid>
    <classid>319966</classid>
    <quarters>
      <quarter>1</quarter>
      <quarter>2</quarter>
    </quarters>
    <room>200</room>
    <name>Artificial Intell1</name>
    <period>6</period>
    <teacher>
      <objectclass>tjhsstTeacher</objectclass>
      <cn>Michael Stueben</cn>
      <!-- Teacher details omitted -->
    </teacher>
    <students>
      <student><!-- Student details omitted --></student>
      <student><!-- Student details omitted --></student>
      <student><!-- Student details omitted --></student>
      <student><!-- Student details omitted --></student>
      <student><!-- Student details omitted --></student>
      <student><!-- Student details omitted --></student>
      <student><!-- Student details omitted --></student>
      <student><!-- Student details omitted --></student>
      <student><!-- Student details omitted --></student>
      <student><!-- Student details omitted --></student>
      <student><!-- Student details omitted --></student>
      <student><!-- Student details omitted --></student>
    </students>
    <error></error>
    <debug></debug>
  </class>
</studentdirectory>
```



## GET /api/studentdirectory/search{?q}

Returns *Info* resources matching a given query. Only fields that are accessible by the current user through the `info` method are searched.

The result is an XML representation of each *Info*, each of which is inside an `result` tag. All of them are children of the `search` tag, which is a child of the root `studentdirectory` tag. The search tag has a parameter `q` whose value is the provided query.


#### Parameters

- `q` (required) Query to search for


#### Example Result

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE studentdirectory [

<!ELEMENT studentdirectory (body,error,debug)>
<!ELEMENT body (#PCDATA)>
<!ELEMENT error (#PCDATA)>
<!ELEMENT debug (#PCDATA)>
]>
<studentdirectory>
  <search q="awilliam">
    <result>
      <objectclass>tjhsstStudent</objectclass>
      <iodineuid>awilliam</iodineuid>
      <iodineuidnumber>8889</iodineuidnumber>
      <cn>Angela William</cn>
      <sn>William</sn>
      <header>TRUE</header>
      <givenname>Angela</givenname>
      <graduationyear>2017</graduationyear>
      <mail>ahamilto@tjhsst.edu</mail>
      <style>i3-light</style>
      <boxcolor>0063ff</boxcolor>
      <mailentries>-1</mailentries>
      <perm-showaddress-self>FALSE</perm-showaddress-self>
      <perm-showtelephone-self>FALSE</perm-showtelephone-self>
      <perm-showbirthday-self>FALSE</perm-showbirthday-self>
      <perm-showschedule-self>FALSE</perm-showschedule-self>
      <perm-showeighth-self>FALSE</perm-showeighth-self>
      <perm-showmap-self>FALSE</perm-showmap-self>
      <perm-showpictures-self>FALSE</perm-showpictures-self>
      <perm-showlocker-self>FALSE</perm-showlocker-self>
      <startpage>news</startpage>
      <chrome>TRUE</chrome>
      <eighthagreement>TRUE</eighthagreement>
      <allset>1</allset>
    </result>
    <result>
      <!-- Student details omitted -->
    </result>
    <error></error>
    <debug></debug>
  </search>
</studentdirectory>
```



## GET /api/studentdirectory/pictures{/uid}

Returns an XML file with URLs to pictures corresponding to a specific user.

The URLs in the XML file are of the form `/pictures/{uri}/{type}`, linking to pictures. The picture is always 172 pixels in width, but its height varies around 210-230 pixels. If the picture is available, it will be in JPEG format. If the picture is not available because of access restrictions or because it does not exist, the picture will be a PNG with the words "Picture Not Available".

The result is an XML file with a `pictures` tag, which is the child of the root `studentdirectory` tag. Inside of that are the `main`, `freshman`, `sophomore`, `junior`, and `senior` tags, each of which contains a URL linking to a picture. The image linked to by the `main` tag is determined by the user's Iodine settings.


#### Parameters

- `uid` (default: current user) The Iodine UID or UID number of the user to retrieve (ex. `awilliam`, `8889`)


#### Example Result

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE studentdirectory [

<!ELEMENT studentdirectory (body,error,debug)>
<!ELEMENT body (#PCDATA)>
<!ELEMENT error (#PCDATA)>
<!ELEMENT debug (#PCDATA)>
]>
<studentdirectory>
  <pictures>
    <main>https://iodine.tjhsst.edu/pictures/8889</main>
    <freshman>https://iodine.tjhsst.edu/pictures/8889/freshmanPhoto</freshman>
    <sophomore>https://iodine.tjhsst.edu/pictures/8889/sophomorePhoto</sophomore>
    <junior>https://iodine.tjhsst.edu/pictures/8889/juniorPhoto</junior>
    <senior>https://iodine.tjhsst.edu/pictures/8889/seniorPhoto</senior>
    <error></error>
    <debug></debug>
  </pictures>
</studentdirectory>
```
