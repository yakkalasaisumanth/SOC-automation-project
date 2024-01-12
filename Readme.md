# Soc Automation Project.

- in this project we are going to setup a SOC lab which contains three virtual machines.
  - agent machine(windows 10)
  - wazuh server(Ubuntu 22.04)
  - the hive(Ubuntu 22.04)

- ![soc image](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/soc%20image.png)

- before proceeding further lets see what are the requirements of the project.

- a windows 10 machine with at least following components.
  - [x] ram = 4 GB
  - [x] hard disk = 40 GB
  - [x] sysmon up and running
  - [x] at least 2 vCPU's 

- a Ubuntu 22.04 wazuh machine should contain.
  - [x] ram = 6 GB
  - [x] hard disk = 50 GB
  - [x] at least 2 vCPU's 
  - [x] update and upgrade and take a snap shot

- a Ubuntu 22.04 the hive machine should contain.
  - [x] ram = 6 GB
  - [x] hard disk = 50 GB
  - [x] at least 2 vCPU's 
  - [x] update and upgrade and take a snap shot

## Lab Set Up Through Virtual Machine.

- please go [here.](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/lab-setup-Readme.md)

- or refer to the lab setup Readme.md

## Installing the wazuh and the hive.

- please go [here.](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/Installation-Readme.md)

- or refer to the Installation Readme.md

## Configuring The Wazuh & The Hive.

- please go [here.](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/configure-Readme.md)

- or refer to the configure Readme.md

## Generating Windows Telemetry And Creating Custom Alerts.

- open the windows 10 vm

- open the config file
```
local disk c: -> program files x86 -> ossec-agent -> ossec.conf
```

- right click and open with notepad

- just scroll down little bit we see log analysis under the security notation we can see some event id's

- close the file and choose the don't save and copy the file to a safer place and give a name ossec-backup

- now open the ossec.conf and go to log analysis now just copy the local file tag and right under application paste it.

- now hit windows button search event viewer.

- application &service -> Microsoft windows -> sysmon -> operational right click and properties.

- now we can see the full name of the application
```
Microsoft-windows-sysmon/operational
```

- copy the name and paste that in to the application.

- optional

  1. you can remove the security and local file application and system also.


- - now save the file

- open the services and restart the wazuh manager

- ![restart wazuh](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/48.png)

- now open the wazuh dashboard under events make sujre you are in the alert index just search sysmon 

- now we have to download the mimikatz

- mimikatz is a application used by the red team people to extract the data.

- make sure to disable the defender and download with any browser.

- if required please add the downloads folder as exclusion in the windows 10

- to do that just go to security settings -> threat protection -> scroll down - > exclusions -> select the downloads folder.

- make sure that you disabled the security settings in the browser also

