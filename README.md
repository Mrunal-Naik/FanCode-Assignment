# FanCode-Assignment
Gives information of all the users belong to City `FanCode` who's Completed task percentage are greater than 50%

# Steps to Run Project:
Install Soap UI in your system.
Download FanCode-soapui-project.xml.
In SoapUI: Click on File --> Import Project --> Open XML.
Expand: FanCode(Project) --> FanCodeAssignment(TestSuite) --> FanCode City(TestCase)
Open FanCode City with User Completed Task more than 50%(Groovy Script) 
Run Groovy Script.

# Output
UserID: 1   City: Gwenborough
Task Completed: 55.00%
Task Left to Complete: 45.00%
UserID: 5   City: Roscoeview
Task Completed: 60.0%
Task Left to Complete: 40.0%
UserID: 10   City: Lebsackbury
Task Completed: 60.0%
Task Left to Complete: 40.0%

# Code in Groovy Script:
import groovy.json.JsonSlurper

testRunner.runTestStepByName("Get All Users") //Run Test Step Get All Users
def response = testRunner.testCase.testSteps["Get All Users"].getPropertyValue("Response") //Store response of Get All Users
def jsonSlurper = new JsonSlurper() //define json Slurper
def jsonResponse = jsonSlurper.parseText(response) //parse json data
def userID = jsonResponse.id.size() //get no. of userID present
def lat = jsonResponse.address.geo.lat //get lattitude values
def lng = jsonResponse.address.geo.lng //get longitude values

def fanCodeUsers = []   //Store FanCode UserID
def fanCodeCities = []  //Store FanCode Cities
for(int i=0 ; i<userID; i++)
{
	def latvalue = Double.parseDouble(lat[i])
	def lngvalue = Double.parseDouble(lng[i])
	if (latvalue >= -40 && latvalue <= 5 && lngvalue >= 5 && lngvalue <= 100) {  // lat between ( -40 to 5) and long between ( 5 to 100)
          fanCodeUsers << jsonResponse.id[i]
          fanCodeCities << jsonResponse.address[i].city
    }
}

for(int i=0; i<fanCodeUsers.size(); i++)
{
	log.info("UserID: " +fanCodeUsers[i] +"   City: "+fanCodeCities[i]) //Print UserID and City
	testRunner.testCase.testSuite.setPropertyValue("userID",fanCodeUsers[i].toString()) //Store UserID
	testRunner.runTestStepByName("Get ToDos Tasks for each User") //Run TestStep Get ToDos Tasks for each User
	def response1 = testRunner.testCase.testSteps["Get ToDos Tasks for each User"].getPropertyValue("Response") //Store Response
     def jsonResponse1 = jsonSlurper.parseText(response1) //parse json data
     def todos = jsonResponse1.completed.size() //get no. of todos task
     //Get no. of task completed and not completed
     def completed = 0
     def notCompleted = 0
     for (int j=0 ; j< todos; j++)
     {
     	if(jsonResponse1.completed[j] == true)
     	{
     		completed ++
     	}
     	else
     	{
     		notCompleted ++
     	}
     }
     def taskCompletPercentage = (completed/todos) * 100 //get percentage of task completed
     def taskInCompletPercentage = 100 - taskCompletPercentage //get percentage of incomplete Task
     log.info("Task Completed: "+taskCompletPercentage+"%") //print percentage of task completed
     log.info("Task Left to Complete: "+taskInCompletPercentage+"%") //print percentage of incomplete Task
}


