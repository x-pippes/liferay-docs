# Running Service Builder and Understanding the Generated Code [](id=running-service-builder-and-understanding-the-generated-code)

This tutorial explains how to run Service Builder and provides an overview of
the code that Service Builder generates. If you'd like to use Service Builder in
your application but haven't yet created a `service.xml` file, visit the
[Defining an Object-Relational Map with Service Builder](/develop/tutorials/-/knowledge_base/7-0/defining-an-object-relational-map-with-service-builder)
tutorial and then come back to this one.

## Running Service Builder [](id=running-service-builder)

To build a service from a `service.xml` file, you can use *Liferay IDE*,
*Liferay Developer Studio*, or use a terminal window. In this tutorial, you'll
refer to the Event Listing example project that's referenced throughout the
Liferay Service Builder tutorials. 

Now let's learn how to run Service Builder.

### Using Liferay IDE or Developer Studio [](id=using-liferay-ide-or-developer-studio)

From the Package Explorer, right click your parent project's folder and select
*Liferay* &rarr; *Gradle* &rarr; *build-service*. As you can see, the IDE
support Gradle by default. You can, however, use any build tool you'd like for
building your services.

<!--
From the Package Explorer, open the
`service.xml` file from your `*-service` module's root folder. By
default, the file opens up in the Service Builder Editor. Make sure you are in
Overview mode. Then click the *Build Services* button near the top-right corner
of the view. The Build Services button has an image of a document with the
numerical sequence *010* in front of it. 

Make sure to click the *Build Services* button and not the *Build WSDD* button
that appears next to it. Building the WSDDs won't hurt anything, but you'll
generate files for the remote service instead of the local one. For information
about WSDDs (web service deployment descriptors), please refer to the
[SOAP Web Services](/develop/tutorials/-/knowledge_base/7-0/soap-web-services)
tutorial. 

[Figure 1: The *Overview* mode in the editor provides a nested outline which you can expand, a form for editing basic Service Builder attributes, and buttons for building services or building web service deployment descriptors.](../../images/service-xml-overview.png)

Another simple way to run Service Builder is to right-click on your project's
name in the Package Explorer and then to select *Liferay* &rarr; *Build
Services* (or, equivalently, *Liferay* &rarr; *SDK* &rarr; *build-service*).
-->

<!-- The text above does not currently work because Service Builder feature is
not fully functional in IDE 3.0. IDE 3.1 is planned to have this functionality
available. -Cody -->

After running Service Builder, your generated files are available. More
information about the generated files appears below.

### Using the terminal [](id=using-the-terminal)

Open a terminal window and navigate to your module project's root folder, which
should be located in your Liferay Workspace's `modules` directory. To learn more
about creating your module project in a Liferay Workspace, visit the
[Creating a Liferay Workspace with Blade CLI](/develop/tutorials/-/knowledge_base/7-0/creating-a-liferay-workspace-with-blade-cli)
tutorial. Liferay Workspaces use Gradle as their build tool, so this will be the
assumed build language used in this tutorial. Liferay is tool agnostic, however,
and you can use other tools, as well.

Enter the following command in your module project's root folder to build your
services:

    gradlew buildService

+$$$

**Note:** Liferay Workspaces provide the Gradle Wrapper script for usage, if you
don't have Gradle installed globally in your classpath. It is located in the
workspace's root folder, so you can call it from your module project's root
folder, if necessary (e.g., `../../gradlew buildService`).

$$$

If your module project uses Maven, you can build services running the following
command from the module project's root folder:

    mvn service-builder:build

**Important:** The `mvn service-builder:build` command only works if you're
using the `com.liferay.portal.tools.service.builder` plugin version 1.0.145+.
Maven projects using an earlier version of the Service Builder plugin should
update their POM accordingly. See the
[Service Builder Template](/develop/reference/-/knowledge_base/7-0/using-the-service-builder-template)
and
[Using Service Builder in a Maven Project](/develop/tutorials/-/knowledge_base/7-0/using-service-builder-in-a-maven-project)
tutorials for more information on using Maven to run Service Builder.

When the service has been successfully generated, a `BUILD SUCCESSFUL` message
appears in your terminal window. You should also see that a large number of
files have been generated in your project. These files include a model layer,
service layer, and persistence layer. Don't worry about the number of generated
files--you'll never have to customize more than three of them. To review the
code that Service Builder generates for your entities, see the next section.

## Understanding the Code Generated by Service Builder [](id=understanding-the-code-generated-by-service-builder)

