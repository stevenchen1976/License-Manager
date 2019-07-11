# License Manager (windows)

This license manager implements an authorization mechanism for a projector controller application so that an unser can access the application only when using a specific pc. The user must enter an authenticated license every time when accessing the application. 

## Process ##

1. get computer information
    - hard disk serial number
    - mac address
    - system time (set as registration time)
2. get projector serial number from **projector\\id.txt**
3. encrypt the above information and make a request to cloud(by email); create folder **email_info** and 
   save the encrypted data **email_info\\[host_name]-[user_name].txt**
4. generate license
    - decrypt the information in email_info
    - add service status and license duration to the decrypted package from 
      **service_status\\[host_name]-[user_name].json**
    - encrypt the entire data as a license; create folder **my_license** and save the 
      license under **my_license\\license.txt**
5. authenticate the license
    - decrypt the license and parse the following information
        - hardware info (for validating pc)
        - registration time (for checking expiration)     
        - license duration (for checking expiration)
        - service status (for activating corresponding services)
        - projector serial number
6. output the result in **output\\out.json**. The file contains:
    - service status
    - registration time
    - license duration
    - projector serial number
- note that this license_manager is designed to generate a single license from multiple requests(if any).

## usage ##

```shell=
git clone https://github.com/minghsu0107/license_manager.git
```

When using main.cpp for testing, one should enter *hostname* and *whoami* in cmd to attain computer name and user name respectively. Then go to **license_manager\\service_status** and rename MINGHSU-Ming Hsu.json as **[host_name]-[user_name].json**.

One can modify the json file mentioned above to change the service status and the license duration.

Run the following command:

```shell=
cd license_manager
g++ -std=gnu++11 main.cpp get_computer_info.cpp license_generator.cpp license_validation.cpp xxtea.cpp
```
Debug mode:
```shell=
g++ -std=gnu++11 -DDEBUG main.cpp get_computer_info.cpp license_generator.cpp license_validation.cpp xxtea.cpp 
```
The above command will generate **result.json**, which is the unencrypted result of running license_generator.cpp.
