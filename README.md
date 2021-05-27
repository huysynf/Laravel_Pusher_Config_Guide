# laravel-pusher-channel

# khi mà thao tác với private channel thì sẽ cần phải access point các cách:
# cách 1 Thêm đoặn code 

```javascript

 Pusher.logToConsole = true;

    var pusher = new Pusher(window.PUSHER_KEY, {
        cluster: 'ap1',
        encrypted: true,
        authEndpoint: `{{config('app.url')}}/pusher/auth`, // just a helper method to create a link
        auth: {
            headers: {
                'X-CSRF-Token':`{{csrf_token()}}` // CSRF token
            }
        }
    });

    var channelName =  `private-sent-notification-for-create-new-quiz-report-channel.${USER_CURRENT.id}`; // channel for the user id
    var channel = pusher.subscribe(channelName);

    channel.bind('sent-notification-for-create-new-quiz-report-event', function (data)
    {
        console.log(data); // send the notification in the JS app
    });
```

```php 
Route::post('pusher/auth', function (\Illuminate\Http\Request $request){
    $user = auth()->user();

    if ($user) {
        $pusher = new Pusher\Pusher(config('broadcasting.connections.pusher.key'), config('broadcasting.connections.pusher.secret'), config('broadcasting.connections.pusher.app_id'));
        echo $pusher->socket_auth(request()->input('channel_name'), request()->input('socket_id'));
        return;
    }else {
        header('', true, 403);
        echo "Forbidden";
        return;
    }
});

```
# cách 2 thêm gauth vào private channel ví dụ 

```php

Broadcast::channel('channel', function() {
    // ...
}, ['guards' => ['web', 'admin']]);


Broadcast::channel('sent-notification-for-create-new-quiz-report-channel.{quizAuthorId}',
    function ($user, $quizAuthorId) {
        return $user->id == $quizAuthorId;
    },['guards' => ['web']]);
```
