# EpluconControl
Control Heatpump via pythonscript (re)using capabilities of the Eplucon website

Hello,

I am a big fan of using Home Assistant and was quite disappointed about the possibilities to integrate the Ecoforest Heatpump which I own.
Of course you can say you should have thought about it before buying it. And during the procurement I did aks for the possiblities, it was told that you could control temperature with for example Google. In the end yes there was the possiblities for the room controllers to integrate, but sadly not the main controller "Th-Touch" which is actually controlling the heatpump.

First I started with scraping the eplucon website - using the nice multiscrape integration - to read information and present it in Home Assistant which enable to ask Google about the current temperature and if the heating was on. I had all sensors and found which html elements where representing the values. Luckily they provided later the API providing the actual information of the heatpump in structured way. So this was a big improvement and more stable as I experienced in the beginning some changes on the website and then I could try to find again the html elements. However there was and is still no possbility to control for example the temperature or the mode of the heatpump using the API. I asked for it but without any reaction.

The app and the website as offered by the provider could be used to control and that is it. So not fully integrated within my home automation and always in the need to use another app as well without opportunity to do some automations. It should be possible!!!

I looked around and identified two possible solutions (1) adding an additional card in the pump yourself to read and control it locally using modbus etc. or (2) scrape it from the site. For now I did not want to open the heatpump and add an additional card to prevent any guarantee issues. I just want to stay away from any possible discussion about it in case of a mallfunction. 

Sadly the th-touch controller connected to the heatpump does not provide capabilities to connect locally from Home Assistant. Even not for reading while it is sending all data from the heatpump to the service provider. I have thought about something to intercept the traffic between the th-touch. However in the end i choose to experiment with seeing what requests are being sent from the website to read and update data of the heatpump. I was in the believe that it should be possible to replay this with some creativity and tools which are currently available. I accept that it is not local and the dependency on having an connection with the cloud.

The solution i have is now us assuming that you have (1) an account on the eplucon website and you're able to control your heatpump and (2) you are allready abl to read data and have sensors witin Home Assistant. You need to have the indoor temperature at least. You can use the Ecoforest integration from Koen Hendriks (https://github.com/koenhendriks/ha-eplucon) or using your own implementation of invoking the API as provided by Eplucon (https://portaal.eplucon.nl/docs/api). You can use te multiscrape or rest integration to invoke the API. I am still my own invocation of the API as the Ecoforest integration was not available and it have some issues. Also i wanter to be able to pause it as the API has a service window which then provides a 0 for a minute or 15 which makes the diagrams unreadable.