Now you'll examine the files Service Builder generated for your entity. Note
that the files listed under Local Service and Remote Service below are only
generated for an entity that has both `local-service` and `remote-service`
attributes set to `true`. Service Builder generates services for these entities
in two locations in your project. These locations use the package path that you
specified in your `service.xml` file. For Liferay's Bookmarks application, for
example, these two locations are the following ones:

- `/bookmarks-api/src/main/java/com/liferay/bookmarks`
- `/bookmarks-service/src/main/java/com/liferay/bookmarks`

The `bookmarks-api` module contains the API for the Bookmarks project. All the
classes and interfaces in the `*-api` module are packaged in a `.jar` file
called `PROJECT_NAME-api.jar` in the module's `build/libs` folder. This `.jar`
file is generated whenever you compile and deploy your module. When deploying
this JAR to Liferay, the necessary interfaces to *define* the service API are
available.

The `bookmarks-service` module contains the implementation of the interfaces
defined in the `bookmarks-api` module. These interfaces provide OSGi services
for the portal instance to which your application is deployed. Service Builder
generates classes and interfaces belonging to the persistence layer, service
layer, and model layer in the
`/bookmarks-api/src/main/java/com/liferay/bookmarks` and
`/bookmarks-service/src/main/java/com/liferay/bookmarks` packages.

Now you'll look at the classes and interfaces generated for the entities you
specified. Each entity has similar classes generated for it, depending on what
you specfied for them in the `service.xml`. You won't have to customize more
than three classes for each entity. These customizable classes are
`*LocalServiceImpl`, `*ServiceImpl`, and `*ModelImpl`.

- Persistence
    - `[ENTITY_NAME]Persistence`: Persistence interface that defines CRUD
      methods for the entity such as `create`, `remove`, `countAll`, `find`,
      `findAll`, etc. 
    - `[ENTITY_NAME]PersistenceImpl`: Persistence implementation class that
      implements `[ENTITY_NAME]Persistence`. 
    - `[ENTITY_NAME]Util`: Persistence utility class that wraps
      `[ENTITY_NAME]PersistenceImpl` and provides direct access to the database
      for CRUD operations. This utility should only be used by the service
      layer; in your portlet classes, use `[ENTITY_NAME]LocalServiceUtil` or
      `[ENTITY_NAME]ServiceUtil` instead. 

    ![Figure 1: Service Builder generates these persistence classes and interfaces. You shouldn't (and you won't need to) customize any of these classes or interfaces.](../../../images/service-builder-persistence-diagram.png)

- Local Service (generated for an entity only if an entity's `local-service`
  attribute is set to `true` in `service.xml`)
    - `[ENTITY_NAME]LocalService`: Local service interface. 
    - `[ENTITY_NAME]LocalServiceImpl` (**LOCAL SERVICE IMPLEMENTATION**): Local
      service implementation. This is the only class in the local service that
      you should modify manually. You can add custom business logic here. For
      any custom methods added here, Service Builder adds corresponding methods
      to the `[ENTITY_NAME]LocalService` interface the next time you run it.
    - `[ENTITY_NAME]LocalServiceBaseImpl`: Local service base implementation.
      This is an abstract class. Service Builder injects a number of instances
      of various service and persistence classes into this class. `@abstract`
    - `[ENTITY_NAME]LocalServiceUtil`: Local service utility class which wraps
      `[ENTITY_NAME]LocalServiceImpl` and serves as the primary local access
      point to the service layer. 
    - `[ENTITY_NAME]LocalServiceWrapper`: Local service wrapper which implements
      `[ENTITY_NAME]LocalService`. This class is designed to be extended and it
      allows developers to customize the entity's local services.

    ![Figure 2: Service Builder generates these service classes and interfaces. Only EventLocalServiceImpl allows custom methods to be added to the service layer.](../../../images/service-builder-service-diagram.png)

