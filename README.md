**Note:**	Please view this file in a web browser. Detailed Project report is present in a different file: [ProjectReport](https://bitbucket.org/chinmay2312/amrish_jhaveri_chinmay-gangal_cp/raw/master/Project%20Report.pdf "ProjectReport")

# DevOps-Pipeline-Analyzer

DevOps-Pipeline-Analyzer collects large number of DevOps pipeline programs and obtains statistical data that describe the content and patterns in DevOps pipelines.

## Getting Started

----------

### Prerequisites

- Node `v9.9.0`(NPM is installed with Node)
- Python `v3.6.1`. Also `pip` & `pip install scipy`.
- Docker `v18.01.0-ce` running on `192.168.99.100` (if different then change the IP in code files).

### Installing

1. Assuming docker is running on your system. We have to install Jenkins on the docker. Open docker terminal and execute the following command:

	`docker run --detach --name jenkins -p 9080:8080 -p 50000:50000 ajhave5/jenkins:latest`
	*8080 : exposes web interface,	50000: access to remote JAVA API.*

	Open the URL `192.168.99.100:9080` . Follow the instructions of installation and install the defualt plugins(pipeline-model-definition should be there).Create a `admin` user with password `123456789`. Important that you keep the username and password same as given above since Basic Authorization is used to connect to the Jenkins Server. If asked for a key during installation then use the command:
	`docker logs jenkins` and you will find the key there and then setup the `admin` user.

2. Make sure `node` and `npm` is installed on the system. To check run the following commands from command prompt or shell: `node -v` & `npm -v`.

3. Run `npm install` from the directory where `package.json` is present.
This will install the required modules present in `package.json` into the project directory inside `node_modules` directory.

### Project Structure
Inside `app` folder various folders like `q1`,`q2`,`q3` and `q4` are present. Each represent the research questions we are answering.
Inside each such folder, a JS file, Python script and multiple JSON files will be seen.

1. JS File collects the data from Github, hits Jenkins's pipeline-model-definition API to convert each Jenkinsfile into a JSON representation, parse this JSON to answer the research question and output the results into a `finalOutput.json`.
2. Various intermediate files are created which are required for the Python Script.
3. Python Script reads the `intermediateOutput.json` and uses to calculate co-relation co-efficient or create distribution graphs as required.
4. If its a co-relation co-efficient result than its updated in the `finalOutput.json`. If graphs than they are saved to the same question folder. 

### Running the Application
**Important Note-** *It is possible to run all 4 files(q1.js,q2.js,q3.js,q4.js) simultaneously, we would advice you to run them independently. If you don't then a issue 'You have triggered an abuse detection mechanism.' will arise form the Github API end.*

Follow the below steps for each question:

- Navigate to folder `q1`.
- Run the file `q1.js` by the command `node q1.js`. Once completed, it will create the output files as required.
- Once completed, you can run the python script in that particular folder: `python post_section.py` 
- The graph will be created.

If issue like this occur: 

    Traceback (most recent call last):
	  File "post_section.py", line 28, in <module>
	fig.savefig('post_section.jpg')
	  File "F:\Program Files\Python\Python36\lib\site-packages\matplotlib\figure.py", line 1834, in savefig
	self.canvas.print_figure(fname, **kwargs)
	  File "F:\Program Files\Python\Python36\lib\site-packages\matplotlib\backend_bases.py", line 2170, in print_figure
	canvas = self._get_output_canvas(format)
	  File "F:\Program Files\Python\Python36\lib\site-packages\matplotlib\backend_bases.py", line 2113, in _get_output_canvas
	'%s.' % (format, ', '.join(formats)))
	ValueError: Format "jpg" is not supported.
	Supported formats: eps, pdf, pgf, png, ps, raw, rgba, svg, svgz.

then execute `pip3 install pillow`.

