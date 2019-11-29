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
    $currentConfig = config('sms.' . $currentDriver);
    switch (config('sms.' . $currentDriver . '.driver')) {
        case 'twilio':
            return new Twilio($currentConfig);
            break;
        case 'voipcheap':
            return new Voipcheap($currentConfig);
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