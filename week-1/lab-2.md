Lab: Recommending Products Using Cloud SQL and Spark
In this lab, you populate rentals data in Cloud SQL for the rentals recommendation engine to use. The recommendations engine itself will run on Dataproc using Spark ML.

Create Cloud SQL instance

Create database tables by importing .sql files from Cloud Storage

Populate the tables by importing .csv files from Cloud Storage

Allow access to Cloud SQL

Explore the rentals data using SQL statements from CloudShell

Tips for Course Labs
Get the most out of Coursera and Qwiklabs by trying our tips below.

Avoid account confusion with private browsing
Close this page and log back in to Coursera in Incognito mode before moving on. When you return to this course and lab instructions page, click Open Tool to continue.

By using incognito mode, this ensures that you don't accidentally use your own Google account (including Gmail) while accessing the Google Cloud Console. This also prevents Qwiklabs from logging you out of your own Google accounts.

Detailed instructions for using Incognito mode in Google Chrome are available here. Depending on your browser, Incognito mode might also be called Private Browsing or InPrivate Browsing.

To ensure lab completion is marked in Coursera:
1. Access each individual lab by clicking Open Tool in Coursera:


2. Complete the lab in Qwiklabs.

3. Click End Lab in Qwiklabs:


4. Close the Qwiklabs browser window or tab.

Note: You are limited to 3 attempts of this lab. If you need support, submit a request to Qwiklabs Support

By opening the tool, you are agreeing to Qwiklabs' Terms of Service.

This course uses a third-party tool, Lab: Recommending Products Using Cloud SQL and Spark, to enhance your learning experience. The tool will reference basic information like your name, email, and Coursera ID.

Coursera Honor Code  Learn more

I understand that submitting work that isn’t my own may result in permanent failure of this course or deactivation of my Coursera account.


Recommending Products Using Cloud SQL and Spark
1 hour 30 minutes
Free
Overview
In this lab, you populate rentals data in Cloud SQL for the rentals recommendation engine to use. The recommendations engine itself will run on Dataproc using Spark ML.

Objectives
In this lab, you learn how to perform the following tasks:

Create a Cloud SQL instance

Create database tables by importing .sql files from Cloud Storage

Populate the tables by importing .csv files from Cloud Storage

Allow access to Cloud SQL

Explore the rentals data using SQL statements from Cloud Shell

Set up your environments
Qwiklabs setup
For each lab, you get a new GCP project and set of resources for a fixed time at no cost.

Make sure you signed into Qwiklabs using an incognito window.

Note the lab's access time (for example, img/time.png and make sure you can finish in that time block.

There is no pause feature. You can restart if needed, but you have to start at the beginning.

When ready, click img/start_lab.png.

Note your lab credentials. You will use them to sign in to Cloud Platform Console. img/open_google_console.png

Click Open Google Console.

Click Use another account and copy/paste credentials for this lab into the prompts.

If you use other credentials, you'll get errors or incur charges.

Accept the terms and skip the recovery resource page.
Do not click End Lab unless you are finished with the lab or want to restart it. This clears your work and removes the project.

Check project permissions
Before you begin your work on Google Cloud, you need to ensure that your project has the correct permissions within Identity and Access Management (IAM).

In the Google Cloud console, on the Navigation menu (Navigation menu icon), click IAM & Admin > IAM.

Confirm that the default compute Service Account {project-number}-compute@developer.gserviceaccount.com is present and has the editor role assigned. The account prefix is the project number, which you can find on Navigation menu > Home.

Default compute service account name

If the account is not present in IAM or does not have the editor role, follow the steps below to assign the required role.

In the Google Cloud console, on the Navigation menu, click Home.

Copy the project number (e.g. 729328892908).

On the Navigation menu, click IAM & Admin > IAM.

At the top of the IAM page, click Add.

For New principals, type:

  {project-number}-compute@developer.gserviceaccount.com
