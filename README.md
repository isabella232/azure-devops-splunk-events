# azure-devops-splunk-observability-cloud-events
Add events to Splunk Observability Cloud from Azure DevOps Deploy and Release Pipelines.

## Setup your Service Connection
After installing the Splunk Observability Cloud Events extension you will need to setup 
your Service Endpoint 
1. Click Project Settings from the bottom left side of your screen in the main Azure DevOps Project area
2. Click Service Connections from the Pipelines area of Project Settings
3. Click New Service Connection in the top right
4. Choose the `Splunk Observability Events API` as your new service connection
5. Enter your Splunk Observability Ingest API URL *including trailing `/`* (E.G. `https://ingest.us1.signalfx.com/v2/`)
6. Enter your Splunk Observability Cloud Ingest API token which you obtained from Splunk Observability Cloud > Access Tokens. Make sure the token is scoped for API and Ingest ![Example](./images/access-token-box.png)

## Setup your Build Pipeline Job to emit an event
Once your Service Connection is setup you can start setting up your Pipeline to emit events when it is run.
1. Edit your Pipeline to view its YAML configuration
2. If you are not already using a Multi-Job Pipeline configure the appropriate `jobs:` and `-job:` tags and formatting. See the [Azure DevOps Docs](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml#multi-job-configuration) for details.
3. Add the configuration for your Splunk Event. You can either search for `Splunk` in the Tasks sidebar or add the appropriate YAML manually
    ```
      - job: notify_splunk
        pool: server

        steps:
        - task: Splunk@0
          inputs:
            SplunkService: 'Splunk'
            environment: 'production'
            eventType: 'Azure DevOps deployment of test-the-tools'
    ```
    **NOTE:** *THIS MUST BE ITS OWN JOB WITH `pool: server`* otherwise you will get a message indicating that the task is looking for the wrong integration.
    - The `eventType` will be the title of your event in Splunk Observability Cloud and will be used to match the event later for visualizing as an [overlay on Splunk Observability charts/dashboards](https://docs.signalfx.com/en/latest/dashboards/dashboard-add-info.html#overlaying-event-markers-on-charts-in-a-dashboard).
4. Save your pipeline

## Setup your Release Pipeline to emit an event
1. Edit your Release Pipeline by clicking Releases under the Pipeline heading in the left sidebar
2. Select your Release Pipeline and click the Edit button in the upper right
3. Click either Pre-Deployment or Post-Deployment conditions 
    ![deployment-conditions](./images/deployment-conditions.png)
4. Enable Gates and click Add to add your Splunk Events configuration
    ![release-gate-config](./images/release-gate-settings.png)
    - You can then enter a Display Name for your Event gate, select the Service Connection you'd like to use, and assign your `environment` and `eventType` as described above for [event overlays on your Splunk Observability dashboard charts](https://docs.signalfx.com/en/latest/dashboards/dashboard-add-info.html#overlaying-event-markers-on-charts-in-a-dashboard).

## License

Copyright 2021 Splunk Inc.
 
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at
 
http://www.apache.org/licenses/LICENSE-2.0
 
Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.