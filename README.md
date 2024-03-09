
# Setup-Frida-Mitmproxy-on-emulator
Detailed Setup up frida tool and mitmproxy server on emulator

## Step-1: Create an android emulator
First create android emulator by android studio (I Choose Pixel 2 device with API 30 and system image x86)

## Step-2: Install adb tool and set up
--First goto this link "https://developer.android.com/tools/releases/platform-tools" and downloads sdk platforms tools according to your system

--Extract the downloaded file and copy to your c drive and add to the path

## Step-3: Start your Android Emulator
Goto android studio and start your AVD emulator



 




           



## Step-4: Install rootAVD and root the Android Emulator
  --Go to this link "https://github.com/newbit1/rootAVD"

--Click the code button and then Click "Download ZIP"

--Extract the Downloaded folder move to directory rootAVD-master using cd command in command prompt

 --Run the command

```bash
 rootAVD.bat ListALLAVDs
```
 You will get the list of many android versions, execute correct one your emulator by copy the command of your selected versions (In my case rootAVD.bat system-images\android-30\google_apis\x86\ramdisk.img)
and then execute

It will shutdown your emulator automatically.

--From Android Studio, Device Manager Click Cold Boot Now to your selected emulator 

--Open new command prompt and run the command
```bash
 adb devices
``` 
It will shows List of devices attached

 --Then run the command
 ```bash
adb shell
``` 
 It will nevigate to your emulator path (In my case generic_x86_arm:/ $)

  --Run the command
   ```bash
 ls
``` 
It will shows list of data

 -- Run the command
   ```bash
 cd /data/data
``` 
-- Run the command 
 ```bash
ls
``` 
It will shows ls: .: Permission denied

--Run the command
 ```bash
su
``` 
It will prompt on your Android Emulator -Super User Request

--Click on Grant within 10 seconds

**** You will get the root access on your android emulator***** 


## Step-5:  Set up frida-server
--Open your command prompt Run the command
 ```bash
pip install frida-server
``` 
--Open the link "https://github.com/frida/frida/releases" and download the server according to your emulator system image (In my case "frida-server-16.2.1-android-x86.xz")

 --Extract the Downloaded file move to directory where you extract the file using cd command in command prompt

--Run the command
 ```bash
adb devices
``` 
 It will shows List of devices attached

 --Run the command
  ```bash
frida-ls-devices
``` 
   It will shows  Id             Type    Name                   OS   of your emulator

--Run the command
  ```bash
frida-ps -U
``` 

 It will shows like
-------------------------------------------------------------
			      PID  Name
                1732  Google
			      6792  Google Play Movies & TV
			      4088  Phone
			      5452  Photos
			      5389  Settings........

-- Run the command
  ```bash
adb root
``` 
--Run the command
```bash
adb push frida-server-16.2.1-android-x86 /data/local/tmp
``` 
***Replace frida-server-16.2.1-android-x86 with your frida server name and version.

-- Run the command
```bash
adb shell
``` 
It will nevigate to your emulator path (In my case generic_x86_arm:/ $)

  -- Run the commands one by one
  ```bash
  cd /data/local/tmp
rm frida-server
  ls
``` 
It will shows like frida-server-16.2.1-android-x86  nohup.out

 -- Run the commands 
  ```bash
./frida-server-16.2.1-android-x86
``` 
It will shows /system/bin/sh: ./frida-server-16.2.1-android-x86: can't execute: Permission denied

  -- Run the commands one by one
 ```bash
mv frida-server-16.2.1-android-x86 frida-server
chmod 777 frida-server
./frida-server 
           ****press cltr+c then run the command
./frida-server &
        
```

--Next, make sure adb can see your device: open a new command prompt and run the command
  ```bash
adb devices -l
``` 
This will also ensure that the adb daemon is running on your desktop

--Now, on your desktop it’s time to make sure the basics are working. Run:
  ```bash
frida-ps -U
``` 
 This should give you a process list along the lines of: 
  ```bash
                                  PID NAME
									1590 com.facebook.katana
									13194 com.facebook.katana:providers
									12326 com.facebook.orca
									13282 com.twitter.android
                                   					.....
``` 
 ****Tracing open() calls in Chrome****

--Alright, let’s have some fun. Fire up the Chrome app on your emulator device and return to your desktop and run
 ```bash
frida-trace -U -i open -N com.android.chrome
``` 
It will shows like
 ```bash
Instrumenting...
								open: Loaded handler at "D:\\Android\\__handlers__\\libc.so\\open.js"
								Started tracing 1 function. Press Ctrl+C to stop.

``` 

-------Now just play around with the Chrome app and you should start seeing open() calls flying in:
 ```bash
                     1392 ms	open()
						1403 ms	open()
						1420 ms	open()
                                                
``` 

## Step-6: Setup mitmproxy server
--First connect the wifi conection of your AVD emulator same as your desktop

--Download the mitmproxy installer from the link "https://docs.mitmproxy.org/stable/overview-installation/" for your system

--Install the mitmproxy and add to the path of your bin folder (In my case C:\Program Files\mitmproxy\bin)

--Open command prompt and run
 ```bash
mitmweb
                                                
``` 
It will shows like
 ```bash
    HTTP(S) proxy listening at *:8080.
	Web server listening at http://127.0.0.1:8081/ and redirect to your browser.
                                       
``` 
--Open new command prompt and run
 ```bash
ipconfig                            
``` 
It will shows at last
 ```bash
 Wireless LAN adapter Wi-Fi:
   			 Connection-specific DNS Suffix  . : domain.name
  			 IPv4 Address. . . . . . . . . . . : 191.164.1.8
   			 Subnet Mask . . . . . . . . . . . : 255.255.255.0
  			 Default Gateway . . . . . . . . . : 191.164.2.1                          
``` 
--Note your IPv4 Address goto your emulator wifi setting-->edit wifi setting -->Set proxy as manual-->Set proxy host name as your noted IPv4 Address-->Proxy port as 8080-->Save

--Goto your emulator web browser(chrome) and goto link " http://mitm.it" and download Android mitmproxy ca certificate.

--Goto your emulator setting-->security-->Encryption & credentials-->Install a certificate--Ca certificate--Install anyway-->Select downloaded ca certificate.

*****Alright, let’s have some fun. Fire up the Chrome app on your device and return to your desktop ********


