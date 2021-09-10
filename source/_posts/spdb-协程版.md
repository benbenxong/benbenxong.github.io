title: spdb_协程版
author: John Doe
date: 2021-09-10 17:10:17
tags:
---
spdb_gevent.py
``` python
from gevent import monkey
monkey.patch_all()
import gevent
from gevent.pool import Pool
from functools import partial
import subprocess

import os, time, random, threading 
from datetime import datetime,timedelta
#from concurrent.futures import ThreadPoolExecutor, wait, ALL_COMPLETED
import argparse
from itertools import repeat

import re

pool = Pool(50)

def to_log (logfile, messages):
    #print('thread1 %s >>> (%s)' % (threading.current_thread().name, 'ok'))
    with open(logfile, "a") as f:
        print(messages, file=f)
     
def long_time_task(ftime, dblink, file, step, others):
    #print('Run task %s (%s)...' % (ftime, threading.active_count()))
    print('thread [%s] of %s>>> %s' % (re.match(r'<(.*?):',str(gevent.getcurrent())).group(1), pool.free_count(), ftime))
    #print('thread %s of %s>>> %s' % (gevent.getcurrent(), pool.free_count(), ftime))
    p = pool.spawn(to_log, "output.log", 'thread [%s] of %s>>> %s' % (re.match(r'<(.*?):',str(gevent.getcurrent())).group(1), pool.free_count(), ftime)); p.join()

    
    #pool.submit(to_log, "output.log", 'thread %s >>> %s' % (threading.current_thread().name, ftime))
    p = pool.spawn(to_log, "output.log", "sqlplus " + dblink + " @" + file + " " + ftime +" " + step+ " > nul"); p.join()
    start = time.time()
    #print(os.system(r"sqlplus zhanghongjing/zhanghongjing@o39 @D:\worknote\zhaobin\脚本及结果\20210817执法大队-针刺\sp_rtd.sql " + ftime +" 30>nul"))
    #ret = os.system("sqlplus " + dblink + " @" + file + " " + ftime +" " + step+ " > nul")
    sub = subprocess.Popen("sqlplus " + dblink + " @" + file + " " + ftime +" " + step+ " \"" + others + "\" > nul", shell=True)
    ret, err = sub.communicate()
    
    end = time.time()
    print('Task %s runs %0.2f seconds with ret %s err %s.' % (ftime, (end - start), ret, err))
    p = pool.spawn(to_log, "output.log", 'Task %s runs %0.2f seconds with ret %s err %s.' % (ftime, (end - start), ret, err)); p.join()
    
    #return res

def make_date_pars(startp, endp, step):
    pars = list()
    i = 0
    while startp <= endp:
        #t = executor.submit(long_time_task,startftime)
        #print("in main : get page {}s success".format(t))
        pars.append(startp)
        ftimed = datetime.strptime(startp,'%Y%m%d%H%M') + timedelta(minutes = int(step))
        i += 1
        startp = ftimed.strftime("%Y%m%d%H%M")
    return pars

def make_num_pars(startp, endp, step):
    startp = int(startp)
    endp = int(endp)
    step = int(step)
    pars = [str(x) for x in range(startp, endp) if x % step == 1 ]
    return pars


def make_pars(num, startp, endp, step):
    numbers = {
        0 : make_date_pars,
        1 : make_num_pars
    }
    method = numbers.get(num)
    if method:
        return(method(startp, endp, step))
        
def spooldb(file, dblink, executor, step, pars, others):
  
    start = time.time()

    executor.map(partial(long_time_task, file=file, step=step, dblink=dblink, others=others), pars)
        
    
    end = time.time()
    print('All subprocesses done.')
    print('Task all runs %0.2f seconds.' % ((end - start)))
    p = pool.spawn(to_log, "output.log", 'All subprocesses done.'); p.join()
    p = pool.spawn(to_log, "output.log", 'Task all runs %0.2f seconds.' % ((end - start))); p.join()
    
parser = argparse.ArgumentParser(description='Test for argparse')
parser.add_argument('--file', '-f', help='导出脚本sql，必要参数', required=True)
parser.add_argument('--dblink', '-l', help='数据库连接串，非必要参数，但是有默认值：zhanghongjing/zhanghongjing@o39', default='zhanghongjing/zhanghongjing@o39')
parser.add_argument('--start', '-s', help='开始值，必要参数', required=True)
parser.add_argument('--end', '-e', help='结束值，必要参数', required=True)
parser.add_argument('--step', '-t', help='间隔分钟数或数值，非必要参数，但是有默认值：30', default=30)
parser.add_argument('--maxworks', '-x', help='最大线程数，非必要参数，但是有默认值：25', default=25)
parser.add_argument('--stepp', '-T', help='间隔方法（0分钟或1数值），非必要参数，但是有默认值：0', default=0)
parser.add_argument('--others', '-o', help='其它sql参数，非必要参数，但是有默认值：null', default='')



args = parser.parse_args()

def main(file, dblink, startp, endp, step, maxworks, stepp, others):
    stepp = int(stepp)
    executor = Pool(maxworks) 
    pars = make_pars(stepp, startp, endp, step)
    print(pars) 
    print(args.stepp)
    spooldb(file, dblink, executor, step, pars, others)
       
if __name__ == '__main__':
    # try:
    #     spooldb(str(args.file), str(args.dblink), str(args.starttime), str(args.endtime), str(args.step), int(args.maxworks))
    # except Exception as e:
    #     print(e)
    #     p = pool.spawn(to_log, "output.log", e); p.join()
    main(args.file, args.dblink, args.start, args.end, args.step, args.maxworks, args.stepp, args.others)
```
