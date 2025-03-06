# Digital.ai Release GitOps Example

This repo shows how to use GitOps with Digital.ai Release.

### Start environment

Perequisites:

* Docker

To start the environment:

```commandline
cd dev-environment
docker compose up -d --build
```

Docker compose will do its magic and launch Release with an evaluation license. 

Note: if the license server can't be reached, you can store a license file in `dev-environment/digitalai-release/default-conf/xl-release-license.lic`.

To tear down the entire demo:

```commandline
docker compose down
```

## Scenario 1: Single-branch promotion with environment-specific connections

In this scenarios we have three environments: DEV, STAGING and PROD.

Each environment has its own instance of a Deploy server.
The environments use the same release template, that is shared through a git repository.

Promotion is done by saving a template in version control form one environment, and promoting it by pulling it from Git in the next.

### Configuration in Release

There are three folders, one for each environment. The folders contain the environment specific configuration: a connection to the Deploy server for that specific environment, and a folder variable with a common name that points to that variable.

Each folder contains a subfolder `Onboarding` with the release template that is pulled from the git repository. The Onboarding folders are initially empty, but are configured to point to the git repository.

This results in the following structure:

```
Acme DEV folder
    - Deploy Dev connection 
    - ${folder.deploy-server}
    - Onboarding folder
        - Git configuration
Acme Staging folder
    - Deploy Staging connection 
    - ${folder.deploy-server}
    - Onboarding folder
        - Git configuration
Acme PROD folder
    - Deploy Production connection 
    - ${folder.deploy-server}
    - Onboarding folder
        - Git configuration
```

See [acme-folders.yaml](dev-environment/digitalai-release-setup/acme-folders.yaml) for the as-code configuration of the folders.

This setup is automatically applied when launching the environment.

After the server has setup, log in as admin/admin and inspect the environment.

### Get the template from Git

Now go into Acme DEV / Onboarding folder and select **Version control**.

The folder has already been configured to point to this very repository, https://github.com/xebialabs-community/release-gitops-examples, and to pull its contents from the [Onboarding] folder on the `main` branch.

Select the version **Onboarding/1.0** and click **Apply this version**.

### Run the template in DEV

Go to templates, you will see that the **Onboard to Deploy** template has appeared. 
You can use the folder switcher (black triangle next to the folder name) to switch to another Onboarding folder, for example in Acme STAGING to see that the template is not there yet.

Create a new release from the template. Open the task **My first deployment**. You will see that it points to the `${folder.deploy-server}` for the Server value. Now run the template. There is a precondition on the task that will make it skip so we don't do an actual deployment.
When the task has completed, you will see that the Server value has changed to "Deploy Dev Server"

Do the same for another environment, for example Acme STAGING. You will see that the Server value is now "Deploy Staging Server".

### Make a change

Change the template in DEV, use Version control to save a new version and then pull it in STAGING.

Note: you will need to have push rights on the repo. If you fork the repo, make sure to update the FolderVersioningSettings configuration in
[acme-folders.yaml](dev-environment/digitalai-release-setup/acme-folders.yaml)

### Conclusion

You can use different environments with the same release template, and promote the template by saving it in version control. Environment-specifc value can be stored in folder variables.

## Scenario 2: Multi-branch promotion

TBD.

## Scenario 3: Promotion with an environment-specific Create Release Task 

TBD.