---
title: Come usare Micrometer con Azure Application Insights Java SDK | Microsoft Docs
description: "Guida dettagliata sull'uso di Micrometer con le applicazioni Spring Boot e non Spring Boot di Application Insights. "
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: lagayhar
ms.openlocfilehash: 1818d064fc8c067514e97cc435d312cab01b2f0e
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298347"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Come usare Micrometer con Azure Application Insights Java SDK
Il monitoraggio per applicazioni Micrometer misure le metriche per il codice di applicazione basato su Java Virtual Machine e consente di esportare i dati in sistemi di monitoraggio preferito. Questo articolo illustra come usare Micrometer con Application Insights per applicazioni Spring Boot e non Spring Boot.

## <a name="using-spring-boot-15x"></a>Uso di Spring Boot 1.5x
Aggiungere le dipendenze seguenti al file pom.xml or build.gradle: 
* [Application Insights Spring-boot-Starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter) 1.1.0-BETA o versione successiva
* Micrometer Azure Registry 1.1.0 o versione successiva
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 o versione successiva (esegue il backport del codice di autoconfigurazione nel framework Spring).
* [Risorsa di ApplicationInsights](../../azure-monitor/app/create-new-resource.md )

Passaggi

1. Aggiornare il file pom.xml dell'applicazione Spring Boot e aggiungervi le dipendenze seguenti:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0-BETA</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Aggiornare il file application. properties o yml con la chiave di strumentazione di Application Insights usando la proprietà seguente:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Compilare l'applicazione ed eseguirla
2. Il codice precedente deve avviare l'esecuzione con metriche pre-aggregate raccolte in modo automatico in Monitoraggio di Azure. Per informazioni su come ottimizzare Application Insights Spring Boot Starter, vedere [readme in GitHub](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Uso di Spring 2.x

Aggiungere le dipendenze seguenti al file pom.xml or build.gradle:

* Application Insights Spring-boot-starter 2.1.2 o versione successiva
* Azure-spring-boot-metrics-starters 2.0.7 o versione successiva  
* [Risorsa di Application Insights](../../azure-monitor/app/create-new-resource.md )

Passaggi:

1. Aggiornare il file pom.xml dell'applicazione Spring Boot e aggiungervi la dipendenza seguente:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Aggiornare il file application. properties o yml con la chiave di strumentazione di Application Insights usando la proprietà seguente:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Compilare l'applicazione ed eseguirla
4. Il codice precedente deve avviare l'esecuzione con metriche pre-aggregate raccolte in modo automatico in Monitoraggio di Azure. Per informazioni su come ottimizzare Application Insights Spring Boot Starter, vedere [readme in GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Metriche predefinite

*    Metriche configurate in modo automatico per Tomcat, JVM, metriche Logback, metriche Log4J, metriche del tempo di attività, metriche del processore, FileDescriptorMetrics.
*    Ad esempio, se Netflix hystrix è presente nel percorso della classe, si ottengono anche queste metriche. 
*    Le metriche seguenti possono essere disponibili aggiungendo gli oggetti bean relativi. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - Metriche OkHttp3 
        - Metriche Kafka 

 

Come disattivare la raccolta automatica delle metriche 
 
- Metriche JVM: 
    - management.metrics.binders.jvm.enabled=false 
- Metriche Logback: 
    - management.metrics.binders.logback.enabled=false
- Metriche tempo di attività: 
    - management.metrics.binders.uptime.enabled=false 
- Metriche processore:
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics:
    - management.metrics.binders.files.enabled=false 
- Metriche Hystrix, se la libreria è nel classpath: 
    - management.metrics.binders.hystrix.enabled=false 
- Metriche AspectJ, se la libreria è nel classpath: 
    - spring.aop.enabled=false 

> [!NOTE]
> Specificare le proprietà precedenti nel file application.properties o application.yml dell'applicazione Spring Boot

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Usare Micrometer con applicazioni Web non Spring Boot

Aggiungere le dipendenze seguenti al file pom.xml or build.gradle:

* Application Insights 2.5.0 Web auto-BETA o versione successiva
* Micrometer Azure Registry 1.1.0 o versione successiva
* [Risorsa di Application Insights](../../azure-monitor/app/create-new-resource.md )

Passaggi:

1. Aggiungere le dipendenze seguenti al file pom.xml or build.gradle:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0-BETA</version>
        </dependency>
     ```

2. Inserire `ApplicationInsights.xml` il file nella cartella risorse

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
    
       <!-- The key from the portal: -->
       <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
    
       <!-- HTTP request component (not required for bare API) -->
       <TelemetryModules>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
       </TelemetryModules>
    
       <!-- Events correlation (not required for bare API) -->
       <!-- These initializers add context data to each event -->
       <TelemetryInitializers>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>
    
    </ApplicationInsights>
    ```

3. Esempio di classe Servlet (genera una metrica di timer):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

4. Esempio d classe di configurazione:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

Per altre informazioni sulle metriche, vedere la [documentazione di Micrometer](https://micrometer.io/docs/).

Altro codice di esempio su come creare diversi tipi di metriche è disponibile nel [repository ufficiale di Micrometer in GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Come associare una raccolta di metriche aggiuntive

### <a name="springbootspring"></a>SpringBoot/Spring

Creare un oggetto bean della rispettiva categoria di metriche. Si nota ad esempio che sono necessarie le metriche di cache Guava:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Esistono diverse metriche non abilitate per impostazione predefinita, ma che possono essere associate nel modo indicato in precedenza. Per un elenco completo, vedere il [repository ufficiale di Micrometer in GitHub](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>App non Spring
Aggiungere il codice di associazione seguente al file di configurazione:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni su micrometrico, consultare la [documentazione](https://micrometer.io/docs)del micrometro ufficiale.
* Per informazioni su Spring in Azure, vedere la documentazione ufficiale di [Spring in Azure](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable).
