@title[queue]
### queue
Sending notifications can take time, especially if the channel needs an external API call to deliver the notification. To speed up your application's response time, let your notification be queued by adding the ShouldQueue interface and Queueable trait to your class. The interface and trait are already imported for all notifications generated using make:notification, so you may immediately add them to your notification class

It's important to have a dedicated queue for "urgent" notifications
+++
#### Customize the queue channel
```php
// On the notification costructor
$this->queue = 'queue-name';
// When scheduling the notification
$user->notify((new App\Notifications\SampleNotification())->onQueue("queue-name"));
```
