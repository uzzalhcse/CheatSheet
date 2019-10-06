# Add this code into composer

    "tymon/jwt-auth": "^1.0.0-rc.5"


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