Copied!
Replace {project-number} with your project number.

For Role, select Project (or Basic) > Editor. Click Save.
Task 1. Create a Cloud SQL instance
In the Google Cloud Console, Select Navigation menu > SQL (in the Databases section).

Click Create instance.

Click Choose MySQL.

For Instance ID, type rentals.

ab1cdf08212ecadf.png

Scroll down and specify a Root password. Before you forget, note down the root password.

For Region select us-central1.

Click Create instance to create the instance. It will take a minute or so for your Cloud SQL instance to be provisioned.

Task 2. Create tables
While you wait for your instance to be created, read the below mySQL script and answer the questions that follow.

CREATE DATABASE IF NOT EXISTS recommendation_spark;
USE recommendation_spark;
DROP TABLE IF EXISTS Recommendation;
DROP TABLE IF EXISTS Rating;
DROP TABLE IF EXISTS Accommodation;
CREATE TABLE IF NOT EXISTS Accommodation
(
  id varchar(255),
  title varchar(255),
  location varchar(255),
  price int,
  rooms int,
  rating float,
  type varchar(255),
  PRIMARY KEY (ID)
);
CREATE TABLE  IF NOT EXISTS Rating
(
  userId varchar(255),
  accoId varchar(255),
  rating int,
  PRIMARY KEY(accoId, userId),
  FOREIGN KEY (accoId)
    REFERENCES Accommodation(id)
);
CREATE TABLE  IF NOT EXISTS Recommendation
(
  userId varchar(255),
  accoId varchar(255),
  prediction float,
  PRIMARY KEY(userId, accoId),
  FOREIGN KEY (accoId)
    REFERENCES Accommodation(id)
);
SHOW DATABASES;
Copied!

How many tables will this script create?

1

3

2


When a user rates a house (giving it four stars for example), an entry is added to the _______ table.

Recommendation

Rating

Accommodation


General information about houses, such as the number of rooms they have and their average rating is stored in the _________ table.

Recommendation

Rating

Accommodation


The job of the recommendation engine is to fill out the ___________ table for each user and house: this is the predicted rating of that house by that user.

Recommendation

Rating

Accommodation

In Cloud SQL, click rentals to view instance information.

Connect to the database
Find the Connect to this instance box on the page and click on Open Cloud Shell.
Note: You could also connect to your instance from a dedicated Cloud Compute Engine VM but for now you'll have Cloud Shell create a micro-VM for you and operate from there.
If required, click Continue. Wait for Cloud Shell to load.

Once Cloud Shell loads, you will see the below command already typed:

gcloud sql connect rentals --user=root --quiet

Press ENTER.

Wait for your IP Address to be whitelisted.

Allowlisting your IP for incoming connection for 5 minutes...⠹
When prompted, enter your password and press ENTER (note: you will not see your password typed in or even ****).
You can now run commands against your database! mysql-connection.png

Run the following command:
SHOW DATABASES;
Copied!
You should see the default system databases:

+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
Note: You must always end your mySQL commands with a semi-colon ;
Copy and paste the below SQL statement you analyzed earlier into the command line.

CREATE DATABASE IF NOT EXISTS recommendation_spark;
USE recommendation_spark;
DROP TABLE IF EXISTS Recommendation;
DROP TABLE IF EXISTS Rating;
DROP TABLE IF EXISTS Accommodation;
CREATE TABLE IF NOT EXISTS Accommodation
(
  id varchar(255),
  title varchar(255),
  location varchar(255),
  price int,
  rooms int,
  rating float,
  type varchar(255),
  PRIMARY KEY (ID)
);
CREATE TABLE  IF NOT EXISTS Rating
(
  userId varchar(255),
  accoId varchar(255),
  rating int,
  PRIMARY KEY(accoId, userId),
  FOREIGN KEY (accoId)
    REFERENCES Accommodation(id)
);
CREATE TABLE  IF NOT EXISTS Recommendation
(
  userId varchar(255),
  accoId varchar(255),
  prediction float,
  PRIMARY KEY(userId, accoId),
  FOREIGN KEY (accoId)
    REFERENCES Accommodation(id)
);
SHOW DATABASES;
Copied!
Press ENTER.

