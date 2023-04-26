## Pre-requisite

 - We need to have Jenkins and nexus server up and running (by default Jenkins runs on 8080 and nexus at 8081), to install on ubuntu refer [link](https://github.com/DeekshithSN/cheatsheet/blob/master/installtion_guide_ubuntu.md)

## Initial setup 

In nexus click on gear button --> click on repositories --> click on create repository ( below image can help in creating )

![nexus1](https://user-images.githubusercontent.com/29688323/136389371-2bc67fb7-8a21-47d8-82bf-59753d6a1ee4.JPG)

once we click on create repository ( types of repository will be listed ) --> click on helm(hosted)

![nexus5](https://user-images.githubusercontent.com/29688323/136393148-6a4b4e39-dcd6-48c7-91aa-377993cbef1a.JPG)

give a meaningful name to repository and click on create repository  

![nexus6](https://user-images.githubusercontent.com/29688323/136393453-78e9486a-c0c0-4703-909f-b1f0d409fa28.JPG)

Now no other configuration is need in Jenkins host because we will nexus repo api and publish the helm charts 

the below line can help you in pushing the helm chart 

```
 curl -u admin:$nexus_password http://nexus_machine_ip:8081/repository/helm-hosted/ --upload-file myapp-${helmversion}.tgz -v
```


