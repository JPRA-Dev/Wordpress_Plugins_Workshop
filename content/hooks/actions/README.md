# Actions

Developers can create a custom Action using the Action API to add or remove code from an existing Action by specifying any existing Hook. This process is called “hooking”.

Actions are used to executed custom functions after an event occurs which an action executed by the WP core. They are initialized by the do_action( 'action_name' ) function within the WordPress code.

## 1) Actions and filters: two names, one goal ?

If you take a peek into the core, you will see that add_action is actually a wrapper function for add_filter (wp-includes/plugin.php):

```
function add_action( $hook_name, $callback, $priority = 10, $accepted_args = 1 ) {
	return add_filter( $hook_name, $callback, $priority, $accepted_args );
}
```
Filters should filter information, thus receiving information/data, applying the filter and returning information/data, and then used. However, filters are still action hooks.

WordPress defines [add_filter](https://developer.wordpress.org/reference/functions/add_filter/) as "Hooks a function to a specific filter action," and add_action as "Hooks a function on to a specific action."

## 2) Actions and callbacks: do_action() & add_action()

```
do_action('wp_head')
# creates an action hook

add_action('wp_head','custom_register', int $priority = 10, int $accepted_args = 1 );
# when do_action('wp_head') is called WP execute custom_register() as well
```

Developers can create a custom Action using the Action API to add or remove code from an existing Action by specifying any existing Hook. This process is called “hooking”.

#### $hook_name
(string) (Required) The name of the action to add the callback to.

#### $callback
(callable) (Required) The callback to be run when the action is called.

#### $priority
(int) (Optional) Used to specify the order in which the functions associated with a particular action are executed. Lower numbers correspond with earlier execution, and functions with the same priority are executed in the order in which they were added to the action.

Default value: 10

#### $accepted_args
(int) (Optional) The number of arguments the function accepts.

Default value: 1
