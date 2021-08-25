title: 多线程导数程序spooldb1带参数版
author: John Doe
date: 2021-08-25 09:40:33
tags:
---
说明：
1、参数都是any类型。使用时需转换一下。
2、本想写个带配置文件的，后来考虑配置文件应该是一次配置永远不变的。写了配置文件，每次执行需修改就没必要了。
3、executor.map传多个参数。
  executor.map(long_time_task, pars, repeat(file), repeat(step), repeat(dblink))
  任务名，后跟可迭代参数。repeat就是迭代函数，且无限迭代。直到pars迭代完成。
```python
import os, time, random, threading 
from datetime import datetime,timedelta
from concurrent.futures import ThreadPoolExecutor, wait, ALL_COMPLETED
import argparse
from itertools import repeat


def long_time_task(ftime, file, step, dblink):
    print('Run task %s (%s)...' % (ftime, threading.active_count()))
    print('thread %s >>> %s' % (threading.current_thread().name, ftime))
    start = time.time()
    #print(os.system(r"sqlplus zhanghongjing/zhanghongjing@o39 @D:\worknote\zhaobin\脚本及结果\20210817执法大队-针刺\sp_rtd.sql " + ftime +" 30>nul"))
    print(os.system("sqlplus " + dblink + " @" + file + " " + ftime +" " + step+ ">nul"))
    
    end = time.time()
    print('Task %s runs %0.2f seconds.' % (ftime, (end - start)))
    #return res

def spooldb(file, dblink, starttime, endtime, step, maxworks):
  
    start = time.time()

    i = 0
    startftime = starttime
    endftime = endtime
    
    #executor = ThreadPoolExecutor(max_workers=25)
    
    pars = list()
    while startftime <= endftime:
        #t = executor.submit(long_time_task,startftime)
        #print("in main : get page {}s success".format(t))
        pars.append(startftime)
        ftimed = datetime.strptime(startftime,'%Y%m%d%H%M') + timedelta(minutes = int(step))
        i += 1
        startftime = ftimed.strftime("%Y%m%d%H%M")
     
    with ThreadPoolExecutor(max_workers=maxworks) as executor:
        executor.map(long_time_task, pars, repeat(file), repeat(step), repeat(dblink))
        
    
    end = time.time()
    print('All subprocesses done.')
    print('Task all runs %0.2f seconds.' % ((end - start)))    
 
parser = argparse.ArgumentParser(description='Test for argparse')
parser.add_argument('--file', '-f', help='导出脚本sql，必要参数', required=True)
parser.add_argument('--dblink', '-l', help='数据库连接串，非必要参数，但是有默认值：zhanghongjing/zhanghongjing@o39', default='zhanghongjing/zhanghongjing@o39')
parser.add_argument('--starttime', '-s', help='开始时间，必要参数', required=True)
parser.add_argument('--endtime', '-e', help='结束时间，必要参数', required=True)
parser.add_argument('--step', '-t', help='间隔分钟数，非必要参数，但是有默认值：30', default=30)
parser.add_argument('--maxworks', '-x', help='最大线程数，非必要参数，但是有默认值：25', default=25)

args = parser.parse_args()

if __name__ == '__main__':
    try:
        spooldb(str(args.file), str(args.dblink), str(args.starttime), str(args.endtime), str(args.step), int(args.maxworks))
    except Exception as e:
        print(e)
```
