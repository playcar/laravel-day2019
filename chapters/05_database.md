@title[database channel]
### database channel
+++
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Notification extends Model
{
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
                                ->where("created_ad","<",Carbon::now())
                                ->whereNull('read_at')
                                ->get();
```