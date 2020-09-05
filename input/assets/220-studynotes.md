---
Title: 'AZ-220: Study Notes'
---
> *Disclaimer: This is not my list entirely, I have taken references from others by searching on Internet*

## Implement the IoT Solution Infrastructure (15-20%)  
### Create and configure an IoT Hub
- Create an IoT Hub
- Register a device  
[Create IoT Hub via Portal](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-through-portal)  
[Create IoT Hub via PowerShell](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-using-powershell)  
[Create IoT Hub via CLI](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-using-cli)  
- Configure a device twin  
[Get Started with Device Twins (.Net)](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-twin-getstarted)  
- Configure IoT Hub tier and scaling  
[IoT Hub Scaling](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-scaling)  
 
### Build device messaging and communication
- Build messaging solutions by using SDKs (device and service)  
[Understand and use Azure IoT Hub SDKs](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-sdks)  
- Implement device-to-cloud communication  
[Message Routing - Device to Cloud messages](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-messages-d2c)  
[Device-to-cloud communications guidance](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-d2c-guidance)  
- Implement cloud-to-device communication  
[Send cloud-to-device messages from an IoT hub](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-messages-c2d)  
[Cloud-to-device communications guidance](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-c2d-guidance)  
[Send messages from the cloud to your device with IoT Hub (.NET)](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-c2d)  
- Configure file upload for devices  
[Upload files from your device to the cloud with IoT Hub (.NET)](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-file-upload)  
 
