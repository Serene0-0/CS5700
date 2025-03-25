# High-level Approach
My approach can be divided into following four parts:
1. parse command line and URL
    This part is divided into two function to parse the command line. 
    First is to parse the command line into three components. And then parsing the parameter contains the information 
    of ftp username, password, host and convert it into dictionary.
2. set up connections of two channels
    This part uses the socket module to establish the two channels:
    The control channel is set up first, and the default mode is configured by sending TYPE I, MODE S, and STRU F commands.
    When required, the data channel is opened using the PASV command and is closed after data transmission.
3. handle the ftp commands
    Simple commands of DELE, MKD, RMD are handled by sending the respective requests to the FTP server. 
    For LS command, the response data is reorganized to display directory contents in a clearer and more user-friendly format.
    For the commands of STOR and RETR are handled in similar approaches: sending command to the server, 
    the local file is opened for reading or writing.
4. implementation main function
    First, the program determines whether the parameter contains information connecting to the FTP server.
    After establishing the control channel connection, the appropriate function is called based on the operation: ls, mkdir, rm, rmdir, cp.
    For the mv command, the function first performs a file upload or download and then deletes the original file.
---------------------------------------------------------
# Challenges
1. error handling in PASV
    In the part of enter PASV, I met the issue with raise Error. At first, I directly determine the successful 
    connection by checking the response code, while the server may not send back only one response. Therefore, it is 
    possible that the first response does not contain the code "227" indicating the success, but error is raised here soon after
    the first response in the first version of function, which break the program. So I change the error handling here by
    keeping receiving response until the code "227" appears, or the connection failed.
----------------------------------------------------------
# Testing Overview
1. ensure the client is executable
   ./4700ftp --help
2. test basic command
   1) ls
   ./4700ftp ls ftp://chen.shiyu1:SERCRET_FLAG@ftp.4700.network/
   2) mkdir
   ./4700ftp mkdir ftp://chen.shiyu1:SERCRET_FLAG@ftp.4700.network/test_new_folder
   3) cp
   ./4700ftp cp /project2/test_upload.txt ftp://chen.shiyu1:SERCRET_FLAG@ftp.4700.network/uploaded_test.txt
   4) rm
   ./4700ftp rm ftp://chen.shiyu1:SERCRET_FLAG@ftp.4700.network/uploaded_test.txt
    and using ls to check if the file is deleted
   5) rmdir
   ./4700ftp rmdir ftp://chen.shiyu1:SERCRET_FLAG@ftp.4700.network/my_new_folder
   and using ls to check if the directory is deleted
   6) mv
   ./4700ftp mv ftp://chen.shiyu1:SERCRET_FLAG@ftp.4700.network/uploaded_test.txt project2/test.txt
   and using ls to check if the file is deleted, also check the local file is written successfully
