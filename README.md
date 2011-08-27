# Date Repetition Language

  Expands on a standard for how static dates are represented:
  
    Year/Month/Day Weekday Hour:Minute:Second

  But instead of a number in a representing a static date/time element, the presence of a value means it repeats at that interval.
  
  In other words, the date-specification represents this moment in time whenever the the current time matches the pattern.
  
  For example:
  
  - This is a one-time occurence: `1970/1/1 12:00:00`
  - This represents every minute of every day: `*/*/* * *:*:00`
    - This is because it allows for any value in any field except the seconds, which are fixed at 00. The only time the current date/time matches that is ever minute.
    - This can be shorted to: `::0` or just `*`

## Examples

  - Every 6am:

> ``
     */*/* * 06:00:00
     6
``

Note: In the absence of any date fields, it defaults to `*/*/* *`

  - Mondays at noon:

> ``
     */*/* Monday 12:00:00
     M 12
``

  - Mondays and Thursdays at midnight:

> ``
     */*/* Monday,Thursday 00:00:00
     M,Th 0:0:0
``

  - The first of the month at 6pm:

> ``
     */*/01 * 18:00:00
     1 18
``

## A point about defaults and shorthands

  The default matching is "every minute". That is,

>     *
>
>  means
>  
>     */*/* * *:*:00

  Times can be shortened as follows:
  
>   - Any number, `H`, means hours as: `H:*:00`
>   - A minute, `:m`, means: `*:m:00`
>   - A second, `::s`, means: `*:*:s`

  Dates can be shortened as follows:

>   - A year, `Y//`, means: `Y/*/* * *:*:00`
>   - A month, `M/`, means: `*/M/* * *:*:00`
>   - A day, `/D`, means: `*/*/D * *:*:00`
>     - Alternatively, `D t`, where `t` is a time or a weekday and time.
>   - A weekday, `W`, means: `*/*/* W *:*:00`
>     - Such a default weekday must be symbolic, not numeric.
>       - Weekdays are, minimally(en_US): Su M Tu W Th F Sa
>     - Alternatively, `d W t`, where `d` is a date and `t` is a time.
>       - In which case, weekday can be numeric, 1-7, starting on Sunday.

## Examples: Negative days

  - The last of the month at 6pm:

> ``
     */*/-1 * 18:00:00
     -1 18
``

Note: The negation of a day essentially means to count from the end of the month instead of the beginning.

  - Every midnight of the last 15 days (2 weeks 1 day) of the year:

> ``
     */12/-14 * 00:00:00
     12/-15 0
     12/-2w1d 0
``

## Examples: Ranges and Increments

  - Every hour of the third Monday of every month:

> ``
     */*/* Monday+[3] *:00:00
     M+[3] :0
``

  - Every minute on the first and third Monday of every month:

> ``
     */*/* Monday+[1,3] *:*:00
     M+[1,3]
``

  - The last Thursday in November at noon:

> ``
     */11/* Thursday-[1] 12:00:00
     11/ Th-[1] 12
``

  - Every 15 minutes on Sundays that fall on or between the first and tenth of the month:

> ``
     */*/1-10 Sunday *:00,15,30,45:00
     1-10 Su :00,15,30,45
``Alternatively, using increments for the minutes,``
     */*/1-10 Sunday *:0+15:00
     1-10 Su :0+15
``

  - Every 4 after minute 1 or 6 minutes after minute 3 on any Sundays that fall on any third day of the month:

> ``
     */*/1+[3] Sunday *:1+[4],3+[6]:00
     1+[3] Su :1+[4],3+[6]
``

## Examples: Dynamic Ranges and Increments 

  %Y/%M/%D %W %H:%m:%s

  - Every 4 after minute 1 or 6 minutes after minute 3 on any Sundays that fall on any third day of the month:

> ``
     */*/1+[%H] Sunday *:%s+[4],3+[6]:00
     1+[%H] Su :%s+[4],3+[6]
``

## Examples: Exclusions

  Exclusions begin with !

  - All but the first day of the month:

> ``
     /!1
``

---

# DRAFT ... ideas ...

## Context switch

  ' (tick or forward,single-quote) is the context switch token

  - 100 hours before the end of the month. Alternatively, 4d 4h
>     -100'M
>     */*/* * 00-100'Monthly:00:00
         or  
>     /-4 -4
>     */*/1-4 * 00-4:00:00
      can this mean the last 4 days of the month and the last 4 hours
      of every day? /4 4 means on the 4th day at 4a so it makes sense
      that /-4 4 means 4 days from the end of the month at 4a. therefore
      it makes sense that /-4 -4 is 4 days from the end of the month and
      4 hours before the end of that day. that is, -100 hours from the
      end of the month. right?

  - 1000 minutes before the end of the year.
