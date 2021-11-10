# Spring cloud contract - examples

Repository with some examples of Spring cloud contracts usage for consumer/producer, 
also simulating some complex architecture/errors that I've crossed.

# Scenarios

## Controllers divide by profile or packages

**Check sales-products module for this scenario.**

### Description

- We have an app that contains 2 profiles to determine which set of controller we want to expose or not:
  - **local**: All controllers that we don't want to expose outside out own vpn or, for example, outside an internal k8s network, so the
idea behind it is that only apps with inside the particular network will have access to this endpoint.
  - **external**: All controller that we want to expose it for other client outside our vpn. For example and external client can reach this API, but not a Local one.
- All controller are divide into packages to keep our code organized based on the profile: internal and external packages.
- All contracts stub are present into another GIT repo: https://github.com/viniciustoni/scc-contracts-files 
- Contracts are also divide into 2 "folders"(for scc will be like version), *internal-v1* and *external-v1*

### Issues

- Considering that I have 2 different packages with controllers and also on my stubs folder I'm dividing into 2 different folders to keep it organized,
how can I configure spring-cloud-contract-maven-plugin to make it work properly?
  - First try was to use **baseClassMappings**, but for this case I'll need to add a mapping per class, I could do that for the profile with less controllers, 
    but for new contract test that is under this class mapping I'll need to remember to add it, is there a possibility to add a  *packageWithBaseClasses* config here?
    So then I can create 2 folders under, for example, "v1" version and based on the class mapping I can set my base package. 
  - Creating 2 execution configuration for maven, one for internal and another for external with proper *packageWithBaseClasses*:
    - The problem here was that I still needed to have one "main" configuration outside executions section, or it'll give me an error;
    - During download process, it's working fine for the first execution, but for the second one, the plugin identify that we already have
    done one download for this repo it'll use the same contracts files, instead of get it again but from different "version"; 
      
For this case you can check *sales-products/pom.xml*, it contains both type of configuration:
- Divide by "folder" and execution profile
- Divide by **baseClassMappings**.
