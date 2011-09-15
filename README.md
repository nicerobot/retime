# isnow

## Date/Time Pattern Language for Repetition (DTimpalr)

  A language for concisely describing date/times, from simple, static date/times to complex, repetitions and increments.

  The syntax expands on a standard for how fixed date/times are represented as:

    Year/Month/Day Weekday Hour:Minute:Second

  But in addition to a number representing a fixed date/time element, there is additional syntax to representing "any value" as well as "increments".

  The result is a somewhat familiar format for a variable date/time-specification that can represent "this moment in time" whenever the the current time matches the pattern.

  For example:

  - This is a standard, fixed date, a one-time occurrence: `1970/1/1 12:00:00`
  - This represents every minute of every day: `*/*/* * *:*:00`

# Motivation

  The motivation behind this standard is to provide a simple means to represent complex date/time repetitions and increments to answer a simple question:

>  Is `*/*/1 * 12:*:00` now?

---

## Example

  - Every day at 6am:

>    The fully qualified expression is:
>
>     */*/* * 06:00:00
>
>    An alternative minimal expression is:
>
>     6
>
>    Note:
>
>    - In the absence of any date fields, it defaults to `*/*/* *`
>    - In the absence of any time fields, it defaults to `*:*:00`

#### All the following examples follow this formatting convention:

  - Example Text

>     [a fully-qualified example]
>     [minimal example]
>     ...

Except that `[a fully-qualified example]` excludes Start/End Dates unless called for.

---

## More Examples

  - Mondays at noon:

>     */*/* Monday 12:00:00
>     M 12:0:0
>     M noon
>     M n

  - Mondays, Wednesdays and Fridays at midnight:

>     */*/* Monday,Wednesday,Friday 00:00:00
>     M,W,F midnight
>     MWF 0:0:0
>     MWF m

  - The first of every month at 6pm:

>     */*/01 * 18:00:00
>     /1 18
>     1 18

  - Every day in January at noon:

>     */1/* * 12:00:00
>     1/ noon
>     1/ 12
>     1/ n

---

## General Syntax

  _Field_ means any Year, Month, Day, Weekday, Hour, Minute, or Second.

  - The general syntax for a complete definition is:

>      Y/m/d w H:M:S sY/m/d w H:M:S eY/m/d w H:M:S

  Alternatively, "." (period) or "_" (underscore) can be used in place of " " (space).

>      Y/m/d.w.H:M:S.sY/m/d.w.H:M:S.eY/m/d.w.H:M:S
>      Y/m/d_w_H:M:S_sY/m/d_w_H:M:S_eY/m/d_w_H:M:S

    where:

      Y is a year field
      m is a month field
      d is a day field
      w is a weekday field
      H is a 24-hour field
      M is a minute field
      S is a second field
      s is the starting date: > or >=
      e is the ending date: < or <=

  - The general syntax for all _fields_ is:

       !v-v±[N]

      where:

        !    Optional. Specifies exclusions, "not".
        v    is a value
        -v   Option. Species the value is a range.
        ±    Option. Either + or -
             +    Specifies an increment.
             -    Specifies "from the end".
        [N]  is an expression to increment or subtract


## Defaults and Shorthands

  The default matching is "every minute". That is,

>     *
>
>  means
>
>     */*/* * *:*:00

### Times can be shortened as follows:

>   - Any number, `H`, means hours as: `H:*:00`
>   - A minute is `:M`, means: `*:M:00`
>   - A second is `::S`, means: `*:*:S`

### Dates can be shortened as follows:

>   - A year is `Y//`, means: `Y/*/* * *:*:00`
>   - A month is `m/`, means: `*/m/* * *:*:00`
>   - A day is `/d`, means: `*/*/d * *:*:00`
>     - Alternatively, `d t`, where `t` is a time or a weekday and time.
>   - A weekday is `w`, means: `*/*/* w *:*:00`
>     - Such a default weekday must be symbolic, not numeric.
>     - Alternatively, `d w t`, where `d` is a date and `t` is a time.
>       - In which case, weekday can be numeric, 1-7, starting on Sunday.

### Symbolic names

>   - All symbolic names are case-insensitive.
>   - Weekdays are, minimally(en_US): Su M Tu W Th F Sa MWF SS TT
>     - That is, the minimal letters required for each to uniquely identify the weekday.
>   - Midnight can be any shortening of the word(en_US) "midnight", even just "m" or "mn".
>   - Noon/Midday can be any shortening of the words(en_US) "noon" or "midday" that ensure it is unique from "midnight", even just "n" or "md".

---

## Examples: Negative days

  - The last of the month at 6pm:

>     */*/-1 * 18:00:00
>     -1 18

Note: The negation of a day essentially means to count from the end of the month instead of the beginning.

  - Every midnight of the last 15 days (2 weeks 1 day) of the year:

>     */12/-14 * 00:00:00
>     12/-15 0
>     12/-2w1d 0

---

## Examples: Ranges

  - Every minute, Monday thru Friday

>     M-F

  - Every leap-day at midnight

>     2/29-* 0

---

## Examples: Increments

  - Every hour of the third Monday of every month:

>     */*/* Monday+[3] *:00:00
>     M+[3] :0

  - Every minute on the first and third Monday of every month:

>     */*/* Monday+[1,3] *:*:00
>     M+[1,3]

  - The last Thursday in November at noon:

>     */11/* Thursday-[1] 12:00:00
>     11/ Th-[1] 12

  - Every minute of every 3rd week per year

>     */*/0+[3w] * *:*:00
>     /+[3w]

  - Every 3rd or 4th week per year at noon

>     */*/0+[3w,4w] * 00:00:00
>     /+[3w,4w]

---

## Examples: Ranges and Increments

  - Every 15 minutes on Sundays that fall on or between the first and tenth of the month:

>     */*/1-10 Sunday *:00,15,30,45:00
>     1-10 Su :00,15,30,45
>
>   Alternatively, using increments for the minutes,
>
>     */*/1-10 Sunday *:0+[15]:00
>     1-10 Su :0+[15]

  - Every 4 minutes after minute 1 or 6 minutes after minute 3 on any Sundays that fall on any of every third day of the month:

>     */*/1+[3] Sunday *:1+[4],3+[6]:00
>     1+[3] Su :1+[4],3+[6]

---

## Examples: Exclusions

  Exclusions begin with !

  - Every minute of every day excluding the first day of the month:

>     */*/!01 * *:*:00
>     /!1

---

## Examples: Start and End Dates

  - Every day at noon on or after the last Thursday of every month:

>     */*/* * 12:00:00 >=*/*/* Thursday-[1] *:*:*
>     12 >=Th-[1]

  - Every 9 seconds from 6am until 6pm.

>     */*/* * *:*:00+[9] >=*/*/* * 6:00:00
>     ::+[9] >=6 <=18

  - Every day at noon until September 1st

>     */*/* * 12:00:00 <*/09/01 * 00:00:00
>     12:0 <9/1

  - Every three weeks at noon for 5 years (from 2011/01/01)

>     */*/1+[3w] * 12:00:00 >=2011/01/01 * 00:00:00 <2016/01/01 * 00:00:00
>     /1+[3w] 12 >=2011 <2016

Start and End Dates modify the meaning of increments. Normally, increments default to being incremental within the context of the field. That is, increments of seconds reset every 60 seconds, increments in hours reset ever 24 hours, and so on. When Start/End Dates are in effect, the context is the entire range from start to end (or now, whichever is lesser).

---

## More Examples

  - The first of every quarter

>     */1+[3]/1 * *:*:00
>     1+[3]/1
>
> alternatively,
>
>     */1,4,7,10/1 * *:*:00
>     1,4,7,10/1

  - The end of every quarter

>     */0+[3]/-1 * *:*:00
>     0+[3]/-1
>
> alternatively,
>
>     */3,6,9,12/-1 * *:*:00
>     3,6,9,12/-1