>      :-1000'Y
>      */*/* * *:00-1000'Yearly:00
          or
>      -41'Y:-16'Y
>      */*/* * 00-41'Yearly:00-16'Yearly:00

  - Every leap-day at midnight
>     2/[29-] 0

  - By default, all fields process within the context of the field to the immediate left.
    Note: days and weekdays are within the month context.
    That is, minutes are within hours.
    
    The Context switch forces a specific context.
    
    e.g. minute 59 in different contexts
      - The last minute of every hour (the default context for minutes):
         - `*/*/* * *:59:00`
         - `*/*/* * *:-1:00`
         - `*/*/* * *:59'h:00`
         - `*/*/* * *:-1'h:00`
         - `:-1`
         - `:59`
         - `:-1'h`
         - `:59'h`
      - The last minute of the day:
        - `*/*/* * *:59'd:00`
        - `:59'd`
        - `:-1'd`
      - The last minute of the week:
        - `*/*/* * *:59'w:00`
        - `:59'w`
        - `:-1'w`
      - The last minute of the month:
        - `*/*/* * *:59'm:00`
        - `:59'm`
        - `:-1'm`
      - The last minute of the year:
        - `*/*/* * *:59'y:00`
        - `:59'y`
        - `:-1'y`
    
     e.g. minute 59 in different contexts
       - `*/*/2(+2) * *:59'd:00` is the the last minute of the even days
       - `*/[5-9]/* * *:59'm:00` is the the last minute of the May through September
       - `*/*/* We *:59'm:00` is the the last minute of month if it's a Wednesday
    
       It's possible to create impossible to succeed values:
       - `*/*/* We *:59'w:00` is the the last minute of week if it's a Wednesday
       - `*/*/2(+2) * *:59'y:00` is the the last minute of the year when it's an even day
    
     e.g. the last Thursday in different contexts
        - `Thrusday-1` is the the last Thursday of the month
        - `Thrusday-1'y` is the the last Thursday of the year
    
     e.g. 


# Iterative repetitions:

  {} specifies an iteration.

  Iterative repetitions require a fixed, anchor date and/or time from which to calculate.

  1. Every 9 seconds from 6am. Since the anchor is only a time, the repetition
     resets every day at 6am.
>     6:0:0{+9}
>     */*/* * 06:00:00{+9}

  - Every 3 minutes from noon on the first of the month.
>     1 12:0{+3}
>     */*/1 * 12:00{+3}:00

  - Every 7 hours from Wednesday at midnight.
>     W 0{+7}
>     */*/* Wednesday 00{+7}:00:00

  - Every 12 days.
>     1 12:0{+3}
>     */*/1 * 12:00{+3}:00

  - The last 7 Wednesdays of the year at midnight.
        TODO this shows the need to be able to change context
          within a element. That is, instead of wday always
          being within a monthly context, it is useful to be
          in a yearly context.
>     W-7{+7}'Y
>     */*/* Wednesday-7{+7}'Yearly 00:00:00


# Mutli-group repetitions

  () specifies grouping names.

  1. A single DR that contains multiple, separate repetitions.
      For example, to represent a twice-daily at 8a and 8p and thrice-weekly at 8a, noon and 8p build schedule.
>     [M,Th] 12,[8,20](D):
>     */*/* [Monday,Thursday]{Weekly} 12{Weekly},[08,20]{Daily}:00:00

      without the grouping, it means
>     [M,Th] 12,[8,20]:00
      
      

  - Or an hourly build schedule starting at 8a-8p each day during the last week of each month
    otherwise, the above schedule.
>     -7(M) [M,Th](W) 12(W),[8,20](D),[8-20](M):00

  - Is there a reason for doing something like this:
>     */2(A)/4{A,B} 6{B,C},8(B):*0{A,C}
>     */2(A)/4{A,B} [6,8](B),6(C):*0{A,C}
    Three group: A, B and C
    A - Any ten minutes of February 4th
    B - The 4th of any month at 6a and 8a
    C - Any ten minutes of 6a every day

## Conventions used in this document

  Each example contains the the fully qualified solution followed by one or more minimal solutions.

  _Field_ means any Year, Month, Day, Weekday, Hour, Minute, or Second.
  
  - The general syntax for any _field_ is:
  
        ! v -N (R) {G} 'C
  
      where:
      
        !    is an exclusions
        v    is the field value, including ranges [value-value]
        -N   backwards from value but not range
        {R}  repeats R increments of the field
        (G)  groups disparate fields
        'C   calculates with in the C context where C is:
                  Yearly, Monthly, Weekly, Daily, Hourly

