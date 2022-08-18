# SolutionsArchitectDiagram

Link to image:

https://viewer.diagrams.net/?tags=%7B%7D&highlight=0000ff&edit=_blank&layers=1&nav=1#G1wRyUZhNczFIRIUlq4lWncutpR-wNFufw


The Problem at Hand:


Company A runs a 3D printing shop. They want to let customers submit CAD files to their website and then receive an estimate by email for how much printing their design will cost.
They already have the Java code required to analyze and estimate the file. They also already have a fully functioning website already running in their current cloud provider.

They would like a design for a cloud-native solution that would let them add a “Get a Free Estimate!” feature to their existing website. Ideally, the team who wrote the CAD code should be able to run and manage this new feature without too much extra burden on the very busy team who manage the website. 

Walk through of the diagram:
1. The User is brought to a login page when they try to upload a cad file on the website.
2. After creating an account and logging in, an Api gateway is used to access a lambda function that authenticates users 
3. Cognito(AWS service) Sends to the first step function the user credentials such as the user's email address and name. It also gives the user a token which can be verified via the authenticator lambda function to allow a successful login
4. After the user has successfully logged in, they are given the chance to upload their CAD file to the website 
5. The first lambda function in the step function, accesses a dynamodb table in AWS and addes the users email address, the size of the file and the the time the file was uploaded.
6. The second lambda function adds the file to a queue using SQS as the CAD file may take a while to upload to the S3 bucket
7. A simple message is shown on the user’s screen after the dynamo db table is created confirming that the CAD file is being uploaded and has been added to the queue
8. After the upload is complete we move on to the second step function
9. The first Lambda function in this step function, pulls in the necessary values from the S3 bucket & the dynamodb table including things such as the size of the file, the user email etc.
10. The second lambda function takes in these values and estimates the cost to print the design and logs the price into the dynamodb table mentioned in step 5. 
11. Finally the estimated price is sent as an email to the user via the SES service in AWS. 

