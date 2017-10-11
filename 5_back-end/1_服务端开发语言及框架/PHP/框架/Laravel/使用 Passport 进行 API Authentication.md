Laravel 自带的 passport 包用于API 认证， 如 OAuth2, JWT 等。

在下面这个实例中，我们将 Passport Package 用于 `user auth via api`.

你将得到：
1. Login API
2. Register API
3. Details API

## `Step1: Install Laravel`

首先，我们创建一个名为 blog 的 laravel 的新项目。
```
composer create-project --prefer-dist laravel/laravel blog
```

## `Step2: Install Passport`

进入 blog 文件夹， run bellow command
```
composer require laravel/passport
```

成功安装 passport 包之后， 注册 Passport 

`config/app.php`
```
'providers' => [
	// ....
	Laravel\Passport\PassportServiceProvider::class,
],
```

## `Step3: 生成数据表`
```
# 生成数据表
php artisan migrate

# 
php artisan passport:install
```

## Passport 配置
包括以下三个部分
- model 增加 HasApiTokens class
- AuthServiceProvider 注册认证相关路由
- auth.php 

`app/User.php`
```
<?php

namespace App;

use Laravel\Passport\HasApiTokens;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    //==== 增加 passport 的 HasApiTokens class
    use HasApiTokens, Notifiable;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name', 'email', 'password',
    ];

    /**
     * The attributes that should be hidden for arrays.
     *
     * @var array
     */
    protected $hidden = [
        'password', 'remember_token',
    ];
}
```

`app/Providers/AuthServiceProvider.php`
```
<?php

namespace App\Providers;

use Laravel\Passport\Passport;
use Illuminate\Support\Facades\Gate;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

class AuthServiceProvider extends ServiceProvider
{
    /**
     * The policy mappings for the application.
     *
     * @var array
     */
    protected $policies = [
        'App\Model' => 'App\Policies\ModelPolicy',
    ];

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();
        
        //===== 注册所需路由
        Passport::routes();
    }
}
```

`config/auth.php`
```
<?php

return [
    // .....
    'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],
        'api' => [
            // 驱动改为 passport
            'driver' => 'passport',
            'provider' => 'users',
        ],
    ],
   // .....
]
```

## 创建API路由
`routes/api.php`
```
Route::post('login', 'API\UserController@login');
Route::post('register', 'API\UserController@dregister');

Route::group(['middleware' => 'auth:api'], function() {
    Route::post('details', 'API\UserController@details'); 
});
```

## 创建控制器
`app/Http/Controllers/API/UserController.php`
```
<?php

namespace App\Http\Controllers\API;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use App\User;
use Illuminate\Support\Facades\Auth;
use Validator;

class UserController extends Controller
{

    public $successStatus = 200;

    /**
     * login api
     *
     * @return \Illuminate\Http\Response
     */
    public function login(){
        if(Auth::attempt(['email' => request('email'), 'password' => request('password')])){
            $user = Auth::user();
            $success['token'] =  $user->createToken('MyApp')->accessToken;
            return response()->json(['success' => $success], $this->successStatus);
        }
        else{
            return response()->json(['error'=>'Unauthorised'], 401);
        }
    }

    /**
     * Register api
     *
     * @return \Illuminate\Http\Response
     */
    public function register(Request $request)
    {
        $validator = Validator::make($request->all(), [
            'name' => 'required',
            'email' => 'required|email',
            'password' => 'required',
            'c_password' => 'required|same:password',
        ]);

        if ($validator->fails()) {
            return response()->json(['error'=>$validator->errors()], 401);            
        }

        $input = $request->all();
        $input['password'] = bcrypt($input['password']);
        $user = User::create($input);
        $success['token'] =  $user->createToken('MyApp')->accessToken;
        $success['name'] =  $user->name;

        return response()->json(['success'=>$success], $this->successStatus);
    }

    /**
     * details api
     *
     * @return \Illuminate\Http\Response
     */
    public function details()
    {
        $user = Auth::user();
        return response()->json(['success' => $user], $this->successStatus);
    }
}
```

## 测试
register
```
// ...
注册成功会返回一个 $accessToken
```

detail
```
# 请求 detail api， 需要设置两个头部信息

'headers' => [
    'Accept' => 'application/json',
    'Authorization' => 'Bearer '.$accessToken,
]
```
