# CS643-AWS-ProgAssgn-2

 

Step 1: Launch an instance
You can launch a Linux instance using the AWS Management Console as described in the following procedure. 
To launch an instance
1.	Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.
2.	From the EC2 console dashboard, in the Launch instance box, choose Launch instance, and then choose Launch instance from the options that appear.
3.	Under Name and tags, for Name, enter a descriptive name for your instance.
4.	Under Application and OS Images (Amazon Machine Image), do the following:
a.	Choose Quick Start, and then choose Amazon Linux. This is the operating system (OS) for your instance.
b.	From Amazon Machine Image (AMI), select Ubuntu Server 20.04 LTS. Notice that these AMIs are marked Free tier eligible. An Amazon Machine Image (AMI) is a basic configuration that serves as a template for your instance.
5.	Under Instance type, from the Instance type list, you can select the hardware configuration for your instance. Choose the t2.large instance type, which is selected by default. The t2.large instance type is not eligible for the free tier. 
6.	Under Key pair (login), for Key pair name, choose the key pair that you created when getting set up.

Warning
Do not choose Proceed without a key pair (Not recommended). If you launch your instance without a key pair, then you can't connect to it.
7.	Next to Network settings, choose Edit. For Security group name, you'll see that the wizard created and selected a security group for you. You can use this security group, or alternatively you can check Allow SSH traffic from [Anywhere 0.0.0.0/0].
8.	For Configure storage, configure it from 8 GB to 16 GB (To install and configure modules/packages on the EC2 instances).
9.	Review a summary of your instance configuration in the Summary panel, and when you're ready, choose Launch instance.

Step 2: Change the permissions
right click on the file > properties > security > advanced > disable inheritance > 'Convert inherited permissions into explicit permission on this object'
Click on "allow | everyone | Full Control" > edit > 'select a principal' > type your username > 'check names' > select your username > ok > ok > ok (ok until all windows are closed)
Another inexplicable way to complicate things from Microsoft.

Step 3: Connect to the Instance
Connect to the EC2 instances via PuTTY using this Amazon Guide.
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html

Step 4: Connect to the Instance
Now, perform a pull down from the GitHub repository:
$ git clone https://github.com/Ramachetan/CS-643-PA-2
Navigate to the Directory:
$ cd CS643-AWS-ProgAssgn-2



Step 5: Connect Master nodes to Slave nodes

Create a bash script that will perform commands to install, extract, and remove the Apache-spark files on both the master and the four slave nodes.

$ vi auto.sh
Insert the following lines into the bash script:
#/bin/bash
sudo apt-get update
sudo apt-get install -y curl vim 
wget software-properties-common ssh net-tools ca-certificates 
sudo apt install -y default-jre
sudo wget --no-verbose -O apache-spark.tgz
"https://archive.apache.org/dist/spark/spark-3.2.0/spark-3.2.0-b in-hadoop2.7.tgz" 

sudo mkdir -p /opt/spark
sudo tar -xf apache-spark.tgz -C /opt/spark --strip-components=1 sudo rm apache-spark.tgz
Change permissions to execute the script:
$chmod 755 auto.sh
Now, create a script for the master to operate the Apache Spark Cluster. This is only for the master node
$vi master_node.sh