So now i could read the data in a nice structured way. The next idea, make an integration for it, however Koen Hendriks allready started this (https://github.com/koenhendriks/ha-eplucon) so no need to work on that. However i still wanted to be able to control the temperature from my Hom Assistant.

Why? Just because it can and should be possible without the use of multiple apps/websites. Also I experienced that the heatpump finished heating the house in the morning just before waking up. When waking up the flow was allready a bit cooling down. I just wanted to the delay the moment that is starts heating during the night so it is still running when waking up and the floor is still nice comfortly warm. But now i also now i can use it when i have my home in holiday mode, it lowers the temperature, and start heating before coming home. It turns down the production for heating the boiler.

Using the developet tools from the brower i experiemented to see what is sent/received when logging into the website and changing the things on the heatpump. Then I created a Python script to reproduce the steps and could control the heatpump. By having it in a script it could be used within automations in Home Assistant. It is the first attemmpt and there is still plenty room for improvement, however for me currently it does the job for now. Lets hope that they do not change the implemntation to frequently. As mentione before i experiences some changes in the past 18 months when the overhauled the website.

Altough the script is written in Python is is pending on the Pyscript Python scripting integration (https://github.com/custom-components/pyscript) to be installed in Home Assistant. In that way you could use the script as an action within an automation and not have to start a bash session. Also it enables some opportunities to write to the log of Home Assisant and provide response to the automations in Home Assistant. So the script does not run in a bash session on its own.

1. Install Pyscript
See https://github.com/custom-components/pyscript. After installation it should be available as an integration.

![image](https://github.com/user-attachments/assets/67ed64f6-82cc-40a9-a910-211a14eefe08)

2. Place the script in the pyscript folder
You can use SSH, Samba integration or just create the file via Studio Code server integration and copy/paste the code into that file.

![image](https://github.com/user-attachments/assets/77a20283-870f-4645-9262-793df895cb6e)

3. Go to Developer Tools in Home Assistant

If all went OK the script is available as an action within Home Assisstant and can be used accordingly in automations etc.

![image](https://github.com/user-attachments/assets/37ef6b16-1a98-49f3-9d7d-aff8b54f9dc9)

When selecting the action in the Developer Tools it shows which parameters are expected. Sadly i could not find a easy way to add username, password and index as a secret in YAML. So for now it expected as parameters. Of course you can add them as default in the python script. Or define helpers in Home Assistant and use them in your automations.

To be able to run the script and control your heatpump you need to use the same account which you use to login on the eplucon site and also know the module_index referring to the heatpump. 

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
Press the perfom Action, if all is OK the button wil show green and in the Response is stated that everything went OK.

![image](https://github.com/user-attachments/assets/fe51e2a4-5eec-4851-9d1f-e4c8eb8d6ac2)

After a short while you should see the temperature also updated in the Eplucon Website or the App to the selected value.

Congratulations. Now you have control on the heatpump from Home Assistant and can use it in automations and your dashboards.

9. Create a thermostat (if needed)
I've created a thermostat so i can control it in the same way as a can control the temperature in all my other rooms. Also because the thermostat can be exposed in standard way to Google Home.

![image](https://github.com/user-attachments/assets/67cc7db6-d6f4-4d32-b8fb-2c7fad17db83)

In Home Assistant you can create a generic thermostat which requires a sensor measuring the temperature and a switch to controlsomething to set it on or off
The latter we will not use and therefore just create a dummy switch as a helper. the sesnsor measuring the temperature can be the indoor_temperature from the Ecofores integration.

So just create a helper defining an input_boolean and use that one in the thermostat listed below. 

climate:
  - platform: generic_thermostat
    name: Woonkamer
    unique_id: 519d7b14-965c-48e3-b877-d47371bf5a14
    heater: input_boolean.dummy_thermostat_switch
    target_sensor: sensor.indoor_temperature
    min_temp: 18
    max_temp: 30
    target_temp_step: 0.1
    initial_hvac_mode: "heat"
    ac_mode: false

It does nothing yet except showing the current temperature. To enable that the confgured temperatue is passed to the heatpump an automation is needed listening to state changes of this thermostat.
When a change happens it incokes the python script as an action.


alias: Heatmpump Management
description: >-
  Managing the heatpump  
triggers:
  - trigger: state
    entity_id:
      - climate.woonkamer
    attribute: temperature
    for:
      hours: 0
      minutes: 0
      seconds: 5
    id: Temperature changed via Thermostat
conditions: []
actions:
  - if:
      - condition: trigger
        id:
          - Temperature changed via Thermostat
    then:
      - action: pyscript.eplucon_set_value
        data:
          username: username@domain.com
          password: password
          command: indoor_temperature
          value: "{{ state_attr('climate.woonkamer','temperature') | float(18.0) }}"
          module_index: x1a11b1234bc987f0123a1e1a2ab1d89
        enabled: true
mode: single




15. 



16. Determine your module_index of the heatpump (using the API or logging in and use the developertool of the browser
17. Create a thermostaty
18. Publish the thermostat for exmapl to google.
19. Do some automations as needed



![image](https://github.com/user-attachments/assets/3c2c0cff-e981-4d55-afad-04594f2ea0dd)








I have some jokers as friends which where settin the temperature to a high. So my automation limits this and put it to a lower automatically.






I put this in some python script which than can be invoked from within Home Assistant. 






 



