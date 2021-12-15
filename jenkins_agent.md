# Using Jenkins agents

## Agents node setup
**Jenkins plugins**
1. NodeJS
2. Amazon EC2


## Generating an SSH key pair
- In a terminal window run the command: `ssh-keygen -f ~/.ssh/jenkins_agent_key`

- Provide a passphrase to use with the key

## Create a Jenkins SSH credential
1. Go to your Jenkins **dashboard**;

2. Go to **Manage Jenkins** option in main menu and click on **credentials** button
3. select the drop option **Add Credentials** from the global item;

4. Fill the form:

  - **Kind:** SSH Username with private key;

  - **id:** jenkins

  - **description:** The jenkins ssh key

  - **username:** jenkins

  - **Private Key:** select **Enter directly** and press the **Add** button to insert your private key from **~/.ssh/jenkins_agent_key**

  - Passphrase: (if you instered one when created the key) fill your passphrase used to generate the SSH key pair and then press *OK*

## Setup up the agent1 on jenkins:
1. Go to your Jenkins dashboard;

2. Go to Manage Jenkins option in main menu;

3. Go to **Manage Nodes** and **clouds** item;
4. Fill the Node/agent name and select the type; (e.g. Name: agent1, Type: Permanent Agent)

5. Go to New Node option in side menu;

6. Now fill the fields:

  **Remote root directory**; (e.g.: /home/jenkins )

  **label**; (e.g.: agent1 )

  **usage**; (e.g.: only build jobs with label expression…​)

  **Launch method**; (e.g.: Launch agents by SSH )

    Host; (e.g.: localhost or your IP address )

    Credentials; (e.g.: jenkins )

    Host Key verification Strategy; (e.g.: Manually trusted key verification …​ )

7. Press the button save and the agent1 will be registered, but offline. Click on it

8. Now press the button Launch agent and wait some seconds, then you should receive
the message: Agent successfully connected and online on the last log line.