ca-siteminder-monitoring-extension
==================================

An AppDynamics extension to be used with a stand alone Java machine agent to provide metrics for CA's Siteminder.
I added an option to support V3, but needs to be tested


## Use Case ##

CA Site Minder (a.k.a. Netegrity Site Minder SSO) is a web access management system that enables user authentication and secure Internet SSO (single sign-on),
policy-driven authorization, federation of identities, and complete auditing of all access to the web applications it protects.

## Prerequisites ##

1. This extension extracts the metrics from CA SiteMinder through SNMP. For the extension to work, the SNMP subagent in SiteMinder needs to be started.
This snmp subagent module is found in the `<SiteMinder_Installation_Dir>\bin\snmprun.bat` on Windows. A similar location for UNIX can be easily located.
The port on which the SNMP subagent is listening can be found from the `<SiteMinder_Installation_Dir>\config\snmp.conf` directory.

2. The extension uses UDP to connect to SiteMinder. Please make sure that the necessary port is open for UDP traffic.

3. The extension is pre-configured with metrics and their associated OIDs. In case if the OIDs need to be validated, the MIB file can be found
in the `<SiteMinder_Installation_Dir>\MIB` directory.


## Troubleshooting steps ##
1. Make sure to enable UDP traffic if not enabled by default.

2. Once the SNMP subagent module is started, the value for the respective OIDs can be viewed in a tool provided by CA. This tool can be found in
the `<SiteMinder_Installation_Dir>\bin\snmpwalkrun.bat` directory.

## Metrics Provided ##

This extension extracts metrics for the policyServer and webagents components.


## Installation ##

