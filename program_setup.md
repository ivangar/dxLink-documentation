# dxLink - Accredited Programs Setup content

Click on the following links to see the other sections [ [1](programs.md) ]

---------------------------------------
## Basic program setup

### index.php

The main script file that controls the accredited program is the first index.php file that can be found within the parent folder of each program. 
For instance for the vaccine program (MRK_125) the index.php file is the main program file. 

Before, the main files were named after the program names like for HZ1Rev it was zoster.php, for hpv - hpv.php, etc. However to make it more consistent, index.php was chosen and will be used going forward. 

The next set couple of lines of index.php is also very important to declare. The __membersite_config.php__ file is very important to import, since it has the database login information, access to fg_membersite with all the user account information, etc.
As it was explained in the previous page, the __program.php__ file is the main file that has the Program class declaration. Here all the variables and functions are declared. This file retrieves from the database the program status, progress of each section, certificate of completion. As each section (pre-test, post-test, etc) of the program is submitted, and the main page (index.php) is refreshed, it updates the database and fetches from the database the updated information.

The following line instantiates the class Program :

```
$EnglishProgram = new Program();
```

The CONSTANT VARIABLES are declared first. These are the database ID's that correspond to the program and its different sections. For instance, for the MRK_125 program the program_id column in the database is MRK_125, the program_section_id column for pre-test is MRK_125_Pre_01, and so on. You have to use the same IDs in the code. The last variable is an array which holds each of the forum topic IDs; this array will be used in the forum.php file.

**Note - As you probably have noticed there are some French IDs as well ($program_id_fr, $evaluation_id_fr); you can ignore them they don't need to be declared at all. The reason why they were used in here is because when dxLink was launched, every French program was a replica of the English program and vice versa. Thus, there was more code to check dynamically if the same French program was completed or if any of the sections were submitted in the French site. This way a user could not complete the same program twice. However French programs are no longer duplicates of the English programs.*


After declaring the permanent variables, there are some flag variables :

```php
$program_status = false;     
$program_completed = false;   
$sections_status = array();
$no_sections_completed = 0;
```

$program_status variable will check if the program is already completed. If it is not, then the script checks section by section to see if all of them are submitted and then updates the status.

$program_completed is a flag variable that will be used to display the hidden link to get the certificate in the accreditation.html file

$sections_status is an array that holds the status of each of the sections. This array is used accross several files that check the status of the corresponding section in order to see if it is submitted or not. When all sections have been submitted it would look like this:

```php
array(
      [pretest] => 1
      [postTest] => 1
      [forum] => 1
      [evaluation] => 1
  )
```

$no_sections_completed keeps track of the number of sections that have been submitted. This counter is used in the JavaScript file which prints the modal dialog box showing the user his progress every time he submits a mandatory section to get the certificate.

The following line of code is used in the JavaScript functions to display the black $.blockUI box to show a hint to click on the arrows previous/next.:

```php
if( !isset($_SESSION['arrow_box'])){ $_SESSION['arrow_box'] = true; }
```

It only sets the session variable once, so the message pops up only one time when the user logs in.

The rest of the PHP code is already commented. Basically the first time the user access the program it will insert a new record on his profile with a 0 as a program status. Then each time the user submits a form or posts on the Discussion Forum, the page is reloaded and the code checks each of the sections, setting the flag variables mentioned above. Once the user has completed all the sections required to complete the program, the doctor profile gets updated and the certificate link will now be visible for the user.

Following along the lines, you can find 3 JavaScript variables: 
* var showTip - boolean variable used to display the black $.blockUI box with the hint
* var section_submitted - boolean variable used to display the black $.blockUI with the progress status
* var no_sections_completed - counter of the number of sections completed so far

The reason why these variables are declared here and not in the program.js file is because the server executes all the PHP code before sending the response header to the browser, so none of the PHP code is accessible from the external files.

Following is a short description of the external CSS and JavaScript files that are imported in the file :

