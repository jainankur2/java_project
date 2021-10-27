
# part-1
1.  docker run infracloudio/csvserver:latest

2. 2021/10/27 12:42:11 error while reading the file "/csvserver/inputdata": open /csvserver/inputdata: no such file or directory

3.1. echo -e '#!/bin/bash\nINPUT=$1\nif [[ $INPUT -eq "" ]]\nthen\n  INPUT=10\nfi\nfor i in $(seq 0 $INPUT);\ndo\n  echo "$i, $((1 + $RANDOM % 100000))";\ndone > inputFile' >> gencsv.sh
3.2  chmod +x gencsv.sh

4. docker run -it -d --name csvserver --volume=`pwd`/inputFile:/csvserver/inputdata infracloudio/csvserver:latest

5.1 docker exec -it csvserver /bin/bash
5.2 netstat -tulpn | grep LISTEN
5.3 docker rm -f csvserver

6. docker run -it -d --name csvserver --volume=`pwd`/inputFile:/csvserver/inputdata -p 9393:9300 --env CSVSERVER_BORDER=Orange infracloudio/csvserver:latest
