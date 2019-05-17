# Step 1

Let us generate a microservice application using JDL.

Create a folder called `store-istio`

`mkdir store-istio`

`cd store-istio`

Create a file called `app.jdl`

`touch app.jdl`

<details> <summary> And add the following contents into it. </summary>
	<p>
		```
		/*
		 * This is a microservice e-commerce store sample
		 */

		application {
		  config {
		    baseName store,
		    applicationType microservice,
		    packageName com.jhipster.demo.store,
		    serviceDiscoveryType no,
		    authenticationType jwt,
		    prodDatabaseType mysql,
		    cacheProvider hazelcast,
		    buildTool gradle,
		    clientFramework react,
		    useSass true
		  }
		  entities *
		}

		application {
		  config {
		    baseName notification,
		    applicationType microservice,
		    packageName com.jhipster.demo.notification,
		    serviceDiscoveryType no,
		    authenticationType jwt,
		    databaseType mongodb,
		    prodDatabaseType mongodb,
		    devDatabaseType mongodb,
		    cacheProvider no,
		    enableHibernateCache false,
		    buildTool gradle,
		    serverPort 8082,
		    skipUserManagement true
		  }
		  entities Notification
		}

		/* Entities */

		/** Product sold by the Online store */
		entity Product {
		    name String required
		    description String
		    price BigDecimal required min(0)
		    size Size required
		    image ImageBlob
		}

		enum Size {
		    S, M, L, XL, XXL
		}

		entity ProductCategory {
		    name String required
		    description String
		}

		entity Customer {
		    firstName String required
		    lastName String required
		    gender Gender required
		    email String required pattern(/^[^@\s]+@[^@\s]+\.[^@\s]+$/)
		    phone String required
		    addressLine1 String required
		    addressLine2 String
		    city String required
		    country String required
		}

		enum Gender {
		    MALE, FEMALE, OTHER
		}

		entity ProductOrder {
		    placedDate Instant required
		    status OrderStatus required
		    code String required
		    invoiceId Long
		}

		enum OrderStatus {
		    COMPLETED, PENDING, CANCELLED
		}

		entity OrderItem {
		    quantity Integer required min(0)
		    totalPrice BigDecimal required min(0)
		    status OrderItemStatus required
		}

		enum OrderItemStatus {
		    AVAILABLE, OUT_OF_STOCK, BACK_ORDER
		}

		relationship OneToOne {
		    Customer{user(login) required} to User
		}

		relationship ManyToOne {
			OrderItem{product(name) required} to Product
		}

		relationship OneToMany {
		   Customer{order} to ProductOrder{customer(email) required},
		   ProductOrder{orderItem} to OrderItem{order(code) required} ,
		   ProductCategory{product} to Product{productCategory(name)}
		}

		service Product, ProductCategory, Customer, ProductOrder, OrderItem with serviceClass
		paginate Product, Customer, ProductOrder, OrderItem with pagination

		entity Invoice {
		    code String required
		    date Instant required
		    details String
		    status InvoiceStatus required
		    paymentMethod PaymentMethod required
		    paymentDate Instant required
		    paymentAmount BigDecimal required
		}

		enum InvoiceStatus {
		    PAID, ISSUED, CANCELLED
		}

		entity Shipment {
		    trackingCode String
		    date Instant required
		    details String
		}

		enum PaymentMethod {
		    CREDIT_CARD, CASH_ON_DELIVERY, PAYPAL
		}

		relationship OneToMany {
		    Invoice{shipment} to Shipment{invoice(code) required}
		}

		service Invoice, Shipment with serviceClass
		paginate Invoice, Shipment with pagination

		entity Notification {
		    date Instant required
		    details String
		    sentDate Instant required
		    format NotificationType required
		    userId Long required
		    productId Long required
		}

		enum NotificationType {
		    EMAIL, SMS, PARCEL
		}
		microservice Notification with notification
		```
	</p>
</details>


# Step 2
With JHipster generate the application

`jhipster import-jdl app.jdl`

# Step 3

Now package the applications

`cd ../store`

and Run

`./gradlew bootJar -Pprod jibDockerBuild`

and then run

`cd ../notification`

and run

`./gradlew bootJar -Pprod jibDockerBuild`

The above command will generate the necessary Docker images for us to deploy.


# Step 4

Now go a step back in the folder.
`cd ..`

Create a folder called `k8s`

`mkdir k8s`

Go inside the folder

`cd k8s`

and Run

`jhipster kubernetes`

# Step 5

Tag and push the images to the docker repository.

# Step 6
Install the istio on the running Kubernetes machine.

https://istio.io/


https://istio.io/docs/setup/kubernetes/install/kubernetes/

# Step 7

Now run everything on the cluster using.

`cd ../k8s`

`./kubectl-apply.sh`


# Step 8 (Optional - Run JHipster console)

** Note this will work only when you enable JHipster console when running `jhipster kubernetes` **

The registry will be available at [http://localhost:8761/#/](http://localhost:8761/#/)

Now access Kibana at [localhost:5601](http://localhost:5601) and Zipkin at [localhost:9411](http://localhost:9411).


:tada: we have completed the fourth milestone :tada:
