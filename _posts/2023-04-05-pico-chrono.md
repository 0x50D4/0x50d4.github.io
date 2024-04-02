---
title: PicoCTF 2023 - Chrono
date: 2023-04-05 1:00:00 +0200
categories: [PicoCTF2023]
tags: [Linux]
---

In chrono we get an instance we have to ssh into, and the task description just asks in what way do we automate a task to run at intervals on a linux server.
On linux this is done by so called cronjobs, and one of the files where cron jobs can be found is /etc/crontab, and catting that file we get:

```bash
picoplayer@challenge:~$ cat /etc/crontab
# picoCTF{ find the flag yourself ;) }
```
