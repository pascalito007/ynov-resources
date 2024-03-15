# Infrastructure Cloud - CAPSTONE PROJECT II

You are hired as **Cloud Architect**,
The **X** company has a trading application in their traditional infrastructure.
The company has a file storage solution in his on-premises infrastructure.

The files in the company on-premise storage comes from different sources (Third party providers or external Partners).

## Decoder

The trading application has a small piece of code (written in Java) that **decode** each new file as soon as it is added in the storage. A cron Job have been created to execute that function every 5min in order to check for new files. (This is something that the company want to improve when moving to Cloud because they want the piece of code to be executed immediatly as soon as new files arrive).

Once the decode finished, the output data (final result) is stored in a Postgres relational database.

## Exposer

The company have a REST API (written also in Java) that retrieve and verify the data from the postgres database. The API is exposed to the end users or some python clients.

## Move to Cloud

The team want to migrate this trading application into AWS to take benefit of cloud.

- To reduce cost, they want the application to be fully serverless in the cloud.
- The company want to keep receiving files locally, but they want to send every new file to an object storage in AWS. They want some kind of synchronization between on-premises files and cloud objects storage.

**_Note:_** Please be aware that only new files are processed.

- As this is a financial institution, security mater. You need to make sure your solution is entirely secured and the files **MUST** be encrypted in the cloud.

## Your goal is to Lead the development team to migrate such application in AWS cloud.

## Tasks

- First you need to draw an architectural diagram of the solution and make sure you explain your choice.
- You need a hybrid solution as the company want to keep files locally you need to make use of some kind of VPN to connect the on-premises (which is your physical laptop in this scenario) to the AWS.
- Keep in mind that your solution needs to be fully serverless.
- For the program that decode the files, feel free to consider a sample code to illustrate that. The code can be written in any programming language.
- Same for the program that expo the result to users (REST API)
- Make sure your solution makes use of least privilege for resources access using IAM.
- Please make use of AWS Client VPN for implementing hybrid connection from your local laptop to your resources to AWS. This is what can help you send files from your on-premises (local laptop) to the object storage solution to the cloud for immediate processing.

For Client VPN authentication, you will need a directory service deployment https://docs.aws.amazon.com/directoryservice/latest/admin-guide/directory_simple_ad.html . please make use of simple AD mode.

- For networking perspective, everything needs to be done in a private only network. No Internet gateway or Nat Gateway can be used.
- You will have **30 minutes** maximum to present your work.
- Make sure you submit your PDF and your PowerPoint to Moodle
- **BONUS:** deploy the infrastructure using Terraform

  ### References:

  1. https://aws.amazon.com/privatelink/?nc1=h_ls
  2. https://aws.amazon.com/vpn/client-vpn-download/?nc1=h_ls
  3. https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/cvpn-getting-started.html
  4. https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/what-is.html
  5. https://aws.amazon.com/vpn/client-vpn/?nc1=h_ls

# Thanks, and be creative ![Enjoy](image.png)
