# WCF Container Examples
Demonstrate how to run WCF service as well as .NET Core WCF client in containers

## Run WCF Service in Container
WCF service is supported on .NET Framework, which can only run in Windows cotnainers. Right click the docker icon and select "Switch to Windows containers..."

### Build a Container with IIS-hosted WCF Service
Build container image of `WcfServiceWebApp` and run it. Parameter -d will run the container in detached mode.
```
C:\WcfContainerExamples\WcfServiceWebApp> docker build -t wcfservice:iis-hosted .
C:\WcfContainerExamples\WcfServiceWebApp> docker run -d --name service wcfservice:iis-hosted
```
Find the IP address of the container instance. This will be used later for WCF client to connect to the service.
```
C:\WcfContainerExamples\WcfServiceWebApp> docker inspect --format="{{.NetworkSettings.Networks.nat.IPAddress}}" service
172.23.70.146
```
### Build a Container with Self-hosted WCF Service
Buld project `WcfServiceConsoleApp` in either Visual Stuio or command line. Then build container image of `WcfServiceConsoleApp` and run it.
```
C:\WcfContainerExamples\WcfServiceConsoleApp> docker build -t wcfservice:self-hosted .
C:\WcfContainerExamples\WcfServiceConsoleApp> docker run -it --rm --name myservice wcfservice:self-hosted
The service is ready at http://localhost/Service1.svc
The service is ready at net.tcp://localhost/Service1.svc
The service is running...
```
Open another console window to find the IP address of the self-hosted WCF service (Alternatively, you can also run the self-hosted WCF service container in detached mode by `docker run -d --name myservice wcfservice:self-hosted`).
```
C:\WcfContainerExamples\WcfClientNetCore>docker inspect --format="{{.NetworkSettings.Networks.nat.IPAddress}}" myservice
172.23.69.75
```
## Run WCF Client in Container
The WCF client project, `WcfClientNetCore`, is a .NET Core 2.0 application. First, let's run it locally to make sure everything works. The IP address of service is passed through the environment variable `host`.
```
C:\WcfContainerExamples\WcfClientNetCore>set host=172.23.70.146
C:\WcfContainerExamples\WcfClientNetCore>dotnet build
C:\WcfContainerExamples\WcfClientNetCore>dotnet run
Client OS: Microsoft Windows NT 6.2.9200.0
Service Host: 172.23.70.146
Hello WCF via Http from Container!
Hello WCF via Net.Tcp from Container!
```
### Build Windows Container of WCF Client
Now, let's build the Windows container image of `WcfClientNetCore` and run it. The host IP address is pass into container through `--env` parameter. Parameter `--rm` will clean up the container after it's done.
```
C:\WcfContainerExamples\WcfClientNetCore>docker build -t wcfclient .
C:\WcfContainerExamples\WcfClientNetCore>docker run --env host=172.23.70.146 --rm wcfclient
Client OS: Microsoft Windows NT 10.0.14393.0
Service Host: 172.23.70.146
Hello WCF via Http from Container!
Hello WCF via Net.Tcp from Container!
```
### Build Linux Container of WCF Client
As .NET Core is cross-platform, we can also build a Linux container image of `WcfClientNetCore` and run it. To do that, first, right click the docker icon and select "Switch to Linux containers..." and then run the same commands again, except you will notice the client OS becomes Unix as below.
```
C:\WcfContainerExamples\WcfClientNetCore>docker build -t wcfclient .
C:\WcfContainerExamples\WcfClientNetCore>docker run --env host=172.23.70.146 --rm wcfclient
Client OS: Unix 4.9.49.0
Service Host: 172.23.70.146
Hello WCF via Http from Container!
Hello WCF via Net.Tcp from Container!
```
