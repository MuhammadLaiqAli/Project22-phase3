# SYLIUS E-COMMERECE SOLUTION
# Architecture Overview
Sylius is built from components and Symfony bundles(PHP web application framework that is free and open-source, as well as a collection of reusable PHP component libraries so its time saving and completely compliant with business rules, is organized, manageable, and upgradeable,), which are integration layers with the framework. The name standards and data persistence methods are the same for all bundles. Sylius manages all entities by default using the Doctrine ORM.In PHP 5.2. 3+, Doctrine is an object relational mapper (ORM -In computer science, object-relational mapping is a programming method for transforming data across type systems using object-oriented programming languages.)
that is built on top of a potent database abstraction layer (DBAL). One of its standout features is the ability to create database queries using the Doctrine Query
Language (DQL), a proprietary object-oriented SQL dialect that was modeled after Hibernates HQL.
All architectural decisions need to be backed by a valid reason. The fundamental signposts we use to make such choices are explained in the Architectural Drivers section. Specific decisions we make during the development are often explained using Architectural Decision Records. They’re stored in the main Sylius repository for better visibility.
 
# Components and Bundles
 
Sylius is built from components and Symfony bundles, which are integration layers with the framework. All bundles share the same conventions for naming things and the way of data persistence.Each and every Sylius component can be utilized independently. Consider the Taxation component as an example. Its sole function is to compute taxes; it is completely detached from whether these taxes are on goods or on anything else. Your objects must implement the TaxableInterface in order for the Taxation component to work on them. Since then, they are able to compute their taxes.
Each part of Sylius is approached in this manner.
Every single component of Sylius can be used standalone. Besides components that are strictly connected to the e-commerce needs, we have plenty of components that are more general. For instance Attribute, Mailer, Locale etc. All the components are packages available via Packagist.
 ```php
<?php
 
/*
 * This file is part of the Sylius package.
 *
 * (c) Paweł Jędrzejewski
 *
 * For the full copyright and license information, please view the LICENSE
 * file that was distributed with this source code.
 */
 
declare(strict_types=1);
 
namespace Sylius\Component\Taxation\Calculator;
 
use Sylius\Component\Taxation\Model\TaxRateInterface;
 
interface CalculatorInterface
{
	public function calculate(float $base, TaxRateInterface $rate): float;
}
```

# Bundles
The bundles allow you to use components with just little configuration.
The TaxationBundle may be included into your application with little to no configuration,  allowing you to access all the services, models, set up the tax rates and categories, and utilize them for whatever taxes you may require.
These are the Symfony Bundles - therefore if you are a Symfony Developer, and you would like to use the Taxation component in your system, but you do not want to spend time on configuring forms or services in the container.
# Platform
 
We may access the standard, fairly feature-rich webshop.  We must choose if we want a complete platform with all the capabilities or whether we want to develop something extremely unique, maybe smaller, with distinct features utilizing decoupled bundles and components.
The platform itself is, of course, quite adaptable and simple to adjust to any and all business requirements you may have.
 
 
# Divisions
## Core
Another element that unifies all the others is the Core. Here, for instance, the ProductVariant discovers at last that it has a TaxCategory.
The ProductVariant implements the TaxableInterface and other interfaces necessary for its functioning in the Core component.
Sylius has here a fully integrated concept of everything that is needed to run a webshop.
 
## Shop
ShopBundle essentially functions as a typical B2C interface for all system activity. Yaml templates and settings make up the majority of it.
Also here views have been built using the SemanticUI.
 
## API
API uses the REST( REpresentational State Transfer, establishes standards for communication between computer systems on the web) approach.
Since they are now reusable in the API, controllers are format independent. So, when you make a product request through the shop's front end,
you are performing the exact same activity as when you make an api call.
The most important features of API:
All operations are grouped by shop and admin context (two prefixes)
Developers can enable or disable entire API by changing single parameter (check this chapter)
Endpoints are implementing the REST principles and using http verbs (POST, GET, PUT, PATCH, DELETE)
Returned responses contain minimal information (developer should extend serialization if need more data)
Entire business logic is separated from API - if it necessary we dispatch command instead mixing API logic with business logic
 
