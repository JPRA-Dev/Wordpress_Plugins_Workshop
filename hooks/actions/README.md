# Actions

Les actions sont utilisées pour exécuter des fonctions personnalisées à un moment précis de l’exécution du cœur de WordPress.
Les actions sont définies / créées par la fonction do_action( 'action_name' ) dans le code de WordPress.

## actions == filters ?

If you take a peek into the core, you will see that add_action is actually a wrapper function for add_filter:

wp-includes/plugin.php
```
function add_action( $hook_name, $callback, $priority = 10, $accepted_args = 1 ) {
	return add_filter( $hook_name, $callback, $priority, $accepted_args );
}
```

## do_action() & add_action()

When you add_action('wp_head','custom_register'), you are telling PHP that when do_action('wp_head') is called, to call custom_register() as well. 
