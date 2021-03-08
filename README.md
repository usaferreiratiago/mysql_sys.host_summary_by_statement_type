# mysql_sys.host_summary_by_statement_type

SELECT 
    IF((`performance_schema`.`events_statements_summary_by_host_by_event_name`.`HOST` IS NULL),
        'background',
        `performance_schema`.`events_statements_summary_by_host_by_event_name`.`HOST`) AS `host`,
    SUBSTRING_INDEX(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`EVENT_NAME`,
            '/',
            -(1)) AS `statement`,
    `performance_schema`.`events_statements_summary_by_host_by_event_name`.`COUNT_STAR` AS `total`,
    FORMAT_PICO_TIME(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_TIMER_WAIT`) AS `total_latency`,
    FORMAT_PICO_TIME(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`MAX_TIMER_WAIT`) AS `max_latency`,
    FORMAT_PICO_TIME(`performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_LOCK_TIME`) AS `lock_latency`,
    `performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_ROWS_SENT` AS `rows_sent`,
    `performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_ROWS_EXAMINED` AS `rows_examined`,
    `performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_ROWS_AFFECTED` AS `rows_affected`,
    (`performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_NO_INDEX_USED` + `performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_NO_GOOD_INDEX_USED`) AS `full_scans`
FROM
    `performance_schema`.`events_statements_summary_by_host_by_event_name`
WHERE
    (`performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_TIMER_WAIT` <> 0)
ORDER BY IF((`performance_schema`.`events_statements_summary_by_host_by_event_name`.`HOST` IS NULL),
    'background',
    `performance_schema`.`events_statements_summary_by_host_by_event_name`.`HOST`) , `performance_schema`.`events_statements_summary_by_host_by_event_name`.`SUM_TIMER_WAIT` DESC
