title: spooldb2
author: John Doe
date: 2021-08-16 15:43:19
tags:
---
```
<p>
from gevent import monkey
monkey.patch_all()
import gevent
from datetime import datetime,timedelta
import os, time

startftime = '202101011500'
endftime =   '202101011700'
step = 30

i = 0
prange = round((datetime.strptime(endftime,'%Y%m%d%H%M').timestamp() - datetime.strptime(startftime,'%Y%m%d%H%M').timestamp())/(step * 60)) + 1
#print( list((datetime.strptime(startftime,'%Y%m%d%H%M') + timedelta(minutes = i*30)).strftime("%Y%m%d%H%M") for i in (range(prange)) ))

def task(ftime,step):
    print('Run task %s ...' % (ftime))
    start = time.time()
    gevent.sleep(0)
    #print(r"sqlplus zhanghongjing/zhanghongjing@o39 @D:\worknote\zhaobin\脚本及结果\20210715通州数据分析01182-王鹃\sp_rtd.sql " + ftime +" " + str(step) +">nul")
    os.system(r"sqlplus zhanghongjing/zhanghongjing@o39 @D:\worknote\zhaobin\脚本及结果\20210715通州数据分析01182-王鹃\sp_rtd.sql " + ftime +" " + str(step) +" >nul")
    end = time.time()
    print('Task %s runs %0.2f seconds.' % (ftime, (end - start)))

start_time = time.time()

rqs = list((datetime.strptime(startftime,'%Y%m%d%H%M') + timedelta(minutes = i*step)).strftime("%Y%m%d%H%M") for i in (range(prange)) )
jobs = [gevent.spawn(task, ftime, step) for ftime in rqs]
gevent.joinall(jobs)
#[job.value for job in jobs]
print("all done in %s" % (time.time() - start_time))
</p>
```