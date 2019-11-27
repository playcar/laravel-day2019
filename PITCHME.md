
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
$speaker->roles = ['Full Stack Developer', 'DevOps', 'Hardware'];
$speaker->wannaBe = 'Master of the world';
$speaker->talkSpeed = 1.2;
$speaker->save();
```

### focus
<p class="fragment text-left text-07">Know better Laravel Notification System</p>

---
@title[101]
### 101
```php
class User
{
    use Notifiable; // Magic trait
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

    public function via($notifiable)
    {
        // Custom logic to inject channels
        return ['mail','database','broadcast'];
    }

    public function toMail($notifiable)
    {
        return (new Illuminate\Notifications\Messages\MailMessage)->view(
        'email_template', [
            // view params
        ])->subject('mail subject');
    }
    
    public function toArray($notifiable)
    {
        return [
            // Notification map
        ];
    }
    // Additional custom formatters
}
```
---
@title[channels]
### channels

---
@title[custom channels]
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

    public function send(Driver $notifiable, BaseNotification $notification)
    {
        $message = $notification->toSms($notifiable);

        $sanitizedNumber = str_replace("-", "", $notifiable->mobile_phone);

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
+++
```php
$this->app->bind(SmsSender::class, function () {
    $currentDriver = config('sms.default');
        switch (config('sms.' . $currentDriver . '.driver')) {
            case 'twilio':
                return new \Ennetech\SmsSender\Providers\Twilio(config('sms.' . $currentDriver));
                break;
            case 'voipcheap':
                return new \Ennetech\SmsSender\Providers\Voipcheap(config('sms.' . $currentDriver));
                break;
            default:
                return new \Ennetech\SmsSender\Providers\Log();
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
---
@title[queue]
### queue
---
@title[Questions]
# Questions?
![QR](assets/img/qr.png)
<p style="text-align: center !important;">https://joind.in/talk/80680</p>
---
@title[hiring]
![fleet](assets/img/fleetsmall.jpg)
