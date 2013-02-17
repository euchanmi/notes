## PV - Pipeview

pv is a terminal-based tool for monitoring the progress of data through a pipeline. It can be inserted into any normal pipeline between two processes to give a visual indication of how quickly data is passing through, how long it has taken, how near to completion it is, and an estimate of how long it will be until completion. 

   * brew install pv => to install pv in Mac
   * zcat dsp.sql.gz | pv | mysql -u root dsp -p toor


## AB(Apache Benchmark)

   * is a benchmark tool that is very good for load testing 
   * ab -n 10000 -c 1000 -r "http://test.com" 
   * The commend above send 10000 GET requests with 1000 threads to "http://test.com"
   * Use -p option to send POST as shown below:
-p postfile     File containing data to POST. Remember also to set -T
      * Make EXTRA sure your post file is properly URL encoded with no extra non-printing characters or anything at the end. The most error-free way is just create it with code. I used some python to create mine:
>>>import urllib
>>> outfile = open('post.data','w')
>>>params=({'auth_token':'somelongstringthatendswithanequalssign='})
>>> encoded = urllib.urlencode(params)
>>> outfile.write(encoded)
>>> outfile.close()
Example output:
auth_token=somelongstringthatendswithanequalssign%3D 

## Siege 

   * Also a benchmark tool that is very good for load testings
   * siege -c 1000 -t 1H -g "http://test.com" 
   * The commend above send GET requests from 1000 concurrent users to "http://test.com"
   * To send Post:
      * siege -g “http://www.haha.com/reader.php POST user=howard&pass=stern” 
      * siege -g "http://www.haha.com/reader.php POST < /path/to/file"

## To send a remote command: ssh username@host 'command to send':

   * ssh systemsboy@rhost.systemsboy.edu 'ls -l'
   * ssh -p 5050 eun@tapadmainserver7 'curl localhost:8080/api/stats/bidder/229' | python -mjson.tool
To get the json format prettified in terminal:

   * some_command_gernating_json | python -mjson.tool 
To work on multiple remote terminals simultaneously:
   * csshX eun@tapadmainserver1:5050 eun@tapadmainserver1:5050 ... 
Find Command:

   * find / -name foo
   * This will search the whole system for any files named foo and display their pathnames.
Tar:

   * $ tar -zcvf prog-1-jan-2005.tar.gz /home/jerry/prog ==> tar
   * $ tar -zxvf prog-1-jan-2005.tar.gz ==> untar


## Curl:

   * To add/replace header, user -H or --header option. For example, 

   * 
      * curl --header "Cookie:" -v "http://localhost:9090/tap?pixel=adsfasdfasdfa" ==> sending empty cookie header
      * curl --header "Cookie:===" -v "http://localhost:9090/tap?pixel=adsfasdfasdfa" ==> sending invalid cookie header

   * To add cookie strong or file to read cookies from, use -b or --cookie option:

   * 
      * curl --b "id=something" -v "http://localhost:9090/tap?pixel=adsfasdfasdfa"

   * To write cookies to this file after operation, use -c or --cookie-jaroption:
      * curl --c cookie.txt -v "http://localhost:9090/tap?pixel=adsfasdfasdfa"


