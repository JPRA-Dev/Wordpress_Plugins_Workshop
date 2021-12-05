## Context
Previously we saw how to set up a filter with the helps of multiple WP APIs. We're going to abord a step offering more flexibility and control client-side regarding the featues.

We are going to develop a word count plugin but you'll see that it is more of a pretext to discover multiple hooks and demistfying WordPress mecanics the more you know the tools the best will be its used.


## Development

### Goals

### Requirement

- 
- sanitize

### Coding Steps

- definition of the hook callback needed to render our plugin within admin panel 
	- menu
	- page
- definition of the hook callback rendiring the pluggin settings available both on front meaning fields/meta data and back meaning register with default value  
	- checkboxes => Read Time, Word Count, Character Count
	- dropdown   => Location
	- text 		 => Headline
- definition of the filter provinding the 

definition of a field within the page
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
      'Word Count Settings',            
      'Word Count',                     
      'manage_options',                 
      'word-count-settings-page',       
      array($this, 'ourHTML')          
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

#### 2) definition of section
```

   add_settings_section(
      'wcp_first_section',          
      null,             
      null,           
      'word-count-settings-page'    
   );
```
##### 3) settings fields and register handler
##### 3.1) Location
```
   add_settings_field(           
      'wcp_location',               
      'Display Location',           
      array($this, 'locationHTML'), 
      'word-count-settings-page',  
      'wcp_first_section'          
   );
   
   register_setting(            
      'wordcountplugin',     
      'wcp_location',              
      array(                        
         'sanitize_callback' => array($this, 'sanitizeLocation'), 
         'default' => '0'
      )
   );
```
###### 3.2) Headline
```
   add_settings_field(
      'wcp_healine', 
      'Headline Text', 
      array($this, 'headlineHTML'), 
      'word-count-settings-page', 
      'wcp_first_section'
   );
      
   register_setting(
      'wordcountplugin', 
      'wcp_healine',
      array(
         'sanitize_callback' => 'sanitize_text_field', 
         'default' => 'Post Statistics'
      )
   );
```
###### 3.3) Word Count
```
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
```
###### 3.4) Character Count
```
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
```
###### 3.5) Read Time
```
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
```

