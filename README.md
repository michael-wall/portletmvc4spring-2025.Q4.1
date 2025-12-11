## Introduction ##
- This repository contains the source code for a sample Jakarta enabled PortletMVC4Spring that can be run in **Liferay DXP 2025.Q4.1**
- The **Steps Followed to Setup the Module** section contains the steps taken to generate and update the blade tool generated widget to work.
- The previous issue with the functions tag lib is resolved (https://liferay.atlassian.net/browse/LPD-64760) but it is necessary to replace references to xmlns:fn="http://java.sun.com/jsp/jstl/functions" with xmlns:fn="jakarta.tags.functions" for the functions tag lib to work.

## Prerequisites ##
- JDK 21
- Blade version 7.0.5 or later. Use blade update to get the latest version.
  - If using Windows and an error occurs trying to run 'blade update' then try installing the latest Liferay Dev Studio (currently 3.10.4) which should include the latest Blade version.
- Liferay DXP 2025.Q4.1

## Steps Followed to Setup the Module ##
- Create a new Liferay Workspace using dxp-2025.q4.1
```
blade init -v dxp-2025.q4.1 workspace
```
- Create a new module (from within the workspace folder) using --framework portletmvc4spring
```
blade create -t spring-mvc-portlet --framework portletmvc4spring --view-type jsp -v dxp-2025.q4.1 -p com.mw.springmvc -c TestSetupPortlet com-mw-springmvc
```
-  In module .jsp and .jspx files, replace ALL references to:
```
xmlns:fn="http://java.sun.com/jsp/jstl/functions"
```
with:
```
xmlns:fn="jakarta.tags.functions"
```
- Update build.gradle file:
  - Change the springVersion and springSecurityVersion versions to be:
```
String springVersion = "6.2.10"
String springSecurityVersion = "6.5.3"
```
  - Change hibernate validator by replacing:
```
implementation(group: "com.liferay", name: "org.hibernate.validator", version: "6.2.5.Final.JAKARTA-LIFERAY-PATCHED-1") {
```
with:
```
implementation(group: "org.hibernate.validator", name: "hibernate-validator", version: "8.0.2.Final") {
```
  - Update com.liferay.portletmvc4spring versions by replacing:
```
implementation group: "com.liferay.portletmvc4spring", name: "com.liferay.portletmvc4spring.framework", version: "5.3.2"
implementation group: "com.liferay.portletmvc4spring", name: "com.liferay.portletmvc4spring.security", version: "5.3.2"
```
with:
```
implementation group: "com.liferay.portletmvc4spring", name: "com.liferay.portletmvc4spring.framework", version: "6.0.0-M1"
implementation group: "com.liferay.portletmvc4spring", name: "com.liferay.portletmvc4spring.security", version: "6.0.0-M1"
```
  - Some of the Jakarta gradle dependencies are not yet available in Maven Central, meaning the following must be temporarily included:
    - Update buildscript > repositories section to include the following:
```
maven {
  name = "liferay-third-party"
  url = uri("https://repository.liferay.com/nexus/content/repositories/third-party")
}
mavenCentral()
```
  - Add the following repositories component before the dependencies component:
```
repositories {
    maven {
        url "https://repository-cdn.liferay.com/nexus/content/groups/public"
    }
    maven {
        name = "liferay-third-party"
        url = uri("https://repository.liferay.com/nexus/content/repositories/third-party")
    }
    mavenCentral()
}
```
- Build the module (from within workspace/modules/com-mw-springmvc)
```
..\..\gradlew clean build
```
- Deploy the module to a running Liferay DXP 2025.Q4.1 and check the Liferay logs to ensure the module deployed and started as expected.
- Add the Sample > com-mw-springmvc widget to a page and confirm it works as expected.
