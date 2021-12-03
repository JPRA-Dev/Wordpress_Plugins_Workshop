# Shortcodes

## What is a shortcode?

WordPress shortcodes are a feature that allows you to do cool stuff with little effort. You can do just about anything with them. They are those little pieces of code in square brackets. They work a bit like a shortcut, bridging the gap between the WordPress editor and the PHP function behind it. The advantage of these little pieces of code is that they are generally customizable and don't require a lot of development knowledge.

There are two main types of short code in WordPress


![alt](https://kinsta.com/fr/wp-content/uploads/sites/4/2019/12/codes-courts-auto-fermeture-fermeture-peuvent-valides-sans-attributs.png)


## Usage

In your wordpress folder go to "wp-content -> plugin" and create a folder called "Mes-shortecodes" in it create a file called index.php.



```php
<?php
/*
  Plugin Name: mes shortcodes
  Description: Plugin fournissant des shortcodes
  Author: gael
  Version: 1.0.0
 */

function box_shortcode( $atts, $content = null )
{
    extract( shortcode_atts( array(), $atts ) );

      return "
		<style type='text/css'>
		.box_shortcode{
			border: 2px solid;
			padding:10px;
		}
		</style>

      <div class="."box_shortcode"." style="."color:" . $atts['color'] ." style="."border:" . $atts['border'].">" . $content . '</div>';

}
add_shortcode('box', 'box_shortcode');

```

