# Sample application for e2e DevOps Pipeline

# This is a sample application to demonstrate an end to end DevOps Pipeline

# Step -1 Install jenkins in kubernetes cluster via manifest file

# Step -2 Set agent in jenkins

  1 - Go to manage jenkins > clouds > create new cloud 
  
    > name,kubernetes url (if its in different network, if its same network where jenkins installed namespace is enough)
    ,namespace,jenkins url > test connection
    
    > pod label - key: jenkins,value:agent, kept as it is all
    
    > save

  2 - for that create pod Templates 
  
      > add pod template (ex:kube-agent)
      
      > name (kube-agent),namespace(devops-tools),labels(kube-agent),usage(only build jobs with label...),
      
      > containers > container template name (name:jnlp),docker image (jenkins/inbound-agent:latest),working directory (/home/jenkins/agent),
        kept rest of setting blank
        
      > containers > container template name (name:docker),docker image (docker:latest), working directory (/home/jenkins/agent),command to run (cat)
      
      > volumes > host path volumes > host path (/var/run/docker.sock), > mount path (var/run/docker.sock) 
      
      > kept all setting as it is
      
      > save
# Step -3 Install important plugins

   1 - maven intergration,pipeline maven intergration,Eclipse temurin installer
   
   2 - kubernetes > docker (api,build step,pipeline,commons,build and push) 
   
   3 - sonarqube > sonar Quality Gates

# Step -4 set maven and java jdk in jenkins

   1 - Go to manage jenkins > Tools 
   
      > JDK Installtion > Name (ex:java17) > Install automatically > install from adotium.net > version ( jdk-22.0.2+8)
      
      > Maven Installtion > Maven Name (maven3) > Install automatically > Install from Apache > Version (3.9.8)
      

# Step -5  create credentials for scm (Github)

   1 - Go to manage jenkins > Credentials
   
      > New credentials > usename with password > username (github username) > password (personal access tocken of github) > id (github) > description
        (github) > save

# step -6 Install sonarqube in kuberentes cluster via manifest file or helm chart

# Step -7 Change Username and Password of sonarqube

    1 - Go to Administrator of sonarqube > security > Generate Tokens > Name (jenkins),type (user) > copy the token (use in jenkins credentials)

    2 - Go to jenkins > Manage jenkins > credentials > new credential (jenkins-sonarqube), > kind (secret text), secret (copy token from sonarqube),id 
    
    (jenkins-soanrqube)

    3 - Go to jenkins > Manage jenkins > Tools > Sonarqube Scanner installtion > Add Sonarqube Scanner > Name (soanrqube-scanner),> Install automatically >
    
       install from maven central > version (sonarqube Scanner 6.1.0.4477)

    4 - Go to Mange Jenkins > system > sonarqube scanner > sonarqube installtion > Name (sonarqube-scanner), server url (http://ip:port/sonarqube),
    
    server authentication token (ex:jenkins-sonarqube) > save

    5 - create webhook in soanrqube
       
       > Go to sonarqube > Administration > configuration > Name (jenkins-webhook), url of jenkins (http://ip:port/sonarqube-webhook)

# Step -8 Add Credential of dockerhub login

    1 - Manage jenkins > credential > username (docker username),password (docer personal access token), id (dockerhub)
    
# Step -9 Create Dockerfile 

# Step -10 Install Argocd in kubernetes cluster via manifest file 

    1 - Add your kubernetes cluster in argocd so in cmd > export KUBECONFIG=~/.kube/config
    
       > argocd login ip:port > username (admin) > password > (token) > argocd cluster add kubernetes-admin@kubernetes --name app-cluster 
        ( choose context kubectl config get-contexts)
        
       > add git repository in argocd username(github),> password (github=token),



 
