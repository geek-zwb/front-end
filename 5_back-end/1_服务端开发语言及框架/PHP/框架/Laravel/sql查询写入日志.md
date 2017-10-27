app/Providers/AppServiceProvider.php
```
public function boot()
{
    // ...
    
    DB::listen(function ($query) {
        // $query->sql
        // $query->bindings
        // $query->time
        Log::info('sql: '.$query->sql."\r\n query_time: ".$query->time);
    });
}