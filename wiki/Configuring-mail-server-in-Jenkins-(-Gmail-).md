## Pre-requisite 
- running Jenkins server 
- Gmail account with insecure apps enabled 

## setup 

Install ```Email Extension Plugin``` in Jenkins 

Once plugin installed in jenkins, click on manage jenkins --> configure system there under E-mail Notification section configure the details as shown in below image 

![mail1](https://user-images.githubusercontent.com/29688323/136433493-426f90e1-16cb-4751-8055-44b17f508224.JPG)

this is to just verify mail configuration 

Now under Extended E-mail Notification section configure the details as shown in below images 

![mail2](https://user-images.githubusercontent.com/29688323/136435258-538d0ea2-20f3-4afc-b499-e1e47a70a654.JPG)
![mail3](https://user-images.githubusercontent.com/29688323/136435248-1000cddc-f7dc-4a11-b9c6-c1d90b5de85a.JPG)
![mail4](https://user-images.githubusercontent.com/29688323/136435260-fdef923a-3c42-4269-9d2c-143d84471dcf.JPG)

By using below code i can send customized mail 

```
post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "deekshith.snsep@gmail.com";  
		}
	}
```

also which ever the mail you use for authentication in that mail setting "Less secure apps access" should be enabled 

![mail5](https://user-images.githubusercontent.com/29688323/136435833-879ce27a-1212-4398-a319-f4e6b778ebe6.JPG)

Sometimes we need do extra setting https://g.co/allowaccess allowed access from outside for a limited time and this solved my problem. 

Default Subject

```
$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!
```

```
$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS:

Check console output at $BUILD_URL to view the results.
```