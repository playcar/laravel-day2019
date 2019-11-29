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
    // TODO: Check this
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
                                ->where("created_at","<",Carbon::now()) // TODO: remove this
                                ->whereNull('read_at')
                                ->get();
```