## Third Party Libraries
Sylius utilizes a multitude of third-party libraries for a variety of functions, including:
For content management, SymfonyCMF
For filesystem abstraction, see Gaufrette (store images locally, Amazon S3 or external server)
Consider trimming and creating thumbnails for photos that are being processed.
Quick to create PDF files
For pagination, use Pagerfanta.
For payments, payum


# Performance Optimizations
 
## Query optimization
A huge performance boost is achieved by optimizing queries. For example, let’s take a look at the customers’ list. The page load without caching mechanisms in dev mode might take up to several seconds compared to less than a second in Sylius. Storages are services managing data in any type of container.
 
## By default there are three storages available:
The methods of every storage work the same way. The only difference is the type of container we store in
 
## Cookie Storage
CookieStorage is used to manage data in the cookies’ ParameterBag of Symfony’s Request class, which needs to be set in the storage before making any operations on it.
 ```php
<?php
 
use Sylius\Component\Storage\CookieStorage;
use Symfony\Component\HttpFoundation\Request;
 
$request = // The request which cookies' data you would like to manage.
 
$storage = new CookieStorage();
 
$storage->setRequest($request);
This service implements StorageInterface.
 
#Doctrine Cache Storage
DoctrineCacheStorage is used to manage data in objects implementing the Doctrine’s Cache interface.
 
<?php
 
use Doctrine\Common\Cache\Cache;
use Sylius\Component\Storage\DoctrineCacheStorage;
 
$cache = // Your doctrine's cache.
 
$storage = new DoctrineCacheStorage($cache);
 
#Session Storage
SessionStorage is used to manage data in any class implementing the Symfony’s SessionInterface.
 
<?php
 
use Sylius\Component\Storage\SessionStorage;
use Symfony\Component\HttpFoundation\Session\Session;
 
$session = new Session();
$session->start();
 
$storage = new SessionStorage($session);
```

# Usage of Content Delivery Network
The integration with Enqueue Library helped decouple non-critical processes from the main browser request of users.
 
# Lazy loading
Lazy entities are used to generate entities and collections that aren't yet populated, such as in Doctrine ORM.
Lazy initialization will only obtain their state by running SQL queries when one of their attributes is first accessed.

# Security Requirements
 
With a view to ensuring the security and in order to process your personal data – during the use of their services – in a legal, reliable and transparent manner, they hereby referred to as the Privacy Policy.
To protect its Users’ personal data, the Service Provider applies appropriate organizational and technical measures that prevent interference with the Users’ privacy. These steps ensure security on a level prescribed by the applicable law, including:
 
1. The Personal Data Protection Act of 10 May 2018
2. GDPR – the Regulation 2016/679 of the European Parliament and of the Council of 27 April 2016 on the protection of natural persons with regard to the processing
of personal data and on the free movement of such data, and repealing Directive 95/46/EC (General Data Protection Regulation);
3. The Electronically Supplied Services Act of 18 July 2002
4. The Act of 16 July 2004 Telecommunications Law.
 
# Reliability Requirements
 
## Extendability
Sylius offers a lot of standard e-commerce features that could and should be used as a base to introduce more advanced and business-specific functionalities.
 
## Question to be asked:
Is it possible to easily add new, more advanced functionality to the module/class/service I implement? 
Examples: Promotions actions and rules registered with tags, state machine callbacks, resource events, Customizability, Seemingly similar to the previous one, but essentially different. Focuses on making it possible to override the standard functionality with a different one, while still keeping the whole process working. The most important (but not the only) step to reach it is using interfaces with small, focused and granular services. Customizability should be kept on all levels - from the single service, to the whole module/component.
 
