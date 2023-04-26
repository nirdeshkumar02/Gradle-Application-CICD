## Pre-requisite 
 - We need to have Jenkins and sonarqube server up and running (by default Jenkins runs on 8080 and sonarqube at 9000), to install on ubuntu refer [link](https://github.com/DeekshithSN/cheatsheet/blob/master/installtion_guide_ubuntu.md)


## Initial setup 
In Jenkins install plugins like docker, sonarqube and Sonar quality gate plugin (manage Jenkins --> manage plugins --> in available tab search for respective plugins)
After sonar plugin installation in configure system enter sonar information as shown in below picture 

![jenkins1](https://user-images.githubusercontent.com/29688323/136374259-56b1ea6c-e884-4441-b20d-877877fb6df8.JPG)


- Name: any meaningful name, but this will be referred in pipeline while executing sonar steps 
- Server URL: sonarqube url
- Sever authentication token: this token has to be created in sonarqube
To create that token in sonarqube navigate to administration --> Security --> Users ( after which you will see list of users)

![sonar3](https://user-images.githubusercontent.com/29688323/136375592-fb2e4653-40e1-4ae2-be9f-39cc1db45b09.JPG)

Click on token button, you will prompted to create token as shown in below pictures  

![sonar1](https://user-images.githubusercontent.com/29688323/136375038-b723289b-f52a-40d8-b573-f835fc987061.JPG)

Give meaningful name and click on generate 

![sonar2](https://user-images.githubusercontent.com/29688323/136375241-eeee8e71-3422-459e-904d-becddb9131a0.JPG)
 
Copy the token to create secret text in jenkins 
 
![sonar4](https://user-images.githubusercontent.com/29688323/136375897-2071e830-18c3-4465-86f1-b373e55d381a.JPG)

In Jenkins navigate manage Jenkins --> manage credentials --> add credentials --> select kind as secret text --> give value in secret ( will show up in sonar authentication token dropdown)

![jenkins2](https://user-images.githubusercontent.com/29688323/136376501-b6c625c5-be58-4dca-85e7-0f21fd7ca108.JPG)

 
Also need to create webhook to have communication between jenkins and sonarqube
To create webhook navigate to administration --> configuration --> webhooks --> then provide name and url of your jenkins sufixed with /sonarqube-webhook/

![sonar5](https://user-images.githubusercontent.com/29688323/136376934-2ad4cc25-9593-40ff-8ef7-a84516348e26.JPG)

```
 timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
```

Then in sonarqube configure quality gates, below is default if want we can change it 
 
![s0nar6](https://user-images.githubusercontent.com/29688323/136377228-299ab5b1-de97-4677-9bdc-cf353340c406.JPG)


Jenkinsfile would look like this, as you can see we are verifying our code against sonarqube quality gate, it code satisfies with quality gate then it will procced further else it will stop build in that stage 
 
![jenkisn3](https://user-images.githubusercontent.com/29688323/136377435-40a5741f-4f57-4ba1-9b3c-b84cc41810fb.JPG)

If you are quality gate satisfices then you will see passed in jenkins page 

![jenkins4](https://user-images.githubusercontent.com/29688323/136377693-ab37f100-c73c-4b36-8c08-214f7ceee88a.JPG)

