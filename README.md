# Docker JBoss BPMS

Before start
------------
Download files below and put in Dockerfile directory.

* jboss-eap-7.0.0.zip
* jboss-eap-7.0.9-patch.zip
* jboss-bpmsuite-6.4.0.GA-deployable-eap7.x.zip
* jboss-bpmsuite-6.4.11-patch.zip

Start
-----
Open terminal in docker-compose.yml directory and run command below.

```bash
$ docker-compose up --build
```

JBoss BPMS
----------
Browse to http://localhost:8080/business-central[http://localhost:8080/business-central]

User: bpmsAdmin
Pass: redhat99

JBoss EAP
---------
Browse to http://localhost:9990/console[http://localhost:9990/console]

User: jbossAdmin
Pass: redhat99

Create Pojo and Rule to Test.
-----------
The steps below create a simple Pojo and Rule, the simple pojo as named ExamplePojo with String fields 'exampleField' and 'status', and a Rule as named exampleRule.

```bash
$ git clone ssh://jbossAdmin@localhost:8001/repository1
$ cd repository1
$ tee project1/src/main/java/org/kie/example/project1/ExamplePojo.java << END
package org.kie.example.project1;
public class ExamplePojo implements java.io.Serializable {
   static final long serialVersionUID = 1L;
   @org.kie.api.definition.type.Description("Example Field.")
   @org.kie.api.definition.type.Label("Example Field")
   private java.lang.String exampleField;
   @org.kie.api.definition.type.Description(value = "Status.")
   @org.kie.api.definition.type.Label(value = "Status")
   private java.lang.String status;
   public java.lang.String getExampleField() {
      return this.exampleField;
   }
   public void setExampleField(java.lang.String exampleField) {
      this.exampleField = exampleField;
   }
   public java.lang.String getStatus() {
      return this.status;
   }
   public void setStatus(java.lang.String status) {
      this.status = status;
   }
   public ExamplePojo(java.lang.String exampleField, java.lang.String status) {
      this.exampleField = exampleField;
      this.status = status;
   }
}
END
$ tee project1/src/main/resources/org/kie/example/project1/exampleRule.drl << END
package org.kie.example.project1;

rule "exampleRuleOk"
dialect "mvel"
when
    examplePojo : ExamplePojo(exampleField == "test")
then
    System.out.println("Rule fired :: ".concat(this.getRule().getName()));
    examplePojo.setStatus("OK");
end

rule "exampleRuleNok"
dialect "mvel"
when
    examplePojo : ExamplePojo(exampleField != "test")
then
    System.out.println("Rule fired :: ".concat(this.getRule().getName()));
    examplePojo.setStatus("NOK");
end
END
```

Build project and Run Execution Server
--------------------------------------
Browse to http://localhost:8080/business-central[http://localhost:8080/business-central], build and deploy the project

Test with JMeter
----------------
Execute a test scenario described in kie-server-test-scenario.jmx.
