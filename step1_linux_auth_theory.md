 # open terminal 

 -------------------------------------------------- command --------------------------------------------------------------------- 
 # sudo nano /etc/pam.d/common-auth
 -------------------------------------------------- command --------------------------------------------------------------------- 



 ### /etc/pam.d/common-auth : This file is part of the PAM (Pluggable Authentication Modules) configuration. It controls the authentication behavior for the system.

### Purpose of /etc/pam.d/common-auth:
The common-auth file contains rules and settings that determine how users are authenticated on the system. 
It typically defines how passwords are verified, or whether additional authentication mechanisms (e.g., biometrics, two-factor authentication) are used.
Changes to this file can affect login behavior system-wide.




### Why be careful?
Modifying this file improperly could lock you out of the system. For example:
Disabling the default password authentication might prevent you from logging in and bugs like system lock , 
solution of system lock is there is another txt file .


 -------------------------------------------------- optionl command --------------------------------------------------------------------- 
this is  backup command 
# sudo cp /etc/pam.d/common-auth /etc/pam.d/common-auth.bak
 -------------------------------------------------- optionl command --------------------------------------------------------------------- 








# defualt pam.d file

  GNU nano 7.2                                                                                 /etc/pam.d/common-auth                                                                                           

#/etc/pam.d/common-auth - authentication settings common to all services

#This file is included from other service-specific PAM config files,
#and should contain a list of the authentication modules that define
#the central authentication scheme for use on the system
#(e.g., /etc/shadow, LDAP, Kerberos, etc.).  The default is to use the
#traditional Unix authentication mechanisms.

#As of pam 1.0.1-6, this file is managed by pam-auth-update by default.
#To take advantage of this, it is recommended that you configure any
#local modules either before or after the default block, and use
#pam-auth-update to manage selection of other modules.  See
#pam-auth-update(8) for details.



#here are the per-package modules (the "Primary" block)
### auth    [success=1 default=ignore]      pam_unix.so nullok


#here's the fallback if no module succeeds
### auth    requisite                       pam_deny.so
#prime the stack with a positive return value if there isn't one already;
#this avoids us returning an error just because nothing sets a success code
#since the modules above will each just jump around
### auth    required                        pam_permit.so
#and here are more per-package modules (the "Additional" block)
### auth    required        pam_ecryptfs.so unwrap
### auth    optional                        pam_cap.so
#end of pam-auth-update config






 -------------------------------------------------- command --------------------------------------------------------------------- 
 # sudo nano /etc/pam.d/common-auth
 -------------------------------------------------- command --------------------------------------------------------------------- 

 ### edit the file 
 add a new line before ---->    auth    [success=1 default=ignore]      pam_unix.so nullok


 line to be added ===> auth    [default=bad success=ok user_unknown=ignore]    pam_exec.so seteuid     home/user/actual/path/of/your/script.sh 


 


###  final line
 auth    [default=bad success=ok user_unknown=ignore]    pam_exec.so seteuid    home/user/actual/path/of/your/script.sh 
 add a new line before ---->    auth    [success=1 default=ignore]      pam_unix.so nullok


# cntrl o  and cntrl x to save and exit 





### final pam.d file look like 

  GNU nano 7.2       /etc/pam.d/common-auth                                                                                           

#As of pam 1.0.1-6, this file is managed by pam-auth-update by default.
#To take advantage of this, it is recommended that you configure any
#local modules either before or after the default block, and use
#pam-auth-update to manage selection of other modules.  See
#pam-auth-update(8) for details.
#XTRA ADDED LINE ==> MAIN LINE THAT RUNS OUR SCRIPT DURING AUTHENTICATION 
### auth    required    pam_exec.so seteuid  home/user/actual/path/of/your/script.sh 

#here are the per-package modules (the "Primary" block)
### auth    [success=1 default=ignore]      pam_unix.so nullok


#Capture photo only on failed authentication
### auth    [default=bad success=ok user_unknown=ignore]    pam_exec.so seteuid    /home/adarsh/Desktop/imposter/main.sh


#here's the fallback if no module succeeds
### auth    requisite                       pam_deny.so
#prime the stack with a positive return value if there isn't one already;
#this avoids us returning an error just because nothing sets a success code
#since the modules above will each just jump around
### auth    required                        pam_permit.so
#and here are more per-package modules (the "Additional" block)
### auth    required        pam_ecryptfs.so unwrap
### auth    optional                        pam_cap.so
#end of pam-auth-update config

