External Signals
=======================

Project to test external signals with a broadcast scope.

Kie Server configuration
-------------------------

1. Enable `KIE.SERVER.SIGNAL.QUEUE` in `kie-server.war/META-INF/kie-server-jms.xml`
2. Enable ejb `org.jbpm.process.workitem.jms.JMSSignalReceiver` in `kie-server.war/WEB-INF/ejb-jar.xml`

Project Configuration
-------------------------

1. In the project add the following dependency:
   ```xml
    <dependency>
      <groupId>org.jbpm</groupId>
      <artifactId>jbpm-workitems-jms</artifactId>
      <version>7.23.0.Final-redhat-00003</version>
      <scope>provided</scope>
    </dependency>
   ```

2. In the `kie-deployment-descriptor` add the work item handler definition:

   ```xml
    <work-item-handler>
        <resolver>mvel</resolver>
        <identifier>new org.jbpm.process.workitem.jms.JMSSendTaskWorkItemHandler("java:/JmsXA", "java:jboss/exported/jms/queue/KIE.SERVER.SIGNAL")</identifier>
        <parameters/>
        <name>External Send Task</name>
    </work-item-handler>
   ```

Specifying the target
----------------------

In the process context, it's possible to narrow the signal scope leveraging the following process variables:

- `SignalDeploymentId`: this string accepts a regular expression to match an existing Deployment Unit Id (container id), in such way all deployment unit matching the regular expression will receive the signal.
- `SignalProcessInstanceId`: to target a specific process instance.
- `SignalWorkItemId`: to target an existing workitem instead of a signal.


Testing this sample
----------------------

1. Deploy this project twice, using the naming convention `<project-name>_<project-version>` e.g. `cross-signals_1.0.0` and `cross-signals_1.0.1`
2. Start the process `observer` in the deployment unit `cross-signals_1.0.0`
3. Start the process `sender` in  the deployment unit `cross-signals_1.0.1`
4. The `observer` process is reached by the signal because the `sender` process defines the `SignalDeploymentId` as `cross-signals_.*`.