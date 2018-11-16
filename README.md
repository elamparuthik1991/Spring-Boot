# Spring-Boot
Spring boot stater project.

## 1. pom.xml
We need to include the following "pom.xml" file. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example.pro</groupId>
	<artifactId>noor-payments</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>war</packaging>

	<name>elam-pro</name>
	<description>Demo project for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.10.RELEASE</version>
		<relativePath /> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>


	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

		<!-- https://mvnrepository.com/artifact/org.apache.commons/commons-lang3 -->
		<dependency>
			<groupId>org.apache.commons</groupId>
			<artifactId>commons-lang3</artifactId>
			<version>3.7</version>
		</dependency>

		<!-- marked the embedded servlet container as provided -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<scope>provided</scope>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>

		<!-- https://mvnrepository.com/artifact/com.squareup.okhttp3/okhttp -->
		<dependency>
			<groupId>com.squareup.okhttp3</groupId>
			<artifactId>okhttp</artifactId>
			<version>3.10.0</version>
		</dependency>

		<!-- https://mvnrepository.com/artifact/com.googlecode.json-simple/json-simple -->
		<dependency>
			<groupId>com.googlecode.json-simple</groupId>
			<artifactId>json-simple</artifactId>
		</dependency>

		<dependency>
			<groupId>org.json</groupId>
			<artifactId>json</artifactId>
			<version>20090211</version>
		</dependency>
		<dependency>
			<groupId>javax.validation</groupId>
			<artifactId>validation-api</artifactId>
			<version>1.1.0.Final</version>
		</dependency>
		<dependency>
			<groupId>org.scala-lang</groupId>
			<artifactId>scala-library</artifactId>
			<version>2.11.0</version>
		</dependency>
		<dependency>
			<groupId>org.apache.hadoop</groupId>
			<artifactId>hadoop-core</artifactId>
			<version>1.0.0</version>
		</dependency>
		<!-- swagarfox -->
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger2</artifactId>
			<version>2.9.2</version>
		</dependency>
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger-ui</artifactId>
			<version>2.9.2</version>
		</dependency>
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-bean-validators</artifactId>
			<version>2.9.2</version>
		</dependency>

	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
  
</project>
```


## 2. Application
We need to include the following "ExampleApplication.java" file. 

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.support.SpringBootServletInitializer;
import org.springframework.context.annotation.Bean;
import org.springframework.data.domain.AuditorAware;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;

@SpringBootApplication
@EnableAutoConfiguration
//@EnableScheduling
public class NoorPaymentsApplication extends SpringBootServletInitializer {
	
	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
		return application.sources(NoorPaymentsApplication.class);
	}

	public static void main(String[] args) throws Exception {
		SpringApplication.run(NoorPaymentsApplication.class, args);
	}
}
```
## 3. Controller
We need to include the following "ExampleApplication.java" file. 

```java
@RestController
@RequestMapping("/api/v1/")
public class ExampleController {

	private static final Logger LOGGER = LoggerFactory.getLogger(ExampleController.class);

	@Autowired
	private CustomerService customerService;
  
  @PostMapping("/createCustomer")
	public ResponseEntity<Result<Customer>> createErpCustomer(@Valid @RequestBody ErpCustomerBean erpCustomerBean) {

		Customer customer = customerService.getCustomerByCustomerName(erpCustomerBean.getCustomer_name());

		Result result = new Result();
		if (customer != null) {
			result.setStatus("error");
			result.setMessage(resource.getMessage("customer.error.exists", null, new Locale(lang)));
			return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(result);
		}

		result.setStatus("success");
		result.setResult(customerService.createCustomerByErp(erpCustomerBean));
		return ResponseEntity.ok().body(result);
	}
  
//  @GetMapping("/createCustomer")
//  @PutMapping("/createCustomer")
//  @DeleteMapping("/createCustomer")
```

## 4. JSR-303
### JSR-303 in controller
```java
@PostMapping("/{value}/addBank")
	public ResponseEntity<Result<CustomerBank>> addPaymentProvider(
			@Valid @RequestBody CustomerBankBean customerBankBean, @ApiIgnore Errors errors,
			@PathVariable("value") String value) {
      
      Result result = new Result();
		if (customerBankBean.getNoorAccountNumber().isEmpty()) {
			result.setStatus("error");
			result.setMessage(resource.getMessage("noorAccountNumber.error.empty", null, new Locale(lang)));
		}

		if (errors.hasErrors()) {
			result.setStatus("error");
			result.setMessage(errors.getAllErrors().stream()
					.map(x -> resource.getMessage(x.getDefaultMessage(), null, new Locale(lang)))
					.collect(Collectors.joining(",")));

			return ResponseEntity.badRequest().body(result);
		}
... ...
...
... ...
```

### JSR-303 in bean

```java
@NotEmpty(message="bankAccountNumber.error.empty")
	public String getBankAccountNumber() {
		return bankAccountNumber;
	}
```
## 5. CustomerService.java

```java
package com.example.pro.service;

import java.util.List;

public interface CustomerService {

	int checkForCustomerExistByName(String name);
}
```
## 6.CustomerServiceImpl.java
```java
package com.example.pro.service.impl;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class CustomerServiceImpl implements CustomerService{

	@Autowired
	private CustomerRepository customerRepository;

	@Override
	@Transactional(readOnly = true)
	public int checkForCustomerExistByName(String name) {
		// TODO Auto-generated method stub
		return customerRepository.checkForCustomerExistByName(name);
	}

```

## 7. CustomerRepository.java
We need to include the following "ExampleApplication.java" file. 
```java
package com.example.pro.repository;

import java.util.List;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;

public interface CustomerRepository extends JpaRepository<Customer, Long> {

	@Query("select count(id) from Customer where name=?1")
	int checkForCustomerExistByName(String name);
```

## Conclusion
End of the application, We need to include all the above credentials to make demo example application.

## References
To make edit this document please use [edit readme.md](https://www.makeareadme.com/#rendered-1).