1. Download the SiteMinderMonitor.zip from [AppDynamics Exchange][http://community.appdynamics.com/t5/eXchange-Community-AppDynamics/idb-p/extensions].
You can build it from the source as well by running `mvn clean install`.
2. Unzip as "SiteMinderMonitor" and copy the "SiteMinderMonitor" directory to `<MACHINE_AGENT_HOME>/monitors`


# Configuration ##

Note : Please make sure to not use tab (\t) while editing yaml files. You may want to validate the yaml file using a [yaml validator](http://yamllint.com/)

1. Configure the siteminder instances by editing the config.yml file in `<MACHINE_AGENT_HOME>/monitors/SiteMinderMonitor/`.
2. Below is the default config.yml which has metrics configured already
   For eg. 
   
   ```
     #This will create this metric in all the tiers, under this path
     #metricPrefix: Custom Metrics|SiteMinder

     #This will create it in specific Tier/Component. Make sure to replace <COMPONENT_ID> with the appropriate one
     #from your environment. To find the <COMPONENT_ID> in your environment, please follow the screenshot in
     #https://docs.appdynamics.com/display/PRO42/Build+a+Monitoring+Extension+Using+Java
     metricPrefix: Server|Component:<COMPONENT_ID>|Custom Metrics|SiteMinder


     instances:
       - host: "sample.host.com"
         port: 161
         communityString: public
         snmpVersion: 2 # Only required for snmp v1, will default to 2. Support only v1 and v2.
         displayName: "SiteMinder Manager"  #Required field for display purposes
         #timeout: 1 # second, by default
         #retries: 5
         #maxRepetitions: 10

         #resources: creates a resource mapping that can be used to create readable metric paths.
         #You may want to see machine names and not ids in the metric path.
         #You can choose to remove the resources section completely and it will show you machine ids in the metric path.
         resources:
             #maps policyServerIndex to policyServerHostID
           - component: "policyServer"
             include:
               policyServerHostID: "1.3.6.1.4.1.2552.200.300.1.1.1.3"
             #maps webAgentIndex to webAgentHostID
           - component: "webagents"
             include:
               webAgentHostID: "1.3.6.1.4.1.2552.200.300.2.1.1.3"


        #You can configure multiple components like policyServer,webagents. The component names should match with
        #the ones in the resources section
         metrics:
           - component: "policyServer"   #component name
             include:
               - policyServerMaxSockets: "1.3.6.1.4.1.2552.200.300.1.3.1.17"
               - policyServerSocketCount: "1.3.6.1.4.1.2552.200.300.1.3.1.18"
               - policyServerAuthAcceptCount: "1.3.6.1.4.1.2552.200.300.1.3.1.19"
               - policyServerAuthRejectCount: "1.3.6.1.4.1.2552.200.300.1.3.1.20"
               - policyServerAzAcceptCount: "1.3.6.1.4.1.2552.200.300.1.3.1.21"
               - policyServerAzRejectCount: "1.3.6.1.4.1.2552.200.300.1.3.1.22"
               - policyServerStatus: "1.3.6.1.4.1.2552.200.300.1.3.1.5"
                 convert: {
                   "Active" : "1",
                   "Inactive" : "2",
                   "$default" : "0"
                 }

           - component: "webagents"
             include:
               - webAgentSocketCount: "1.3.6.1.4.1.2552.200.300.2.1.1.17"
               - webAgentResourceCacheCount: "1.3.6.1.4.1.2552.200.300.2.1.1.18"
               - webAgentResourceCacheHits: "1.3.6.1.4.1.2552.200.300.2.1.1.19"
               - webAgentResourceCacheMax: "1.3.6.1.4.1.2552.200.300.2.1.1.20"
               - webAgentResourceCacheMisses: "1.3.6.1.4.1.2552.200.300.2.1.1.21"
               - webAgentUserSessionCacheCount: "1.3.6.1.4.1.2552.200.300.2.1.1.22"
               - webAgentUserSessionCacheHits: "1.3.6.1.4.1.2552.200.300.2.1.1.23"
               - webAgentUserSessionCacheMax: "1.3.6.1.4.1.2552.200.300.2.1.1.24"
               - webAgentUserSessionCacheMisses: "1.3.6.1.4.1.2552.200.300.2.1.1.25"
               - webAgentIsProtectedCount: "1.3.6.1.4.1.2552.200.300.2.1.1.26"
               - webAgentIsProtectedErrors: "1.3.6.1.4.1.2552.200.300.2.1.1.27"
               - webAgentIsProtectedAvgTime: "1.3.6.1.4.1.2552.200.300.2.1.1.28"
               - webAgentLoginCount: "1.3.6.1.4.1.2552.200.300.2.1.1.29"
               - webAgentLoginErrors: "1.3.6.1.4.1.2552.200.300.2.1.1.30"
               - webAgentLoginFailures: "1.3.6.1.4.1.2552.200.300.2.1.1.31"
               - webAgentLoginAvgTime: "1.3.6.1.4.1.2552.200.300.2.1.1.32"
               - webAgentValidationCount: "1.3.6.1.4.1.2552.200.300.2.1.1.33"
               - webAgentValidationErrors: "1.3.6.1.4.1.2552.200.300.2.1.1.34"
               - webAgentValidationFailures: "1.3.6.1.4.1.2552.200.300.2.1.1.35"
               - webAgentValidationAvgTime: "1.3.6.1.4.1.2552.200.300.2.1.1.36"
               - webAgentAuthorizeCount: "1.3.6.1.4.1.2552.200.300.2.1.1.37"
               - webAgentAuthorizeErrors: "1.3.6.1.4.1.2552.200.300.2.1.1.38"
               - webAgentAuthorizeFailures: "1.3.6.1.4.1.2552.200.300.2.1.1.39"
               - webAgentAuthorizeAvgTime: "1.3.6.1.4.1.2552.200.300.2.1.1.40"
               - webAgentCrosssiteScriptHits: "1.3.6.1.4.1.2552.200.300.2.1.1.41"
               - webAgentBadURLcharsHits: "1.3.6.1.4.1.2552.200.300.2.1.1.42"
               - webAgentBadCookieHitsCount: "1.3.6.1.4.1.2552.200.300.2.1.1.43"
               - webAgentExpiredCookieHitsCount: "1.3.6.1.4.1.2552.200.300.2.1.1.44"
               - webAgentStatus: "1.3.6.1.4.1.2552.200.300.2.1.1.5"
                 convert: {
                   "Active" : "1",
                   "Inactive" : "2",
                   "$default" : "0"
                 }



     # number of concurrent tasks.
     # This doesn't need to be changed unless many instances are configured
     numberOfThreads: 10

   ```

Please make sure to replace the COMPONENT_ID with the correct id in your environment. Please read the comment in the config.yml for
more details.

3. Configure the path to the config.yml file by editing the <task-arguments> in the monitor.xml file in the `<MACHINE_AGENT_HOME>/monitors/SiteMinderMonitor/` directory. Below is the sample

     ```
     <task-arguments>
         <!-- config file-->
         <argument name="config-file" is-required="true" default-value="monitors/SiteMinderMonitor/config.yml" />
          ....
     </task-arguments>
    ```
Make sure to use the right path for Windows.

## Contributing ##

Always feel free to fork and contribute any changes directly via [GitHub][].

## Community ##

Find out more in the [AppDynamics Exchange][].

## Support ##

For any questions or feature request, please contact [AppDynamics Center of Excellence][].

**Version:** 1.0.0
**Controller Compatibility:** 4.0+


[Github]: https://github.com/Appdynamics/siteminder-monitoring-extension
[AppDynamics Exchange]: http://community.appdynamics.com/t5/AppDynamics-eXchange/idb-p/extensions
[AppDynamics Center of Excellence]: mailto:help@appdynamics.com
