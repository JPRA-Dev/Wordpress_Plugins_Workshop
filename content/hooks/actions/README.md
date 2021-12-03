# Actions

Developers can create a custom Action using the Action API to add or remove code from an existing Action by specifying any existing Hook. This process is called “hooking”.

## 1) Actions and filters: two names, one goal ?

If you take a peek into the core, you will see that add_action is actually a wrapper function for `add_filter()` in `wp-includes/plugin.php`:

```
function add_action( $hook_name, $callback, $priority = 10, $accepted_args = 1 ) {
	return add_filter( $hook_name, $callback, $priority, $accepted_args );
}
```

* `$hook_name` the name of the action to add the callback to
* `$callback` The callback to be run when the action is called
* `$priority` used to specify the order in which the functions associated with a particular action are executed
* `$accepted_args` the number of arguments the function accepts

WordPress defines [add_filter](https://developer.wordpress.org/reference/functions/add_filter/) as "Hooks a function to a specific filter action," and add_action as "Hooks a function on to a specific action."

We can say actions are filters that do not return any value. 

## 2) Action and action's callbacks

Developers can create a custom Action using the Action API to add or remove code from an existing Action by specifying any existing Hook. This process is called “hooking”.

```
do_action('wp_head')
# creates an action hook

add_action('wp_head','custom_register', int $priority = 10, int $accepted_args = 1 );
# when do_action('wp_head') is called WP execute custom_register() as well
```