Confirm that you now see recommendation_spark as a database:

+----------------------+
| Database             |
+----------------------+
| information_schema   |
| mysql                |
| performance_schema   |
| recommendation_spark |
| sys                  |
+----------------------+
Run the following command to show the tables:

USE recommendation_spark;
SHOW TABLES;
Copied!
Press ENTER.

Confirm that you see the three tables:

+--------------------------------+
| Tables_in_recommendation_spark |
+--------------------------------+
| Accommodation                  |
| Rating                         |
| Recommendation                 |
+--------------------------------+
Run the following query:
SELECT * FROM Accommodation;
Copied!

How many rows are in the Accommodation table?

Empty set (0)

100

1,000

Task 3. Stage data in Cloud Storage
Option 1: Use the command line
Open a new Cloud Shell tab (do not use your existing mySQL Cloud Shell tab).

Copy and paste the following command:

echo "Creating bucket: gs://$DEVSHELL_PROJECT_ID"
gsutil mb gs://$DEVSHELL_PROJECT_ID
echo "Copying data to our storage from public dataset"
gsutil cp gs://cloud-training/bdml/v2.0/data/accommodation.csv gs://$DEVSHELL_PROJECT_ID
gsutil cp gs://cloud-training/bdml/v2.0/data/rating.csv gs://$DEVSHELL_PROJECT_ID
echo "Show the files in our bucket"
gsutil ls gs://$DEVSHELL_PROJECT_ID
echo "View some sample data"
gsutil cat gs://$DEVSHELL_PROJECT_ID/accommodation.csv
Copied!
Press ENTER.

Option 2: Use the Cloud Console UI
Skip these steps if you have already loaded your data using the command line.

Navigate to Storage and select Cloud Storage > Browser.

Click Create Bucket (if one does not already exist).

Specify your project name as the bucket name.

Click Create.

Download the below files locally and then upload them inside of your new bucket:

accommodation.csv

rating.csv

Task 4. Load data from Cloud Storage into Cloud SQL tables
Navigate back to SQL.

Click on rentals.

Import accommodation data
Click Import (top menu).

Specify the following:

Source: Click Browse > [Your-Bucket-Name] > accommodation.csv

Click Select.

Format of import: CSV

Database: select recommendation_spark from the dropdown list

Table: copy and paste: Accommodation

Click Import.
importdata

You will be redirected back to the Overview page. Wait one minute for the data to load.

Import user rating data
Click Import (top menu).

Specify the following:

Source: Click Browse > [Your-Bucket-Name] > rating.csv

Click Select.

Format of import: CSV

Database: select recommendation_spark from the dropdown list

Table: copy and paste: Rating

Click Import.

You will be redirected back to the Overview page. Wait one minute for the data to load.

Task 5. Explore Cloud SQL data
If you closed your Cloud Shell connection to mySQL, open it again by finding Connect to this instance and clicking Open Cloud Shell.

Press ENTER when prompted to log in.

Provide your password and press ENTER.

Query the ratings data:

USE recommendation_spark;
SELECT * FROM Rating
LIMIT 15;
Copied!
Use a SQL aggregation function to count the number of rows in the table.
SELECT COUNT(*) AS num_ratings
FROM Rating;
Copied!

How many ratings are in the table?

1186

100,000

5,203

What is the average review rating of accommodations?
SELECT
    COUNT(userId) AS num_ratings,
    COUNT(DISTINCT userId) AS distinct_user_ratings,
    MIN(rating) AS worst_rating,
    MAX(rating) AS best_rating,
    AVG(rating) AS avg_rating
FROM Rating;
Copied!

What is the average rating across all reviews?

