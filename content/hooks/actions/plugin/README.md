# Word Count Plugin Development

Previously we saw how to set up a filter with the helps of multiple WP APIs. We're going to abord a step offering more flexibility and control client-side regarding the featues. 

We are going to develop a word count plugin but you'll see that it is more of a pretext to discover multiple hooks and demistfying WordPress mecanics the more you know the tools the best will be its used.

## Development

1) Create a class in order to set a context resolving function's name conflict issues
```
class WordCountAndTimePlugin {}
```

2) How to make our features available within admin panel
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
            settings_fields('wordcountplugin');                 // define the settings group
            do_settings_sections('word-count-settings-page');   // output settings on page sluged like $param
            submit_button();                                    // output submit button
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
   ** Definition of the dropdown enabling us to choose the position of the fields regarding the the article
    */
   add_settings_field(              // definition of a field within the page
      'wcp_location',               // definition of the field name
      'Display Location',           // HTML title 
      array($this, 'locationHTML'), // callback of the field's HTML
      'word-count-settings-page',   // slug of the section's page
      'wcp_first_section'           // reference to the field section define above
   );
   
   register_setting(                // register a settings and its data
      'wordcountplugin',            // option group (meaning the settings?)
      'wcp_location',               // option name 
      array(                        // security layer defined accepted data in the dropdown
         'sanitize_callback' => array($this, 'sanitizeLocation'), 
         'default' => '0'
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
      array('theName' => 'wcp_wordcount')
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

5) 
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
** Character Count
*/
function checkboxHTML($args) 
{ ?>
   <input type="checkbox" name="<?php echo $args['theName'] ?>" value="1" <?php checked(get_option($args['theName']), 1) ?>>
<?php }

/*
** Character Count
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
