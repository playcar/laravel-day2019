@title[database channel]
### database channel
Important channel if in your application is present a "notification history" feature where a user can see all past notifications or if is needed to log the notification for future inspection
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

    $unreadNotifications = Notification::
        whereNotifiableType(User::class)
        ->whereNotifiableId(1)
        ->whereNull('read_at')
        ->get();
```