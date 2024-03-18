# Counter Python App

The `counter-app` is a sample counter app.

# Tasks

1. Containerize the application. The image that you will build must be named <ynov-Your-Team-First-Names>
2. complete the Dockerfile with the TODO
3. Publish the custom image to your own dockerhub account
4. Create 3 virtual machines with ubuntu server.

   - The first virtual machine will be named `gitlab-instance`
   - The second virtual machine will be named `gitlab-runner`
   - The third virtual machine will be named `dev-srv`

Note:

- The first virtual machine will host a gitlab instance
- The second virtual machine will host a shell based gitlab runner
- The third virtual machine will be used to deploy the containerized application
- Create a CICD pipeline using gitlab CI.
- The pipeline must be build by your own created `gitlab-runner`
- The pipeline must have 3 stages

  - The first stage must be named `build-image`
  - The second stage must be named `publish-image` . Please make sure you use the commit hash for the docker image tag
  - The third stage must be named `deploy-app`. This stage must deploy the app in the `dev-srv` server.

- You need to submit your public github repository
- The public repository will contain the pipeline file
- The public repository will must contain a README.md file that shows all steps to run your project. This is for every developer that encounter your repository
- The public repository must contain also a pdf file that shows your work. Please make sure your pdf is well written and contains everything that will demonstrate your work.
