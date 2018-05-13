# springcloudturbine
     Hystrix Dashboard in spring boot:


1) Add below dependencies for hystrix :
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-starter-hystrix</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-starter-hystrix-dashboard</artifactId>
	</dependency>
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>


2) import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;

@HystrixCommand(fallbackMethod = "getAccountsFallBack")
    public List<Account> getAccounts(Integer customerId){
		ResponseEntity<List<Account>> str =	restTemplate.exchange("http://account-service//accounts/customer/"+customerId, HttpMethod.GET, HttpEntity.EMPTY,new ParameterizedTypeReference<List<Account>>() {
		});
    return str.getBody();
    }


public List<Account> getAccountsFallBack(Integer customerId,Throwable t){
		
		System.out.println("  from @HystrixCommand   getAccountsFallBack =============================");
		
		List<Account>  list = new ArrayList<Account>();
		
		
		Account account = new Account();
		
		account.setErrorCode("00000");
		account.setErrorDesc(t.getMessage());
		
		list.add(account);
		
		return list;
		
	}


3)

@EnableHystrixDashboard
@EnableCircuitBreaker

@EnableCircuitBreaker annotation will scan the classpath for any compatible Circuit Breaker implementation. 


@EnableHystrixDashboard and the latter automatically enables the required metrics within our web-application.
After we’ve done restarting the application, we’ll point a browser at http://localhost:8080/hystrix, input the metrics URL of a ‘hystrix.stream’ and begin monitoring.


Add below code to run the hystrix in zuul

@Bean(name = "hystrixRegistrationBean")
public ServletRegistrationBean servletRegistrationBean() {
    ServletRegistrationBean registration = new ServletRegistrationBean(
            new HystrixMetricsStreamServlet(), "/hystrix.stream");
    registration.setName("hystrixServlet");
    registration.setLoadOnStartup(1);
    return registration;
}
http://localhost:8765/hystrix.stream



Turbine::


http://localhost:8989/hystrix/

Below URL in monitor:

http://localhost:8989/turbine.stream

List of dependencies for Turbine

<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-hystrix</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-hystrix-dashboard</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-turbine</artifactId>
		</dependency>



Enbale below annotation on boot application class:

import org.springframework.cloud.netflix.hystrix.dashboard.EnableHystrixDashboard;
import org.springframework.cloud.netflix.turbine.EnableTurbine;

@EnableHystrixDashboard
@EnableTurbine



application.properites file :


server.port=8989
spring.application.name=turbine
eureka.client.service-url.defaultZone=${DISCOVERY_URL:http://localhost:8761}/eureka/
turbine.app-config=service1,gateway-server

All app config details must registry with Eureka then only turbine can aggregate the details

turbine.cluster-name-expression=new String("default")

local default else we need define the cloud cluster details
