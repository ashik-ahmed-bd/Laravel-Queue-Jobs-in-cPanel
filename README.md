# Laravel Queue Job Setup in cPanel

This guide will walk you through setting up Laravel queue jobs in cPanel to process jobs and automatically retry failed ones.

---

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


### 📌 Replace YOUR_CPANEl_USERNAME with your actual cPanel username.

## Recommended Cron Schedule (Run Every Minute)

``` 
* * * * * /usr/local/bin/php /home/YOUR_CPANEl_USERNAME/public_html/artisan queue:work --queue=default,high --retry=3 --tries=3 --timeout=90

```

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
