MODULARITY REQUIREMENTS 
IN DJANGO

Loose Coupling:
The loose coupling and tight cohesiveness of Django's stack are a basic aim. Unless absolutely essential, the different levels of the framework shouldn't "know" about one another. For instance, the view system doesn't care which template system a programmer employs, the template system is ignorant of web requests, and the database layer is ignorant of data display. Although Django ships with a full stack for convenience, the stack's components are, whenever possible, independent of one another.

Less Code:
Django applications should have as minimal boilerplate code as feasible. Django ought to make the most of Python's dynamic features, like introspection.

Quick Development:
In the twenty-first century, the purpose of a web framework is to speed up the time-consuming steps in web development. Web development should be tremendously rapid using Django.

Consistency:
At every level, the framework should remain constant. From the low level (the Python coding style used) to the high level (the "experience" of using Django), consistency is important.

Explicit is better than implicit:
Fields shouldn’t assume certain behaviors based solely on the name of the field. This requires too much knowledge of the system and is prone to errors. Instead, behaviors should be based on keyword arguments and, in some cases, on the type of the field.

SQL efficiency:
It should execute SQL statements as few times as possible, and it should optimize statements internally. This is why developers need to call save() explicitly, rather than the framework saving things behind the scenes silently. This is also why the select_related() QuerySet method exists. It’s an optional performance booster for the common case of selecting “every related object.”

Terse, powerful syntax:
The database API should allow rich, expressive statements in as little syntax as possible. It should not rely on importing other modules or helper objects. Joins should be performed automatically, behind the scenes, when necessary. Every object should be able to access every related object, systemwide. This access should work both ways.

Definitive URLs:
Technically, foo.com/bar and foo.com/bar/ are two different URLs, and search-engine robots (and some web traffic-analyzing tools) would treat them as separate pages. Django should make an effort to “normalize” URLs so that search-engine robots don’t get confused. This is the reasoning behind the APPEND_SLASH setting.

Discourage redundancy:
The majority of dynamic websites use some sort of common sitewide design – a common header, footer, navigation bar, etc. The Django template system should make it easy to store those elements in a single place, eliminating duplicate code. This is the philosophy behind template inheritance.

Module Index

django.conf.urls
django.conf.urls.i18n
django.contrib.admin
Django's admin site.
django.contrib.admindocs
Django's admin documentation generator.
django.contrib.auth
Django's authentication framework.
django.contrib.auth.backends
Django's built-in authentication backend classes.
django.contrib.auth.forms
django.contrib.auth.hashers
django.contrib.auth.middleware
Authentication middleware.
django.contrib.auth.password_validation
django.contrib.auth.signals
django.contrib.auth.views
django.contrib.contenttypes
Provides generic interface to installed models.
django.contrib.contenttypes.admin
django.contrib.contenttypes.fields
django.contrib.contenttypes.forms
django.contrib.flatpages
A framework for managing simple ?flat? HTML content in a database.
django.contrib.gis
Geographic Information System (GIS) extensions for Django
django.contrib.gis.admin
GeoDjango's extensions to the admin site.
django.contrib.gis.db.backends
GeoDjango's spatial database backends.
django.contrib.gis.db.models
GeoDjango model and field API.
django.contrib.gis.db.models.functions
Geographic Database Functions



PERFORMANCE OPTIMIZATIONS

Caching:
Often it is expensive (that is, resource-hungry and slow) to compute a value, so there can be huge benefit in saving the value to a quickly accessible cache, ready for the next time it’s required. It’s a sufficiently significant and powerful technique that Django includes a comprehensive caching framework, as well as other smaller pieces of caching functionality.



Caching framework:
Django’s caching framework offers very significant opportunities for performance gains, by saving dynamic content so that it doesn’t need to be calculated for each request. For convenience, Django offers different levels of cache granularity: you can cache the output of specific views, or only the pieces that are difficult to produce, or even an entire site. Implementing caching should not be regarded as an alternative to improving code that’s performing poorly because it has been written badly. It’s one of the final steps toward producing well-performing code, not a shortcut.

cached_property:
It’s common to have to call a class instance’s method more than once. If that function is expensive, then doing so can be wasteful.Using the cached_property decorator saves the value returned by a property; the next time the function is called on that instance, it will return the saved value rather than re-computing it. Note that this only works on methods that take self as their only argument and that it changes the method to a property. Certain Django components also have their own caching functionality; these are discussed below in the sections related to those components.

Laziness in Django:
Django is itself quite lazy. A good example of this can be found in the evaluation of QuerySets. QuerySets are lazy. Thus a QuerySet can be created, passed around and combined with other QuerySets, without actually incurring any trips to the database to fetch the items it describes. What gets passed around is the QuerySet object, not the collection of items that - eventually - will be required from the database. On the other hand, certain operations will force the evaluation of a QuerySet. Avoiding the premature evaluation of a QuerySet can save making an expensive and unnecessary trip to the database. Django also offers a keep_lazy() decorator. This allows a function that has been called with a lazy argument to behave lazily itself, only being evaluated when it needs to be.






HTTP Performance
Middleware:

Django comes with a few helpful pieces of middleware that can help optimize your site’s performance. They include:

ConditionalGetMiddleware:
Adds support for modern browsers to conditionally GET responses based on the ETag and Last-Modified headers. It also calculates and sets an ETag if needed.

GZipMiddleware:
Compresses responses for all modern browsers, saving bandwidth and transfer time. Note that GZipMiddleware is currently considered a security risk, and is vulnerable to attacks that nullify the protection provided by TLS/SSL. See the warning in GZipMiddleware for more information.


