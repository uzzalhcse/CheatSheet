
# Add this code into composer

    "tymon/jwt-auth": "^1.0.0-rc.5.1"


# Run those command

    composer update
    php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"
    php artisan jwt:secret

# Update your User model

    <?php

    namespace App;

    use Tymon\JWTAuth\Contracts\JWTSubject;
    use Illuminate\Notifications\Notifiable;
    use Illuminate\Foundation\Auth\User as Authenticatable;

    class User extends Authenticatable implements JWTSubject
    {
        use Notifiable;

        // Rest omitted for brevity

        /**
         * Get the identifier that will be stored in the subject claim of the JWT.
         *
         * @return mixed
         */
        public function getJWTIdentifier()
        {
            return $this->getKey();
        }

        /**
         * Return a key value array, containing any custom claims to be added to the JWT.
         *
         * @return array
         */
        public function getJWTCustomClaims()
        {
            return [];
        }
    }
# Configure Auth guard
Inside the `config/auth.php` file you will need to make a few changes to configure Laravel to use the `jwt` guard to power your application authentication.
  
	  'defaults' => [
	        'guard' => 'api',
	        'passwords' => 'users',
	    ],
    'guards' => [
        'api' => [
            'driver' => 'jwt',
            'provider' => 'users',
        ],
    ],
 # Add some basic authentication routes
 First let's add some routes in `routes/api.php` as follows:
     
     Route::group([

    'middleware' => 'api',
    'prefix' => 'auth'

    ], function ($router) {

        Route::post('login', 'AuthController@login');
        Route::post('logout', 'AuthController@logout');
        Route::post('refresh', 'AuthController@refresh');
        Route::post('me', 'AuthController@me');

    });
# Create the AuthController

    php artisan make:controller AuthController
    
   Then add the following:
   
	<?php

	namespace App\Http\Controllers;

	use App\User;
	use Illuminate\Http\Request;

	class AuthController extends Controller
	{
	    /**
	     * Create a new AuthController instance.
	     *
	     * @return void
	     */
	    public function __construct()
	    {
		$this->middleware('jwt', ['except' => ['login']]);
	    }

	    /**
	     * Get a JWT via given credentials.
	     *
	     * @return \Illuminate\Http\JsonResponse
	     */
	    public function login()
	    {
		$credentials = request(['email', 'password']);

		if (! $token = auth()->attempt($credentials)) {
		    return response()->json(
			[
			    'code'=>401,
			    'status'=>'error',
			    'message' => 'Unauthorized'
			]
			, 401);
		}

		return $this->respondWithToken($token);
	    }

	    /**
	     * Get the authenticated User.
	     *
	     * @return \Illuminate\Http\JsonResponse
	     */
	    public function me()
	    {
		if (auth()->check()){
		    return response()->json(
			[
			    'code'=>200,
			    'status'=>'success',
			    'message'=>'User Info Fetched',
			    'data'=> [
				'user' => User::with('Roles')->find(auth()->user()->id)
			    ]
			]
		    );
		}

		return response()->json(
		    [
			'code'=>401,
			'status'=>'error',
			'message' => 'Unauthorized'
		    ]
		    , 401);
	    }

	    /**
	     * Log the user out (Invalidate the token).
	     *
	     * @return \Illuminate\Http\JsonResponse
	     */
	    public function logout()
	    {
		auth()->logout();

		return response()->json(
		    [
			'code'=>200,
			'status'=>'success',
			'message' => 'Successfully logged out'
		    ]
		);
	    }

	    /**
	     * Refresh a token.
	     *
	     * @return \Illuminate\Http\JsonResponse
	     */
	    public function refresh()
	    {
		return $this->respondWithToken(auth()->refresh());
	    }

	    /**
	     * Get the token array structure.
	     *
	     * @param  string $token
	     *
	     * @return \Illuminate\Http\JsonResponse
	     */
	    protected function respondWithToken($token)
	    {
		return response()->json([
		    'code'=>200,
		    'status'=>'success',
		    'message'=>'Login Success',
		    'data'=> [
			'token' => $token,
			'token_type' => 'bearer',
		    ]
		]);
	    }
	}

# Exception handling
replace following code  in `Exceptions/Handler.php` 

    public function render($request, Exception $exception)
        {
            if ($exception instanceof TokenInvalidException){
                return response()->json(
                    [
                        'code'=>400,
                        'status'=>'error',
                        'message' => 'Token Invalid'
                    ]
                );
            }
            elseif ($exception instanceof TokenExpiredException){
                return response()->json(
                    [
                        'code'=>400,
                        'status'=>'error',
                        'message' => 'Token Expired'
                    ]
                );
            }
            elseif ($exception instanceof JWTException){
                return response()->json(
                    [
                        'code'=>400,
                        'status'=>'error',
                        'message' => 'There is Problem With Your Token'
                    ]
                );
            }
            return parent::render($request, $exception);
        }
# Add Jwt Middleware

    php artisan make:middleware JWT
replace following code in 

> App\Http\Middleware\JWT.php

    
    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Tymon\JWTAuth\Facades\JWTAuth;

    class JWT
    {
        /**
         * Handle an incoming request.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @return mixed
         */
        public function handle($request, Closure $next)
        {
            JWTAuth::parseToken()->authenticate();
            return $next($request);
        }
    }

To use the middlewares you will have to register them in `app/Http/Kernel.php` under the `$routeMiddleware` property:

    'jwt' => \App\Http\Middleware\JWT::class,
