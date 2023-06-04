# GUAC

Guac is a system for grading assignments. Guac provides several modules which can be easily configured using YAML files. This method is made possible by the [Avocado](https://github.com/avocado-framework/avocado) test framework. In addition, Guac provides several tools to aid grading such as manually editing task scores, and exporting class results.

## Installation
**Prerequisits**

```python3.8```

Execute ```./install.sh```
This will
* download Avocado in ~/avocado
* configures avocado (installs plugins, configures sequential task execution)
* modify to use correct python version (avocado requires >=3.8 CS machines default to 3.6)
* link guac interface in ~/.local/bin/guac
* install necessary python libraries

## Usage
### Initialize Assignment Grading Directory

* Create a new directory ~ named the same as the assignment (not necessary)
* execute ```guac init```
* see ./docs/GUAC_CONFIG for details of configuration variables

### Define Tests
Tests are defined by defining a series of tasks. Each task consists of a source module (which will be run by Avocado) and a series of config files.
The source modules in guac are intended to be conprehensive, therefore creating a test harness for a new assignment should only require writing a series of config files.
* Tasks are defined in a RECIPE_FILE (see ./docs/RECIPE_FILE and ./examples)
* The varaibles required in a config file is dependent on the module being used (see ./docs/CONFIG_FILES)

### Assign Test Weights
```guac list --recipe RECIPE_FILE```
* Before grading assignments we must assign weights to each one of the tests associated with a particular recipe. 
* After running ```guac list``` copy the list of tests into a new file and assign weights to each test (see ./docs/TEST_WEIGHTS)
* *Hint: you may find the command ```sed "s/$/: 1/g" -i WEIGHTS_FILE``` useful as a starting point*

### Generate Master Results
```guac generate --recipe RECIPE_NAME```
* This step executes the recipe for the submission associated with MASTER
* The results are stored in ASSIGNMENT_HOME/.master_results
* The .master_results directory has the general structure RECIPE_NAME/{VARIABLES} (the variables used depends on the collector module)

### Automatic Grading
```guac run --name NAME --recipe RECIPE_FILE```<br>
```guac run --these STUDENT_FILE --recipe RECIPE_FILE```
* Student submissions can be graded one at a time (by specifying --name) or in sets (by specifying --these)
* See ./docs/STUDENTS_FILE for details
* Results are written to ASSIGNMENT_HOME/.scores/RECIPE/NAME/{TASK_NAMES,NAME.grade}
* verbose flag will change log level

### Manual Grading
#### Inspect 
```guac inspect --name NAME --file FILE```
When manual grading is required, use inspect to cat a file from a student's submission.

### Extract
```guac extract --these STUDETNS_FILE --file FILE --dest DIST_DIR```
* For manual grading of multiple students (e.g. reports) ```guac extract``` will collect all files in a single directory
* DEST_DIR will default to ASSIGNMENT_HOME/bin when unspecified
* Files are coppied to DEST_DIR/FILE_NAME/NAME.FILE_SUFFEX e.g. Bob's file *report.pdf* will be copied to DEST_DIR/report/Bob.pdf

#### Update
```guac update --name NAME --recipe RECIPE_NAME --task TASK --score SCORE``` 
* When grading manually, ```guac update``` updates the score for a single task

### View Grade
```guac grade --name NAME --recipe RECIPE_NAME```
Verbose flag will change grade detail 
* 0 $\rightarrow$ only final score
* 1 $\rightarrow$ include task summary
* 2 $\rightarrow$ include all tests

### Export to Canvas
```guac export --these STUDETNS_FILE --recipe RECIPE_NAME```

