# Laravel Queue Job Setup in cPanel

This guide will walk you through setting up Laravel queue jobs in cPanel to process jobs and automatically retry failed ones.

---
##  Find the Correct PHP Path
First, determine the PHP version your Laravel app is using. Run this command in cPanel > Terminal (or check in cPanel > Select PHP Version):

```
php -v
```
#### Common PHP paths:
* PHP 8.1: /usr/local/bin/php81
* PHP 8.0: /usr/local/bin/php80
* PHP 7.4: /usr/local/bin/php74
* Default: /usr/local/bin/php

## 1. Configure Laravel Queue

Edit the `.env` file and set the queue driver:

```env
QUEUE_CONNECTION=database
```



## 2. Add a Cron Job in cPanel
To keep your queue worker running, set up a cron job in cPanel > Cron Jobs.
### Queue Worker Command
Add the following command to process jobs:

```
 /usr/local/bin/php /home/YOUR_CPANEl_USERNAME/public_html/artisan queue:work --queue=default,high --retry=3 --tries=3 --timeout=90
 ```

```
* * * * * /usr/local/bin/php /home/YOUR_CPANEl_USERNAME/dashboard.yourdomain.com/artisan schedule:run >> /dev/null 2>&1
```

```
* * * * * /usr/local/bin/php /home/YOUR_CPANEl_USERNAME/subdomains/artisan queue:work --queue=default,high --tries=3 --timeout=90 >> /dev/null 2>&1
```

### 📌 Replace YOUR_CPANEl_USERNAME with your actual cPanel username.

## Recommended Cron Schedule (Run Every Minute)

``` 
* * * * * /usr/local/bin/php /home/YOUR_CPANEl_USERNAME/public_html/artisan queue:work --queue=default,high --retry=3 --tries=3 --timeout=90

```

## Subdomain Cron Job

## 3. Automatically Retry Failed Jobs
**Laravel stores failed jobs in the failed_jobs table. To retry them, set another cron job:**

```

* * * * * /usr/local/bin/php /home/YOUR_CPANEl_USERNAME/public_html/artisan queue:retry all

```

#### Alternatively, use:

```
* * * * * /usr/local/bin/php /home/YOUR_CPANEl_USERNAME/public_html/artisan queue:failed:retry --tries=3

```

## 4. Monitor and Manage Failed Jobs
To check the list of failed jobs, run:

```
php artisan queue:failed
```
**To manually retry a failed job:**

```
php artisan queue:retry {job_id}
```

To delete all failed jobs:

```
php artisan queue:flush
```

## 5. Running a Persistent Queue Worker
If your hosting doesn’t support Supervisor, use the following command to run the queue worker persistently in the background:

```
nohup /usr/local/bin/php /home/YOUR_CPANEl_USERNAME/public_html/artisan queue:work --queue=default,high --tries=3 --timeout=90 > /dev/null 2>&1 &

```

* The nohup command ensures the process continues running even if the terminal is closed.
*  /dev/null 2>&1 & prevents output logs from filling up.

## 6. Ensure Queue is Running After Server Reboots
To ensure the queue worker restarts after a reboot, add this command to a startup script or set up another cron job:


```
@reboot /usr/local/bin/php /home/YOUR_CPANEl_USERNAME/public_html/artisan queue:work --queue=default,high --tries=3 --timeout=90

```

## 7. Additional Optimization Tips
* Use **queue:listen** instead of **queue:work** if you want automatic queue restarts when code changes:

```
php artisan queue:listen --queue=default,high --tries=3 --timeout=90

```
* Set QUEUE_CONNECTION=redis for better performance if Redis is available.
* Increase **timeout** and **memory_limit** if jobs take longer to execute.