- `SpryMenuBar.js` - This file uses the JavaScript event handlers for Spry Menu Bar which is the navigation menu at the top
- `styles.css` - the master css file for the general dxLink website
- `SpryMenuBarHorizontal.css` - it has the layout and design styles of the menu bar
- `form-submit-button.css` - has the css rules for the grey buttons used in the forms, login, logout, etc
- `tabStyles.css` - styles for the main vertical navigation unordered list and the content frame attached on the right side
- `form.css` - file that has all the basic styles of the Pre-test, Post-test and Program Evaluation forms
- `nova.css` - contains all the styles for the form elements like unordered lists, labels and radio buttons
- `main.css` - has some local rules for the general paginator (dispays the page number)
- `program_styles.css` - has the majority of the program styles such as the slide images, discussion forum pagination and tables, evaluation form, accreditation page and home page. Most of the accredited program styles come from this file.
- `parsley.js` - this is an asynchonous JS library to validate empty entries of the forms. Here is the documentation for this library http://parsleyjs.github.io/Parsley-1.x/documentation.html
The version that all the programs use is the old version 1.x so this guide is still good as a reference.
- `bowser.min.js` - browser detector, used to assign height and width of the slide pop-up window.
- `hashchange.js` - jQuery plugin that uses the browser #hash history that is triggered on window.onhashchange event. Every time a vertical menu item is is clicked, you can use the browser buttons back and forth to jump between the tabs. More documentation can be found here http://benalman.com/projects/jquery-hashchange-plugin/
- `tabScript.js` - this is the general script that shows the content of the current vertical menu item (tab) and hides the content of all the other tabs
- `program.js` - This script is unique for each program, so it has to be edited for every new program. It contains the page counter functions, resets the first page of the tab, the discussion forum character counter and the $.blockUI modal boxes code.
- `blockUI.js` - plugin that displays the modal box. Documentation and examples can be found here http://malsup.com/jquery/block/

After the navigation bar there are 2 table rows: the first contains the page counter and the second has the vertical menu div content.

The page counter is a div with span elements. All of them are hidden except the last span which displays the page number. The page counter functionality will be explained in mored detail in the program.js file documentation.

The second part - vertical menu - contains the ul > li elements with the tab attribute for each element and the title attribute. The tab attribute is used in the JavaScript code to know which tab to make visible and hide the other tabs, and the title attribute is used by JavaScript to reset each menu item to the first slide. On hovering with the mouse on each menu item, this function can be compared as a "reset to first slide of this section". This way, every time the user clicks on any of the tabs, it always brings the first slide of the section. This behaviour was integrated because it was specifically asked for. 

After the ul there are a sequence of `<div>` tags that load each of the files. Only the `<div>` that corresponds to a slide section has an id. This id is used to load with an AJAX call every slide when the next/previous buttons are cliked. It will be more clear once you see how the slides are loaded with JavaScript.

For example, the following `<li>` has the title `about`. The title has to match the name of the subfolder where you save all the files that correspond to the slides.
As well the `<div>` that links to the 3rd tab has the id that starts with the folder name but with the `_section` added to it. This is used by JavaScript as well. And to name the file that contains each of the slide's content, you name it as well with the subfolder prefix_slide_number.html
```
<li tab="tab3" title="about">About the program</li>
```
```
<div class="tab-content" id="about_section">
    <?php require_once('about/about_slide_1.html'); ?>
</div>    
```
So every time you ad a new set of slides for a section, you use the folder name as the prefix in the IDs, title attributes, and file names.

---------------------------------------

The next step in the development of an accredited program is basically divided in 2 stages: the slides and the program sections (Pre-test, Discussion Forum, etc). The order in which you develop either of them doesn't matter. You can start with the slides first and then finish with the program sections or vice versa. For convinience, the hardest part (program sections) is introduced first.

Before jumping into the program sections setup, I will give a detailed explanation of the file program.js and the role it plays in each of the program sections.

### <a name="program_js"></a> program.js

This external file is included in the index.php file at the top

```
<script type="text/javascript" src="js/program.js"></script>
```

 





