4.51

5.0

2.46


What does the 25 for distinct_user_ratings mean?

There are 25 ratings per accommodation

There are 25 unique users who provided the ratings

There are 25 ratings per user

In machine learning, you will need a rich history of user preferences for the model to learn from. Run the below query to see which users have provided the most ratings.

SELECT
    userId,
    COUNT(rating) AS num_ratings
FROM Rating
GROUP BY userId
ORDER BY num_ratings DESC;
Copied!

How many reviews did the top user leave?

100

50

75

Exit the mysql prompt by typing exit.

Task 6. Launch Dataproc
You use Dataproc to train the recommendations machine learning model based on users' previous ratings. You then apply that model to create a list of recommendations for every user in the database

To launch Dataproc and configure it so that each of the machines in the cluster can access Cloud SQL:

In the Cloud Console, on the Navigation menu (Navigation menu), click SQL and note the region of your Cloud SQL instance:

fc8f254ae64a75b4.png

In the snapshot above, the region is us-central1 and zone is us-central1-c.

In the Cloud Console, on the Navigation menu (Navigation menu), click Dataproc and click Enable API if prompted.

Once enabled, click Create cluster and name your cluster rentals.

Leave the Region as it is i.e. us-central1 and change the Zone to us-central1-c (in the same zone as your Cloud SQL instance). This will minimize network latency between the cluster and the database.

Click on Configure nodes.

For Master node, for Machine type, select n1-standard-2 (2 vCPUs, 7.5 GB memory).

For Worker nodes, for Machine type, select n1-standard-2 (2 vCPUs, 7.5 GB memory).

Leave all other values with their default and click Create. It will take 1-3 minutes to provision your cluster.

Note the Name, Zone and Total worker nodes in your cluster.

Copy and paste the below bash script into your Cloud Shell (optionally change CLUSTER, ZONE, NWORKERS if necessary before running)

echo "Authorizing Cloud Dataproc to connect with Cloud SQL"
CLUSTER=rentals
CLOUDSQL=rentals
ZONE=us-central1-c
NWORKERS=2
machines="$CLUSTER-m"
for w in `seq 0 $(($NWORKERS - 1))`; do
   machines="$machines $CLUSTER-w-$w"
done
echo "Machines to authorize: $machines in $ZONE ... finding their IP addresses"
ips=""
for machine in $machines; do
    IP_ADDRESS=$(gcloud compute instances describe $machine --zone=$ZONE --format='value(networkInterfaces.accessConfigs[].natIP)' | sed "s/\['//g" | sed "s/'\]//g" )/32
    echo "IP address of $machine is $IP_ADDRESS"
    if [ -z  $ips ]; then
       ips=$IP_ADDRESS
    else
       ips="$ips,$IP_ADDRESS"
    fi
done
echo "Authorizing [$ips] to access cloudsql=$CLOUDSQL"
gcloud sql instances patch $CLOUDSQL --authorized-networks $ips
Copied!
Press ENTER. When prompted, type Y, then press ENTER again to continue.

Wait for the patching to complete. You will see the following:

