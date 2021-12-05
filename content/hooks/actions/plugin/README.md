++ page on admin menu first
++ settings 
++ ifwrap + createHTML -- > refere to our defined options and what should be output before content displaying
Everything you'll find within those mds helped us to build what you see so It will surely help you retrieve your way for each steps
Hooks & APIs

add_action()
add_filter()

hooks--
admin_menu
admin_init 
the_content // filter

conditionals--
is_main_query()
is_single()

get_option() // trigger an option ?
esc_html() // filters a string cleaned and escaped for output in HTML 

add_options_page() // add submenu page to the Settings main menu.

settings_fields() // output nonce, action, and option_page fields for a settings page defined by the $arg being the settings group
do_settings_sections() // output all the sections and fields that were added to that $page with add_settings_section() and add_settings_field()
submit_button() // output submit button

add_settings_section() // register a settings error to be displayed to the user.

add_settings_field() // Part of the Settings API. Use this to define a settings field that will show as part of a settings section inside a settings page. The fields are shown using do_settings_fields() in do_settings-sections()

register_setting() // whitelist db for updating saving
add_settings_error() 

checked() // if the two arguments are identical -- marked as checked
esc_attr() // escape within attribute value
selected() // if the two arguments are identical -- marked as selected


adminPage()
ourHTML()
settings()

ifWrap($content)
createHTML($content)

sanitizeLocation($input)

checkboxHTML($args) 
headlineHTML() 
locationHTML() 

index.php + 
```
function __construct()
	{
		add_action('admin_menu', array($this, 'adminPage')); //container - fires before the loading of admin menu
		add_action('admin_init', array($this, 'settings')); //content -  within admin area but after admin_menu
		add_filter('the_content', array($this, 'ifWrap')); //reference
	}

	function ifWrap($content) {
		if (is_main_query() AND is_single() AND (
			get_option('wcp_wordcount', '1') OR 
			get_option('wcp_charcount', '1') OR
			get_option('wcp_readtime', '1')
		)) {
			return $this->createHTML($content);
		}
	}

	function createHTML($content){
		$html = '<h3>'. esc_html(get_option('wcp_headline', 'Post Statistics')) . '</h3><p>';

		if(get_option('wcp_wordcount', '1') OR get_option('wcp_readtime', '1')) {
			$wordCount = str_word_count(strip_tags($content)); 
		}

		if(get_option('wcp_wordcount', '1')) {
			$html .= 'This post has ' . $wordCount . ' words.<br>'; 
		}

		if(get_option('wcp_charcount', '1')) {
			$html .= 'This post has ' . strlen(strip_tags($content)) . ' characters.<br>'; 
		}

		if(get_option('wcp_readtime', '1')) {
			$html .= 
			'This post will take about ' . 
			round($wordCount/255) . 
			' minute' . (round($wordCount/255) > 1 ? 's' : '') . 
			' to read.<br>'; 
		}

		$html .= '</p>';

		if(get_option('wcp_location', '0') == '0')
		{
			return $html . $content;
		}
		return $content . $html;
	}
   $wordCountAndTimePlugin = new WordCountAndTimePlugin();
   ```
# Word Count Plugin Development

Previously we saw how to set up a filter with the helps of multiple WP APIs. We're going to abord a step offering more flexibility and control client-side regarding the featues. 

We are going to develop a word count plugin but you'll see that it is more of a pretext to discover multiple hooks and demistfying WordPress mecanics the more you know the tools the best will be its used.

## Development

We need to make our plugin accessible as soon as the admin panel is loaded

```
function __construct() // Everything is executed as soon as the class is instanciated
{
	add_action('admin_menu', array($this, 'adminPage'));
}
```
1) Create a class in order to set a context resolving function's name conflict issues
```
class WordCountAndTimePlugin {}
```

2) How to make our features available in the settings menu within the admin panel
```
function adminPage()
{
   add_options_page(
      'Word Count Settings',            // title of the page
      'Word Count',                     // name displayed within the settings menu
      'manage_options',                 // access control based on user's permissions
      'word-count-settings-page',       // slug
      array($this, 'ourHTML')           // referencing 
   );
}
```

3) define the HTML of the settings page and output fields
``` 
function ourHTML() { ?>
   <div class='wrap'>
      <h1>Word Count Settings</h1>
      <form action='options.php' method='POST'>
         <?php
            settings_fields('wordcountplugin');                 
            do_settings_sections('word-count-settings-page');   
            submit_button();                                    
         ?>
      </form>
   </div>
<?php } 
```

