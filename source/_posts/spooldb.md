title: spooldb
author: John Doe
date: 2021-08-16 15:31:53
tags:
---
```python
from multiprocessing import Pool
import os, time, random
from datetime import datetime,timedelta

def long_time_task(ftime):
    print('Run task %s (%s)...' % (ftime, os.getpid()))
    start = time.time()
    print(os.system(r"sqlplus zhanghongjing/zhanghongjing@o39 @D:\worknote\zhaobin\脚本及结果\20210728审计数据01241-江宁\sp_rtd.sql " + ftime +" 30>nul"))
    end = time.time()
    print('Task %s runs %0.2f seconds.' % (ftime, (end - start)))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Pool(8)
    start = time.time()
    startftime = '201801010000'
    endftime =   '202107281030'
    i = 0
    while startftime <= endftime:
        p.apply_async(long_time_task, args=(startftime,))
        ftimed = datetime.strptime(startftime,'%Y%m%d%H%M') + timedelta(minutes = 30)
        i += 1
        startftime = ftimed.strftime("%Y%m%d%H%M")
        
    print('Waiting for all subprocesses done...')
    p.close()
    p.join()
    end = time.time()
    print('All subprocesses done.')
    print('Task all runs %0.2f seconds.' % ((end - start)))
 ```