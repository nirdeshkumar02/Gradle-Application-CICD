## Pre-requisite

 - We need to have Jenkins and nexus server up and running (by default Jenkins runs on 8080 and nexus at 8081), to install on ubuntu refer [link](https://github.com/DeekshithSN/cheatsheet/blob/master/installtion_guide_ubuntu.md)
 - On Jenkins host we need to install docker 

## Initial setup 

In nexus click on gear button --> click on repositories --> click on create repository ( below image can help in creating )

![nexus1](https://user-images.githubusercontent.com/29688323/136389371-2bc67fb7-8a21-47d8-82bf-59753d6a1ee4.JPG)

once we click on create repository ( types of repository will be listed ) --> click on docker(hosted)

![nexus2](https://user-images.githubusercontent.com/29688323/136389787-76a847cb-df1b-4a42-b031-2aa364fa76ad.JPG)

fill out the details in Name ( unique name ), enable checkbox beside to HTTP and enter a valid port ( preferred 8083 ) once that click on create repository   

![nexus3](https://user-images.githubusercontent.com/29688323/136390256-f6f67caf-a86a-4049-9712-3f43043fbcbe.JPG)
![nexus4](https://user-images.githubusercontent.com/29688323/136390268-16f2f9dd-2738-4270-9200-055808a604e9.JPG)

Once this set up is done in jenkins host we need to setup Insecure Registries. to do that we need to edit or if not present create a file ```/etc/docker/daemon.json``` in that file add details of nexus 

```
{ "insecure-registries":["nexus_machine_ip:8083"] }
```

once that's done we need to execute ```systemctl restart docker``` this is to apply new changes, also we can verify whether registry is added or not by executing ```docker info```
 
once this is done from jenkins host you can try ```docker login -u nexus_username -p nexus_pass nexus_ip:8083```