- link :- [mimikatz](https://github.com/gentilkiwi/mimikatz/releases/tag/2.2.0-20220919)

- ![mimikatz img](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/49.png)

- now unzip the mimikatz

- now open the power shell as run as administrator and change your director to the mimikatz download directory.

- now lets see the wazuh dashboard for any events as we see there are no logs.

- it is because we didn't write a rule so.

- now open the wazuh vm.

- make sure to take a snap shot of this vm and create a backup file of ossec.conf
```
/var/ossec/etc/ossec.conf
```

- now edit the file 
```
nano /var/ossec/etc/ossec.conf
```

- now you can see the under the alerts tag -> logall is set to no change it to yes also change the logall_jason

- now save the file

- now restart wazuh-manager
```
systemctl restart wazuh-manager
```

- path of log
```
/var/ossec/logs/archives/
```

- press the following commands
```
nano /etc/filebeat/filebeat.yaml
```

- ![filebeat conf](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/50.png)

- now scroll down and change the archive settings archive enabled : true and save the file

- now restart the filebeat service
```
systemctl restart filebeat
```

- now go to the wazuh dash board

- click top left corner and stack management -> index patterns

- ![wazuh index](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/51.png)

- create a index for archive 

- create index and name the index wazuh-archive-* and net and for the time field select timestamp -> create index pattern.

- just open the the new indexer wazuh-archive-*

- ![wazuh indes1](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/52.png)

- now we can see the events

- expand the event id 1

- now we can see the field name as 
```
data.win.eventdata.OrginalFileNmae
```

- now set alert(path)
```
/var/ossec/rulesec/rules
```

- we can access the rules in the dashboard click home button -> dropdown -> management ->rules -> managerulefile

- just search sysmon

- you can see a event

- ![sysmon event](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/56.png)

- open one of these rule id and copy them

- go to custom rules now edit the rule and paste it under rule

- use spaces to maintain frame.

- set 
```
<rule id = "1000002" level = "15">
   <if_group>sysmon_event</ifgroup>
   <feild name ="win.eventdata.originalfilename" type = "pcre2">(?!)mimikatz\.exe</feild>
   <description>mimikatz usage detected</description>
   <mitre>
     <id>T1003</id>
   </mitre>

  </rule>
 ```

- ![sysmon newevent](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/58.png)

- ![sysmon newevent1](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/59.png)

- now save it and restart the manager

- you can try changing the mimikatz file name as iloveyou.exe

- ![sysmon mimikatz](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/60.png)

- ![mimikatz file](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/53.png)

- now open power shell and run the iloveyou.exe

- ![mimikatz detected](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/54.png)

- if you look out the wazuh manager we have successfully triggered even though we changed the mimikatz file name.

- ![mimikats file1](https://github.com/yakkalasaisumanth/soc-automation-project/blob/main/images/55.png)



## Connecting the shuffle and the Hive 

 ** the bellow text is done by speech to text if there are any mistakes let me know because the process takes much time to explain i had used the Microsoft speech to text.

-  what is shuffle?

- shuffle is a SOAR tool which is used to automate things using the SOAR rules.

- SOAR means security, orchestration, automation and response.

-  we will setup shuffle in such a way that which will then send an alert over to The Hive and to our analyst such as yourself via an email.

- first you want to head over to shuffle's site at Shuffle.io and then you want to create an account.

- once you create an account, we can start creating our workflow

-  once you login in to the shuffle.

- you see e can either select new to Shuffle or Experience. But either way, I'll just bypass everything and click on workflows.

- From here we can open up and create our own workflow by clicking on the plus button. I'll name this workflow as Sock Automation Project in the description saisumanth!

- We can do whichever. I mean I'll just select any random ones and then hit done. We get presented with this big change me icon. Now this is your workplace I guess where you get to start adding what are called apps and triggers.

- there is a in the middle called triggers and that is what we want to click on.

- we want to click on triggers and we are presented with a couple of workflow starters. So there's a web hook, a scheduler, Office 365, and Gmail.
I am going to select a web hook simply just drag and drop it right here.

- once you drag and drop the web hook you want to select it, and then on the right hand side we can start naming our web hook to whatever we want to in our demo. I'll name this as Wazuh dash alerts

- for the find associated app it is optional, so I'll leave it as blank, but what I want to do is. Copy the web hook your eye so we can just click on the copy button.

- the reason we copy this web hook is because we'll need to add this into our O set configuration. Host it on our Wazuh manager, but before we head over to the Wazuh manager let's click on the Change me icon and just make sure that it is selected as repeat back to me and the call. I'll remove the Hello world and I'll add in this Plus button and simply select Execution argument and then I will save it. Now let's head over to our Wazuh Manager on our Wazuh Manager CLI.

- we need to tell wazuh that we are going to connect to Shuffle and we can do this by adding what is called an integration tag in the ossec configuration file. So 
let's go ahead and open that up if you don't remember it is under var. ossecc, etc. ossecc. Com and from here I'm just gonna scroll down just a little bit right after the global tag.

- Now you could place this anywhere you like, but I'm just gonna place it here and then I'm gonna copy and paste integration tag similar like the other configuration files. You do want to keep in mind of the indentation and they are using spaces if you don't know how many spaces there are, just look above and try to match it, so it's the same

- now that the integration tag is there we. Want to replace the url here where it says your shuffle url. Let's go ahead and remove all of that, including the hook ID and then paste in the web hook that you copied earlier. After you paste it in your shuffle web hook, you want to make sure that your hook url is not included as the url if that makes sense, so you want to press space in between to make sure that your hook url is white and not purple because if it is purple, it thinks that it's related to the shuffle url, but then if I press space it separates it from the url itself. That is what we want to do now.

- by default it has a level of 3, so this means that any alert that has a level of 3 will be sent to shuffle now. I personally don't want this, and instead I want only our rule ID that we created, which was 100,002, and that was our mimikatz ossec rule that we created earlier.

- Again, if we wanted to send all alerts that were generated with a level of 3 or whatever the number is, we need to change the number to reflect that level, but because I want a rule ID instead of a level number, I will change the level tag to rule ID tag.

- I will remove the level and type in rule underscore ID And then I'll change my 5:100,002.

- Make sure you close it off correctly by typing in rule ID, so this looks good. Our indentation is good to go. My hook url is not attached to the url itself and my rule ID is 100,002. I will go ahead and save this out and remember if we make any changes to our configuration.

- What do we have to do? That's correct. You want to restart your wazuh manager service.

- Let's go and regenerate the mimicats telemetry on our Windows client machine, so I have my powershell here I'll exit out of that and then type in you are awesome and hit enter now let's head over to our shuffle instance.

- Click on our web hook and make sure you select start. Now let's click on the person tab at the bottom as this will show us any executions, so we'll click on that and let's go and test workflow. Now that is extremely strange. There is no event pushing over to my shuffle,

-  so let's do a little bit of troubleshooting, shall we? We'll go? Back to our wazuh manager and we'll open up the configuration file. Now I just need to make sure again read my url or anything such as my rule ID. I do recall it seeing that is a hundred and 2 which is correct, but if you look at the url there's http and https

- so that is clearly wrong. We will remove HTTP and save that out. Now that should be good.

- Let's restart wazuh manager. Now that that is done, let's head back over to our climb machine, exit out of mimicats. 

- now let's go ahead and click on this and then select the execution arguments and we'll expand that over and look at that. We get all of the information that was generated from wazuh itself. Isn't that awesome?

-  Today we will have our mimicats alert be sent over to shuffle. Shuffle will then take that. And extract the file hash from the file and then check the reputation score using virus total. We will then send that over to the hive to create an alert and then we will send an email to the analyst so they can perform further investigation.

- when we look at our return values for the hashes, we notice that is appended by their hashtight. For example, in this case it is appended by Shaw 1 equals and then the hash value. If we wanted to automate this, we would need to parse out the hash value itself because if we do not do that step, the entire value, including the shaw 1 equals will be sent over to let's say virus total to check and we don't want to do that.

-  We just want to send virus total the hash value. Why don't we actually do that? So let's close this out. I'll click on the change me icon and instead of repeat back to me, we actually have a couple of other options that we can use so we can type in rejects and select rejects capture group for the input data. We can select the plus button and select execution argument.

-  Now let's look for our hash and our hash value is this one right here. Now, if you highlight over it, you'll actually get the values or the output on the left hand side. Now at the bottom it tells us to do some rejects,

-  We can head over to our trusty chat GPT using chat GPT we can create a prompt saying create a rejects to parse the shaw to 56 value of the hash and then I'll hit enter

- chat GBT created a redjects for us so we can start copying that once it's finished talking we'll scroll back up, copy the rejects and then I'll head over to shuffle.

- now we can paste in the rejects that chat GPT created for us and I just wanted to say use AI to your advantage. Once we pasted that in, let's save our workflow and then we can click on the person icon again, then click on this refresh button or re run workflow button at the top.

- We'll click on that, and then if we were to expand our results can see that it passed out the shot to 56 hash. That's pretty awesome now when we encounter something that we aren't too familiar with, don't be afraid to utilize chat GPT and ask it to help you

- now that you have actually parsed the hash value for the file, we can automatically send that over to virus total and check the reputation score. Now I will rename change me to Shaw 256 rejects the. Next thing we want to do is utilize Virus Totals API that way we can automatically ask Virus Total to check this hash and return the values to us

- in order to utilize their API we must create an account with them. So let's go and do that, you can head over to Virus Total Site and then click on, Sign up on the top right corner. Once you've created your account, go ahead and copy your API key and then head back over to shuffle

- Once we're in shuffle, we can click on the apps and then we want to search for Virus Total and hit Enter.

- now we can click on Virus Total and then it will activate Virus Total onto our shuffle instance. Once that's done, go ahead and drag Virus Total over and it will automatically connect

- now when you click on Virus Total, I'm going to just rename this as Virus Total and then to find actions I don't want an IP address. Instead I want to look for a hash so I'll click on the drop down. Now you might only see one action that is okay. That's simply because virus total is still activating in the background.

- You want to wait just a bit and then more actions will populate so it's been a couple minutes and I went ahead and refreshed my shuffle instance and now when I click on Virus Total under the find actions I. Can see many other actions. Again, I am not looking for an IP address report. Instead I want to look for the hash

- so if I were to this type in hash I can find that there is a hash report and if you want, you can paste in your API key here or you can authenticate with virus total v. 3 by clicking on the big orange button. That's what I'll do, so I'll paste in my API key that I copied from virus total and then hit submit.

- Make sure for the hash section you want to select the rejects output right now it is pointing to exec all underscore fields agent IP and we don't want that.

- Now we want to select the rejects output for list because again the radjax is there to parse out the value of the hash and now we will go ahead and save the workflow click on the person icon and then we'll select the middle workflow and rerun workflow.

- Now let's take a look at Virus total's output. We'll go ahead and expand that and we notice here that the status is 4 of 4, so that is quite strange. This means that Virus Total was not able to return any results for us. Now if we were to look at the query itself in the URL section, we see that it's going to API V. 3 files and report and then we see our hash equals to hash value.

- let's head over to Virus total's API documentation. If I were to scroll down we see that there is a get a file report by hash and that is what we're interested in, so I'll go ahead and click on that and I want you to take a look at the url here it is going to API V. 3 files and ID.

- That is interesting ID so if we were to go back into our shuffle our url here is going to API V. 3 files and report. The documentation mentioned nothing about report, so maybe that is why we are getting a 4 or 4 error.

- The virus total app in shuffle is going to a incorrect url or I guess it was probably correct before, but Virus Total updated its API. Maybe now how do we fix this?

- we can actually head over to Virus Totals app and edit it so to do that, let's head over to Apps. Once you're in the application page you want to select the new window under the Virus Total app.

- I'm currently hovering over the icon and select that you'll be presented with virus totals application page for shuffle. What you want to do is you want to fork over the application so you can actually edit it so let's click on that scroll down and then let's find our hash report.

- Now we can see that it is indeed referencing Report as you recall looking at API documentation on virus total. It did not mention anything about Report. Instead it was pointing towards an ID variable enclosure closed and curly bracket, so that's what I'll do. I'll change that over here and then hit submit, I'll scroll down and save it out.

- Let's try this again. I've placed the edited virus total onto our workflow so I'll go ahead and select that again. You do have an option to authenticate with the API key here or you can click on the plus icon to authenticate. 

- Now. I do not want to get an IP address report, change this over to hash, get a hash report, change the ID to remember the rejects ID and it is list and then we'll go ahead and save the workflow and re run it. Click on rerun 

- let's expand virus total now and look at that. It's a lot different. If we keep on expanding let's head over to body data attributes and scroll down and look at all of that data. If we scroll down to last analysis stats this one right here and then we can expand it

- We can see that it says malicious 63. That means that 63 scanners had detected this file. As malicious as of recording now you might be asking how did I know which feel to use for the reputation? And that's a great question. When I search our hash through virus total, we can see that it detects 63 out of 71 as of recording. 

- All we need to do now is grab the output that Shuffle had provided us and search for 63 obtained the field name which an arcase was malicious under the last analysis stats, which sounds about right to quickly recap, we set up our sore platform to receive our bizarre alert.

-  We then performed rejects to parse out the shaw to. 56 hash and we then used virus total to check as reputation. Next, we will send the details over to the hive so the hive can create an alert for case management.

- under the application tab at the bottom left hand corner you want to search for the hive and we want the hive 5, so click on that and then we will drag it over to our workflow and then let's take a look at the action

- before we. Move on with shuffle, let's head over to the hive. If you recall, we can log in using the default credentials of admin at the hive local and the password is secret. 

- By default we only have one organization and that is named admin. Let's create a new organization and create a new user for that organization. To do that, you want to click on the plus button at the top left corner and as an organization name.

- click into it immediately. It says no users have been found, so let's add 2 users. The first user I'll leave the type as normal 

- You can select a couple of permissions here, read only analyst and org dash admin. I'm going to select analysts as the profile and I will save

- add another user this time instead of selecting normal, I'm going to select service account or in this case just service for the login. I will type in shuffle at test. Com and the name I'll just type in sore. As for the profile we do have read only analyst and org admin in a real world environment,

- you would want to create a new set of permissions with the. Principle of least privilege and mine and tie that to the service account.

-  We're okay to just select it as analyst and I will hit confirm. The next thing we want to do is create a password for the analyst account

- You can highlight the account and select preview, scroll down and then you'll see set a new password. Once you set a password, hit confirm

- then for the sore user you want to select preview and you want to create an API key for this user. Once your API key is created, go ahead and copy that out and make sure you put it somewhere safe because we will be using this to authenticate with shuffle.

- now I will log out of this administrative account and then I will log into analyst account. Once you log in, you're now presented with a different page. We can actually see cases well, in this case no cases have been found, but we can see cases here and we can also see alerts.

- now let's head over to shuffle and configure that to work with the hive on our shuffle instance. We want to select the plus button beside authenticate because we want to authenticate to the hive using our API key

-  Copy the API. Key over and paste it into your hive. As for the url you want to type in the public IP of your hive instance along with the port number. So in my case my hive instance public IP address is 173.120.227.149 on port 9000 and then I will hit submit

- under the find action. I don't want to query the API. Instead I want to create an alert so I will select Create Alert and at the bottom we can ignore first couple options. However, the one that we need to add on is the date

- Now. To get the date, we must connect our hive to the workflow so I'll connect virus total to the hive that way it can now take a look into Arawaza alert, so selecting the date, I'll click on the plus button and then I'll click on execution arguments

-  I'll select date and hit the plus and now I can see the shaw 256 and virus total as well. It just seems a lot cleaner. To me, so that's why I like to make sure I connect things together.

-  Now we'll go into execution argument and then we'll look for the time field. In this case it's right here UTC time. Now. If you're not sure again, if you just highlight the field, it will populate the field value on the left hand side.

- I'll select UTC time as for the description, let's type in Mimi cats detected host and let's select a field name that contains the host, so I believe computer looks to be good.

- Mimicads detect it on host computer from maybe the user is good to put on right, so let's add on the user. We scroll all the way down.

- So if I were to receive an alert, what are some of the field that will provide a lot of information now? This is very subjective depending on the analyst itself, but for the sake of this demo 

- I'll just leave it as these 2. For now external link
we don't need to add anything for the flag. We'll put it as false. As for the value 2 is the default according to the documentation.

- PAP is Permissible Actions Protocol aka the Level of Exposure of Information Nexus Severity, which I will put it as 2 the source.
typically this is where the alert is coming from, I'll just say LSA and the source.

- reference just a little bit of metadata for the alert itself. I'll just put in the rule ID so 100,002.
the status is the status of the alert that will be created in the hive, so we want the status to be new 

- the summary similar to the description, but a little bit more information. I'll type in Mimi cats activity detected on host.
let's select the host again. Here's the computer here And the process ID Is to find the process ID.
this is all up to you on what kind of information you want it to spit out, but essentially I'm going to ask it for the process ID and the command line and the command line is search for the command line

- for the tags we need to put this into an array. I'll put in the miter attack tag which is t 1003 aka Credential dumping and then the title. The title is mimicats detected. Now this is very static.

-  What we can do is actually just tie it to the alert itself 

- pop is the traffic light protocol, so the confidentiality of information I'll leave it as 2 and the type where did you find this typically internal or where did that alert come from?

- now let's go ahead and save this workflow and before we.
go ahead and run the workflow. We'll need to modify our cloud firewall to allow all IPS coming in bound on port 9000 because that is where my hive instance will live and this will be a temporary rule to allow us to test this automation.

-  create a new rule and put it as custom and then we want to add in the ports as 9000. We will remove all Ipv 6, but we'll keep all Ipv 4 and then I'll save it out.

- now any source will have access to our machines on port 9000.
heading back to our shuffle, we can now rerun the word so I'll click on the person icon and then rerun workflow scrolling down.

- it looks like the hive was successful, so let's go into our hive instance and look at that automatically

- the alert was created, mimicats usage detected click on that and we have our description mimicats detected on host desktop dash that from user Sally and the summary mimicat's detection on host that host and the process ideas ninety eight, forty eight and the command line is that command line.

 - now you can see how powerful this is if you really put in more effort into the summary and description because that will tell the analyst a bunch of information very quickly.

 - the next step is to send an email to our analyst containing relevant information. To do that, you can click on Apps at the bottom left and drag the email application and then connect virus total over to the email.
now we can enter in the recipient

-  so I can literally type in any email I want as long as it is a valid email.

- the subject will be mimicat detected.
that's a scary subject when you wake up to it to be honest, and then I'll put in the time let's head over to our execution arguments and then let's look for UTC time.

- Type entitle and we will put in the title and I think it's useful to put in the computer name as well.
right? If mimicats is detected you want to know where it was detected from computer.

- let's go ahead and save that out and we will read.

- We can click on the new email that was received and look at that shuffle mimicats detected with the time title and host that is super cool to showcase the responsive feature.

- I deployed another virtual machine because from my testing it seems that the responsive scripts for Windows don't work as well.
compared to Linux. I will use a similar workflow. However this time we will add a user input and we will ask the user if they want to block a certain IP.

- create a rule to allow all TCP connections to or this Ubuntu machine.
or you can do this on prem and use an attacker machine that you own to stimulate a SSH boot force attack in shuffle we want to add on the HTTP application so I will go ahead and drag that over.
I will rename this as Get API and have defined action as curl as for the statement at the bottom, you want to include the following and make sure that a firewall rule exists to allow all inbound traffic to Waza on port 55, 000.

- now the reason that we are doing this is because if we want to utilize wasa's API capability, we must first identicate and obtain a JWT token aka a JSON Web Token, and in order to do that, we need to initiate a CURL with a username and password.

- now if you recall there was a wasaa API user that is the user account that we'll be using and then the dash k for insecure and then get https wasaip.
so your public IPA wasa on port 55, 000 now shuffle will be making this request, but that is why we need to make sure.
that that port is opened for all IP addresses.

- now. Once you created your firewall rule to allow traffic inbound to port 55, 000, we can now look for Wazzaz application so let's go ahead and search that and then click it to start downloading it and activating it. Once you have was all activated, you can go ahead and drag and drop onto your workflow.

- Now I do have to mention when you click on the Get API, do remember to fill in the user and password for the LSA API account and include your wazuh IP here. If you leave it as default, it's not gonna work so I'll just go ahead and change this right now and then I'll save the workflow. 

-  we'll head over to the wazuh application. Run command is what we want, so I'll leave the find action as run command. As for the API key, we will select our get API app node, so if I were to rebuild this workflow just a little bit, this is how it will look like the Wazza alert will feed into the Get API. The Get API will then feed over to Virus Total. Virus Total will run its reputation on the source IP and then Virus Total will eventually connect to to what is called a user input and that will ask the user if they want to block that IP.

 -  Now I don't want to put in the user input. Yet so I'll just connect virus total over to Wazza that way we can test the responsive capability without constantly asking the user to see if they want to block an IP. Now that we have wizard connected again the API key, we're going to select it as get API for the URL changed the local host to your wazuh public IP.

 - If we scroll down we see what is called an agent list. Each wazuh agent will have an associated ID with it. To find this we can head back over to Wazzaz dashboard. You want to click on the drop down arrow and select agents as we can see at the bottom. The Ubuntu agent ID is 002. Yours might be different depending on how many hosts you have

 -  Now we can also find this ID in our sample log or alert. If we head over to shuffle and and click on our execution our person icon and then if we were to expand our execution arguments, scroll all the way down. There is a section called agent and then there's an ID here. In my case the idea is 001 for this Windows machine, so as an agent's list I can either type it in manually or I can select it to make it dynamic.

 - Execution arguments scroll all the way down until I see my agent. ID Perfect that. Looks pretty good. I will set the weight for complete to true and then we have alert arguments, command and custom. Now before we move on to the next couple fields, we must configure active response on our wazuh manager.

 - Let's head over to our manager console. On our manager console. We want to open up the OSEC configuration file, scroll all the way down to the bottom and eventually you will find what is called active response. Now we can always hold down control W and type in active response, butchered that active response there you go. So under active response there are some pre built response options that we will actually be using, and if you take a look there are tags called command, so here does a command called disable account.

 - The next command is restart was a firewall drop, host, deny, and these commands are essentially what you invoke. In other words, if something bad happens, then do this command. The most important thing here is that we want to keep in mind of the name itself, so in my example I'll be using Firewall Dash drop

 -  This command will modify the IP tables of RU buntu machine, essentially dropping all traffic to it or to the IP. We will now need to create an active response tag and make sure that the command reflects the command name that we want to use.

 - For example, let me scroll down until there's no more command and and. Right here we see that the active response options here, so I'll remove the comment section and then I'll type in command.

 - So what is the name of the command that I want to run? It is called firewall dash drop

 - Then I want to close out the command tag. Next is the location tag. I'll put this as local and then close that out. Now what local means is that the host that generated the alert. That is where the script will be ran.

 - next is level, so this is the level of the alert if you recall when we were configuring our shuffle integration instead of using the level we used Rule ID. In this case for our active response, I'm not going to be using Rule ID instead I'm going to be using level.

 - I'll close out that tag and finally time out no, and now we have our active response. So let's go ahead and save this out and we will restart the Wazzaw manager. We can restart it by typing system CTL restart wazuh dash manager.

 - There is one important thing to keep in mind if you use Active Response, especially when it comes to Apis. It's that the command name appends the timeout to the name, but it is hidden. I'll explain. Currently our command name was. Set to firewall DASH DROP so something like this right? But if we want to use this via an API we must append our timeout option to it and there is a hidden timeout field.

 -  So my time up was 0 or no, so now it will append a 0 in front of firewall dash drop. Now how would you know that one way to see the name and test to see if your script is active is to use what is called an agent control binary. This is located under var osec in so let's change our directory there type in LS and we can see agent underscore control.

 - That is the binary that we want while type an agent underscore control and without any options that way we can see what kind of options it provides. At the bottom you can see there's a dash capital L This list available active responses, so let's go and use that.

 - Now you can see the response name firewall dash drop 0. That is the name we need to use if we want to utilize the API. So for example, just to make sure everything is crystal clear if you set a timeout option, say 600s, that means your firewall or command name will be something like firewall dash drop 600

 - you can always verify using the agent control binary dash L to testo your actor response actions. We can run agent control while type in agent control dash lowercase. B for block and then we will block the IP address of 8 ETA ET, which is google's DNS, and then we will specify dash F for the active response command. Now our command was firewall dash drop and because we're using agent Control we need to specify the 0. Next I will specify the dash you for the agent ID 0. 0. 2 and that is my Ubuntu machine.

 - you can always verify using the agent control binary dash L to testo your actor response actions. We can run agent control while type in agent control dash lowercase. B for block and then we will block the IP address of 8 ETA ET, which is google's DNS, and then we will specify dash F for the active response command. Now our command was firewall dash drop and because we're using agent Control we need to specify the 0. Next I will specify the dash you for the agent ID 0. 0. 2 and that is my Ubuntu machine.

 - So let's head back over to our Wazza. From here we will go ahead and execute our active response and now it says running active response firewall drop on 002

 - Let's head back over to Roubuntu machine and we see that it stop. I guess it works perfect, so let me go ahead and cancel this out and then we'll type in IP tables dash dash list and then we can see that it's dropping DNS Google responses.

 - We can take a look at our active response log on our Ubuntu machine to see if the active response was successful. This can be found under var osec logs and if you type in LS we want to look for active responses so we can go ahead and cat out of responses and we can see that there are some responses. Here that was successful. You can see that our 8. 8. 8 was here on our shuffle instance. Let's add in the command firewall dash drop 0. We scroll down on our Wazza application

 -  we want to select under command firewall dash drop 0 and put in the arguments of a da Ra. Da Ra and I do know that you do need to add in in array. Now I will save this out and then I will rerun the workflow. Let's head down to Wazza and expand that out and it says AR command was sent to all agents.

 -  Let's go and take a look at our Ubuntu machine on the machine. I'll clear this out and then type in active response log. Now immediately I see an error that says cannot read source IP from data so that we know for a fact that it does not work

 - Now just above it we do see our log. We see that the extra arguments our IP has listed in there and it cannot read our source IP from the data, but if you recall we did use Agent Control to execute our active response to test it out, and that is this log file right here

 -  Now if we look at the parameters extra arguments and I know it's kind of hard to read with the parameters extra arguments it is. Blank right here it is blank.

 - There's no data and for the alert that is where the source IP lives, and if you look at the log that Shuffle had sent over, there is nothing in the alert itself, so that tells me that our IP needs to be in the alert section, but just to be safe, I am going to copy everything from the alert up to the second curly brat,

 - so I'll go ahead and save that out or a copy and then I'll head over to our shuffle instance on our shuffle we want to scroll down. Let's remove the arguments here and under the alert section. Let's go ahead and paste in our data here

 - Now let's go ahead and try that. I'll save this out and then we will rerun the execution, but before I run that let's set up another ping towards 8.8 on our U Bun 2 machine just in case it does work. So I'll set out another ping And this is okay.

 -  Head back over to our shuffle and now we will rerun our workflow. Now let's scroll all the way down and select Arawaza, and again it does say an AR command was sent to all agents so it should be successful. If our command was correctly done, let's head over to our Ubuntu and look at that. It's stopped. I'll exit out of this and then I'll take a look at our IP tables And it's there DNS. Google drop all perfect.

 - So we know for a fact that our shuffle is sending an active response over to wazuh, and wazuh is instructing Yubuntu to actually perform that command. Now the next step is to set up a user input that way. It will send an email to our sock analyst providing all of that information, and if the analyst says yes, go ahead and block it. Wasas should instruct you buntu to block that IP, so the only thing that we need to add now is let's remove this chain or that link

 -  then we'll go under triggers and we want to drag over user input. I'll connect virus total over to the user input and here I'll make sure that the email is selected 

 - We can just say, would you like to block this source IP? What is the source IP? Let's go take a look here. We can just click on this icon and didn't look at the execution arguments, so here's the source IP that we're interested in, so I'll highlight this and then I'll copy it. I'll remove it and then head over to the user input.

 - We can just say, would you like to block this source IP? What is the source IP? Let's go take a look here. We can just click on this icon and didn't look at the execution arguments, so here's the source IP that we're interested in, so I'll highlight this and then I'll copy it. I'll remove it and then head over to the user input.

 - So in order to do that, I just clicked on the plus button, looked at the execution argument, and then I tried to find out where the source IP was and it was located at the bottom, and if I didn't know for sure, I looked at the value that was presented to me on the left. So I clicked on source IP and then I just took the variable here, copied it, removed it because I don't want virus total to take a look at it.

 - Instead, I added it over to our user input, so in theory of this workflow ran it would ask the user if they would like to block this source IP and it will also present the source IP to the analyst. And if the analyst says yes, I want to block that source IP. The responsive feature should then kick in.

 - so let's go ahead and connect that over to our wazza. Make sure our command is all good to go
now the source IP is no longer going to be a static IP. Instead I'm going to remove that and then I will select the plus icon execution arguments scroll all the way down to. Source IP and then I'll select that and I noticed that the variable is outside of the bracket, so I'll remove that and then put it in between the quotes. There you go, now that looks a lot better

- I'll go ahead and save this workflow out, and now it's the moment of truth. I'm going to take a look at what the source IP is this case, so the source ip is 103.180149.26. I'm going to go ahead and ping out that ip. Hopefully they accept pings Perfect so they do accept the pings

- let's hope our sore workflow works under our workflow. We will re run the workflow, so once we run it it should go out and grab the API key for Waza, then it will enrich the data in this case IP, and then it will send an email to our analyst and depending on its response it should activate a responsive action.

- click on inbox and let's refresh this and here we go. We have an email. Here it says would you like to block this source IP? One o 3 dot one 80 dot one 4. 9, dot 2. 6 if this is true, click this. In other words, if you want to block it, access this link, yes, I want to block it. Let's go ahead and do that. And here it says, would you like to block this source IP? And. It automatically select it.

- the next thing to do now is head over to our Ubuntu machine and see if our active response kicked in and because there's just way too many going on, i'll go ahead and exit that out, and then i'll type in my ip tables. Let's list it out, and our ip of 103.180149.26 has been added to our IP tables. Our active response actions kicked in from here.

- so we have finally see that every thing is working perfectly.

- we can do as many automation's as we want just go and practice with them and happy playing.

- **i am very sorry i cannot take much screenshots because my laptop is hanging because it is a very big project i recommend you to to set up in a cloud platform for better experience**

### Thank You For Your Valuable Time.