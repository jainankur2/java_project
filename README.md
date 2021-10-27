
# part-1
### Run following command to run the container from the image
1.  docker run infracloudio/csvserver:latest

### Following are the logs generated from command from step 1, which clearly shows it is failed because inputdata directory does not exist inside the container
2. 2021/10/27 12:42:11 error while reading the file "/csvserver/inputdata": open /csvserver/inputdata: no such file or directory

### Run Following command to create script which will generated data in required format
3.1. echo -e '#!/bin/bash\nINPUT=$1\nif [[ $INPUT -eq "" ]]\nthen\n  INPUT=10\nfi\nfor i in $(seq 0 $INPUT);\ndo\n  echo "$i, $((1 + $RANDOM % 100000))";\ndone > inputFile' >> gencsv.sh

### Give executable permission to the script
3.2  chmod +x gencsv.sh

### Run Following command to create container from image and mount volume to copy inputfile insde the container
4. docker run -it -d --name csvserver --volume=`pwd`/inputFile:/csvserver/inputdata infracloudio/csvserver:latest

### Run Following command to login inside container find the port application is listening to and then remove the container
5.1 docker exec -it csvserver /bin/bash
5.2 netstat -tulpn | grep LISTEN
5.3 docker rm -f csvserver

### Create container again with CSVSERVER_BORDER variable and map port 9300 of container with local port 9393
6. docker run -it -d --name csvserver --volume=`pwd`/inputFile:/csvserver/inputdata -p 9393:9300 --env CSVSERVER_BORDER=Orange infracloudio/csvserver:latest