Patching Cloud SQL instance...done.
On the main Cloud SQL page, under Connect to this instance, copy your Public IP Address to your clipboard. (Alternatively, write it down because you're using it next.)
copy_public_ip

Task 7. Run the ML model
Next, you create a trained model and apply it to all the users in the system. Your data science team has created a recommendation model using Apache Spark and is written in Python. Copy it over into your staging bucket.

Copy over the model code by executing the below commands in Cloud Shell:
gsutil cp gs://cloud-training/bdml/v2.0/model/train_and_apply.py train_and_apply.py
cloudshell edit train_and_apply.py
Copied!
When prompted, select Open in New Window.

Wait for the Editor UI to load.

Open the train_and_apply.py file, find line 30: CLOUDSQL_INSTANCE_IP, and paste the Cloud SQL IP address you copied earlier.

# MAKE EDITS HERE
CLOUDSQL_INSTANCE_IP = '<paste-your-cloud-sql-ip-here>'   # <---- CHANGE (database server IP)
CLOUDSQL_DB_NAME = 'recommendation_spark' # <--- leave as-is
CLOUDSQL_USER = 'root'  # <--- leave as-is
CLOUDSQL_PWD  = '<type-your-cloud-sql-password-here>'  # <---- CHANGE
Find line 33: CLOUDSQL_PWD and type in your Cloud SQL password,

The editor will autosave but to be sure, select File > Save.

From the Cloud Shell ribbon, click on the Open Terminal icon and copy this file to your Cloud Storage bucket using this Cloud Shell command:

gsutil cp train_and_apply.py gs://$DEVSHELL_PROJECT_ID
Copied!
Task 8. Run your ML job on Dataproc
In the Dataproc console, click rentals cluster.

Click Submit job.

For Job type, select PySpark and for Main python file, specify the location of the Python file you uploaded to your bucket. Your <bucket-name> is likely to be your Project ID, which you can find by clicking on the Project ID dropdown in the top navigation menu.

e15bafe2c29956b5.png

gs://<bucket-name>/train_and_apply.py

For Max restarts per hour, enter 1.

Click Submit.

Select Navigation menu > Dataproc > Job tab to see the Job status.

Note: It will take up to 5 minutes for the job to change from Running to Succeeded. You can continue to the next section on querying the results while the job runs. If the job Failed, please troubleshoot using the logs and fix the errors. You may need to re-upload the changed Python file to Cloud Storage and clone the failed job to resubmit.
Task 9. Explore inserted rows with SQL
In a new browser tab, open SQL (in the Databases section).

Click rentals to view details related to your Cloud SQL instance.

Under Connect to this instance section, click Open Cloud Shell. This will start a new Cloud Shell tab. In the Cloud Shell tab press ENTER.

It will take a few minutes to allow your IP for the incoming connection.

When prompted, type the root password you configured, then press ENTER.

At the mysql prompt, type:

USE recommendation_spark;
SELECT COUNT(*) AS count FROM Recommendation;
Copied!
If you are getting an Empty Set (0) - wait for your Dataproc job to complete. If it's been more than 5 minutes, your job has likely failed and will require troubleshooting.

Tip: You can use the up arrow in Cloud Shell to return your previous command (or query in this case)


How many recommendations did the model provide?

50

125

100

Find the recommendations for a user:

SELECT
    r.userid,
    r.accoid,
    r.prediction,
    a.title,
    a.location,
    a.price,
    a.rooms,
    a.rating,
    a.type
FROM Recommendation as r
JOIN Accommodation as a
ON r.accoid = a.id
WHERE r.userid = 10;
Copied!
Your result should be similar to the below result:

+--------+--------+------------+-----------------------------+...
| userid | accoid | prediction | title                       |...
+--------+--------+------------+-----------------------------+...
| 10     | 41     |  1.7748766 | Big Calm Manor              |...
| 10     | 21     |  1.7174504 | Big Peaceful Cabin          |...
| 10     | 46     |  1.7159091 | Colossal Private Castle     |...
| 10     | 31     |  1.5783813 | Colossal Private Castle     |...
| 10     | 32     |  1.5584077 | Immense Private Hall        |...
+--------+--------+------------+-----------------------------+...
These are the five accommodations that you would recommend. Note that the quality of the recommendations is not great because the dataset was so small (note that the predicted ratings are not very high). Still, this lab illustrates the process you'd go through to create product recommendations.

Congratulations!
You have populated rentals data in Cloud SQL for the rentals recommendation engine to use.

Recap:
In this lab, you:

Created a fully-managed Cloud SQL instance for rentals
Created tables and explored the schema with SQL
Ingested data from CSVs
Edited and ran a Spark ML job on Dataproc
Viewed prediction results

