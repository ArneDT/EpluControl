# EpluconControl
Control Heatpump via pythonscript (re)using capabilities of the Eplucon website

Hello,

I am a big fan of using Home Assistant and was quite disappointed about the integration possibilities with the Ecoforest Heatpump which I own.
Of course you can say you should have thought about it before and i did as I asked for it during the sales which confirmed that there were possibilities to integrate for example with Google.
In the end yes there was the possiblities for the room controllers, but not the main controller Th-Touch which is controlling the heatpump.

The app and the website as offered by the provider could be used and that was it. So not fully integrated within my home automation ans alwaays in the need to use another app as well. 
There were two solutions (1) adding an additional card in the pump yourself to read and control it locally using modbus etc or (2) scrape it on the site. I did not want to open the heatpump while there is guarantee.
So i choose for the sofware solution and yes it is cloudbase and depending on having a connection, and has some delay. But i did not mind this and also wanted to see it i can get it working using scapring.

Started with scraping the eplucon site - using the nice multiscrape integration - to read information and present it in Home Assistant which enable to ask Google about the current temperature and if the heating was on.
Luckily they provided later themselves an API providing the actual informaiton in a more structured way. However there was and is still no possbility to change for example the temperature or the mode of the heatpump except using the app or the website.

So now i could read the data in a nice structured way. I had the idea to make it as an integration for home Assistant, however Koen Hendriks allready started this (https://github.com/koenhendriks/ha-eplucon) so no need to work on that.
However i still wanted to be able to control the temperature from my Hom Assistant. Why? Well i discoved that the heatpump started heating in the night and finished in the morning. I just wanted to the delay the moment that is starts heating during the night so it is still running when waking up the floor is still nice comfortly warm
But also when putting the house in holiday mode that the alarm goes on, the temperature is decreased and will be increased a day or two before coming home, including the boiler. 

I used the developer tools from the browser to see what is sent/received when logging into the website and you change some settings. Using python script i was able to reproduce the stepts and could control the heatpump. By having it in a script it could be used within automations in Home Assistant.
It is the first attemmpt and there is plenty room for improvement, however it does the job for now.

Lets hope that they do not change it to frequently and every time need to change the equests in the script. I experienced it in the past 18 months when the overhauled the site a couple of times and the scrapting did then not work to read anymore. Luckilty they provide an api now for reading. And for setting they use themselves some ajax/json methods. To bad those are not endpoints as part of ther API.

I have choosen to write it in Python however you need to have he Puscript Python scriping integration (https://github.com/custom-components/pyscript) installed in Home Assistant. In that way you could use the script as an action within an automation and no not to start a shell session. So the script does not run in a bash session as it also used some HA specific calls to log and provide parameters.

1. Install Pyscript
See https://github.com/custom-components/pyscript. After installation it should be available as an integration.

![image](https://github.com/user-attachments/assets/67ed64f6-82cc-40a9-a910-211a14eefe08)

2. Place the python file in the pyscript folder created in the previous step
You can use an SSH, of Samba integration or jus create the file in Studio Code server and cope paste the code into that file.

![image](https://github.com/user-attachments/assets/77a20283-870f-4645-9262-793df895cb6e)

3. Go to Developer Tools in Home Assistant
If all went OK the script is available as an action within Home Assisstant and can be used accordingly in automations etc.

![image](https://github.com/user-attachments/assets/37ef6b16-1a98-49f3-9d7d-aff8b54f9dc9)

When selecting the action it shows which parameters are expected. Sadly i could not find a easy way to add username, password and index as secret in YAML. So for now it expects it as parameters. Of course you can add them as default in the python script.

To be able to run the script and control your heatpump you need to use the account which you use to login on the eplucon site and the module_index referring to the heatpump. 

![image](https://github.com/user-attachments/assets/3a53d9fb-4dde-4471-995b-3c66d8d9ccc9)

5. Determine the module_index of the heatpump
The quickest way is to open the Developer Tools (F12 in Edge) and look which requests are sent from the website when the Warmtepomp is selected

Login the Eplucon Site
Select Heatpump/Warmtepomp in the menu

![image](https://github.com/user-attachments/assets/9ab5c8b8-872d-4186-942c-a6e47b348baf)

Open the Developer Tools using F12 and click on the OpenDev button. 
Select the Network Tab which will show the requests sent from the page to the webserver from Eplucon.

![image](https://github.com/user-attachments/assets/a2ba6fdb-0733-4d5d-a2d3-d6625a8a5265)

Make sure that the networkconsole is opened and active (a red circle should be shown) in the Developer Tools to see which requests are sent.

![image](https://github.com/user-attachments/assets/75e82242-47a9-4539-a9f0-d49bb05cbc09)

Just wait for a couple of seconds and some rows will appear in the list. The string after the = you should copy ans past it as the module_index

![image](https://github.com/user-attachments/assets/fbb1bb7a-beca-4b86-a1ac-63d86d83aa0e)

7. Now you can go to he developer tools and using all the informaiton to actually change a setting of the Heatpump
Fill in the loginname, password, and module_index. And lets use the command to change the indoor_temperature to a value.
Press the perfom Action, if all is OK it will become green and in the Response is stated that everything went OK.

After a while you must see in the Eplucon Website or the app that the temperature has been set to the selected value.

Congratulations. Now you have control on the heatpump for Home Assistant and can use it in automations or on you dahsboard.

9. Create a thermostat.
I've created a thermostat so i can control it as i can control the temperature in my other rooms




11. 

12. 
13. 

14. 

15. 



16. Determine your module_index of the heatpump (using the API or logging in and use the developertool of the browser
17. Create a thermostaty
18. Publish the thermostat for exmapl to google.
19. Do some automations as needed



![image](https://github.com/user-attachments/assets/3c2c0cff-e981-4d55-afad-04594f2ea0dd)








The python script is depe







I put this in some python script which than can be invoked from within Home Assistant. 






 