## Architecture
----------
![](https://bitbucket.org/chinmay2312/amrish_jhaveri_chinmay-gangal_cp/raw/master/images/Architecture_db_add.png)

- A Jenkins Server will be running on the local Docker VM exposed via `9080` port and `192.168.99.100` IP address.
- The NodeJS application will collect the data from Github via its API and then pass the jenkinsfile to pipeline-model-definition API of Jenkins.
- Once response is received we retrieve the required data from the JSON representation of the Jenkinsfile and use the data to answer the research questions.
- Final Output is stored in JSON files in a pre-defined structure(shown later).
- IntermediateOutput JSON files are used by python scripts to create the graphs or calculate co-relation co-efficients.

##Detailed Report
----------

Please find the report of the analysis by opening the following document:

[https://bitbucket.org/chinmay2312/amrish_jhaveri_chinmay-gangal_cp/raw/master/Project%20Report.pdf](https://bitbucket.org/chinmay2312/amrish_jhaveri_chinmay-gangal_cp/raw/master/Project%20Report.pdf "Project_Report")



##Flow Chart for Q1:
----------
The below image shows the flow for Q1. Flow for other questions is similar to this:

![](https://bitbucket.org/chinmay2312/amrish_jhaveri_chinmay-gangal_cp/raw/master/images/q1.png)

## Output Structure
----------
The output present in `finalOutput.json` will have the following structure :

Attribute Name|Data Type|Purpose
--|--|--
research_question_1a|JSON String|What are the most frequent post-condition blocks in the post section within jenkins pipelines? Create distribution graphs for post-condition blocks.
counts_of_post_elements|JSON Object|Only for Q1. Counts of various conditional blocks found in POST section of jenkins file
research_question_1b|JSON String|What are the most frequent activities in the post section conditional blocks within jenkins pipelines? Create distribution graphs for post-condition blocks.
counts_of_activities_in_post_blocks|JSON Object|Only for Q1. Counts of various activities executed inside the conditional blocks of POST section.
research_question_2|JSON String|How is the presence of triggers in a pipeline correlates with the number of stages in the pipeline? What are the common types of triggers used in pipelines?
corelation_coefficient|JSON String or JSON Number|Only for Q2. The actual value is calculated from the python script.
counts_of_types_of_triggers|JSON Object|Only for Q2. Counts of different triggers present in the triggers section.
counts_of_triggers_and_stages|JSON Array|Only for Q2. A list of no. of triggers and stages for each jenkisnfile.
research_question_3a|JSON String|What are the most and the least frequent commands in pipeline stages?
counts_commands_in_stages_operations|JSON Object| Only for Q3. stores objects for each stage with a list of for commands executed for that stage.
research_question_3b|JSON String| What are the most and the least frequent operations in pipeline stages?
counts_of_operation_stages|JSON Object| Only for Q3.Stores counts for each operation stages.
research_question_4|JSON String| For stages in parallel, fail-fast feature is used for what type of operations(stage names)? When it is used/unused, what are the operations running in parallel?
parallel_stages_analysis|JSON Array|Only for Q4. A list containing objects for each jenkinsfile with data of outer_stage_name,parallel stages, fail-fast used or not.
valid_jenkinsfiles_scanned|JSON Number|Keeps the count of the valid files scanned for the analyses.
project_details|JSON Array|List of Jenkinsfile's Project and the parsed JSON output of the jenkinsfile.


##Issues Faced:
----------
1. Jenkins API Bottleneck:
	
	Many simultaneous requests are sent to Jenkins pipeline-model-definition API which converts the Jenkinsfile and provides in JSON form. If port is not free for new request, the request returns an error code `ECONNRESET`. 
	
	Currently we made recursive calls until we get a valid response or some other error message. This recursive call is limited by the number provided else it goes into infinite loop.
	
2. Github `Abuse Detection Mechanism`:
 
	If the application is run from index.js in root level, all questions module would be running simultaneously. This is detected by Github API as a `You have triggered an abuse detection mechanism.`. So please execute manually all the questions.


## Built With

----------

* [Node](https://nodejs.org/en/) - Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient.
* [Octokit](https://github.com/octokit/rest.js) - GitHub REST API client for Node.js
* [Python](https://www.python.org/) - Used for graph creation and co-relation factor calculations
* [Matplotlib](https://matplotlib.org/) - Python library which is used for graph creation.
* [Pipeline-model-definition-plugin of Jenkins](https://github.com/jenkinsci/pipeline-model-definition-plugin/blob/master/EXTENDING.md#conversion-to-json-representation-from-jenkinsfile) - Takes a Jenkinsfile and converts it to the JSON representation for its pipeline step.
* [Docker](https://www.docker.com/) - Serves as platform for various application containers.
* [Jenkins](https://jenkins.io/) - Server to automate building, testing and deploying software.

## Authors

----------

* [**Amrish Jhaveri**](https://github.com/AmrishJhaveri)
* [**Chinmay Gangal**](https://github.com/chinmay2312)
