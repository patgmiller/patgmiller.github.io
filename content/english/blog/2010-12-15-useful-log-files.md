---
title: Useful log files
author: Patrick Miller
date: "2010-12-15T12:36:28Z"
draft: false
categories:
- Programming
tags:
- php
- linux
- shell
---
We have a simple API where I work that isn't anything impressive, it is just a couple of scripts that accept POST and GET requests. However the previous developer who worked on it hadn't considered the total ramifications and logged all requests using an email with print statements. Additionally, for each request there could be up to 3 emails generated, one for the initial request, one for errors, and one that included the payment gateway request and response. Not mention it definitely breaks PCI Compliance by emailing people's credit card information, but we were also experiencing load issues on the web server for all the emails that were being generated through PHP Send Mail and the end result is about 10,000 to 100,000 emails waiting in my email account.
<!--more-->

So I changed the logging method to be a, wait for it, ... a log file, what a novel idea. I wanted it to be just as useful as any other server log file so that I could use any of the Linux shell commands. For example if I wanted to do any of the following.

{{< highlight bash >}}
$> cat filename | grep searchstring
$> tail -f | grep searchstring
{{< / highlight >}}

You get the general idea. I basically wanted to have logs where I could put all the requests, errors, MySQL errors, and variables for debugging purposes. I also wanted the code to be general enough so I could use it wherever I needed it. Here is a snippet of the code.

{{< highlight php >}}
<?php
function array_to_string($array, $first = false, $delim = "|", $key_value_char = "=>") {
  $delim_pattern     = '/[|*/,;\[\].=!#%\(\)>]+/';
  $delim             = (preg_match($delim_pattern, $delim)) ? $delim : "|";
  $key_value_char    = (preg_match($delim_pattern, $key_value_char)) ? $key_value_char : "=>";
  $return_string     = '';
  foreach($array as $key=>$value) {
   if(is_array($value)) {
     $return_string .= " $delim ".$key." Array( ".array_to_string($value, $_first = true, $delim, $key_value_char)." )";
    } else {
     $return_string .= ($first) ? $key.$key_value_char.$value : " ".$delim." ".$key.$key_value_char.$value;
     $first = false;
    }
   }
   return $return_string;
 }
 function SaveLog($message, $filename = 'default_log') {
  $file = (preg_match('/[^a-zA-Z0-9\/_-]/', $filename)) ? 'default_log' : $filename;
  $log = "/home/user/logs/".$file;
  $log = "[".date('d-M-Y H:i:s')."] API: ".preg_replace('/\s+/',' ',$message);
  $log .= (!empty($_SERVER)) ? " --- SERVER: ".array_to_string($_SERVER, true) : '';
  $log .= (!empty($_GET)) ? " --- GET: ".array_to_string($_GET, true) : '';
  $log .= (!empty($_POST)) ? " --- POST: ".array_to_string($_POST, true) : '';
  $log .= (!empty($_FILES)) ? " --- FILES: ".array_to_string($_FILES, true) : '';
  $log .= "\n";
  error_log($log, 3, $file);
 }
{{< / highlight >}}

I also wanted to have a daily log of the requests in case I needed to track down any problems, errors, fraud attempts, etc. So I wrote two shell scripts that run around midnight to archive the file and delete any archives older than 30 days. I am not by any stretch of the imagination a master shell script coder, but here it is.

**Edit Note:** The following shell scripts are **not**
the proper method to manage log files. A better way is to use the Linux [logrotate](https://www.linuxcommand.org/man_pages/logrotate8.html) utilty.


{{< highlight bash >}}
#!/bin/bash
# archive_api.sh
# Archive API Message logs
# and clear the contents of the current one
###########################################
DATE=`date +"%Y%m%d"`
gzip -c /home/user/logs/api_error > /home/user/logs/api/api_error.$DATE.gz
gzip -c /home/user/logs/api_message > /home/user/logs/api/api_message.$DATE.gz
gzip -c /home/user/logs/processor_message > /home/user/logs/api/processor_message.$DATE.gz
cat /dev/null > /home/user/logs/api_error
cat /dev/null > /home/user/logs/api_message
cat /dev/null > /home/user/logs/processor_message
{{< / highlight >}}

{{< highlight bash >}}
#!/bin/bash
# delete_api.sh
# Archive the Learning Center API Message logs
# and clear the contents of the current one
###########################################
which stat > /dev/null
# make sure stat command is installed
if [ $? -eq 1 ]
then
 echo "stat command not found!"
 exit -1
fi
cutoffdate=`date -d "-30 days" +"%s"`    #delete files older than this date
echo "Delete files older than $cutoffdate"
for file in `find /home/user/logs/api/ -iname '*.20*'`;
do
 lastchanged=`stat -c %Z $file` #number of seconds since the epoch
 if [ "$lastchanged" -lt "$cutoffdate" ]
 then
 echo "$file last changed on $lastchanged, deleting $file"
 rm $file
 fi
done
{{< / highlight >}}

At any rate I enjoyed doing it since I got to learn some shell script coding as well, and it cleaned up our server load issues as well as my inbox.
