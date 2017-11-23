# XGBoost-EMR-Build-Recipe

This recipe will instruct how to use XGboost library on Apache Spark on AWS ElasticMapReduce 5.10

## build the jar
First, on an EC2 instance, then install `cmake`


### cmake
Do not install `cmake` from yum, as the version from yum repository is out of date (2.8 as opposed to 3.3+). Instead, build fomr source:
		wget https://cmake.org/files/v3.10/cmake-3.10.0.tar.gz
		tar xzf cmake-3.10.0.tar.gz
		cd cmake-3.10.0
		./bootstrap --prefix=/usr
		make
		sudo make install
