## Context
Previously we saw how to set up a filter with the helps of multiple WP APIs. We're going to abord a step offering more flexibility and control client-side regarding the featues.

We are going to develop a word count plugin but you'll see that it is more of a pretext to discover multiple hooks and demistfying WordPress mecanics the more you know the tools the best will be its used.


## Development

### Goals

### Requirement

### Coding Steps

- definition of the hook callback needed to render our plugin within admin panel 
	- menu
	- page
- definition of the hook callback rendiring the pluggin settings available both on front meaning fields/meta data and back meaning register with default value  
	- checkboxes => Read Time, Word Count, Character Count
	- dropdown   => Location
	- text 		 => Headline
- definition of the filter provinding the 

// definition of a field within the page
	// definition of the field name
	// HTML title 
	// callback of the field's HTML
	// slug of the section's page
	// reference to the field section define above

#### 1) Admin option page configuration
```
add_action('admin_menu', array($this, 'adminPage'));
```

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

#### 2) definition of settings on both side

```
function settings()
{
   add_settings_section( // registers a form section
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
