
    01. Installation
    02. What are they? Plug in the core - Plugins and themed-plugins
    03. The plugin library

# A bit of context

A WordPress plugin is essentially one or more functions defined in PHP files, as PHP is the main scripting language powering WordPress. It also typically has 3 other components:  
* hooks (action hooks and filter hooks) 
* shortcodes 

These are the main elements of WordPress plugin development.

When WordPress gets updated to a new version, it overrides its core files. Because of this, if you add a custom functionality to a WordPress site by directly modifying the WordPress core, your changes will be wiped out upon WordPress upgrade. This leads to one of the core WordPress development concepts – any functionality you want to add or modify should be done using plugins.

A WordPress plugin is essentially one or more functions defined in PHP files, as PHP is the main scripting language powering WordPress. It also typically has 3 other components:  hooks (action hooks and filter hooks), shortcodes and widgets. These are the main elements of WordPress plugin development.

## Plug-in the core

## Plugins and theme-plugins

### wp-content/plugins/
### functions.php

## Key Steps

1) Define the Requirements
2) Create a WordPress Plugin Directory Structure
3) Configure your Plugin
4) Add Functionality to your Plugin

```
<?php 
/** 
 *
 * Plugin Name: Hello World 
 * Plugin URI: https://mysite.com/hello-world 
 * Description: Prints "Hello World" in WordPress admin. 
 * Version: 0.0.1 
 * Author: Your Name 
 * Author URI: https://mysite.com 
 * Text Domain: hello-world 
 * License: GPL v2 or later 
 * License URI: http://www.gnu.org/licenses/gpl-2.0.txt 
 *
 */
?>
```
