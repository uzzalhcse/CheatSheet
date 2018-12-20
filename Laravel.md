#Asset Function

`function asset($path, $secure = null){
        if ($_SERVER['HTTP_HOST']=='127.0.0.1:8000')
            return app('url')->asset($path, $secure);
        if ($_SERVER['HTTP_HOST']=='localhost:8000')
            return app('url')->asset($path, $secure);
        else
            return app('url')->asset('public/'.$path, $secure);
    }
    `
    
    
    #Foregin Key
    
`
            $table->foreign('attendance_id')->references('id')->on('attendances');`