### Configure physical IoT devices
- Recommend an appropriate protocol based on device specifications  
[Choose a communication protocol](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-protocols)  
- Configure device networking, topology, and connectivity  
[IoT Hub support for virtual networks with Private Link and Managed Identity](https://docs.microsoft.com/en-us/azure/iot-hub/virtual-network-support)  
[IoT Hub IP addresses](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-understand-ip-address)  
## Provision and Manage Devices (20-25%)  
### Implement the Device Provisioning Service (DPS)  
- Create a Device Provisioning Service  
[Set up the IoT Hub Device Provisioning Service with the Azure portal](https://docs.microsoft.com/en-us/azure/iot-dps/quick-setup-auto-provision)  
[Set up the IoT Hub Device Provisioning Service with Azure CLI](https://docs.microsoft.com/en-us/azure/iot-dps/quick-setup-auto-provision-cli)  
- Link an IoT Hub to the DPS  
[Linked Hub](https://docs.microsoft.com/en-us/cli/azure/iot/dps/linked-hub)
- Create a new enrollment in DPS  
[Manage device enrollments with Azure Portal](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments)  
[Manage device enrollments with Azure Device Provisioning Service SDKs](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments-sdks)  
- Manage allocation policies by using Azure Functions  
[How to use custom allocation policies](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-use-custom-allocation-policies)  

### Manage the device lifecycle
- Provision a device by using DPS  
[Auto-provisioning concepts](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-auto-provisioning)  
[Set up a device to provision using the Azure IoT Hub Device Provisioning Service](https://docs.microsoft.com/en-us/azure/iot-dps/tutorial-set-up-device)  
- Deprovision an autoenrollment  
[How to deprovision devices that were previously auto-provisioned](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-unprovision-devices)  
- Decommission (disenroll) a device  
[How to disenroll a device from Azure IoT Hub Device Provisioning Service](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-revoke-device-access-portal)  

### Manage IoT devices by using IoT Hub
- Manage devices list in the IoT Hub device registry  
[Understand the identity registry in your IoT hub](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry)  
- Modify device twin tags and properties  
[Understand and use device twins in IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins)  
- Trigger an action on a set of devices by using IoT Hub Jobs and Direct Methods  
[Schedule jobs on multiple devices](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-jobs)  
- Set up Automatic Device Management of IoT devices at scale  
[Automatic IoT device and module management using the Azure portal](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-automatic-device-management)  

### Build a solution by using IoT Central
- Define a device type in Azure IoT Central  
[Define a new IoT device type in your Azure IoT Central application](https://docs.microsoft.com/en-us/azure/iot-central/core/howto-set-up-template)  
- Configure rules and actions in Azure IoT Central  
[Configure rules and actions for your device in Azure IoT Central](https://docs.microsoft.com/en-us/azure/iot-central/core/quick-configure-rules)  
- Define the operator view  
[Customize the operator dashboard and manage devices in Azure IoT Central](https://docs.microsoft.com/en-us/azure/iot-central/retail/tutorial-in-store-analytics-customize-dashboard)  
- Add and manage devices from IoT Central  
[Manage devices in your Azure IoT Central application](https://docs.microsoft.com/en-us/azure/iot-central/core/howto-manage-devices)  
- Monitor devices  
[Use Azure IoT Central to monitor your devices](https://docs.microsoft.com/en-us/azure/iot-central/core/quick-monitor-devices)  
- Custom and industry-focused application templates  
[What are application templates?](https://docs.microsoft.com/en-us/azure/iot-central/core/concepts-app-templates)  

## Implement Edge (15-20%)
### Set up and deploy an IoT Edge device
- Create a device identity in IoT Hub  
[Register an Azure IoT Edge device](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device)  
- Deploy a single IoT device to IoT Edge  
[Deploy Azure IoT Edge modules from the Azure portal](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-deploy-modules-portal)  
[Deploy Azure IoT Edge modules with Azure CLI](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-deploy-modules-cli)  
[Deploy Azure IoT Edge modules from Visual Studio Code](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-deploy-modules-vscode)  
[Deploy your first IoT Edge module to a virtual Windows device](https://docs.microsoft.com/en-us/azure/iot-edge/quickstart)  
- Create a deployment for IoT Edge devices  
[Deploy IoT Edge modules at scale using the Azure portal](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-deploy-at-scale)  
- Install container runtime on IoT devices  
[Install the Azure IoT Edge runtime on Debian-based Linux systems](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-install-iot-edge-linux)  
[Create and provision an IoT Edge device with a TPM on Linux](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-auto-provision-simulated-device-linux)  
[Create and provision an IoT Edge device using symmetric key attestation](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-auto-provision-symmetric-keys)  
- Define and implement deployment manifest  
[Learn how to deploy modules and establish routes in IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/module-composition)  
- Update security daemon and runtime  
[Update the IoT Edge security daemon and runtime](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-update-iot-edge)  
- Provision IoT Edge devices with DPS  
[IoT Edge DPS demo](https://azure.microsoft.com/en-us/resources/videos/iot-edge-dps-demo/)  
- IoT Edge automatic deployments  
[Deploy IoT Edge modules at scale using the Azure portal](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-deploy-at-scale)  
- Deploy on constrained devices  
[Reduce memory space used by IoT Edge hub](https://docs.microsoft.com/en-us/azure/iot-edge/production-checklist#dont-optimize-for-performance-on-constrained-devices) 
- Secure IoT Edge solutions  
[Security standards for Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/security)  
[Azure IoT Edge security manager](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-security-manager)  
- Deploy production certificates  
[Understand how Azure IoT Edge uses certificates](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-certs)  
[Install production certificates](https://docs.microsoft.com/en-us/azure/iot-edge/production-checklist#install-production-certificates)  

### Develop modules
- Create and configure an Edge module  
[Develop your own IoT Edge modules](https://docs.microsoft.com/en-us/azure/iot-edge/module-development)
[Learn how to deploy modules and establish routes in IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/module-composition)    
- Deploy a module to an Edge device  
[Deploy IoT Edge module via Portal](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-deploy-modules-portal)
[Deploy IoT Edge module via CLI](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-deploy-modules-cli)
[Deploy IoT Edge module via VS Code](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-deploy-modules-vscode)
- Publish an IoT Edge module to an Azure Container Registry  
[Develop IoT Edge modules for Windows devices](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-develop-for-windows)
[Develop IoT Edge modules for Linux devices](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-develop-for-linux)



### Configure an IoT Edge device
- Select and deploy an appropriate gateway pattern  
[How an IoT Edge device can be used as a gateway](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-as-gateway)  
[Configure an IoT Edge device to act as a transparent gateway](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-create-transparent-gateway)  
[Authenticate a downstream device to Azure IoT Hub](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-authenticate-downstream-device)  
[Connect a downstream device to an Azure IoT Edge gateway](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-connect-downstream-device)  
- Implement module-to-module communication  
[Module communication](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-runtime#module-communication)  
- Implement and configure offline support (including local storage)  
[Understand extended offline capabilities for IoT Edge devices, modules, and child devices](https://docs.microsoft.com/en-us/azure/iot-edge/offline-capabilities)  
[Give modules access to a device's local storage](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-access-host-storage-from-module)  

## Process and manage data (15-20%)
### Configure routing in Azure IoT Hub
- Implement message enrichment in IoT Hub  
[Use Azure IoT Hub message enrichments](https://docs.microsoft.com/en-us/azure/iot-hub/tutorial-message-enrichments)  
- Configure routing of IoT Device messages to endpoints  
[Use the Azure CLI and Azure portal to configure IoT Hub message routing](https://docs.microsoft.com/en-us/azure/iot-hub/tutorial-routing)  
[Part 2 - View the routed messages](https://docs.microsoft.com/en-us/azure/iot-hub/tutorial-routing-view-message-routing-results)  
- Define and test routing queries  
[IoT Hub message routing query syntax](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-routing-query-syntax)  
- Integrate with Event Grid  
[React to IoT Hub events by using Event Grid to trigger actions](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-event-grid)  

### Configure stream processing
- Create ASA for data and stream processing of IoT data  
[Process real-time IoT data streams with Azure Stream Analytics](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices)  
- Process and filter IoT data by using Azure Functions  
[Processing data from IoT Hub with Azure Functions](https://docs.microsoft.com/en-us/samples/azure-samples/functions-js-iot-hub-processing/processing-data-from-iot-hub-with-azure-functions/)  
[Azure IoT Hub output binding for Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-event-iot-output)  
[Azure IoT Hub bindings for Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-event-iot)  
- Configure Stream Analytics outputs  
[Outputs from Azure Stream Analytics](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-define-outputs)  
[Input and output](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-edge#input-and-output-streams)  

### Configure an IoT solution for Time Series Insights (TSI)
- Implement solutions to handle telemetry and time-stamped data  
[Time series solutions](https://docs.microsoft.com/en-us/azure/architecture/data-guide/scenarios/time-series)  
- Create an Azure Time Series Insights (TSI) environment  
[Create an Azure Time Series Insights Gen1 environment](https://docs.microsoft.com/en-us/azure/time-series-insights/tutorial-create-populate-tsi-environment)  
- Connect the IoT Hub and the Time Series Insights (TSI)  
[Add an IoT hub event source to your Azure Time Series Insight environment](https://docs.microsoft.com/en-us/azure/time-series-insights/how-to-ingest-data-iot-hub)  

## Monitor, troubleshoot, and optimize IoT solutions (15-20%)
### Configure health monitoring
- Configure metrics in IoT Hub  
[Understand IoT Hub metrics](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-metrics)  
- Set up diagnostics logs for Azure IoT Hub  
[Set up and use metrics and diagnostic logs with an IoT hub](https://docs.microsoft.com/en-us/azure/iot-hub/tutorial-use-metrics-and-diags)  
- Query and visualize tracing by using Azure Monitor  
[Query and visualize](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-distributed-tracing#query-and-visualize)  

### Troubleshoot device communication
- Establish maintenance communication  
[Use a simulated device to test connectivity with your IoT hub](https://docs.microsoft.com/en-us/azure/iot-hub/tutorial-connectivity) (might not be the best fit)  
- Verify device telemetry is received by IoT Hub  
[Send telemetry from a device to an IoT hub and read it with a back-end application (.NET)](https://docs.microsoft.com/en-us/azure/iot-hub/quickstart-send-telemetry-dotnet)  
- Validate device twin properties, tags and direct methods  
[Understand and use device twins in IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins)  
[Understand and invoke direct methods from IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-direct-methods)  
- Troubleshoot device disconnects and connects  
[Monitor, diagnose, and troubleshoot disconnects with Azure IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-troubleshoot-connectivity)  

### Perform end-to-end solution testing and diagnostics
- Estimate the capacity required for each service in the solution  
[IoT Hub Scaling](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-scaling)  
- Conduct performance and stress testing  
[Accelerating IoT solution ](https://azure.microsoft.com/en-us/blog/accelerating-iot-solution-development-and-testing-with-azure-iot-device-simulation/?WT.mc_id=thomasmaurer-blog-thmaure)  
[Azure IoT Hub Stress Test](https://github.com/IoTChinaTeam/Azure-IoTHub-StressTest)

## Implement security (15-20%)
### Implement device authentication in the IoT Hub
- Choose an appropriate form of authentication  
[IoT device authentication options](https://azure.microsoft.com/en-in/blog/iot-device-authentication-options/)  
- Manage the X.509 certificates for a device  
[Device Authentication using X.509 CA Certificates](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-x509ca-overview)  
- Manage the symmetric keys for a device  
[Provision a simulated device with symmetric keys](https://docs.microsoft.com/en-us/azure/iot-dps/quick-create-simulated-device-symm-key)  

### Implement device security by using DPS
- Configure different attestation mechanisms with DPS  
[Attestation mechanisms with DPS](https://docs.microsoft.com/en-us/azure/iot-dps/use-hsm-with-sdk)  
- Generate and manage x.509 certificates for IoT Devices  
[Device Authentication using X.509 CA Certificates](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-x509ca-overview)  
- Configure enrollment with x.509 certificates  
[Enroll X.509 devices to the Device Provisioning Service using C#](https://docs.microsoft.com/en-us/azure/iot-dps/quick-enroll-device-x509-csharp)  
- Generate a TPM endorsements key for a device  
[TPM attestation](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-tpm-attestation)  
- Configure enrollment with symmetric keys  
[How to provision legacy devices using symmetric keys](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-legacy-device-symm-key)  

### Implement Azure Security Center (ASC) for IoT
- Enable ASC for IoT in Azure IoT Hub  
[Onboard Azure Security Center for IoT service in IoT Hub](https://docs.microsoft.com/en-us/azure/asc-for-iot/quickstart-onboard-iot-hub)  
- Create security modules  
[Create an azureiotsecurity module twin](https://docs.microsoft.com/en-us/azure/asc-for-iot/quickstart-create-security-twin)  
- Configure custom alerts  
[Create custom alerts](https://docs.microsoft.com/en-us/azure/asc-for-iot/quickstart-create-custom-alerts)  