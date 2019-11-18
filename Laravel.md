# Asset Function

        function asset($path, $secure = null){
                if ($_SERVER['HTTP_HOST']=='127.0.0.1:8000')
                    return app('url')->asset($path, $secure);
                if ($_SERVER['HTTP_HOST']=='localhost:8000')
                    return app('url')->asset($path, $secure);
                else
                    return app('url')->asset('public/'.$path, $secure);
          }
    
    
   # Foregin Key
    
`
            $table->foreign('attendance_id')->references('id')->on('attendances');`


# Route Groput with Prefix

        Route::prefix('admin')->group(function() {
            Route::get('/', 'AdminController');
        });

# common Facker Object
        randomElement($array = array ('a','b','c')) // 'b' ref:  https://github.com/fzaninotto/Faker

# upload Single image file
        if ($request->file('image')){
            $file = $request->file('image');
                $photo = time().$file->getClientOriginalName();
                $destination =public_path() . '/uploads/category';
                $file->move($destination, $photo);
                $category->image= $photo;
        }
# upload multiple images 
        $images=array();

        if($files=$request->file('images')){
            foreach($files as $file){
                $name= time().$file->getClientOriginalName();
                $destination =public_path() . '/uploads/products';
                $file->move($destination,$name);
                $images[]=$name;
            }
        }
        ImageProduct::insert( [
            'image'=>  implode("|",$images),
            'product_id' =>$product->id,
        ]);
 
 # Search with relational Tables
         $user = User::with('Profile')->where('status', 1)->whereHas('Profile', function($q){
            $q->where('gender', 'Male');
        })->get();
        
 #Api with additional data
 
        return TransactionLineResource::collection($result)->additional(['extra' => [
            'balance' => $debit-$credit,
            'debit' => $debit,
            'credit' => $credit,
        ]]);
        
 #Api with additional data
        public static function boot()
            {
                parent::boot();
                self::creating(function ($model) {
                    if (empty($model->id)) {
                        $model->id = Str::uuid()->toString();
                    }
                });
            }
