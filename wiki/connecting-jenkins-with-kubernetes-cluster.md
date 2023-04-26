Install Kubernetes Continuous Deploy Plugin, once its installed successfully. goto manage jenkins --> manage credentials --> Click on jenkins global --> add credentials

![k8s1](https://user-images.githubusercontent.com/29688323/136548689-520f667a-073c-4005-a71b-8ecc0f51f307.JPG)
  
select 

**Kind** : Kubernetes Configuration 
**Scope** : Global
**ID** : kubernetes-configs ( any meaningful name )
**Kubeconfig** : copy the content of kubernetes config file and place it at the file ```/root/kube/config``` in jenkins host 

And click **OK**

and in Jenkinsfile you refer as mentioned below 

```

	stage('connecting to k8s cluster'){
		  steps{
	            script{
	    		withCredentials([kubeconfigFile(credentialsId: 'kubernetes-config', variable: 'KUBECONFIG')]) {
			  dir ("kubernetes/"){  
				sh 'helm list'
				sh 'helm upgrade --install --set image.repository="nexus_ip:8083/springapp" --set image.tag="${VERSION}" myjavaapp myapp/ ' 
			  }
		       } 
		    }		
		  }
		}
```