## Question to be asked: 
Is it possible to replace this functionality and not break the whole process?
Examples: service for calculating variant price that can be overridden to provide more advanced pricing strategies resource configuration, that gives the possibility to configure any service as resource-specific controller/factory/repository etc.

# Testability
As mentioned before, Sylius embraces test-driven methodologies from its very beginning. Therefore, every class (with some exceptions) should be described with unit tests, every functionality should be designed through Behat acceptance scenarios.
Highly tested code is crucial to ensure another, also important driver, which is reliability of the software.

# Very Adaptable
Every aspect of Sylius may be easily customized because of the contemporary and thoughtful architecture. Building on a strong base, create your own implementation.
 For excellent performance on a big project, Scalable Sylius may be set up on a single server as well as in the cloud using a multi server,
fault-tolerant, auto scaling architecture.

# Process Requirement
## Sylius follows the Semantic Versioning strategy:
A new Sylius patch version (e.g. 1.0.1, 1.0.2, etc.) comes out usually once a month, depending on the number of bug fixes developed. A new Sylius minor version (e.g. 1.1, 1.2, etc.) is released depending on various factors (see below), usually once a few months. New Sylius minor releases will drop unsupported PHP versions.

# Scope-based vs time-based
## Development
The full development period for any minor version is divided into two phases:
 
Development: First 5/6 of the time intended for the release to add new features and to enhance existing ones.
Stabilization: Last 1/6 of the time intended for the release to fix bugs, prepare the release, and wait for the whole Sylius ecosystem (third-party libraries, plugins, and projects using Sylius) to catch up.

## Maintenance
Each minor Sylius version is maintained for a fixed period of time after its release. This maintenance is divided into:
Bug fixes and security fixes: During this period, being eight months long, all issues can be fixed.
The end of this period is referenced as being the end of maintenance of a release.
## Security fixes: 
During this period, being sixteen months long, only security related issues can be fixed. The end of this period is referenced as being the end of life of a release.

# Backward compatibility
## Reporting an Issue
The easiest contributions you can make are reporting issues: a typo, a grammar mistake, a bug in a code example, a missing explanation, and so on.
## Steps:
Submit a new issue in the GitHub tracker;
(optional) Submit a patch.

# Documentation needs
Sylius is completely open source (MIT license) and free, maintained by a diverse and creative community of developers and companies.
The core values of Sylius are:
1. Components based approach
2. Unlimited flexibility and simple customization
3. Developer-friendly, using latest technologies
4. Developed using best practices and BDD approach
5. Highest quality of code
Sylius is constructed from fully decoupled and flexible e-commerce components for PHP. It is also a set of Symfony bundles, which integrate the components into the full-stack framework. On top of that, Sylius is also a complete e-commerce platform crafted from all these building blocks.

It is your choice how to use Sylius, you can benefit from the components with any framework, integrate selected bundles into existing or new Symfony app or built your application on top of Sylius platform.

## Steps to use Sylius :
1. Follow standards, base branch pull requests
2. User Guide, Tutorials, blogs
3. Customization guide
4. Getting started

To begin creating your new project, run this command:
 ```php
composer create-project sylius/sylius-standard acme
 ```
 
 This will create a new Symfony project in the acme directory. Next, move to the project directory:
 
 ```php
cd acme
 ```
Sylius uses environment variables to configure the connection with database and mailer services. You can look up the default values in .env file and customise them by creating .env.local with variables you want to override. For example, if you want to change your database name from the default sylius_%kernel.environment% to my_custom_sylius_database, the contents of that new file should look like the following snippet:

 ```php
DATABASE_URL=mysql://username:password@host/my_custom_sylius_database
 ```
 
 After everything is in place, run the following command to install Sylius:
 
 ```php
php bin/console sylius:install
 ```
 
## Configuring Mailer
In order to send emails you need to configure Mailer Service. Basically you need to:

Create an account on a mailing service.
In your .env file modify the MAILER_URL variable.
MAILER_URL=gmail://username:password@local
