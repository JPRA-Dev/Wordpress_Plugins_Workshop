
   1) add_action()
   2) add_option_page(
		Title in tab, 
		name in the menu, 
		permissions,
		slug,
		output HTML function
	  )
   3) function output HTML definition
   4) define a class to avoid unique name requirement issues
   5) instanciate/declare with the class name
   §) in __construct() the 1) add_action
   7) replace the callback name in add_action with array(object, b)
   8) go an see wp_options tab cause we want to add stuff here set add_action('admin_init') with the callback of the key value settings
   9) register_settings_section in the 'settings' function 
   10) sanitize
