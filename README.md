# XGBoost-EMR-Build-Recipe

This recipe will instruct how to use XGboost library on Apache Spark on AWS ElasticMapReduce 5.10

## build the jar
First, ssh into an EMR EC2 instance, then


### install cmake
Do not install `cmake` from `yum`, as the version from yum repository is out of date (2.8 as opposed to 3.3+). Instead, build from source:

	wget https://cmake.org/files/v3.10/cmake-3.10.0.tar.gz
	tar xzf cmake-3.10.0.tar.gz
	cd cmake-3.10.0
	./bootstrap --prefix=/usr
	make
	sudo make install
### install maven
	wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo
	sed -i s/\$releasever/6/g /etc/yum.repos.d/epel-apache-maven.repo
	sudo yum install -y apache-maven
	mvn --version

### install git
	sudo yum -y install git

## Build XGBoost 
### Build the native part
	git clone --recursive https://github.com/dmlc/xgboost
	cd xgboost
	make -j4
	
### Build XGBoost for Java (with JNI)
Add enviroment variable for `JAVA_HOME`

	export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk.x86_64
Build the source from `maven`

	cd jvm-packages
	mvn package -DskipTests

### (Optional) Upload the newly-built jars to S3
After the previous steps, there should be 8 jars built. Use command to list all of them, 
    find . -name "\*.jar"
You will need `xgboost4j-0.7-jar-with-dependencies.jar` and `xgboost4j-spark-0.7-jar-with-dependencies.jar`.
Upload to S3 with command `aws s3 cp`

## Use XGBoost in a SBT project
First, copy the 2 jars into `*project*/lib`
Then add the following lines to `build.sbt` file

	val xgboostSparkPath = "file://" + new File(".").getAbsolutePath + "/lib/xgboost4j-spark-0.7-jar-with-dependencies.jar"
	val xgboostPath = "file://" + new File(".").getAbsolutePath + "/lib/xgboost4j-0.7-jar-with-dependencies.jar"
	
	libraryDependencies ++= Seq(
	  "ml.dmlc" % "xgboost4j" % "0.7"  % "provided" from xgboostPath,
  	  "ml.dmlc" % "xgboost4j-spark" % "0.7"  % "provided" from xgboostSparkPath
	)

After successfully executing all the previous steps, you can use XGBoost on an EMR Spark cluster.



