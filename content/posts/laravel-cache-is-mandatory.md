---
title: "Laravel Cache is mandatory"
date: 2019-11-15T12:57:22-06:00
draft: false
tags:
 - Laravel
 - Queue
 - SQS
 - Redis

categories:
 - Hard learned lessons
---

I was configuring a [Laravel](https://laravel.com/) 5.7 application, and had a 
[Queue](https://laravel.com/docs/5.7/queues) with an [Amazon SQS](https://aws.amazon.com/sqs/) driver.

> [Laravel built-in cache](https://laravel.com/docs/5.7/cache) was **NOT** used explicitly by the application, so I
 pointed it to a non-existent Redis host. This will be important later.
 
To my surprise, when [running the queue worker](https://laravel.com/docs/5.7/queues#running-the-queue-worker), the
 application was fataling as it was trying to use Redis and was failing to connect.
 
My first reaction was to think that maybe the queue was misconfigured to consume from Redis and not from SQS.
However the stacktrace clearly showed some SQS related code.

Digging further, I found out that the internal workings of the 
[Laravel queue worker](https://laravel.com/docs/5.7/queues#running-the-queue-worker) internally depend 
on the [Laravel built-in cache](https://laravel.com/docs/5.7/cache) to store when the queue was last restarted.
Relevant snippet from Laravel internals below: 

{{< highlight php >}}
    /**
     * Get the last queue restart timestamp, or null.
     *
     * @return int|null
     */
    protected function getTimestampOfLastQueueRestart()
    {
        if ($this->cache) {
            return $this->cache->get('illuminate:queue:restart');
        }
    }
{{< / highlight >}}

This is probably not the only place where Laravel internals depend on its Cache.

## Lesson learned:
Even though your application may not explicitly leverage the [Laravel built-in cache](https://laravel.com/docs/5.7/cache),
the framework does, so make sure a vialble cache is configured in production.
