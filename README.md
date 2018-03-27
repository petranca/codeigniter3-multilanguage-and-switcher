# Codeigniter 3 | Multilanguage and switcher
Simplified from https://www.codexworld.com/multi-language-implementation-in-codeigniter/

## 1. Create folder
Create a folder for each language in ROOT/application/language/.
### Example:
ROOT/application/language/en (for english)

ROOT/application/language/de (for german)

## 2. Create files
Create a language file for each language folder with the suffix "_lang". You can choose the name (in my case "application") for the file. Important is only the underscore and the suffix after the name.

### Example:
ROOT/application/language/en/application_lang.php 

ROOT/application/language/de/application_lang.php 

## 3. Set default language and session settings
Set the default language (in my case "en" for english) and sessions in ROOT/application/config/config.php:

$config['language']	= 'en';

Sessions are individual cases. In my case, I saved this as a file in ROOT/application/sessions/ folder. Set the settings as follows:
<pre>
$config['sess_driver'] = 'files';
$config['sess_cookie_name'] = 'app_session';
$config['sess_expiration'] = 7200;
$config['sess_save_path'] = APPPATH.'sessions/';
$config['sess_match_ip'] = FALSE;
$config['sess_time_to_update'] = 300;
$config['sess_regenerate_destroy'] = FALSE;
</pre>

## 4. Create LanguageSwitcher.php

Create "LanguageSwichter.php" file in the ROOT/application/controllers/ folder and insert the following code:

<pre>
<?php if ( ! defined('BASEPATH')) exit('No direct script access allowed');
class LanguageSwitcher extends CI_Controller
{
    public function __construct() {
        parent::__construct();
    }

    function switchLang($language = "") {

        $language = ($language != "") ? $language : "de";
        $this->session->set_userdata('site_lang', $language);

        redirect($_SERVER['HTTP_REFERER']);

    }
}
</pre>

## 5. Add __construct function

Add this function to your controller:

<pre>
  function __construct(){
  	parent :: __construct();
          //Setting language in session on first load
          if(!$this->session->userdata('site_lang')){
        		$this->load->library('session');
        		$newdata = array(
  			      'site_lang'  => $this->config->item('language')
  		      );
  		$this->session->set_userdata($newdata);
  	}
          //Loading language from session variable
  	$this->language = $this->session->userdata['site_lang'];
  	$this->lang->load('backend',$this->language);
  }
 </pre> 

## 6. Autoload

Set this to your ROOT/config/autoload.php file:

<pre>
$autoload['libraries'] = array('session');
</pre>
<pre>
$autoload['helper'] = array('url');
</pre>

## 7. Insert language switcher in your view
<pre>
<select onchange="javascript:window.location.href='<?php echo base_url();>index.php/LanguageSwitcher/switchLang/'+this.value;">
<option value="en" <?php if($this->session->userdata('site_lang') == 'en') echo 'selected="selected"'; ?>>English</option>
<option value="de" <?php if($this->session->userdata('site_lang') == 'de') echo 'selected="selected"'; ?>>Deutsch</option>
</select>
</pre>
