# aurora\_stat\_system\_waits<a name="aurora_stat_system_waits"></a>

Reports wait event information for the Aurora PostgreSQL DB instance\.

## Syntax<a name="aurora_stat_system_waits-syntax"></a>

 

```
aurora_stat_system_waits()
```

## Return type<a name="aurora_stat_system_waits-return-type"></a>

SETOF record

## Arguments<a name="aurora_stat_system_waits-arguments"></a>

None

## Usage notes<a name="aurora_stat_system_waits-usage-notes"></a>

This function returns the cumulative number of waits and cumulative wait time for each wait event generated by the DB instance that you're currently connected to\.

The returned recordset includes the following fields:
+ `type_id` – The ID of the type of wait event\.
+ `event_id` – The ID of the wait event\.
+ `waits` – The number of times the wait event occurred\.
+ `wait_time` – The total amount of time in microseconds spent waiting for this event\.

Statistics returned by this function are reset when a DB instance restarts\.

## Examples<a name="aurora_stat_system_waits-examples"></a>

The following example shows results from calling the `aurora_stat_system_waits` function\.

```
=> SELECT * 
    FROM aurora_stat_system_waits();
    
 type_id | event_id  |   waits   |  wait_time
---------+-----------+-----------+--------------
       1 |  16777219 |        11 |        12864
       1 |  16777220 |       501 |       174473
       1 |  16777270 |     53171 |     23641847
       1 |  16777271 |        23 |       319668
       1 |  16777274 |        60 |        12759
.
.
.
      10 | 167772231 |    204596 |    790945212
      10 | 167772232 |         2 |        47729
      10 | 167772234 |         1 |          888
      10 | 167772235 |         2 |           64
```

The following example shows how you can use this function together with `aurora_stat_wait_event` and `aurora_stat_wait_type` to produce more readable results\.

```
=> SELECT type_name,
             event_name,
             waits,
             wait_time
        FROM aurora_stat_system_waits()
NATURAL JOIN aurora_stat_wait_event()
NATURAL JOIN aurora_stat_wait_type();

 type_name |       event_name       |  waits   |  wait_time
-----------+------------------------+----------+--------------
 LWLock    | XidGenLock             |       11 |        12864
 LWLock    | ProcArrayLock          |      501 |       174473
 LWLock    | buffer_content         |    53171 |     23641847
 LWLock    | rdsutils               |        2 |        12764
 Lock      | tuple                  |    75686 |   2033956052
 Lock      | transactionid          |  1765147 |  47267583409
 Activity  | AutoVacuumMain         |   136868 |  56305604538
 Activity  | BgWriterHibernate      |     7486 |  55266949471
 Activity  | BgWriterMain           |     7487 |   1508909964
.
.
.
 IO        | SLRURead               |        3 |        11756
 IO        | WALWrite               | 52544463 |    388850428
 IO        | XactSync               |   187073 |    597041642
 IO        | ClogRead               |        2 |        47729
 IO        | OutboundCtrlRead       |        1 |          888
 IO        | OutboundCtrlWrite      |        2 |           64
```