## Basic
```
DB::transaction(function()
{
    $newAcct = Account::create([
        'accountname' => Input::get('accountname')
    ]);

    $newUser = User::create([
        'username' => Input::get('username'),
        'account_id' => $newAcct->id,
    ]);
});

//下面是laravel对transaction的底层函数实现
 public function transaction(Closure $callback)
    {
            $this->beginTransaction();

            // We'll simply execute the given callback within a try / catch block
            // and if we catch any exception we can rollback the transaction
            // so that none of the changes are persisted to the database.
            try
            {
                    $result = $callback($this);

                    $this->commit();
            }

            // If we catch an exception, we will roll back so nothing gets messed
            // up in the database. Then we'll re-throw the exception so it can
            // be handled how the developer sees fit for their applications.
            catch (\Exception $e)
            {
                    $this->rollBack();

                    throw $e;
            }

            return $result;
    }
    

//抛出错误用法
DB::transaction(function()
{
    $newAcct = Account::create([
        'accountname' => Input::get('accountname')
    ]);

    $newUser = User::create([
        'username' => Input::get('username'),
        'account_id' => $newAcct->id,
    ]);

    if( !$newUser )
    {
        throw new \Exception('User not created for account');
    }
});
```

## Advanced Transactions in Laravel
```
// Start transaction!
DB::beginTransaction();

try {
    // Validate, then create if valid
    $newAcct = Account::create( ['accountname' => Input::get('accountname')] );
} catch(ValidationException $e)
{
    // Rollback and then redirect
    // back to form with errors
    DB::rollback();
    return Redirect::to('/form')
        ->withErrors( $e->getErrors() )
        ->withInput();
} catch(\Exception $e)
{
    DB::rollback();
    throw $e;
}

try {
    // Validate, then create if valid
    $newUser = User::create([
        'username' => Input::get('username'),
        'account_id' => $newAcct->id
    ]);
} catch(ValidationException $e)
{
    // Rollback and then redirect
    // back to form with errors
    DB::rollback();
    return Redirect::to('/form')
        ->withErrors( $e->getErrors() )
        ->withInput();
} catch(\Exception $e)
{
    DB::rollback();
    throw $e;
}

// If we reach here, then
// data is valid and working.
// Commit the queries!
DB::commit();
```