# SYSTEM LOCK SOLUTION 



due to bugs in pam.d file or in your script.sh file , system may get locked but dont get worried about it , we can restore our system 




### step1  ==>   reboot the computer 
### if you are using dual boot => you will see a advance option for liux mint 
### if single boot press shift and press escape button multiple time 

### step2 ===> select advance linux 
### step3 ===>  select recovery mode 

### step4 ===> after some text loading in terminal , a menu will open 
### step5 ===> select root option 

### step6 ==> at bottom right it says press enter ==> just pres enter 

### step7 ==> a terminal will open in bottom with root access 

### step7 ===>   type a command =====>   nano /etc/pam.d/common-auth 

### step8 ====>  comment the line where we have wriiten the script.sh line

### step9 ===> just edit whole file with default content of pam.d file , which i have given in this repo 

### step10 ====>   cntl O then cntrl X  to save 

### step 11 ===> type reboot to restart system 


now the script  which was running during auth will not run ===> no bugs 