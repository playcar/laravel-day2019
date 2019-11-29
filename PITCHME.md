
@title[Splash]
## Laravel Notification System 201

---
@title[Who and What]
### about me
```php
$speaker = new Nerd();
$speaker->fullName = 'Riccardo Scasseddu';
$speaker->twitterHandle = '@ennetech';
$speaker->education = 'Graduated in Computer Science';
$speaker->occupation = 'CTO @ Playcar';
$speaker->roles = ['Solution architect', 'Hardware integrations'];
$speaker->wannaBe = 'Master of the world';
$speaker->talkSpeed = 1.2;
$speaker->save();
```

### focus
<p class="fragment text-left text-07">Know better Laravel Notification System</p>
+++
### about playcar
<div class="text-05">
<span class="text-13">2015</span><br>Company start as a car sharing operator with 9 cars<br>
<span class="text-13">2016</span><br>Start developing Playmoove as a modular car sharing platform (fleet of 40 cars)<br>
<span class="text-13">2017</span><br>Start using Playmoove as the platform to provide car sharing to people of Cagliari (fleet of 60 cars)<br>
<span class="text-13">2018</span><br>Launch of the free floating service and scale up to 100 cars<br>
<span class="text-13">2019</span><br>Start selling the platform around the world as a B2B Soutions, we have clients in South America, Hungary, Azorre Island with new ones being deployed right now<br>
</div>
<br>
#### Laravel is the core framework for serving all our business logic!
---
@title[101]
### 101
```php
class User
{
    use Notifiable; // Magic trait
    // Having a 'email' field in the User model is all is needed to send email
}
```

+++
### 101
```php
use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Notifiable;
use Illuminate\Notifications\Notification;
use Illuminate\Contracts\Queue\ShouldQueue;

class BaseNotification extends Notification implements ShouldQueue
{
    use Queueable;

    // BONUS: Use contructor method to set up common notifications part
    private $subject;
    private $content;
    private $attachments;

    public function via($notifiable)
    {
        // Custom logic to inject channels
        return ['mail','database','broadcast'];
    }

    public function toMail($notifiable)
    {
        // Classic email
        return (new MailMessage)->view(
        'email_template_view', [
            // view params
        ])->subject('mail subject');
        // Markdown email
        return (new MailMessage)
            ->greeting('Hello!')
            ->line('Some text');
    }
    
    // Used also for the database and broadcast channel
    public function toArray($notifiable)
    {
        return [
            // Notification data values map
        ];
    }
    // Additional custom formatters

    // Maybe some logic to translate the notifications
}
```
---
@title[channels]
### channels
A channel is a connector between the domain specific notification and (normally) an external service
+++
### custom channels

#### laravel-notification-channels.com

```php
use App\Contracts\SmsSender;
use App\Models\Driver;
use App\Notifications\BaseNotification;


class SmsChannel
{
    private $sender;

    public function __construct(SmsSender $sender)
    {
        $this->sender = $sender;
    }

    public function send(
        Driver $notifiable, 
        BaseNotification $notification)
    {
        $message = $notification->toSms($notifiable);

        $sanitizedNumber = number_sanitizer(
                $notifiable->mobile_phone
            );

        $this->sender->sendSMS($sanitizedNumber, $message);
    }
}
```
+++
```php
namespace App\Contracts;

interface SmsSender {
    public function sendSMS(string $to, string $message);
}
```


```php
$this->app->bind(SmsSender::class, function () {
    $currentDriver = config('sms.default');
        switch (config('sms.' . $currentDriver . '.driver')) {
            case 'twilio':
                return new Twilio(config('sms.' . $currentDriver));
                break;
            case 'voipcheap':
                return new Voipcheap(config('sms.' . $currentDriver));
                break;
            default:
                return new Log();
            }
        });
```

+++
```php
class Log
{
    public function sendSMS(string $to, string $message)
    {
        $message = date("Y-m-d H:i:s") . ' ' . $to . ' >> ' . $message;

        file_put_contents(public_path('sms.html'), $message . "<br>", FILE_APPEND);
    }
}
```
---
@title[database channel]
### database channel
Important channel if in your application is present a "notification history" feature where a user can see all past notifications
+++
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Notification extends Model
{
    private $casts = [
        'data' => 'array',
    ]

    public function notifiable()
    {
        return $this->morphTo();
    }

    public function markAsRead($time){
        if(!isset($this->read_at)){
            $this->read_at = $time ?? \Carbon\Carbon::now();
            $this->save();
        }
    }
}

```
+++
```php
    $unreadNotifications = Notification::whereNotifiableType(User::class)
                                ->whereNotifiableId(1)
                                ->whereNull('read_at')
                                ->get();
```
---
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

---
@title[Questions]
# Questions?
![QR](assets/img/qr.png)
<p style="text-align: center !important;">https://joind.in/talk/80680</p>
---
@title[hiring]
![fleet](assets/img/fleetsmall.jpg)
---
