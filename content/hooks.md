# Coding

## Goals

We want a plugin informing us on a post we're about to read by the help of various informations.
- Reading time
- Number of words
- Numbers of characters
- The position of the info block
- The title of the block

We'll have a control on their display.  

## Requirement

1) [Display the plugin in the admin panel](https://github.com/RayaneLamri/pluginsWPworkshop/blob/main/content/03.hooks.md#1-display-the-plugin-in-the-admin-panel)
2) [Create a setting page](https://github.com/RayaneLamri/pluginsWPworkshop/blob/main/content/03.hooks.md#2-create-a-setting-page)
3) [Sanitize all of the setting page inputs](https://github.com/RayaneLamri/pluginsWPworkshop/blob/main/content/03.hooks.md#3-sanitize-all-of-the-setting-page-inputs)
4) [Render the result on client-side](https://github.com/RayaneLamri/pluginsWPworkshop/blob/main/content/03.hooks.md#4-render-the-result-on-client-side)

### 1) Display the plugin in the admin panel
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

### 2) Create a setting page   
#### 2.1) Definition of section (fields container)
```
add_settings_section(
  'wcp_first_section',          
  null,             
  null,           
  'word-count-settings-page'    
);
```
#### 2.2) Settings fields and register handler
##### 2.2.1) Location
```
add_settings_field(           
  'wcp_location',               
  'Display Location',           
  array($this, 'locationHTML'),    
  'word-count-settings-page',  
  'wcp_first_section'          
);
```
```
register_setting(            
  'wordcountplugin',     
  'wcp_location',              
  array(                        
  'sanitize_callback' => array($this, 'sanitizeLocation'), 
  'default' => '0'
 )
);
```
##### 2.2.2) Headline
```
add_settings_field(
  'wcp_healine', 
  'Headline Text', 
  array($this, 'headlineHTML'), 
  'word-count-settings-page', 
  'wcp_first_section'
);
  ```
  ```
register_setting(
  'wordcountplugin', 
  'wcp_healine',
  array(
    'sanitize_callback' => 'sanitize_text_field', 
    'default' => 'Post Statistics'
  )
);
```
##### 2.2.3) Word Count
```
add_settings_field(
  'wcp_wordcount', 
  'Word Count', 
  array($this, 'checkboxHTML'), 
  'word-count-settings-page', 
  'wcp_first_section', 
  array('theName' => 'wcp_wordcount')
);
  ```
  ```
register_setting(
  'wordcountplugin', 
  'wcp_wordcount', 
  array(
    'sanitize_callback' => 'sanitize_text_field', 
    'default' => '1'
  )
);
```
##### 2.2.4) Character Count
```
add_settings_field(
  'wcp_charcount', 
  'Character Count', 
  array($this, 'checkboxHTML'), 
  'word-count-settings-page', 
  'wcp_first_section', 
  array('theName' => 'wcp_charcount')
);
```
```
register_setting(
  'wordcountplugin', 
  'wcp_charcount', 
  array(
    'sanitize_callback' => 'sanitize_text_field', 
    'default' => '1'
  )
);
```
##### 2.2.5) Read Time
```
add_settings_field(
  'wcp_readtime', 
  'Read Time', 
  array($this, 'checkboxHTML'), 
  'word-count-settings-page', 
  'wcp_first_section',
  array('theName' => 'wcp_readtime')
);
```
```
register_setting(
  'wordcountplugin', 
  'wcp_readtime', 
  array(
    'sanitize_callback' => 'sanitize_text_field', 
    'default' => '1'
  )
);
```
### 3) Sanitize all of the setting page inputs
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
```
### 4) Render the result on client-side
```
function checkboxHTML($args) 
{ ?>
   <input type="checkbox" name="<?php echo $args['theName'] ?>" value="1" <?php checked(get_option($args['theName']), 1) ?>>
<?php }
```
```
function headlineHTML() 
{ ?>
   <input type="text" name="wcp_headline" value="<?php echo esc_attr(get_option('wcp_headline')) // ?>">
<?php }
```
```
function locationHTML() 
{ ?>
   <select name="wcp_location">
      <option value="0" <?php selected(get_option('wcp_location'), 0) ?>>Beginning of post</option> <!-- get_option Retrieves an option value based on an option name -->
      <option value="1" <?php selected(get_option('wcp_location'), 1) ?>>End of post</option>
   </select>   
<?php }
```
```
function ifWrap($content) {
	if (is_main_query() AND is_single() AND (
		get_option('wcp_wordcount', '1') OR 
		get_option('wcp_charcount', '1') OR
		get_option('wcp_readtime', '1')
	)) {
		return $this->createHTML($content);
	}
}
```
```
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
```
