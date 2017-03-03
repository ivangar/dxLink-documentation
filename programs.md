# dxLink - Accredited Programs

Click on the following links to see the other sections [ [2](program_setup.md) ]

## Database ER-diagram structure

The first stage of creating a new accredited program and its sections is to generate new records in the database. For best practices, I ususally insert the data in the local database first using Sequel Pro, and then do the same thing in the remote database through phpMyAdmin. The ID's for unique key fields of each table below are chosen to match the program ID of the program itself. So for example if the `program_id` is 'SUN_01', then the `program_section_id` key value for pretest should be 'SUN_01_Pre_01', the `topic_id` key value should be 'SUN_01_topic_01', and so on. I add the _01 at the end of each of ther sections ID because we might have a second version of the same program like we did for Zoster programs for instance, or we may have a similar version of the same program. So numbering them makes it a more efficient way to create IDs.

For the purpose of this guide the Vaccination Communication (/programs/vaccine) program will be used as an example, since this program has all the regular sections (Pre-test, Post-test, Discussion Forum and Program Evaluation). As we will see further down in this document, these sections are not always required for some programs. The program code is usually established by STA. For this program the code established was MRK_125, thus this is the one that was selected to generate all the records in the database for dxLink. Below are the tables that are used to develop the code for accredited programs, with the MRK_125 program populated as an example for each table :

**Note - Create the ID's in the tables following the order they appear below, because you must create the primary key first in order to use it as a foreign key in another table.*

#### programs ####

~~~~
  `program_id` varchar(20) NOT NULL DEFAULT '',
  `area_id` varchar(20) DEFAULT NULL,
  `sponsor` varchar(200) DEFAULT NULL,
  `program_type` varchar(200) DEFAULT NULL,
  `program_title` varchar(200) DEFAULT NULL,
  `program_subtitle` varchar(200) DEFAULT NULL,
  `program_description` varchar(1000) DEFAULT NULL,
  `image` longblob,
  `language` varchar(15) DEFAULT NULL,
  `authors` varchar(400) DEFAULT NULL,
  `url` varchar(200) DEFAULT NULL,
  `launch_date` date DEFAULT NULL,
  `expiration_date` date DEFAULT NULL
~~~~

~~~~
  'MRK_125',
  'AP_01',
  'Merck',
  'accredited',
  'Words Matter! Optimizing Vaccine Uptake by Connecting with your Patients:',
  'Workshop on Adult Immunization Communication (ID 185454)',
  '“Words Matter!” is an interactive educational program aimed at optimizing the communication skills of 
  primary healthcare providers when discussing vaccination with their adult patients. Topics covered 
  include: Principles of effective communication; The motivational interview approach; and Personalizing
  discussions on vaccination based on patient predisposition/attitude.',
  NULL,
  'english',
  'Dr. Marc Steben, Marie-Thérèse Lussier, Dr. Walter Chow and Dr. Jay Keystone',
  'https://dxlink.ca/programs/vaccination/#tab1',
   NULL,
   NULL
~~~~

#### program_sections ####

~~~~
  `program_section_id` varchar(20) NOT NULL DEFAULT '',
  `program_id` varchar(20) NOT NULL DEFAULT '',
  `program_section_name` varchar(150) DEFAULT NULL,
  `program_section_type` varchar(25) DEFAULT NULL
~~~~

~~~~
  'MRK_125_Pre_01',
  'MRK_125',
  'MRK 125 Pre-test',
  'Test form'
~~~~

#### forum_topics ####

~~~~
  `topic_id` varchar(20) NOT NULL DEFAULT '',
  `topic_content` varchar(512) DEFAULT NULL,
  `program_section_id` varchar(20) NOT NULL
~~~~

~~~~
  'MRK_125_topic_01',
  'What are some of the most common/frequent misconceptions about vaccines which you hear from your patients? What are the main sources of these misconceptions (i.e., how/where are your patients getting wrong information or ideas)?',
  'MRK_125_Forum_01'
~~~~

#### questions ####

