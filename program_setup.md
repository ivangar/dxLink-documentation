# dxLink - Accredited Programs Setup content

### index.php

The main script file that controls the accredited program is the first index.php file that can be found within the parent folder of each program. 
For instance for the vaccine program (MRK_125) the index.php file is the main program file. Initially the main files were named after the program names, like for HZ1Rev it was zoster.php, for hpv - hpv.php, etc. However to make it more consistent, index.php was chosen and will be used going forward. 

The next set couple of lines of index.php is also very important to declare. The __membersite_config.php__ file is very important to import, since it has the database login information, access to fg_membersite with all the user account information, etc.
The __program.php__ file is the main file that has the Program Class declaration. Here all the variables and functions are declared. This file retrieves from the database the program status, progress of each section, certificate of completion. As each section (pre-test, post-test, etc) of the program is submitted, and the main page (index.php) is refreshed, it updates the database and gets back from the database the new information.

The following line instantiates the class Program :

```
$EnglishProgram = new Program();
```

The PERMANENT VARIABLES are declared first. These are the database ID's that correspond to the program and its different sections. For instance, for the HZ2Rev program the program_id column in the database is HZ_02_REV, the program_section_id column for pre-test is HZ_Pre_04, and so on. You have to use the same IDs in the code. The last variable is an array which holds each of the forum topic IDs; this topics array will be used in the forum.php file.

After declaring the permanent variables, there are some flag variables :

```
$program_status = false;     
$program_completed = false;   
$sections_status = array();
$no_sections_completed = 0;
```
