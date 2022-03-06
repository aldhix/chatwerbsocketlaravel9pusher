# Chat Websocket Laravel 9 & Pusher

### Install laravel 
```
composer create-project laravel/laravel example-app
```
### Create app Pusher Chanel
[pusher.com](https://pusher.com/)

### Install Pusher : 
```
composer require pusher/pusher-php-server
```

### Install Laravel Echo  :
```
npm install --save-dev laravel-echo pusher-js
```
```
npm i
```


### Setup .env 
```
BROADCAST_DRIVER=pusher
…....
…....
…....
PUSHER_APP_ID=*****
PUSHER_APP_KEY=*********
PUSHER_APP_SECRET=*********
PUSHER_APP_CLUSTER=***
```

### Create event ChatEvent :
```
php artisan make:event ChatEvent
```

### Setup class ChatEvent :
```
class ChatEvent implements ShouldBroadcast
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $message;

    public function __construct($message)
    {
        $this->message = $message;
    }

    public function broadcastOn()
    {
        return new Channel('channel-chat');
    }
}
```

### Setup welcome.blade.php :
```
<!doctype html>
<html lang="en">
  <head>
    <title>Chat Room</title>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
  </head>
  <body>
    <div class="container">
        <div class="row mt-3">
            <div class="col-6 offset-3">
                <div class="card">
                    <div class="card-header">
                        Chat Room
                    </div>
                    <div class="card-body">
                        <div class="form-group">
                            <input type="text" class="form-control" placeholder="Name" id="name">
                        </div>
                        <div class="form-group" id="data-message">

                        </div>
                        <div class="form-group">
                            <textarea type="text" class="form-control" placeholder="Message" id="message"></textarea>
                        </div>
                        <div class="form-group">
                            <button class="btn btn-block btn-primary">Send</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    <script src="{{ url('js/app.js') }}"></script>
    <script>
    $(function(){
        const Http = window.axios;
        const Echo = window.Echo;

        let name = $('#name');
        let message = $('#message');

        $('input, textarea').keyup(function(){
            $(this).removeClass('is-invalid');
        });

        $('button').click(function(){
            if (name.val() == '') {
                name.addClass('is-invalid');
            } else if ( message.val() == '') {
                message.addClass('is-invalid');
            } else {
                Http.post("{{ url('send') }}", {
                    'name': name.val(),
                    'message':message.val()
                }).then(()=>{
                    message.val('');
                })
            }
        });

        var channel = Echo.channel('channel-chat');
        channel.listen('ChatEvent', function(data) {
            $('#data-message')
            .append(`<strong>${data.message.name}</strong> : ${data.message.message} <br>`);
        });

    })
    </script>
  </body>
</html>
```

### Setup Route :
```
use App\Events\ChatEvent;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('welcome');
});

Route::post('send', function(Request $request){
    $request->validate([
        'name'=>'required',
        'message'=>'required'
    ]);

    $message = [
        'name'=>$request->name,
        'message'=>$request->message,
    ];

    ChatEvent::dispatch($message);
});
```


### Setup resrouces/js/bootstrap.js
Uncomment laravel Echo

### Run Dev
```
npm run dev
```

### Run Server
```
php artisan serve
```
