# Eighth

The Eighth API allows users to view and sign up for 8th period activities. It corresponds to the *eighth* module in Iodine.

**All methods in this API require the user to be signed in to an Iodine account.**

Most of the source code pertaining to authentication can be found in [eighth.mod.php5](https://github.com/tjcsl/iodine/blob/master/modules/eighth/eighth.mod.php5), as well as in the rest of the files in [that folder](https://github.com/tjcsl/iodine/tree/master/modules/eighth).



## Block

The *Block* resource (XML) represents an 8th period block. This is a scheduled period during the school day during which users may sign up for 8th period activities to attend.

There are often multiple 8th period blocks in the same day, and they are distinguished by a letter, described in the property `type`. In common parlance, a block with type `B` may be referred to as `B-Block` or `8B`.

Activity signups for each block are closed every day at the end of lunch. At this point, the block is marked as `locked` and only the 8th period office may make further modifications.


### Properties

- `bid` The block's ID
- `date` The date when the block takes place
  - `str` Formatted as an ISO date (e.g. `2015-03-06`)
  - `iso` Formatted as an ISO date/time, with the time set to midnight (e.g. `2015-03-06T00:00:00-05:00`)
  - `disp` Formatted as a displayable string (e.g. `March 6, 2015`)
- `disp` A displayable string identifying the block (e.g. `Friday March 6th, 2015 Block A`)
- `type` A letter identifying which 8th period block of the day this is (e.g. `A`, `B`, ... `F`)
- `locked` Whether the block has been locked and no more changes may be made (`0` or `1`)
- `activity` An *Activity* resource representing the activity that the logged-in user has selected



## Activity

The *Activity* resource (XML) represents an 8th period activity, which users may sign up for. *Activity* resources are generally tied to a certain block. However, activities also have properties that are the same across all instances, such as their name. Activities are usually scheduled weeks in advance for a certain block and day of the week (e.g. every Friday B-Block).

Each *Activity* resource is associated with sponsors and rooms. The sponsors of an activity are the staff members responsible for students during that activity. The rooms are where an activity takes place, and must be reserved in advance. The rooms generally determine what capacity each activity has. These are represented by the attributes `block_sponsors` and `block_rooms`.


### Types of activities

"Restricted" activities are not open to all students. Usually, students are placed into one by the 8th period office at the request of a staff member. In this case, if they select another activity, they may not switch back. Sometimes, certain students are allowed to freely sign up for a restricted activity, but not automatically signed up. In this case, the web interface will not display the activity as restricted. However, **the API does not do this**, which [may be a bug](https://github.com/gengkev/thyroxine/issues/8).

"Sticky" activities are those that the 8th period office has placed you into and mandated that you attend. You may not switch to another activity. These include counselor meetings, school-wide activities, remediation, detention, or other special circumstances. Sticky activities are usually restricted, because otherwise you could accidentally sticky yourself into an activity. (There are a few exceptions, for example `40: Curriculum Fair - no 8th period activities (S)`, which can be verified with `get_activity`. The actual behavior is unclear: can you really sticky yourself into an activity?)

"Both blocks" activities take up two blocks, A and B, continuing through the break between those blocks. You must sign up for the activity both blocks, or neither. These include tests that are longer than 40 minutes, such as the Bio Olympiad, Chem Olympiad, or AMC exams.

"One a day" activities, in contrast, are usually popular. As a result, you may *not* sign up for more than one block of the activity.

"Presign" activities are also popular. Signup is restricted to approximately 24 hours in advance, so that those who sign up early do not have an advantage over others.

"Special" activities are not regularly scheduled, and students may find them interesting. They are listed at the top of the web interface, so that they can be easily noticed.


### Properties

Note that there are some differences between the properties of the *Activity* resources returned in the body of the `get_block` method, and those found elsewhere. These are due to different SQL queries being used when only one activity is queried, versus when multiple activities are queried. **In particular, `member_count` and `capacity` are only retrievable with `get_block`.**

There are also defunct properties that differ in availability. For example, `roomchanged` is also only available with `get_block`. However, that property is no longer used; instead, the comment field is changed (see block 2895, with three examples). Another one of those properties is `block_rooms_comma`, which is simply the room names provided by the `block_rooms` tag joined with commas. In contrast, `rooms` and `sponsors` are *not* retrievable with `get_block`, but they have been superseded by `block_rooms` and `block_sponsors`.

It's not clear what the `calendar` and `advertisement` properties are used for, if they are still used at all.


#### Intrinsic to the activity

These properties are tied to all instances of the activity; they are the same regardless of the block the activity is scheduled for. They depend on only the activity ID.

- `aid` Activity ID
- `name` Name
- `description` Description
- Boolean values (`0` or `1`)
  - `restricted` Whether signup is restricted to certain users
  - `presign` Whether signup is only allowed the day before the activity
  - `oneaday` Whether this activity may be selected only once a day
  - `bothblocks` Whether users must sign up for this activity during both A and B blocks
  - `sticky` Whether users are stuck to this activity (not permitted to select another activity)
  - `special` Whether this activity is a special activity
  - `calendar`


#### Specific to each scheduled activity

These properties are tied to instances of activities scheduled for a specific block. These properties depend on both an activity ID and a block ID.

- `comment` Comment about what's happening in this activity during this block
- `advertisement`
- `bid` The associated block ID
- `block_sponsors` Staff sponsors of this activity
  - `sponsor`
    - `sid` Sponsor ID
    - `fname` First name
    - `lname` Last name
    - `userid` Iodine UID number
- `block_rooms` Rooms in which this activity will take place
  - `room`
    - `rid` Room ID
    - `name` Name
    - `capacity` Capacity
- `member_count` How many people are signed up for this activity *(not always available)*
- `capacity` Maximum number of people that may sign up for this activity; `-1` if capacity is unlimited *(not always available)*
- Boolean values (`0` or `1`)
  - `cancelled` Whether this activity has been cancelled for this block
  - `attendancetaken` Whether this activity's attendance has been taken for this block



## GET /api/eighth/list_blocks{/uid}{?start_date,daysforward}

Retrieves all *Block* resources corresponding to the provided dates. The selected activities listed correspond to the provided UID.

As noted above, the *Activity* resources returned from this method will NOT include the `member_count` or `capacity` fields.

The result is an XML representation of each *Block*, each of which is inside an `block` tag. All of them are children of the `blocks` tag, which is a child of the root `eighth` tag.


#### Parameters

- `uid` (default: current user) The Iodine UID or UID number of the user to obtain the selected activities of; this will fail if the logged-in user does not have permission to view that user's 8th period schedule
- `start_date` (default: date of next block) The date to begin retrieving *Block* resources from
- `daysforward` (default: `99999`) The number of days after the start date to retrieve *Block* resources


#### Example Result

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE eighth [

<!ELEMENT eighth (body,error,debug)>
<!ELEMENT body (block*)>
<!ELEMENT block (bid,date,type,locked)>
<!ELEMENT bid (#PCDATA)>
<!ELEMENT date (#PCDATA)>
<!ELEMENT type (#PCDATA)>
<!ELEMENT locked (#PCDATA)>
<!ELEMENT error (#PCDATA)>
<!ELEMENT debug (#PCDATA)>
]>
<eighth>
  <blocks>
    <block>
      <bid>2922</bid>
      <date>
        <str>2015-03-06</str>
        <iso>2015-03-06T00:00:00-05:00</iso>
        <disp>March 6, 2015</disp>
      </date>
      <disp>Friday March 6th, 2015 Block A</disp>
      <type>A</type>
      <locked>0</locked>
      <activity>
        <aid>204</aid>
        <name>Computer Team (Senior)</name>
        <sponsors/>
        <rooms>
          <room>1</room>
        </rooms>
        <description>Our objective is to practice and enhance problem-solving skills involving programming. We compete in several contests, including USACO, Topcoder, and ACM on the national/international level.</description>
        <restricted>0</restricted>
        <presign>0</presign>
        <oneaday>0</oneaday>
        <bothblocks>0</bothblocks>
        <sticky>0</sticky>
        <special>0</special>
        <calendar>0</calendar>
        <block>
          <bid>2922</bid>
          <date>2015-03-06</date>
          <block>A</block>
          <locked>0</locked>
        </block>
        <bid>2922</bid>
        <block_sponsors>
          <sponsor>
            <sid>1266</sid>
            <fname>Thomas</fname>
            <lname>Rudwick</lname>
            <userid>95</userid>
          </sponsor>
        </block_sponsors>
        <block_rooms>
          <room>
            <rid>1605</rid>
            <name>200 Sys Lab (50)</name>
            <capacity>50</capacity>
          </room>
        </block_rooms>
        <cancelled>0</cancelled>
        <comment></comment>
        <advertisement></advertisement>
        <attendancetaken>0</attendancetaken>
      </activity>
    </block>
    <block>
      <bid>2923</bid>
      <date>
        <str>2015-03-06</str>
        <iso>2015-03-06T00:00:00-05:00</iso>
        <disp>March 6, 2015</disp>
      </date>
      <disp>Friday March 6th, 2015 Block B</disp>
      <type>B</type>
      <locked>0</locked>
      <activity>
        <!-- Activity details omitted -->
      </activity>
    </block>
    <block>
      <bid>2918</bid>
      <date>
        <str>2015-03-09</str>
        <iso>2015-03-09T00:00:00-04:00</iso>
        <disp>March 9, 2015</disp>
      </date>
      <disp>Monday March 9th, 2015 Block B</disp>
      <type>B</type>
      <locked>0</locked>
      <activity>
        <!-- Activity details omitted -->
      </activity>
    </block>
  </blocks>
  <error></error>
  <debug></debug>
</eighth>
```



## GET /api/eighth/get_activity/{aid}

Retrieves the *Activity* with the given AID.

The result is an XML representation of the *Activity* inside of an `activity` tag. This tag is a child of the root `eighth` tag.


#### Parameters

- `aid` AID of the requested *Activity*


#### Example Result

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE eighth [

<!ELEMENT eighth (body,error,debug)>
<!ELEMENT body (#PCDATA)>
<!ELEMENT error (#PCDATA)>
<!ELEMENT debug (#PCDATA)>
]>
<eighth>
  <activity>
    <aid>204</aid>
    <name>Computer Team (Senior)</name>
    <sponsors/>
    <rooms>
      <room>1</room>
    </rooms>
    <description>Our objective is to practice and enhance problem-solving skills involving programming. We compete in several contests, including USACO, Topcoder, and ACM on the national/international level.</description>
    <restricted>0</restricted>
    <presign>0</presign>
    <oneaday>0</oneaday>
    <bothblocks>0</bothblocks>
    <sticky>0</sticky>
    <special>0</special>
    <calendar>0</calendar>
  </activity>
  <error></error>
  <debug></debug>
</eighth>
```
