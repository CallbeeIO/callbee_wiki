# Yeastar S-series VoIP PBX and Bitrix24 Integration Guide (Using Callbee)

!!! info
    * To enable integration, you need to follow the steps in this guide one by one in the order in which they are described.*

## Software Requirements

* S-series VoIP PBX (S20, S50, S100, S300).
* Static IP address (should be purchased from your Internet service provider).
* Any edition of cloud or self-hosted version of Bitrix24.
* A valid SSL certificate for the self-hosted version of Bitrix24.

## Important Warnings

* The integrated service supports up to 4-digit extensions.
* The integrated service does not convert phone numbers. They must be entered in international format both in Bitrix24 and the PBX.

## How the Integration Works

The integrated service enables the connection via AMI between your Yeastar S-series VoIP PBX and Bitrix24 REST API and also converts, server-side, the call records audio files from wav (the only call recording format supported by Yeastar IP-PBX is wav) to mp3.

It is important to know that the VoIP SIP traffic is contained within your PBX.

The integrated service connects with Bitrix24 via REST API: it sends requests to call up a call card, verify a phone number, and forward the recorded call from the PBX.

## 1. Yeastar VoIP PBX Configuration 

### 1.1 Network Services Configuration

Open the admin panel of the PBX and go to Settings - System – Security, then go to the Network Services tab and configure the following parameters:

!!! info "for Yeastar S20"

    * Check the box next to __Enable FTP__
    * Check the box next to __Enable TFTP__

!!! info "for Yeastar S50/S100/S300"

    * Change the protocol from HTTPS to HTTP

    The protocol switch should be performed if a valid SSL certificate is not installed

* Check the box next to __Enable AMI__
* Change the default __Username__ and __Password__ (you will need to enter this username and password in your Callbee account)
* In the Allowed IP Addresses/Subnet Masks field that appears, enter the IP address 89.108.65.246 and the subnet mask 255.255.255.255

![settings-yeastar](../img/yeastar/yeastar-1.png)

### 1.2 Data Storage Configuration

!!! info "for Yeastar S20"

Go to __Settings - System – Data Storage__ and to the __File Share__ tab:

* Check the box next to __Enable FTP Access__
* Check the box next to __Enable Data Storage__

![FTP](../img/yeastar/yeastar-2.png)

### 1.3 API Configuration

!!! info "for Yeastar S50/S100/S300"

Go to __PBX Settings – PBX Settings - API__:

* Check the box next to __Enable API__
* Check the box next to __PBX Monitor__ for all phone numbers and lines

![API](../img/yeastar/yeastar-3.png)

## 2. Network Settings

In order for the integrated service to connect to your PBX, you should have a static IP address, and the following two ports should be forwarded to the PBX using NAT:

* __TCP 5038__ – to access the Yeastar AMI
* __TCP 21__ – to access the Yeastar FTP (for Yeastar S20)
* __HTTP/HTTPS 8088 (default WEB UI port)__ – to access the Yeastar API

!!! info
    The UI for port forwarding configuration can differ greatly depending on the router used in your network. You can find up-to-date instructions on port forwarding for your router on the official website of the router manufacturer.

## 3. Initial Bitrix24 Configuration

### 3.1 Application Installation

Log in to your Bitrix24 corporate portal as an administrator.

* In the Bitrix24 menu, go to __Applications__, select the __IP Telephony__ category and find the __Callbee__ application
* Go to the __Callbee__ application page и and click the __Install__ button
* Next, you need to read and agree to the terms of the license agreement and the privacy policy by checking the corresponding boxes and then click the __INSTALL__ button

<a href="https://www.bitrix24.ru/apps/?app=5757539.callbee" target="_blank">Сallbee</a> in Bitrix24 application directory

### 3.2 User Extensions Configuration

* Go to __Employees__
* Open an __Employee__ profile
* Enter the employee’s extension phone number

![bitrix24-extension](../img/yeastar/yeastar-4.png)

## 4. Integration Configuration via a Callbee account

After performing all the configuration as described above, you need to enable the service integration.

### 4.1 Enabling the Integration on Callbee Side Manually

* Click the __BITRIX24 WITH YEASTAR «Install»__ button
![install integration](../img/yeastar/yeastar_b24_1.png)


