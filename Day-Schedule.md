# Day Schedule

The Day Schedule API provides the bell schedule at TJHSST for every day. It's usually accurate, and best with dates in the recent past or future. Iodine pulls this data from the official TJ calendar. It corresponds with the *dayschedule* module in Iodine.

This API does not require authentication.

Most of the source code pertaining to this module can be found inside [dayschedule.mod.php5](https://github.com/tjcsl/iodine/blob/master/modules/dayschedule/dayschedule.mod.php5).



## Block

The *Block* resource (JSON) represents a block, or period, inside a certain schedule. It provides the name and start/end times of that block.


### Properties

- `num` The block's position in the schedule; 1-indexed (ex. `1`)
- `name` A human-readable description of the block; may contain HTML (ex. `AMC Test/<br>Study Hall`)
- `times`
  - `start` When the block starts; 12-hour time (ex. `8:30`)
  - `end` When the block ends; 12-hour time (ex. `10:00`)
  - `times` The start and end times concatenated with a hyphen (ex. `8:30 - 10:00`)



## Schedule

The *Schedule* resource (JSON) represents the bell schedule on a single day, consisting of multiple *Block* resources.


### Properties

- `dayname` Human-readable date (e.g. "Tuesday, February 3")
- `summary` Description of the schedule for that day
- `date`
  - `today` The date of the retrieved schedule (ex. `20150203`)
  - `yesterday` (ex. `20150202`)
  - `tomorrow` (ex. `20150204`)
- `schedule` A collection of *Block* resources



### GET /ajax/dayschedule/json{?date}

Returns the *Schedule* resource for a specific date.

The result is a JSON object representation of the *Schedule* resource.


#### Parameters

- `date` (required) Date for which the schedule should be retrieved


#### Example Result

Note that only the first two blocks are listed here.

```js
{
    "dayname": "Tuesday, February 3",
    "summary": "Modified Blue Day with AMC",
    "date": {
        "tomorrow": "20150204",
        "yesterday": "20150202",
        "today": "20150203"
    },
    "schedule": {
        "date": "20150203",
        "period": [
            {
                "num": 1,
                "name": "AMC Test\/<br>Study Hall",
                "times": {
                    "start": "8:30",
                    "end": "10:00",
                    "times": "8:30 - 10:00"
                }
            },
            {
                "num": 2,
                "name": "Period 1",
                "times": {
                    "start": "10:10",
                    "end": "11:20",
                    "times": "10:10 - 11:20"
                }
            }
        ]
    }
}
```



## GET /ajax/dayschedule/json_exp{?start,end}

Returns a collection of *Schedule* resources representing the dates ranging from *start* to *end*.

The result is a JSON map of each day to its corresponding *Schedule* resource, which is represented as a JSON object.


#### Parameters

- `start` (required) Start date to retrieve schedules from (ex. `20150203`)
- `end` (required) End date to retrieve schedules up to (ex. `20150205`)


#### Example Result

Note that only the first two blocks of each day are listed here.

```js
{
    "20150203": {
        "dayname": "Tuesday, February 3",
        "summary": "Modified Blue Day with AMC",
        "date": {
            "tomorrow": "20150204",
            "yesterday": "20150202",
            "today": "20150203"
        },
        "schedule": {
            "date": "20150203",
            "period": [
                {
                    "num": 1,
                    "name": "AMC Test\/<br>Study Hall",
                    "times": {
                        "start": "8:30",
                        "end": "10:00",
                        "times": "8:30 - 10:00"
                    }
                },
                {
                    "num": 2,
                    "name": "Period 1",
                    "times": {
                        "start": "10:10",
                        "end": "11:20",
                        "times": "10:10 - 11:20"
                    }
                }
            ]
        }
    },
    "20150204": {
        "dayname": "Wednesday, February 4",
        "summary": "Red Day",
        "date": {
            "tomorrow": "20150205",
            "yesterday": "20150203",
            "today": "20150204"
        },
        "schedule": {
            "date": "20150204",
            "period": [
                {
                    "num": 1,
                    "name": "Period 5",
                    "times": {
                        "start": "8:30",
                        "end": "10:05",
                        "times": "8:30 - 10:05"
                    }
                },
                {
                    "num": 2,
                    "name": "Period 6",
                    "times": {
                        "start": "10:15",
                        "end": "11:45",
                        "times": "10:15 - 11:45"
                    }
                }
            ]
        }
    }
}
```
