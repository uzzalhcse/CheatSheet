# Using Composer to Autoload Files

First one is pretty easy and straightforward. Simply go to `composer.json` file located in your Laravel project and you will see autoload key. Composer has a files key (an array of file paths that you want to autoload) that you can use in your autoload key. It will look like this:

	"autoload": {
	    "files": [
		"app/Helpers/Helper.php"
	    ],
	    "classmap": [
		"database/seeds",
		"database/factories"
	    ],
	    "psr-4": {
		"App\\": "app/"
	    }
	},
After changing composer.json file and adding a new path to the files array, you need to dump the autoloader. Simply run this command from the terminal in your Laravel project directory.

	composer dump-autoload
Now your helper file will be automatically loaded in your Laravel project.


# Using service providers to load file
Let’s see how you can use service providers to autoload Helper file. Go to the command line in the root of your application and run the following command to create a new service provider.


	php artisan make:provider HelperServiceProvider
You will see this message logged onto your console screen

Provider created successfully.
Once the service provider is created successfully, open that file. In the register method require your Helper files.

	public function register()
	{
	    $file = app_path('Helpers/Helper.php');
	    if (file_exists($file)) {
		require_once($file);
	    }
	}
In the register method, we include our dependencies. In a large-scale project, it is possible that you have multiple helper files in a directory and you want to require all of them. You could change the register method like given below and your service provider will load all of the files in the Helpers directory.

	public function register()
	{
	    foreach (glob(app_path() . '/Helpers/*.php') as $file) {
		require_once($file);
	    }
	}
It will require all of the files present in the `app/Helpers` directory.
Now that our service provider is completed, we need to register our service provider, so, that Laravel will load it during bootstrapping. For this, go to `config/app.php` and add the following line in the providers array at the end.


	App\Providers\HelperServiceProvider::class,
If your helper file involves a class that has those helper methods and you have specified namespace, you could use them with little effort by defining an alias. You can do that easily by adding the following at the end of the aliases array in `config/app.php` file.


	'Helper' => App\Helpers\Helper::class,
By adding this in the alias array, you will be able to call your helpers by using the Helper keyword. That’s all for creating your helpers with service providers.