* Fill in __all the fields required__ for integration  
![install integration](../img/yeastar/yeastar_b24_2.png)


  * __Bitrix24 Domain (1)__ -  Enter the __domain name__ of your Bitrix24 __(without https://)__
  * __Yeastar AMI host (2)__ - Enter the __static IP address__ used to connect to the __Yeastar AMI__
  * __Yeastar AMI port (3)__ - Enter your __port__ used to connect to the __Yeastar AMI__ (__5038__ is a standard AMI port)
  * __AMI username (4)__ - Enter the __username__ used to log in to the Yeastar AMI
  * __AMI secret (5)__  - Enter the __password__ used to log in to the Yeastar AMI
  * __Region (6)__ - Selecting the __nearest region__ to connect to Yeastar
  
    !!!Info
        *This feature enables __the fastest connection between Yeastar__ and __CallBee__*    
        *You can select one of the __three available regions__:*
        ![install integration](../img/yeastar/yeastar_b24_5.png)
        
        * __Russia__ - connecting to an integration node located in the Russian Federation
        * __Belarus__ - connecting to an integration node located in the Republic of Belarus
        * __Germany__ - connecting to an integration node located in Germany
        * __AUTO__ - automatically connecting to any node

### 4.2 Enabling the Integration on Callbee Side Using an Installer


* Click __Install Integration__
![settings-b24-asterisk-1](../img/asterisk/asterisk_b24_28.png)
* Select __Bitrix24__ in the __CRM__ field
* Select __Yeastar__ in the __Platform__ field
![install integration](../img/yeastar/yeastar_b24_9.png)
* Fill in all the __required fields__
![install integration](../img/yeastar/yeastar_b24_10.png)
  * __Yeastar AMI host (2)__ - Enter your __static IP address__ used to connect to the __Yeastar AMI__
  * __Yeastar AMI port (3)__ - Enter your __port__ used to connect to the__Yeastar AMI__ (__5038__ is a standard AMI port)
  * __AMI username (4)__ - Enter the __username__ used to log in to the Yeastar AMI
  * __AMI secret (5)__  - Enter the __password__ used to log in to the Yeastar AMI

### 4.3 Call Recording Configuration

* Set the __FTP/API (7)__ toggle in the desired position depending on the __model__ of the Yeastar PBX       
  * __FTP__ (for __Yeastar S20__) / __API__ (for __Yeastar S50/S100/S300) (7)__ - the toggle position depends on the PBX __model__
  * __Convert to mp3 (8)__ - Convert the call record audio file to __mp3__

    !!! info "for Yeastar S20"
        ![install integration](../img/yeastar/yeastar_b24_6.png)

        * __FTP host (9)__ - your __static IP address__ used to connect to the Yeastar FTP
        * __FTP port (10)__ - your __port__ used to connect to the Yeastar FTP (__21__ is a standard FTP port)
        * __FTP username (11)__ - the __username__ used to log in to the Yeastar FTP
        * __FTP secret (12)__ - the __password__ used to log in to the Yeastar FTP
        * __Path records directory (13)__ - if this __field__ is left __blank__, the __default directory__ for __saving the call record files__ at /ftp_media mmc/autorecords/ will be used

    !!! info "for Yeastar S50/S100/S300"
        ![install integration](../img/yeastar/yeastar_b24_7.png)

        * __API host (9)__ - your __static IP address__ used to connect to the Yeastar API
        * __API port (10)__ - your __port__ used to connect to the Yeastar API
        * __API username (11)__ - the __username__ used to log in to the Yeastar API
        * __API secret (12)__ - the __password__ used to log in to the Yeastar API

    !!! warning "Important warning"    
        * When __transferring an unprompted (blind) call__, only __the second part__ of the conversation (with __the second__ speaker) will be recorded, consequently, __the first part__ of the conversation (with __the first__ speaker) __will not__ be saved, even if the recording of the entire line __is enabled__.
        * When __transferring a prompted call__, the entire conversation will be __duly recorded__ (line recording needs to be enabled) and __forwarded__ to __the last speaker__ in the CRM system (starting from the integration version v2.0).

        This warning is pertinent to the operational features of Yeastar PBXs
        
        
### 4.4 Configuring the Integration on Callbee Side for Each Separate PBX Line (Available in Pro Version and Demo Mode)

* Fill in the required fields
    ![install integration](../img/yeastar/yeastar_b24_8.png)
  * __Ignore Lines (DID) Numbers (14)__ - Enter the __DID__ number(s) __separated by a comma and a single space__ which the integration module __should ignore__. This feature allows you __not to respond__ to phone numbers not associated with working in the CRM system. For example, direct dial numbers of the accounting or administrative department.
  * __Line (DID) (15)__ - Department/division phone number. A phone number for __incoming calls__ that need to be referred to a __division/department__ (the phone number is taken from the __DID Number__ field in the __FreePBX settings__)
  * __Description (16)__ - Line (Did)__description__. It will be used to fill in the __Line__ field in a __Contact card__ or a __Deal card__ in __Bitrix24__. If the Description field is left __blank__, the __line number (DID)__ will be forwarded
  * __Manager ID For Missed Calls (17)__ - __Bitrix24 user ID__ for __missed calls__ (a user responsible for missed calls from new customers not registered in Bitrix24). You can set a different value for each line. __ This field should not be left blank!__

    !!! info
         You can find the __Manager ID__ by opening the __employee's Bitrix24 profile__ in the __address bar of your browser__        ![settings-b24-asterisk-1](../img/asterisk/asterisk_b24_40.png)
         In the screenshot above, the__Manager ID__ value is set to __1__   

  * __Smart Call (18)__ - Enabling __smart routing__ (transferring a call to a designated employee) __with working hours indicated__. __Outside of the smart routing schedule__, __the call__ will be forwarded according to the __default route__. This feature is most often used to play a message to a customer that he got through after hours.
  * __Manager time in bitrix24 (19)__ - Enabling __work schedule__ for smart routing __from Bitrix24__
  * __Auto Lead (Deal) (20)__ - Enabling __automatic creation__ of leads or deals (+ contacts) in Bitrix24 (depending on the Bitrix24 mode of operation)
  * __Add to chat bitrix24 (21)__ - Enabling the __displaying of information__ about all calls in the Bitrix24 __chat__
  * __Add DID Lines (22)__ - Adding a line for customization
  * __SAVE (23)__ - __Saving__ the integration settings

### 4.5 Configuring the Integration on Callbee Side for Each Extension.

!!! warning "Important warning"
    *To configure the __extensions__ using the __Callbee__ service, you need to __enable__ the __Use B24 users table__ toggle*
    

* Go to __Integration Settings, click __Settings__
![settings-b24-asterisk-1](../img/asterisk/asterisk_b24_31.png)
* __Enable the __Use B24 users table__ toggle
![settings-b24-asterisk-1](../img/asterisk/asterisk_b24_36.png)
* After enabling the __Use B24 users table__ toggle, the __Bitrix24 users__ menu item will appear, go to it
![settings-b24-asterisk-1](../img/asterisk/asterisk_b24_37.png)
![settings-b24-asterisk-1](../img/asterisk/asterisk_b24_42.png)
![settings-b24-asterisk-1](../img/asterisk/asterisk_b24_43.png)
![settings-b24-asterisk-1](../img/asterisk/asterisk_b24_44.png)
* __Go to settings (1)__ - Go to the settings page
* __Integration Info (2)__ - Integration information
* __Show active users (3)__ - Filter to display __active__ users 
* __Save and restart (4)__ - __Saving__ the settings and __restarting__ the integration
* __Search (5)__ - Searching for entries in the settings table
* __Active (6)__ - Enabling the __user (employee) settings__ via Callbee. __Activating the commercial license__ for this __user__ (__active users__ should __not exceed the number of licenses__). If the toggle is set to __Off__, the integrated service will __ignore__ this user, even if the extension phone number is entered, and all other fields are filled in. 
* __Bitrix ID (7)__ - User (employee) ID __in Bitrix 24__
* __Name (8)__ - User (employee) name __in Bitrix24__
* __Internal Number (9)__ - User’s (employee’s) __extension phone number__
* __External Number (10)__  -  The __personal phone number__ of the user (employee) who will __make calls on a mobile device__ using the __Bitrix24 click-to-call feature__. When using the click-to-call feature  on a mobile device, a call will be made in __both__ directions - to the __user (employee) __ and the __customer__ of your company. The number is used to __connect__ to the Bitrix24 application on a mobile device and __make calls__ using the click-to-call feature.
* __Smart Call (11)__ - Enabling smart routing (transferring a call to a designated employee) __with working hours indicated__. __Outside of the smart routing schedule__, __the call__ will be forwarded according to the __default route__. This feature is most often used to play a message to a customer that he got through after hours.
* __B24 Time (12)__ - Enabling the user’s (employee’s) __work schedule__ for smart routing __from Bitrix24__
* __Add to Chat (13)__ - Enabling the __displaying of information__ about all calls in the Bitrix24 __chat__
* __Auto Answer (14)__ - Enabling the __auto answer__ feature on a __SIP phone__ (hardware or software (softphone)). When making calls using the click-to-call feature via __Callbee__,  the call is initially __routed__ as an __incoming call__ to a __SIP phone__ (hardware or software), and after it is __received__, an __outgoing call__ is made to a customer of your company. Enabling this feature will allow a __SIP phone__ (hardware or software) to receive a call __automatically__, __without the involvement__ of a user (employee). It can be enabled if the __SIP phone__ (hardware or software) __supports this feature__ (for example, Yealink SIP phones with the latest firmware)
* __Multiple Registration (15)__ - Enabling the __Multiple Registration__ feature allows you to use the click-to-call feature when you have __a number of SIP phones__ (hardware or software)



!!! warning "Important warning!"
    *When __connecting to the integrated service for the first time__, __before saving all the settings__, you need to __log in to Bitrix24 as an admin__ in the __next tab of your browser.__*

## 5. Further Bitrix24 Configuration

### 5.1 Bitrix24 Telephony Configuration

* In the __Bitrix 24 main menu__, go to __Telephony__  
  ![settings-b24-asterisk-1](../img/asterisk/asterisk_b24_7.png)

* Then select __Telephony Settings (1)__
  ![settings-b24-asterisk-1](../img/asterisk/asterisk_b24_8.png)
* In Telephony settings, click __Default outbound number (2) -> Application: Asterisk and Yeastar Callbee.io__ 

Then click __Save__

![settings-b24-asterisk-1](../img/asterisk/asterisk_b24_10.png)

__You have completed the integration setup!__ 
