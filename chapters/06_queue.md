@title[queue]
### queue
To speed up response times is necessary to use the background workers to send notifications, but it's also important to have a dedicated queue for "urgent" notifications
+++
#### Customize the queue channel
```php
// On the notification costructor
$this->queue = 'queue-name';
// When scheduling the notification
$user->notify((new SampleNotification())->onQueue("queue-name"));
```
