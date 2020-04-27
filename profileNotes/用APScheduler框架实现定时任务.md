### 用APScheduler框架实现定时任务

1.安装

```python
pip install apscheduler
```

2.实例化一个定时器

```python
from apscheduler.scheduler import Scheduler

sched = Scheduler()
sched.start()
```

3.实现三种简单的定时任务

date-based（延后执行）：

```python
from datetime import date
from apscheduler.scheduler import Scheduler

# 开始定时任务
sched = Scheduler()
sched.start()

# 定义的要定时执行的函数
def my_job(text):
    print text

# 定义函数执行时间
exec_date = date(2009, 11, 6)

# 将定时任务储存在变量中，便于删除或取消
job = sched.add_date_job(my_job, exec_date, ['text'])
```

Interval-based（间隔定时）

```python
from datetime import datetime
from apscheduler.scheduler import Scheduler

# 开始定时任务
sched = Scheduler()
sched.start()

def job_function():
    print "Hello World"

# 给定一个时间点，定时任务将在此时间点之后每两小时执行一次
sched.add_interval_job(job_function, hours=2, start_date='2010-10-10 09:30')
装饰器的方式实现间隔定时任务
# job_function将每两小时被调用一次
@sched.interval_schedule(hours=2)
def job_function():
    print "Hello World"
```

Cron-style（可以实现多种定时方式）

| Field         | Description                                                  |
| ------------- | ------------------------------------------------------------ |
| `year`        | 4-digit year number                                          |
| `month`       | month number (1-12)                                          |
| `day`         | day of the month (1-31)                                      |
| `week`        | ISO week number (1-53)                                       |
| `day_of_week` | number or name of weekday (0-6 or mon,tue,wed,thu,fri,sat,sun) |
| `hour`        | hour (0-23)                                                  |
| `minute`      | minute (0-59)                                                |
| `second`      | second (0-59)                                                |

```python
from apscheduler.scheduler import Scheduler

sched = Scheduler()
sched.start()

def job_function():
    print "Hello World"

# 定时任务将在6-8，11-12月份的第三个星期五的00：00-03：00执行
sched.add_cron_job(job_function, month='6-8,11-12', day='3rd fri', hour='0-3')
# 定时任务将在每周一到每周五早晨5：30执行
sched.add_cron_job(backup, day_of_week='mon-fri', hour=5, minute=30)

也可用装饰器的方式实现各种定时
@sched.cron_schedule(day='last sun')
def some_decorated_task():
    print("I am printed at 00:00:00 on the last Sunday of every month!")
```

4.关闭定时任务

```python
sched.shutdown()
# 默认情况下，定时任务关闭时先执行线程池中的任务，待任务执行完毕后，在关闭，若想立刻关闭定时任务可执行
sched.shutdown(wait=False)
```

5.查看当前任务器中的任务

```python
sched.print_jobs()
```

