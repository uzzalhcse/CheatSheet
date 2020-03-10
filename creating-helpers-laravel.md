# Using Composer to Autoload Files

First one is pretty easy and straightforward. Simply go to composer.json file located in your Laravel project and you will see autoload key. Composer has a files key (an array of file paths that you want to autoload) that you can use in your autoload key. It will look like this:
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
