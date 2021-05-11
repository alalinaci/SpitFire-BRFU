# SpitFire C8000 (iosxr) Show Commands 

This project intends to run certain tests on iosxr routers and check if the conditions meet. Subsequent to which it classifies the test as pass or fail. Broad steps involved to achieve the same are:
* Retrieve the output using iosxr commands
* Process the output using genie generic parsers or by creating a custom parser
* Check for the condition

### Commands that respond well to genie generic are:
* show media
* show platform
* show hardware module

### Commands that respond to custom parsers:
* show filesystem 
* show controllers fabric plane all 
* show environment power

### Commands that use string slicing through jinja2 are:
* show context
* show led

### Miscellaneous Commands:
* show version
* show install active summary
* show install commit summary

## Leveraging genie generic based command checks
* Pre-requisites
  * Ansible
  * Jmespath (`pip install jmespath`)
  * pyats (`pip install pyats`)
  * genie-parser (`pip install genie-parser`)
* Steps to Reproduce
  * Populate hosts file
  * Excute mainplaybook.yml using `ansible-playbook -i hosts main_playbook.yml`
* Result
  * The above steps run a recap, showing output as "Passed" or "Failed" after each command test
  * It produces a combined "Passed"/"Failed" status in the end (by displaying the content of `./roles/file/output.txt`)
* Getting to Know the Files
  * `./roles/file/genie_generic_test_show_xyzcommand.yml` contains the playbook to retrieve cli output, parse and check for the condition
  * `./roles/file/genie_generic_show_xyzcommand.yml` contains the schema for the expected structured output and an index value specifying the index for primary key
  * for a detailed ducumentation refer to [Generic Tabular Parsing](https://github.com/clay584/parse_genie)  
  * `main_playbook.yml` contains the combined roles
  * `hosts` contains the details of the hosts (eg: HOSTNAME ansible_host=xx.xx.xx.xx ansible_network_os=iosxr ansible_user=xyz ansible_ssh_pass=abc) 
  * `./roles/Summary/tasks/main.yml` contains a playbook to clear the `output.txt` content after each test run, and push in new outputs after a successful run
  * `./roles/file/output.txt` contains the summary that needs to be printed towards the end, this file gets populated as the playbooks run, and is wiped out after its contents are displayed 


## Leveraging custom genie parser based command check
-  Pre-requisites
   -  To be able to use the custom parsers defined in the forked repository, virtual environment has to be activated.
      -  Create a virtual environment. `python3 -m venv .venv`
      -  Activate the virtual environment. `source .venv/bin/activate`
     
   -  Some packages and libraries are needed to be installed and updated to work with the custom genie parsers. If pyats, genie, and clay584.parse_genie are already installed, then skip to third line.
     ```
     $ pip install pyats genie
     $ ansible-galaxy install clay584.parse_genie
     $ apt update 
     $ pip install make
     $ pip install pyats.contrib
     ```
     
   -  Then [a forked repository genieparser](https://github.com/deepB123/genieparser.git)  has to be cloned which has the custom parsers. Create/Choose and enter a directory to keep the cloned repository before running the `git clone https://github.com/deepB123/genieparser.git` command. 
     ```
     $ cd genieparser
     $ make develop
     $ make json
     ```
   To verify, run `pip list` and check if `genie.libs.parser` has the location of the cloned genieparser.
  
   *To be used in same virtual environment as shared in the pre-requisites section under Leveraging custom genie parser based command check.*
