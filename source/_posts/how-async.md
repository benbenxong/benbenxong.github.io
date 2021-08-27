title: how_async
author: John Doe
date: 2021-08-28 01:30:40
tags:
---
```python
#import aiohttp
import asyncio
from asyncio.tasks import create_task
import time

from concurrent.futures import ThreadPoolExecutor

async def hello(sleep_time):
    print("Hello world!")
    await asyncio.sleep(sleep_time)
    print("Hello again")
    #async with aiohttp.ClientSession() as client:
    #    resp = await client.get(f'http://127.0.0.1:8000/sleep/{sleep_time}')
    #    resp_json = await resp.json()
    #    print (resp_json)
        
def sync_calc_fib(n):
    if n in [1, 2]:
        return 1
    return sync_calc_fib(n - 1) + sync_calc_fib(n - 2)

def calc_fib(n):
    result = sync_calc_fib(n)
    print(f'第 {n} 项计算完成，结果是：{result}')
    return result

async def main():
    start = time.perf_counter()
# """     tasks_list = [
#         asyncio.create_task(calc_fib(36)),
#         asyncio.create_task(hello(3))
#     ]
#     await asyncio.gather(*tasks_list) """
    loop = asyncio.get_event_loop() 
    with ThreadPoolExecutor(max_workers=4) as executor: 
        tasks_list = [ 
            loop.run_in_executor(executor, calc_fib, 36), 
            asyncio.create_task(hello(3)) 
        ] 
        await asyncio.gather(*tasks_list) 
        end = time.perf_counter()
        print(f'总耗时：{end - start}')
    
asyncio.run(main())
```
    