4) Definition of fields and of register setting within wp-admin/option.php
```
function settings()
{
   add_settings_section(
      'wcp_first_section',          // slug-id of the settings section
      null, //subtitle              // heading line at the top the section
      null, //paragraph             // echoes between heading and fields
      'word-count-settings-page'    // slug of the section's page
   );

   /*
   ** add_settings_field() reserves a place for our setting within the HTML page definied in add_options_page()
   ** Definition of the dropdown enabling us to choose the position of the fields regarding the the article
    */
   add_settings_field(              // definition of a field within the page
      'wcp_location',               // definition of the field name
      'Display Location',           // HTML title 
      array($this, 'locationHTML'), // callback of the field's HTML
      'word-count-settings-page',   // slug of the section's page
      'wcp_first_section'           // reference to the field section define above
   );
   
    /*
   ** Unable the the saving and updating of the option within the wp-options table in database but sanitize before
    */
   register_setting(                // register a settings and its data
      'wordcountplugin',            // option group (meaning the settings?)
      'wcp_location',               // option name 
      array(                        // security layer defined accepted data in the dropdown
         'sanitize_callback' => array($this, 'sanitizeLocation'), 
         'default' => '0' // default value when calling get_option()
      )
   );

   /*
   ** Definition of the client-side ouput headline
    */
   add_settings_field(
      'wcp_healine', 
      'Headline Text', 
      array($this, 'headlineHTML'), 
      'word-count-settings-page', 
      'wcp_first_section'
   );
      
   register_setting(
      'wordcountplugin', //group
      'wcp_healine', //setting
      array(
         'sanitize_callback' => 'sanitize_text_field', 
         'default' => 'Post Statistics'
      )
   );

   /*
   ** Definition of checkboxes enabling us to control the display of informations
   ** Word Count
    */
   add_settings_field(
      'wcp_wordcount', 
      'Word Count', 
      array($this, 'checkboxHTML'), 
      'word-count-settings-page', 
      'wcp_first_section', 
      array('theName' => 'wcp_wordcount') // passes arguments within the callback function checkboxHTML()
   );
      
   register_setting(
      'wordcountplugin', 
      'wcp_wordcount', 
      array(
         'sanitize_callback' => 'sanitize_text_field', 
         'default' => '1'
      )
   );

   /*
   ** Character Count
    */
   add_settings_field(
      'wcp_charcount', 
      'Character Count', 
      array($this, 'checkboxHTML'), 
      'word-count-settings-page', 
      'wcp_first_section', 
      array('theName' => 'wcp_charcount')
   );

   register_setting(
      'wordcountplugin', 
      'wcp_charcount', 
      array(
         'sanitize_callback' => 'sanitize_text_field', 
         'default' => '1'
      )
   );

   /*
   ** Read Time
    */
   add_settings_field(
      'wcp_readtime', 
      'Read Time', 
      array($this, 'checkboxHTML'), 
      'word-count-settings-page', 
      'wcp_first_section',
      array('theName' => 'wcp_readtime')
   );

   register_setting(
      'wordcountplugin', 
      'wcp_readtime', 
      array(
         'sanitize_callback' => 'sanitize_text_field', 
         'default' => '1'
      )
   );

}
```

5) ...
```
function sanitizeLocation($input){
   if($input != '0' AND $input != '1') {
      add_settings_error(
         'wcp_location', 
         'wcp_location_error', 
         'Display location not known.'
      );
      return get_option('wcp_location');
   }
   return $input;
}

/*
** 
*/
function checkboxHTML($args) 
{ ?>
   <input type="checkbox" name="<?php echo $args['theName'] ?>" value="1" <?php checked(get_option($args['theName']), 1) ?>>
<?php }

/*
** 
*/
function headlineHTML() 
{ ?>
   <input type="text" name="wcp_headline" value="<?php echo esc_attr(get_option('wcp_headline')) // ?>">
<?php }

/*
** Definition of the location dropdown 
*/
function locationHTML() 
{ ?>
   <select name="wcp_location">
      <option value="0" <?php selected(get_option('wcp_location'), 0) ?>>Beginning of post</option> <!-- get_option Retrieves an option value based on an option name -->
      <option value="1" <?php selected(get_option('wcp_location'), 1) ?>>End of post</option>
   </select>   
<?php }
```
