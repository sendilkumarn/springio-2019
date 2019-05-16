# Step 1

Let us generate a monolith application using JDL.

Create a folder called `store-microservices`

`mkdir store-microservices`

`cd store-microservices`

Create a file called `app.jdl`

`touch app.jdl`

And add the following contents into it.

```
/*
 * This is a monolithic e-commerce store sample
 */

application {
  config {
    baseName store,
    applicationType gateway,
    packageName com.jhipster.demo.store,
    serviceDiscoveryType eureka,
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
    serviceDiscoveryType eureka,
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

#step 2
With JHipster generate the application

`jhipster import-jdl app.jdl`

#step 3

Now package the applications

`cd ../store`

and Run

`./gradlew -x :npmInstall`

and then spin up another terminal and go to notification directory

`cd ../notification`

and run

`docker-compose -f src/main/docker/mongodb.yml up`

and then boot the application in a different terminal

`./gradlew `


#Step 4

Now Start JHipster registry

`cd ../store`

`docker-compose -f src/main/docker/jhipster-registry.yml up`

:tada: we have completed the third milestone :tada:
