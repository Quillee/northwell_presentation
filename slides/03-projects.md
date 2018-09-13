## Various small Python scripts
***
* Echo data
* Patient Mortality
* Immuno-Therapy email
* 3D Ordering 3d model file dump



## Web applications
***
* User management tool
    * users can request access to different facilities
    * admins can reject and accept those requests
* Web server monitoring
    * [demo](https://github.com/Quillee/northwell_presentation)




```

SELECT TO_CHAR(WEB.ACCESS_TIME, 'MM/DD/YYYY') as DATE_ACCESSED, APP.APP_ID, APP.NAME, HOS.HOS_SEQ, HOS.HOS_DOH, HOS.HOS_LNAM,
COUNT(WEB.IP_ADDRESS) AS AVG_HITS
FROM WEB_TRACKING WEB INNER JOIN HOSPITALS HOS 
ON WEB.HOS_SEQ = HOS.HOS_SEQ AND WEB.HOS_PFI = HOS.HOS_DOH 

INNER JOIN QM_WEB_APP APP ON WEB.APP_ID = APP.APP_ID

WHERE APP.IS_ENABLED = 1  AND (APP.APP_ID = 'ED' OR APP.APP_ID = 'CAD')
    AND (WEB.ACCESS_TIME > TO_DATE('11/30/2017', 'MM/DD/YYYY') 
    AND WEB.ACCESS_TIME < TO_DATE('04/04/2018', 'MM/DD/YYYY')) 

GROUP BY TO_CHAR(WEB.ACCESS_TIME, 'MM/DD/YYYY'), APP.APP_ID, APP.NAME, HOS.HOS_SEQ, HOS.HOS_DOH, HOS.HOS_LNAM
ORDER BY AVG_HITS, HOS_SEQ, HOS_PFI, APP_ID;

```




```
-- new all dates day by day query
SELECT app.app_id, app.name, x.bench_days, NVL(stats.ct, 0) FROM

(SELECT x.bench_days from (select trunc(sysdate-40) + level as bench_days from dual connect by level < 40) x
WHERE   TO_CHAR(x.bench_days, 'DY') <> 'SAT' AND TO_CHAR(x.bench_days, 'DY') <> 'SUN') x

CROSS JOIN
(SELECT APP_ID, NAME FROM QM_WEB_APP WHERE IS_ENABLED = 1 AND APP_ID in ('QM_ASTOM') )app 

LEFT JOIN 
(select APP_ID, TRUNC(ACCESS_TIME) ACC_DATE, COUNT(IP_ADDRESS) CT
FROM WEB_TRACKING GROUP BY APP_ID, TRUNC(ACCESS_TIME)) STATS
ON STATS.APP_ID = APP.APP_ID AND STATS.ACC_DATE = X.BENCH_DAYS;

```
