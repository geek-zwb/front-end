> 设置如下
## 服务端
```
# 创建中间件 
php artisan make:middle EnableCorssRequestMiddleware

# EnableCorssRequestMiddleware 中设置 header
# app/Http/Middleware/EnableCrossRequestMiddleware.php
public function handle($request, Closure $next)
{
    $response = $next($request);
    $response->header('Access-Control-Allow-Origin', '*')
        ->header('Access-Control-Allow-Headers', 'Origin, X-Requested-With, Authorization, Content-Type, Cookie, Accept')
        ->header('Access-Control-Allow-Methods', 'GET, POST, PATCH, PUT, DELETE, OPTIONS');
    return $response;
}

# 注册中间件
# app/Http/Kernel.php
protected $middleware = [
    // ...
    \App\Http\Middleware\EnableCrossRequestMiddleware::class,
];
// ...
protected $middlewareGroups = [
    'web' => [
        // ...
    ],
    
    'api' => [
        // ...
        
        // 注意 这里写的 cors 为下边 $routeMiddleware 数组中设置 EnableCrossRequestMiddleware 的键名
        'cors'
    ],
];
// ...
protected $routeMiddleware = [
    // ...
    'cors' => \App\Http\Middleware\EnableCrossRequestMiddleware::class,
];
```

## 前端
我用的 axios 库， 好像并没有要额外设置啥？
```
# 这个也不用
axios.defaults.headers['Content-Type'] = 'application/x-www-form-urlencoded';
```
> 后续有问题再补充
