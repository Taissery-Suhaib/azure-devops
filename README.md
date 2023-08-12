**Automating Infrastructure Deployments in the Cloud with Ansible and Azure Pipelines**
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/bcc2c6bc-f237-41c9-b8dc-d4e9178c7abd)


### we are taking one vm as agent and for deployemt ###

##---- Agent Setup ----##
either azure agent or self hosted agent :

if its self hosted agent :
         >   create a vm
         >   connect with vm as "ssh"
         >   go to azure devops organisation >> make the directory as default then >> Org settings or Project settings >> Agent pool >> Default > add agent >> copy the commands and paste in the agnet vm 
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/10ad2adb-4af7-451f-9739-04769d0ff0c9)
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/51856824-0e17-44a1-a004-682115ae4ab7)
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/e51fef75-f666-40f5-af3f-89b06f0c2f60)
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/43a78bfb-b021-4eb6-a93d-2e4f166eac1d)





------------------------------------------------------------
***Task 1: Create an Azure service principal with Azure CLI***
> create a Organisation 

**https://azuredevopsdemogenerator.azurewebsites.net/environment/createproject**

> click on  the  above link >> under this mention template as ansible >> specify  the project name >> select the org under which it has to be created
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/e7dcc1b4-7d4f-47a2-b349-d7df00d5ccd6)

> after this open the organisation under where it has created and check the repo if the repo name smart hotel 360
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/bb0055f0-8a4e-4119-96b1-a25d52150344)

> go to azure portal and click on cloudshell 
> give the cmd and copy it to a note pad :
    >> az account show
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/a48f3ab4-3135-47ec-a288-a1c4bf85a2cc)

    >> az ad sp create-for-rbac --name ServicePrincipalName --role Contributor --scopes /subscriptions/<subscriptionid>

in the aboveline replace the serviceprincipal name with ( any name )  and replace <subscriptionID> with sud id which u got ( az accoungt show )

===================================================================================================================================================


***Task 2: Configure Ansible in a Linux machine***

> connect to VM as ssh and install and configure below cmd 
>>   sudo apt update -y
     sudo apt-get install -y python3-pip
     ansible-galaxy collection install azure.azcollection
     pip3 install -r ~/.ansible/collections/ansible_collections/azure/azcollection/requirements-azure.txt
     pip3 install ansible[azure]
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/7a12fa32-1613-4ff9-994e-808e9d8602d0)


__________________________________________________________________________________________________________________

mkdir ~/.azure
nano ~/.azure/credentials

     >>   [default]

          subscription_id=<your-Azure-subscription_id>
          client_id=<azure service-principal-appid>
          secret=<azure service-principal-password>
          tenant=<azure serviceprincipal-tenant>
change <> with the out u got form cloud shell and save

create env variable :
>> nano ~/.bashrc
    >PATH=$PATH:$HOME/.local/bin:$HOME/bin
save
---------------------------------------------------------------------------------------------------------------------------
ssh-keygen -t rsa
chmod 755 ~/.ssh
touch ~/.ssh/authorized_keys
chmod 644 ~/.ssh/authorized_keys
ssh-copy-id azureuser@127.0.0.1     # replace azureuser with vm - username 


>> cat ~/.ssh/id_rsa

copy the private key 
==============================================================================================================================


***Task 3: Create a SSH Service Connection in Azure DevOps***

> open azure_devops = 
     >> Navigate to Project Settings –> Service Connections. Select Create service connection.
       > select ssh 
           >> paste the private key 
           >> replace hostname with vm pub ip     
           >> user and pass of vm and port = 22
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/b1520b8e-cc67-4149-93ec-d53976ba0beb)

=================================================================================================================================

***Exercise 1: Examine the Ansible playbook (IaC) in your Source code***

> open the devops project > go to repo > Select the webapp.yml file under the ansible-scripts folder. Go through the code.
>> edit under line number 66 = 255.255.255.255 with 0.0.0.0

===================================================================================================================================
***Navigate to Pipelines –> Pipelines. Select Ansible-CI and click Edit.***

> open the piple > ansible-ci > edit 
     >> in maven build = check if pom.xml is selected
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/81defc04-679f-4997-94ff-cde1f9082ae7)

     >> in copy files to = under contest **/*.war and **/*.yml
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/1f876705-5bd3-4ca3-92b4-b655f1d5e0dd)


> select agent pool as > default 

![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/c7a8fdd9-349d-4e25-8e20-28a76be5f501)

run pipeline 
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/bd799bae-b3be-458f-a827-ac404d5e0531)

============================================================================================================================================

***Exercise 3: Deploy resources using Ansible in Azure CD Pipeline***

 > Navigate to Pipelines » Releases. Select Ansible-CD and click Edit pipeline.
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/08b22087-ab6d-4476-b590-a5f897de4972)
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/0ede05ec-68a7-42d9-9514-297c61424305)


  >> under replace = make sure it is **/*.yml  and under token _'''_ is selected
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/f4e8eb9b-37ea-41fd-b80f-56551861b4af)

  >> under run  playbook choose ansible location as = agent , select the ssh we configured in the beginning > under inventory = select " host list  " > "vm ip "
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/9e863a7d-82a4-423e-9696-2b1c4e817235)

  >> undeer azure app service deploy >> under azure sub = select the az_sub
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/3011ca96-6c9b-4217-b5dd-d907b1a8fe93)




                        ---------------SAVE------------------------
Run the release pipeline with > ci build version number 
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/9a858e05-9c0e-49e3-b604-b3ae8e44a709)


========================================================================================================================================


open azure portal 
>> open resource group
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/2b04953a-c875-45a3-b528-19b0caad5e74)
 
>> open the app service > click on browse
![image](https://github.com/Taissery-Suhaib/azure-devops/assets/134582331/634f88b5-fd5a-42ed-a6de-b42e3dd24b3b)



