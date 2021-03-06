---
title: "Health Check is now Generally Available"
author_name: "Jason Freeberg and Suwat Bodin"
toc: true
toc_sticky: true
---

App Service makes it easy to automatically scale your apps to multiple instances when traffic increases. This increases your app's throughput, but what if there is an uncaught exception on one of the instances? To address this situation, we began previewing Health Check last year. The Health Check feature allows you to specify a path on your application for App Service to ping. If an instance fails to respond to the ping, the system determines it is *unhealthy* and removes it from the load balancer rotation. This increases your application's average availability and resiliency. 

Health Check is now Generally Available and ready for production applications. Set up Health Check on your applications today in the Azure Portal. Go to your web app and find **Health Check** under **Monitoring** in the left-side navigation menu. You may see "(Preview)" on the Portal blade. This is because the Portal blade uses the latest React libraries, but the feature itself is Generally Available.

![Health Check blade in the Portal]({{site.baseurl}}/media/2020/08/health-check/health-check-portal.png)

> For more details, please refer to the **[health check documentation](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/autoscale-get-started#health-check-path)**

## Behavior

Once you specify a path on your site, App Service will ping it every two minutes. If the path responds with a status code outside of 200 to 299 (or does not respond at all) for 5 pings, then the instance is determined to be unhealthy, and it is removed from the load balancer rotation. This stops the load balancer from routing requests to the unhealthy instances. 

When the instance is unhealthy and removed from the load balancer, the service continues to ping it. If it begins responding with successful response codes (200 to 299), the instance is returned to the load balancer. If it continues to respond unsuccessfully, App Service will restart the underlying VM in an effort to return the instance to a healthy status.

## Health check path

The path must respond within two minutes with a status code between 200 and 299 (inclusive). If the path does not respond within two minutes, or returns a status code outside the range, then the instance is considered "unhealthy". Health Check integrates with App Service's authentication and authorization features, the system will reach the endpoint even if these security features are enabled. If you are using your own authentication system, the health check path must allow anonymous access.

![Health Check Diagram]({{site.baseurl}}/media/2020/08/health-check/health_check_path_diagram.png){: .align-center}

The health check path should check the critical components of your application. For example, if your application depends on a database and a messaging system, the health check endpoint should connect to those components. If the application cannot connect to a critical component, then the path should return a 500-level response code to indicate that the app is unhealthy.

![Health Check diagram, failure]({{site.baseurl}}/media/2020/08/health-check/health-check-diagram-failure.png){: .align-center}

## Alerts

After providing your application's health check path, you can monitor the health of your site using Azure Monitor. From the Health check blade in the Portal, click **Metrics** in the top toolbar. This will open a new blade where you can see the site's historical health status and create a new alert rule. For more information on monitoring your sites, see the guide on Azure Monitor.  

## More resources

See the [Health check documentation](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/autoscale-get-started#health-check-path) for more information about this feature.

### Building and Managing .NET Core with App Service

Skip to **11:28** for more information on App Service health checks and best practices.

<div class="responsive-video-container">
    <iframe src="https://channel9.msdn.com/Events/Build/2020/BOD126/player"
        allowFullScreen 
        frameBorder="0" 
        title="Building and managing .NET Core with App Service - Microsoft Channel 9 Video">
    </iframe>
</div>