- Remote Service (generated for an entity only if an entity's `remote-service`
  attribute is *not* set to `false` in `service.xml`)
    - `[ENTITY_NAME]Service`: Remote service interface. 
    - `[ENTITY_NAME]ServiceImpl` (**REMOTE SERVICE IMPLEMENTATION**): Remote
      service implementation. This is the only class in the remote service
      that you should modify manually. Here, you can write code that adds
      additional security checks and invokes the local services. For any custom
      methods added here, Service Builder adds corresponding methods to the
      `[ENTITY_NAME]Service` interface the next time you run it. 
    - `[ENTITY_NAME]ServiceBaseImpl`: Remote service base implementation. This is
      an abstract class.  `@abstract`
    - `[ENTITY_NAME]ServiceUtil`: Remote service utility class which wraps
      `[ENTITY_NAME]ServiceImpl` and serves as the primary remote access point
      to the service layer.
    - `[ENTITY_NAME]ServiceWrapper`: Remote service wrapper which implements
      `[ENTITY_NAME]Service`. This class is designed to be extended and it allows
      developers to customize the remote entity's services.
    - `[ENTITY_NAME]ServiceSoap`: SOAP utility which the remote `[ENTITY_NAME]ServiceUtil`
      remote service utility can access.
    - `[ENTITY_NAME]Soap`: SOAP model, similar to `[ENTITY_NAME]ModelImpl`.
      `[ENTITY_NAME]Soap` is serializable; it does not implement
      `[ENTITY_NAME]`. 

- Model
    - `[ENTITY_NAME]Model`: Base model interface. This interface and its
      `[ENTITY_NAME]ModelImpl` implementation serve only as a container for the default
      property accessors generated by Service Builder. Any helper methods and
      all application logic should be added to `[ENTITY_NAME]Impl`. 
    - `[ENTITY_NAME]ModelImpl`: Base model implementation. 
    - `[ENTITY_NAME]`: [ENTITY_NAME] model interface which extends
      `[ENTITY_NAME]Model`. 
    - `[ENTITY_NAME]Impl`:  (**MODEL IMPLEMENTATION**) Model implementation. You
      can use this class to add helper methods and application logic to your
      model. If you don't add any helper methods or application logic, only the
      auto-generated field getters and setters are available. Whenever you
      add custom methods to this class, Service Builder adds corresponding
      methods to the `[ENTITY_NAME]` interface the next time you run it.
    - `[ENTITY_NAME]Wrapper`: Wrapper, wraps `[ENTITY_NAME]`. 

    ![Figure 3: Service Builder generates these model classes and interfaces. Only `EventImpl` allows custom methods to be added to the service layer.](../../../images/service-builder-model-diagram.png)

+$$$

**Note:** `*Util` classes are generated for backwards compatibility purposes
only. Your module applications should avoid calling the util classes.

$$$

Each file that Service Builder generates is assembled from an associated
FreeMarker template. You can find Service Builder's FreeMarker templates in the
[portal-tools-service-builder](https://github.com/liferay/liferay-portal/tree/master/modules/util/portal-tools-service-builder/src/main/resources/com/liferay/portal/tools/service/builder/dependencies)
module. For example, if you want to find out how a
`*ServiceImpl.java` file is generated, just look at the
[service_impl.ftl](https://github.com/liferay/liferay-portal/blob/master/modules/util/portal-tools-service-builder/src/main/resources/com/liferay/portal/tools/service/builder/dependencies/service_impl.ftl)
template.

Of all the classes generated by Service Builder, only three should be manually
modified: `*LocalServiceImpl`, `*ServiceImpl` and `*Impl`. If you manually
modify the other classes, your changes are overwritten the next time you run
Service Builder. Whenever you add methods to, remove methods from, or change a
method signature of a `*LocalServiceImpl` class, `*ServiceImpl` class, or
`*Impl` class, you should run Service Builder again to regenerate the affected
interfaces and the service JAR.

During the course of development, you're likely to need to add fields to your
database. This is a normal process of iterative development: you get an idea for
a new feature, or it's suggested to you, and that feature requires additional
data in the database. It's important to note, then, that **new fields added to
`service.xml` are not automatically added to the database.** To add the fields,
you must do one of two things: 

1.  Write an [upgrade process](/develop/tutorials/-/knowledge_base/7-0/creating-an-upgrade-process-for-your-app) 
    to modify the tables and preserve the data, or

2.  Run the `cleanServiceBuilder` task (supported on Gradle, Maven, and Ant),
    which drops your tables so they get re-created the next time your app is
    deployed. 

Use the first option when you have a released application and you must preserve
your users' data. Use the second option when you're in the middle of development
and you're adding new columns during that process. 

## Related Topics [](id=related-topics)

[What is Service Builder](/develop/tutorials/-/knowledge_base/7-0/what-is-service-builder)

[Running Service Builder and Understanding the Generated Code](/develop/tutorials/-/knowledge_base/7-0/running-service-builder-and-understanding-the-generated-code)

[Understanding Service Context](/develop/tutorials/-/knowledge_base/7-0/understanding-servicecontext)

[Creating Local Services](/develop/tutorials/-/knowledge_base/7-0/creating-local-services)