Insert the following lines into the bash script:
#/bin/bash
export SPARK_MASTER_PORT=7077 export SPARK_MASTER_WEBUI_PORT=8090 export SPARK_LOG_DIR=/opt/spark/logs
export SPARK_MASTER_LOG=/opt/spark/logs/spark-master.out export JAVA_HOME=/usr/bin/java
sudo mkdir -p $SPARK_LOG_DIR sudo touch $SPARK_MASTER_LOG sudo ln -sf /dev/stdout $SPARK_MASTER_LOG export SPARK_MASTER_HOST=`hostname`
cd /opt/spark/bin &&
sudo ./spark-class org.apache.spark.deploy.master.Master --ip
$SPARK_MASTER_HOST --port $SPARK_MASTER_PORT --webui-port
$SPARK_MASTER_WEBUI_PORT >> $SPARK_MASTER_LOG
Change permissions to execute the script:
$chmod 755 master_node.sh
Create a bash script for the slave nodes to run commands to install, extract and remove the Apache-spark files.
$ vi slave1.sh on 2nd EC2 instance and vice versa 
$ vi slave2.sh
$ vi slave3.sh
$ vi slave4.sh
Insert the following lines into the bash script:
#/bin/bash
export SPARK_MASTER_PORT=7077 export SPARK_MASTER_WEBUI_PORT=8080 export SPARK_LOG_DIR=/opt/spark/logs
export SPARK_MASTER_LOG=/opt/spark/logs/spark-master.out export JAVA_HOME=/usr/bin/java
export SPARK_MASTER_PORT=7077 \ export SPARK_MASTER_WEBUI_PORT=8080 \ export SPARK_LOG_DIR=/opt/spark/logs \
export SPARK_SLAVE_LOG=/opt/spark/logs/spark-slave.out \ export SPARK_SLAVE_WEBUI_PORT=8080 \ export SPARK_SLAVE_PORT=7000 \
export SPARK_MASTER="spark://ip-172-31-25-126:7077" \ export SPARK_LOCAL_IP
. "/opt/spark/bin/load-spark-env.sh"
sudo mkdir -p $SPARK_LOG_DIR sudo touch $SPARK_WORDER_LOG sudo ln -sf /dev/stdout $SPARK_SLAVE_LOG
cd /opt/spark/bin
sudo ./spark-class org.apache.spark.deploy.slave.Slave --webui-port $SPARK_SLAVE_WEBUI_PORT $SPARK_MASTER >>
$SPARK_SLAVE_LOG
Change permissions to execute the script:
$chmod 755 slave<x>.sh, x = 1,2,3,4
Run the auto.sh script to setup the Apache-spark clusters packages for both the master and four slave nodes.
$./auto.sh
Then, run the master.sh script to setup the Apache Spark Clusters, which will be used to open on the browser. Run the slave<x>.sh scripts to set up the slaves as well :
$./master.sh [for master node]
$./slave<x>.sh [for slave nodes, where <x> is 1,2,3,4]
Step 6: Training
Run this pip install commands before proceeding to run through the Training.py script:
$sudo apt install python3-pip
$pip install numpy
$pip install pandas
$pip install quinn
$pip install pyspark
$pip install findspark
$python3 /home/ubuntu/CS643-AWS-ProgAssgn-2/Training.py
After running the Training.py script, here is the output result received from the code:
 

Step 7: Prediction without Docker
Run the following command to install and configure Java:
$ export JAVA_HOME=/usr/bin/java
Run the following commands to install Anaconda:
$ cd /tmp $ curl -O https://repo.anaconda.com/archive/Anaconda3-2020.11-Linux-x86_64.sh
$ bash Anaconda3-2020.11-Linux-x86_64.sh
Now, without changing anything else in the file, add the following lines of code to the end of your ~/.bashrc file:
To edit ~/.bashrc file, Install nano 
pip install nano
and run 
nano ~/.bashrc
now add the following lines at the end the file
function snotebook ()
{
SPARK_PATH=~/opt/spark/spark-3.2.0-bin-hadoop2.7
export PYSPARK_DRIVER_PYTHON="jupyter" export PYSPARK_DRIVER_PYTHON_OPTS="notebook" export PYSPARK_PYTHON=python3
$SPARK_PATH/bin/pyspark --master local[2]
}
Run the following line of code to load the changes to your .bashrc file:
$ source ~/.bashrc
Close out of your SSH instance and SSH again into your EC2. Run the following code to configure Jupyter with a password (of your choice when prompted) and start up the Jupyter Notebook (without browser) on the EC2 instance.

$ jupyter notebook password
$ jupyter notebook --no-browser
Open a new terminal tab on your local machine and run the following command to setup an SSH tunnel so that you can open the Jupyter Notebook on the EC2:
$ ssh -i “ProgAssgn2.pem” -N -f -L localhost:8888:localhost:8888 ubuntu@<YOUR_INSTANCE_PUBLIC_DNS>
Now navigate to localhost:8888 in your browser and enter the Jupyter password that you set for your EC2 in the previous step.
Create a new Python 3 notebook (New -> Python3) once you've logged in. Paste the code from the prediction.py file (found here) into the first notebook cell. Several things will be written as the algorithm reads the data, prepares it, trains our model, predicts wine quality for Test Dataset, and eventually displays the F1 score.
The highest F1 score I observed (using the Validation Dataset) for the predictions model is: 0.8740347013466714.
Step 8: Prediction with Docker

Initialize docker on your ec2-instance using these steps.
Add the user ubuntu to the ‘docker’ group
$ sudo usermod -aG docker $USER
Login with the follow command:
$ docker login
1.	Enter your credentials
2.	Navigate to the folder containing the Docker file. This folder should also contain the model, Prediction.py, and the dataset to be used for prediction.
$ cd /home/ubuntu/ CS-643-PA-2/
3.	Build this docker image with:
$ docker build -t ramachetan22/cs643-progassgn-2 .
4.	Verify the docker image has been created after it has been built
$ docker images
5.	Run this image build with:
$ docker run -v /app/:/data ramachetan22/cs643-progassgn-2:latest
6.	After running the Predictions with Docker, here is the output result received from the code:

F1 Score for our Model: 0.8730216734867262

7.	Push this image to Docker Hub with:
$ docker push ramachetan22/cs643-progassgn-2:latest
Find that on Docker Hub.