~~~~
  `question_id` varchar(20) NOT NULL DEFAULT '',
  `question` varchar(255) DEFAULT NULL,
  `type` varchar(255) DEFAULT NULL
~~~~

~~~~
  'MRK_125_Q_1',
  'The concept of “herd immunity” refers to:',
  'mc'
~~~~

#### valid_answers ####

~~~~
  `valid_answer_id` varchar(20) NOT NULL DEFAULT '',
  `question_id` varchar(20) DEFAULT NULL,
  `answer` varchar(255) DEFAULT NULL
~~~~

~~~~
  'MRK_125_A_1',
  'MRK_125_Q_1',
  'High immunization rates in a population providing protection to non-immune members.'
~~~~

#### certificate ####

~~~~
  `certificate_id` varchar(20) NOT NULL DEFAULT '',
  `accreditation_code` varchar(20) DEFAULT NULL,
  `no_credits` varchar(8) DEFAULT NULL,
  `type_of_credit` varchar(64) DEFAULT NULL
~~~~

~~~~
  'M_Pro_03',
  'cfpc_01',
  '3',
  'Mainpro+'
~~~~

---------------------------------------

## Database tables overview

#### programs columns ####

* `program_id` - primary key
* `area_id` - this column is a foreign key that references the table therapeutic_areas. It is an optional field, but it helps to categorize the programs in the main dxlink sections (Accredited Programs, Clinical Update and Congress Reports). The value 'AP_01' should be used for all accredited programs.
* `sponsor` - optional value that helps to categorize the sponsor of each program
* `program_type` - optional field that helps to categorize the programs in accredited and non-accredited
* `program_title` - this field is the program title that is inserted in the dynamic generated PDF certificate
* `program_subtitle` - this field is the program subtitle that is inserted in the dynamic generated PDF certificate
* `program_description` - this is the program blurb text or descriptive text that is used in the dxLink home page
* `image` - binary large object data type field that stores the image slider of the program. This field was added for a project but eventually the project was discontinued. Inserting the image is no longer needed, and the NULL value should be used for this field or leave it as default.
* `language` - optional field to categorize the programs by language English or French.
* `authors` - optional field that contains the authors of the program. It is not necessary to add information here, but can be helpful for future reference.
* `url` - optional field that contains the url of the program. This url is sent internally to STA members that need to review the program and test it when the program stage is still in development.
* `launch_date` - optional field of the launch date of the program.
* `expiration_date` - optional field of the end date of the program.

#### program_sections columns ####

* `program_section_id` - primary key
* `program_id` - foreign key from programs table
* `program_section_name` - optional name of the program section (Post-test, Pre-test, Discussion Forum or Evaluation). In this column you can add helpful comments about the section, for instance 'Sunovion Pre-test version #2'
* `program_section_type` - type of section

#### forum_topics columns ####

* `topic_id` - primary key of each of the topics of the Discussion Forum section
* `topic_content` - the topic text
* `program_section_id` - foreign key that links the Discussion Forum section from the program_sections table

#### questions columns ####

* `question_id` - primary key for each question of the Pre-test and Evaluation Form. (Post-test is the same as the Pre-test, thus we only need to insert questions for Pre-test)
* `question` - question text
* `type` - question type : **mc** for multiple choice and **open** for open questions

#### valid_answers columns ####

* `valid_answer_id` - primary key of the valid answer for each question
* `question_id` - foreign key from questions table. Each question has only one valid answer
* `answer` - answer text. Needs to be copied here exactly as it is in the word document, these will be used by a script to match the answers and give a grade

#### certificate columns ####

* `certificate_id` - primary key certificate
* `accreditation_code` - foreign key that links to the accreditation_criteria table
* `no_credits` - number of credits earned for the accredited program
* `type_of_credit` - the type of credit that is granted for this accredited program

---------------------------------------

## <a name="program_structure"></a>Program file structure

