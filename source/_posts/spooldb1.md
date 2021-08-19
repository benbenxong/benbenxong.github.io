title: spooldb1
author: John Doe
date: 2021-08-16 15:41:31
tags:
---
```python
import os, time, random, threading 
from datetime import datetime,timedelta
from concurrent.futures import ThreadPoolExecutor, wait, ALL_COMPLETED

def long_time_task(ftime):
    print('Run task %s (%s)...' % (ftime, threading.active_count()))
    print('thread %s >>> %s' % (threading.current_thread().name, ftime))
    start = time.time()
    print(os.system(r"sqlplus zhanghongjing/zhanghongjing@o39 @D:\worknote\zhaobin\脚本及结果\20210812丰台数据分析01240-郎维晨\sp_rtd.sql " + ftime +" 30>nul"))
    end = time.time()
    print('Task %s runs %0.2f seconds.' % (ftime, (end - start)))
    #return res


    
if __name__=='__main__':


    start = time.time()

    i = 0
    startftime = '201901010000'
    endftime =   '202105312330'
    
    #executor = ThreadPoolExecutor(max_workers=25)
    
    pars = list()
    while startftime <= endftime:
        #t = executor.submit(long_time_task,startftime)
        #print("in main : get page {}s success".format(t))
        pars.append(startftime)
        ftimed = datetime.strptime(startftime,'%Y%m%d%H%M') + timedelta(minutes = 30)
        i += 1
        startftime = ftimed.strftime("%Y%m%d%H%M")
     
    with ThreadPoolExecutor(max_workers=25) as executor:
        executor.map(long_time_task,pars)
        
    
    end = time.time()
    print('All subprocesses done.')
    print('Task all runs %0.2f seconds.' % ((end - start)))
```