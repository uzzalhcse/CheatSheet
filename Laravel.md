# Asset Function

`function asset($path, $secure = null){
        if ($_SERVER['HTTP_HOST']=='127.0.0.1:8000')
            return app('url')->asset($path, $secure);
        if ($_SERVER['HTTP_HOST']=='localhost:8000')
            return app('url')->asset($path, $secure);
        else
            return app('url')->asset('public/'.$path, $secure);
    }
    `
    
    
   # Foregin Key
    
`
            $table->foreign('attendance_id')->references('id')->on('attendances');`


# Route Groput with Prefix
`
Route::prefix('admin')->group(function() {
    Route::get('/', 'AdminController');
});`

# common Facker Object
`randomElement($array = array ('a','b','c')) // 'b' ref: ` https://github.com/fzaninotto/Faker

# upload Single image file
        if ($request->file('image')){
            $file = $request->file('image');
                $photo = time().$file->getClientOriginalName();
                $destination =public_path() . '/uploads/category';
                $file->move($destination, $photo);
                $category->image= $photo;
        }