Once the database has been populated with the program content, the second stage is to begin developing the source code. Since the programs are similar, the best way to get ahead faster is to duplicate an existing program folder with all its contents and start to modify it from there. However, before doing this you must be aware of the sections that are required for the new program; as stated before some programs vary in requisites to obtain the certificate. Some programs don't have Pre-test and Post-test, other programs have an extra section. Based on this analysis, duplicate the program that best corresponds to the criteria of the new program.

Any Accredited program has specific php files that are the backbone of the whole program. These files can be found in (/programs/lib/php).
These main files are:

* [program.php](#program)
* [process_test.php](#process_test)
* [process_evaluation.php](#process_evaluation)
* [forum_topics.php](#topics)
* [program_results.php](#program_results)

Following is a brief description of each of these core-program files. They are the basis of all the accredited programs that are on dxlink so special care needs to be applied if there is a need to modify any of these files. But in general, they shouldn't be modified.

### <a name="program"></a> program.php

At the top of the file there are some __require_once()__ functions, which are the PHP functions to import the external files to be used. The FirePHPCore files are for FirePHP extension to troubleshoot and debug server-side issues. To use FirePHP debugger is very simple. First you need to import the FirePHPCore libraries and turn on output buffering in order to print from the server.

```
require_once($_SERVER['DOCUMENT_ROOT'] . '/FirePHPCore/FirePHP.class.php');
require_once($_SERVER['DOCUMENT_ROOT'] . '/FirePHPCore/fb.php');
ob_start();
```
Then you can print anything with the fb() function:

```
fb($myarray, 'Printing my array');
```
To view the FirePHP output enable the Firefox Firebug console.

The connect_db.php file contains the static variables to connect to the database. This file has commented out the remote database server connection info since it is used in local environment. Be carefull to not update this file in the remote server.

The Program class is the main class that will be instantiated for each program object. The first function will set all the public variables of the class: 

```
function Set_Program($program_id, $pre_test_id, $post_test_id, $forum_id, $evaluation_id, $certificate_id){}
```

`CheckProgramStatus()` - This function queries the *doctor_profiles* table to see if the user currently logged in has completed the program (in the database this is a boolen flag).

`CheckProfileExists()` - This function checks if it is the first time the doctor visits the program and there has not been assigned a profile record yet.

`CreateProfile()` - Creates a new profile with the programs status 0 (not completed). The field duplicate was used before because every French program was an exact replica of the English program. But after it was stated that they should be treated as different programs because they are accredited by different organizations.

`UpdateProfile($date)` - Updates the doctor profile when the program has been completed (sets program_status = 1) with the current date

`ReplicateProfile($date = NULL)` - This function is no longer supported in the code

`GetSectionsStatus()` - Call the functions to set the sections' status in the array

`CheckSectionsCompleted()` - Check if all 3 sections are completed (Pre-test, Post-test, Forum) and return true or false

`CheckSectionsCompletedCustomized($sections)` - A custom version of the previous function. You can pass an array of required sections as an argument here. The array can contain any of the following static variables:

```
array("pretest", "postTest", "forum", "evaluation"); 
```

`GetPreTestStatus()` - This function searches a record that matches the user ID and the Test ID from table results, and sets the section status value. The table results has all the records of the Pre-test and Post-test that have been completed.

`GetPostTestStatus()` - Same function as `GetPreTestStatus()` , the only difference is that it searches the Post Test data.

`GetForumStatus()` - Counts the total number of posts that this user has answered for any of the topics that belong to the current Discussion forum. The criterium for the forum is that at least one post on any topic has to be submitted in order to grant a certificate. Thus, this function counts the posts for all the topics, and if none is found the section status of the forum is 0 (incomplete).

`GetProgramEvaluationStatus()` - Queries the table evaluations to see if the user has submitted the program evaluation, and sets the section status to either 1 or 0. The evaluations table has the records of all the program evaluations of dxLink.

`GetDateOfCompletion()` - This function is no longer used.

### <a name="process_test"></a> process_test.php
[click here to go up](#program_structure)

This file doesn't have a generic class, so all the functions are not contained inside a class. All the pre-test and post-test forms send the answers to this script which does all the heavy processing and registers them in the database. All the information is sent with a $_POST array through jQuery AJAX. The functions used in this script are:

`GetData($data, &$program_section_id, &$no_qs)` - This function has 3 parameters:
  - $data - contains the $_POST array with the number of test questions, Question and Answer nested array, a nested array of the choices selected and the program section ID.
  	- Here is an example of what the $_POST array looks like:
  	```php
array(
			['no_qs'] => 6,
			['qas'] => array(
							[0] => array(
								['name'] => 'MRK_125_Q_1'
								['value'] => 'High immunization rates.'
								)
							[1] => array(
								['name'] => 'MRK_125_Q_2'
								['value'] => '3 patients'
								)
							[2] => array(
								['name'] => 'MRK_125_Q_3'
								['value'] => 'A physician’s recommendation.'
								)
							[3] => array(
								['name'] => 'MRK_125_Q_4'
								['value'] => '“I’m not interested in vaccines.”'
								)
							[4] => array(
								['name'] => 'MRK_125_Q_5'
								['value'] => 'Ask open-ended questions.'
								)
							[5] => array(
								['name'] => 'MRK_125_Q_6'
								['value'] => 'Kindness.'
								)
						),
			['choices'] => array(
							['MRK_125_Q_1'] => 'c'
							['MRK_125_Q_2'] => 'c'
							['MRK_125_Q_3'] => 'a'
							['MRK_125_Q_4'] => 'a'
							['MRK_125_Q_5'] => 'c'
							['MRK_125_Q_6'] => 'd'
						),
			['program_section'] => 'MRK_125_Pre_01'
	)
```
  - $program_section_id - The pretest or posttest ID. You can see that there is an **&** at the beginning of each variable, this is called passing a variable by reference. This is very useful when you declare a variable and don't have a class. So to modify the variable in other functions you must pass it by reference. You can read more about it here : (http://php.net/manual/en/language.references.pass.php)
  - $no_qs - number of questions from the test. This variable is also passed by reference and later changed.

This function loops through the array of questions and answers, compares each answer with the valid answer (from DB table 'valid_answers') and inserts a new record for each of the answers for this test in the database.

`Validate_data($questions_answers, $no_qs)` - Checks that the Q&A array is not empty, and compares the number of arrays submitted (each array for 1 Q&A block) and the number of questions the test has.

`Sanitize($str,$remove_nl=true)` - Sanitizes the string to be manipulated.

`Evaluate_Answer($con, $q_id, $answer)` - Compares the answer selected by the user and the valid answer and keeps a counter of the number of correct answers.

`InsertAnswers($con, $doctor_id, $program_section_id, $q_id, $answer, $choice)` - Inserts each answer in the database in the doctor_answers table.

`getAssessment($total, $correct_answers)` - Divides the number of correct answers by the total, and returns a percentage grade.

`InsertResult($con, $doctor_id, $program_section_id, $assessment=0, $correct_answers=0)` - Insert a result for the completed program section in the results table.

### <a name="process_evaluation"></a> process_evaluation.php
[click here to go up](#program_structure)

This self-contained file with functions is very similar to process_test.php file. It receives the same set of $_POST data from the jQuery AJAX, it validates that all the answers were submitted and it loops through each set of Q&A's before inserting the answers and the evaluation. At the beginning all the programs had the same evaluation form, it was standard. However, every project started to differ and to have its own custom evaluation form. This is the reason why every program has a custom process_evaluation.php file in the resources folder (ex: /programs/vaccine/resources/process_evaluation.php). Following is the short description of the process_evaluation file for the vaccine program, but bear in mind that each program has a slightly different version of this file.

The vaccine program has one grid of multiple choice questions where the user rates from 1 to 5 scale, it has some check-box questions, and the open questions. Since the multiple choice and check-box questions are mandatory these will be validated by the script. The open-ended questions and the bias check-box are optional.

`GetData($data, &$program_section_id)` - Same functionality as in the process_test.php file, however there is some extra validation for the checkboxes. The multiple check-box answers from question 2 are concatenated and inserted as a long string in the `doctor_answer` column. The Yes/No bias check-box value is inserted in the comments column. To make it simple any of the Yes/No questions that may appear in an evaluation form are threated the same way: if the user inserts anything in the text-area, regardles of whether he selected yes or no, it will be saved as the comments column in the answers table.

`EmtpyFields($choices, $no_qs)` - First checks if the `[choices]` array submitted is equal to the $no_qs (number of questions). In this case the number of questions corresponds only to the multiple choice questions (7 questions). Then it validates that each of the values in the array is not empty.

`EmptyAnswer($questions_answers, &$checkboxes)` - Validates that at least one check-box has been selected for the second question only. The bias Yes/No question is optional so it is not validated, as well as the open questions.

`insertCheckbox($con, $doctor_id, $program_section_id, $q_id, $answer, &$longAnswer, $choice, $checkboxes, &$checkboxArray)` - Concatenates each of the check-box values to the $longAnswer string and keeps a $checkboxArray counter for each one of the check-boxes already concatenated. When the counter reaches the number of check-boxes submitted, then it inserts the string as a complete answer. The $_POST array below contains 3 check-box values, and each time the array is iterated the value is concatenated.

```php
array(
      ['qas'] => array(
              [3] => array(
                ['name'] => 'AIT_E_14'
                ['value'] => 'Communicator'
                )
              [4] => array(
                ['name'] => 'AIT_E_14'
                ['value'] => 'Manager'
                )
              [5] => array(
                ['name'] => 'AIT_E_14'
                ['value'] => 'Professional'
                )
  )
```

The answer inserted in the database will look like the following:

```sql
... 'AIT_E_14', 'Communicator, Manager, Professional', ...
```

`UpdateAnswer($con, $doctor_id, $program_section_id, $q_id, $comment)` - If the user writes a comment to the bias (Yes/No) question, it is updated in this function. The array with the Question ID is processed and inserted first, so the record already exists and the next loop will update that question as a comment. For example, below the AIT_E_15 Q_ID is inserted first with the answer yes, and then the next loop it updates the previous record with the value as a comment.

```php
array(
      ['qas'] => array(
              [8] => array(
                ['name'] => 'AIT_E_15'
                ['value'] => 'yes'
                )
              [9] => array(
                ['name'] => 'bias'
                ['value'] => 'This is a bias comment'
                )
  )
```
In the database it looks like :

```sql
... 'AIT_E_15', 'yes', 'This is a bias comment', ...
```

`InsertAnswers($con, $doctor_id, $program_section_id, $q_id, $answer, $choice)` - Same functionality as for the process_test script.

`InsertEvaluation($con, $doctor_id, $program_section_id)` - Registers in the database in the evaluations table the evaluation completed by the user. 

### <a name="topics"></a> forum_topics.php
[click here to go up](#program_structure)

The class ForumTopic has two main uses:
* to insert the post linked to the forum topic 
* this class is also used to load dynamically batches of topics already posted when the paginator is clicked. 

The first option (insert a post) is pretty straightforward. At the bottom of the file (end of the class definition) you can observe that there is an if statement :

```php
if(isset($_POST['submit_comment']) && (!empty($_POST['submit_comment'])) ){
```

Here is where the post array is submitted, the class object is instantiated and then a simple insert query function is called to insert the post linked to the corresponding topic ID.
The $_POST array looks similar to this example:

```php
array(
      [topic] => MRK_125_topic_01
      [comment] => This is a comment to the topic
      [submit_comment] => 1
  )
```

The following functions are called to insert the post:

`InsertPost($comment)` - It queries the table doctors to get the Province and Profession of the doctor who submitted the comment. Then it proceeds to insert the comment, and the topic ID to which this comment belongs.

`send_email_alert($message, $topic_id)` - This function is a simple email alert to the moderator to keep track that the messages inserted respect the rules and that there isn't any offensive content.

The second option of this class is to load the discussion forum comments, in batches of 10 rows for each of the topic.

```php
if (isset($_REQUEST['action'])) {
```

This statement is called by the file forum.php which sends the topic ID, the start row (0 by default) and the action string: 'get_rows' or 'row_count' and it switches each action to call a different set of functions:

```php
  switch ($action) {
    case 'get_rows':        //This is sent with the $_REQUEST['action'] array to print the posts per topic
      $forum->getRows();
      break;
    case 'row_count':   //Sent with $_REQUEST['action'] array to count the number of total posts per topic
      $forum->getRowCount();
      break;
    default;
      break;
  }
```

`getRows()` - This function sets the start row from which the query will be sorted on. By default the start row is 0, and each time the paginator is clicked it will send the pagination number to the script via $_REQUEST['start']. This variable is then converted in multiples of 10 and passed on to the function that Selects the posts.

`Get_Posts($start_row)` - It receives the start row as a parameter. It queries the table posts to select all the posts for the specific topic ID, orders the query by last posted message and limits the result starting with the offset (start row) and only brings 10 rows.

`Generate_Posts($province, $profession, $message)` - Concatenates each result row into an HTML code with table cells each for the province, profession and message and assigns this result to $this->posts global variable.

`Print_Posts()` - Creates the HTML table body, inserts all the posts and returns the result with an echo statement.

And that is all the script does to fetch and print the posts. The other action that the switch statement runs through is for the case 'row_count'. This value is sent in order to get the number of posts per topic and set a hidden field for the paginator, which is used by the JavaScript code to set the number of pagination numbers.

`getRowCount()` - Get number of total comments that a topic has in the database.

### <a name="program_results"></a> program_results.php
[click here to go up](#program_structure)

Once the Pre-test and Post-test have been answered, this script gets from the database the number of correct answers, the assessment and the answer choices for each test. It does this for Pre-test and Post-test for the program that is being loaded. This file is not called directly by the the AJAX request as it was the case in the previous files. The AJAX calls the results.php file which imports the program_results.php file and cretas the **TestEvaluation** object, and then it uses all its functions and public variables.

`Set_Program($data)` - This function sets the data received from the AJAX request, sets the User ID and connects to the database. Here is an example of the AJAX $_POST request received :

```php
array(
      ['no_questions'] => 11,
      ['question_ids'] => array(
                          [0] => 'HPV_Q_1'
                          [1] => 'HPV_Q_2'
                          [2] => 'HPV_Q_3'
                          [3] => 'HPV_Q_4'
                          [4] => 'HPV_Q_5'
                          [5] => 'HPV_Q_6'
                          [6] => 'HPV_Q_7'
                          [7] => 'HPV_Q_8'
                          [8] => 'HPV_Q_9'
                          [9] => 'HPV_Q_10'
                          [10] => 'HPV_Q_11'
                      ),
      ['sections'] => array(
                      ['preTest'] => 'HPV_Pre_01'
                      ['postTest'] => 'HPV_Post_01'
                    )
  )
```

`Set_Tests($data)` - Sets the class variables with the $_POST data received

`Get_Final_Evaluation()` - Has a loop that iterates through each of the sections (Pre-test and Post-test) and calls a function to get the test results.

`Set_Section_Results($section_id)` - receives as a parameter the test ID and queries the database to get the assessment and the number of correct answers. It then creates an array with each section and sets each of the values in the array to with the corresponing assessment and correct answer number.

`Get_Assessment()` - Creates an HTML string with the results statement, which prints the number of correct answers and the assessment of the Pre-test and Post-test.

`Get_Pretest_Answers()` - Loops through each of the question ID's from the array and gets the choice letter which corresponds to the answer. It pushes each answer onto the end of an array.

`Get_Postest_Answers()` - Does the same operation as **Get_Pretest_Answers()**, but it inserts the answers in the **postest_answers** array

Now that the database structure has been reviewed and the general overview of each of the main files has been explained the following pages wil describe in more detail the accredited program setup.

Click on the following links to see the other sections [ [2](program_setup.md) ]






