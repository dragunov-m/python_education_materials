
Source: https://djangocentral.com/django-orm-cheatsheet/

# 1. Model Definition

```python
from django.db import models


class MyModel(models.Model): 
	field_name = models.CharField(max_length=100) 
	another_field = models.IntegerField() 
	date_field = models.DateTimeField(auto_now_add=True)
```

# 2. Queryset Basics

```python
from myapp.models import MyModel

# Create objects 
obj = MyModel.objects.create(field_name='value', another_field=42) 
# Retrieve all objects 
all_objects = MyModel.objects.all() 
# Retrieve a single object 
single_object = MyModel.objects.get(pk=1) 
# Filter objects 
filtered_objects = MyModel.objects.filter(field_name='value') 
# Chaining filters 
chained_filters = MyModel.objects.filter(field_name='value', another_field=42) 
# Exclude objects 
excluded_objects = MyModel.objects.exclude(field_name='value') 
# Ordering 
ordered_objects = MyModel.objects.order_by('field_name') 
# Count objects 
count_objects = MyModel.objects.count() 
# Check if an object exists 
exists = MyModel.objects.filter(field_name='value').exists() 
# Delete objects 
MyModel.objects.filter(field_name='value').delete()
```

# 3. Creating and Updating Objects

```python
# Create and Save
obj = MyModel(field_name='value', another_field=42)
obj.save()

# Bulk Create (improves performance)
MyModel.objects.bulk_create([
    MyModel(field_name='value1', another_field=42),
    MyModel(field_name='value2', another_field=43),
])

# Update
MyModel.objects.filter(field_name='old_value').update(field_name='new_value')
```

# 4. Querying with Q Objects (Complex Queries)

```python
from django.db.models import Q

# OR query
q = Q(field_name='value') | Q(another_field=42)
or_query = MyModel.objects.filter(q)

# AND query
q = Q(field_name='value') & Q(another_field=42)
and_query = MyModel.objects.filter(q)
```

# 5. Related Objects

```python
from myapp.models import MyModel, RelatedModel

# One-to-One Relationship
class MyModel(models.Model):
    related_model = models.OneToOneField(RelatedModel, on_delete=models.CASCADE)

# One-to-Many Relationship
class MyModel(models.Model):
    related_models = models.ForeignKey(RelatedModel, on_delete=models.CASCADE)

# Many-to-Many Relationship
class MyModel(models.Model):
    related_models = models.ManyToManyField(RelatedModel)
```

# 6. Related Objects Querying

```python
# Reverse relation
related_objects = RelatedModel.objects.filter(mymodel__field_name='value')

# Prefetch related objects (reduce queries)
my_objects = MyModel.objects.prefetch_related('related_models')
```

# 7. Aggregation and Annotation

```python
from django.db.models import Avg, Sum, Count

# Aggregate functions
average_value = MyModel.objects.aggregate(avg=Avg('another_field'))
total_sum = MyModel.objects.aggregate(sum=Sum('another_field'))
total_count = MyModel.objects.aggregate(count=Count('pk'))

# Annotate (add calculated fields)
annotated_objects = MyModel.objects.annotate(avg=Avg('another_field'))
```

# 8. F-Expressions (Update and Annotate)

```python
from django.db.models import F

# Update fields with F-expression
MyModel.objects.update(another_field=F('another_field') + 10)

# Annotate with F-expression
annotated_objects = MyModel.objects.annotate(sum=F('another_field') + F('field_name'))
```

# 9. Transactions

```python
from django.db import transaction

# Manual transaction
with transaction.atomic():
    # Your transactional operations here
```

# 10. Signals

```python
from django.db.models.signals import post_save
from django.dispatch import receiver

# Signal receiver
@receiver(post_save, sender=MyModel)
def my_signal_receiver(sender, instance, **kwargs):
    # Signal handling code here
    pass
```

# 11. Select Related and Prefetch Related

```python
# Select Related (reduces related object queries)
my_objects = MyModel.objects.select_related('related_model')

# Prefetch Related (reduces related object queries and improves performance)
my_objects = MyModel.objects.prefetch_related('related_models')
```

# 12. Filtering with Lookups

```python
# Case-insensitive exact match
filtered_objects = MyModel.objects.filter(field_name__iexact='value')

# Contains
filtered_objects = MyModel.objects.filter(field_name__contains='value')

# Startswith and Endswith
filtered_objects = MyModel.objects.filter(field_name__startswith='prefix')
filtered_objects = MyModel.objects.filter(field_name__endswith='suffix')

# In
filtered_objects = MyModel.objects.filter(another_field__in=[1, 2, 3])

# Range
filtered_objects = MyModel.objects.filter(another_field__range=(10, 20))
```

# 13. Date and Time Queries

```python
from datetime import date

# Exact Date Match
filtered_objects = MyModel.objects.filter(date_field__date=date(2023, 7, 31))

# Year, Month, Day
filtered_objects = MyModel.objects.filter(date_field__year=2023)
filtered_objects = MyModel.objects.filter(date_field__month=7)
filtered_objects = MyModel.objects.filter(date_field__day=31)

# Greater Than and Less Than
filtered_objects = MyModel.objects.filter(date_field__gt=date(2023, 7, 1))
filtered_objects = MyModel.objects.filter(date_field__lt=date(2023, 8, 1))
```

# 14. Raw SQL Queries

```python
from django.db import connection

# Execute Raw SQL Query
with connection.cursor() as cursor:
    cursor.execute("SELECT * FROM myapp_mymodel WHERE field_name=%s", ['value'])
    results = cursor.fetchall()

with connection.cursor() as cursor:
    cursor.execute('SELECT * FROM myapp_mymodel WHERE field_name=%s', ['value'])

with connections['other_db'].cursor() as cursor:
    cursor.execute('SELECT * FROM myapp_mymodel WHERE field_name=%s', ['value'])
    results = dictfetchall(cursor)

# Helper function for mapping raw SQL results to dictionaries
def dictfetchall(cursor):
    columns = [col[0] for col in cursor.description]
    return [dict(zip(columns, row)) for row in cursor.fetchall()]
```

# 15. Working with Aggregates and Grouping

```python
from django.db.models import Count, Sum

# Group by field_name and annotate
grouped_objects = MyModel.objects.values('field_name').annotate(count=Count('pk'))

# Group by field_name and calculate sum
grouped_objects = MyModel.objects.values('field_name').annotate(total_sum=Sum('another_field'))
```

# 16. Handling Null and Empty Values

```python
# Filter for null values
filtered_objects = MyModel.objects.filter(field_name__isnull=True)

# Filter for empty strings (useful for CharField)
filtered_objects = MyModel.objects.filter(field_name='')

# Exclude empty strings
excluded_objects = MyModel.objects.exclude(field_name='')
```

# 17. Chaining Querysets

```python
# Chain multiple filters
filtered_objects = MyModel.objects.filter(field_name='value').filter(another_field=42)

# Chain multiple excludes
excluded_objects = MyModel.objects.exclude(field_name='value').exclude(another_field=42)
```

# 18. Defer and Only

In Django's ORM, the `defer()` and `only()` methods are used to control which fields of a model are fetched from the database when querying. This can help improve performance by fetching only the necessary data and deferring the loading of less critical or heavier fields until they are actually accessed.

Here are practical examples of how to use `defer()` and `only()` in Django ORM queries:

Using `only()` to fetch only specific fields

```python
# Fetch only the publication years and ratings of books, loading all other fields
books = Book.objects.only('publication_year', 'rating')

for book in books:
    print(book.title, book.author)  # All fields except publication_year and rating are deferred
    print(book.publication_year, book.rating)  # These fields are loaded from the database
```

Using `defer()` to defer loading of specific fields

```python
# Fetch only the titles and authors of books, deferring other fields
books = Book.objects.defer('publication_year', 'summary', 'cover_image', 'rating', 'genre')

for book in books:
    print(book.title, book.author)  # Only these fields are loaded from the database
    # Accessing other fields will trigger database queries to load them
    print(book.publication_year, book.summary)
```

# 19. Case and When Expressions

```python
from django.db.models import Case, When, Value, IntegerField

# Use Case and When for conditional expressions
updated_objects = MyModel.objects.annotate(
    custom_field=Case(
        When(field_name='value1', then=Value(1)),
        When(field_name='value2', then=Value(2)),
        default=Value(0),
        output_field=IntegerField(),
    )
)
```

# 20. Bulk Update with Values

```python
# Bulk update specific fields with given values
MyModel.objects.filter(field_name='old_value').update(field_name='new_value')
```

# 21. Bulk Delete with Querysets

```python
# Bulk delete with a queryset
MyModel.objects.filter(field_name='value').delete()
```

# 22. Custom Managers

```python
# Define a custom manager for a model
class CustomManager(models.Manager):
    def custom_method(self):
        return self.filter(field_name='value')

# Use the custom manager in the model
class MyModel(models.Model):
    field_name = models.CharField(max_length=100)

    custom_manager = CustomManager()
```

# 23. Aggregation Functions

```python
from django.db.models import Sum, Avg, Max, Min, Count

# Get the sum of a specific field
total_sum = MyModel.objects.aggregate(total_sum=Sum('field_name'))

# Get the average value of a specific field
average_value = MyModel.objects.aggregate(average_value=Avg('field_name'))

# Get the maximum value of a specific field
max_value = MyModel.objects.aggregate(max_value=Max('field_name'))

# Get the minimum value of a specific field
min_value = MyModel.objects.aggregate(min_value=Min('field_name'))

# Get the count of objects in the queryset
object_count = MyModel.objects.aggregate(object_count=Count('pk'))
```

# 24. Exists Method

```python
# Check if at least one object exists in the queryset
exists_result = MyModel.objects.filter(field_name='value').exists()
```

# 25. Union, Intersection, and Difference

```python
# Union of two querysets (combines and removes duplicates)
union_result = MyModel.objects.filter(field_name='value') | MyModel.objects.filter(another_field=42)

# Intersection of two querysets
intersection_result = MyModel.objects.filter(field_name='value') & MyModel.objects.filter(another_field=42)

# Difference between two querysets
difference_result = MyModel.objects.filter(field_name='value').difference(MyModel.objects.filter(another_field=42))
```

# 26. Conditional Queries

```python
# Use Q objects for complex conditional queries
from django.db.models import Q

complex_query = Q(field_name='value1') | Q(field_name='value2', another_field=42)
filtered_objects = MyModel.objects.filter(complex_query)
```

# 27. Custom QuerySet Methods

```python
# Create custom methods for QuerySet
class MyModelQuerySet(models.QuerySet):
    def custom_method(self):
        return self.filter(field_name='value')

# Use the custom QuerySet in the model
class MyModel(models.Model):
    field_name = models.CharField(max_length=100)

    objects = MyModelQuerySet.as_manager()
```

# 28. Subqueries

```python
from django.db.models import Subquery, OuterRef

# Subquery: Use a queryset as a subquery in another queryset
subquery = MyModel.objects.filter(field_name=OuterRef('related_field'))
main_query = MyModel.objects.filter(pk__in=subquery)

# Correlated Subquery: Use a subquery that references the outer query
correlated_subquery = MyModel.objects.filter(related_field=OuterRef('field_name'))
correlated_main_query = MyModel.objects.filter(pk__in=correlated_subquery)
```

# 29. Subqueries and Outer Refs

```python
from django.db.models import Subquery, OuterRef

# Get products with prices greater than the average price of all products
average_price = Product.objects.aggregate(avg_price=Sum('price'))['avg_price']
products = Product.objects.filter(price__gt=Subquery(Product.objects.filter(pk=OuterRef('pk')).values('price')))
```

# 30. JSONField (PostgreSQL-specific)

```python
from django.db import models
from django.contrib.postgres.fields import JSONField

class MyModel(models.Model):
    data = JSONField()
```

# 31. Storing JSON objects

```python
from myapp.models import MyModel

# Create a new record with JSON data
data = {
    "name": "John Doe",
    "age": 30,
    "email": "john@example.com"
}

my_instance = MyModel(data=data)
my_instance.save()
```

# 32. Filter by JSON field

```python
from myapp.models import MyModel

# Get all records where 'age' is 30
result = MyModel.objects.filter(data__age=30)

# Get all records where 'email' contains 'example.com'
result = MyModel.objects.filter(data__email__contains='example.com')
```

# 33. ArrayField (PostgreSQL-specific)

```python
from django.db import models
from django.contrib.postgres.fields import ArrayField

class MyModel(models.Model):
    tags = ArrayField(models.CharField(max_length=100))
```

# 34. Filtering by ArrayField

```python
# Get objects where tags contain 'django'
MyModel.objects.filter(tags__contains=['django'])

# Get objects where tags are exactly ['django', 'python']
MyModel.objects.filter(tags=['django', 'python'])

# Get objects where the number of tags is 3
MyModel.objects.filter(tags__len=3)
```

# 35. PostGIS (Spatial Data Operations) 

```python
# Create a model with a PointField to store geographic coordinates
from django.contrib.gis.db import models

class Location(models.Model):
    name = models.CharField(max_length=100)
    coordinates = models.PointField()

# Create a model with a PolygonField to store geographic boundaries
class Area(models.Model):
    name = models.CharField(max_length=100)
    boundary = models.PolygonField()

# Create a model with a LineStringField to store geographic paths/routes
class Route(models.Model):
    name = models.CharField(max_length=100)
    path = models.LineStringField()

# Query to find locations within a certain distance of a given point
from django.contrib.gis.measure import D
from myapp.models import Location

nearby_locations = Location.objects.filter(coordinates__distance_lte=(point, D(m=1000)))

# Perform a spatial union on all Area objects
from django.contrib.gis.db.models import Union
from myapp.models import Area

unioned_area = Area.objects.aggregate(union=Union('boundary'))['union']

# Get a buffer of 100 meters around a location
from django.contrib.gis.geos import Point
from myapp.models import Location

location = Location.objects.get(pk=1)
buffered_location = location.coordinates.buffer(100)

# Get the intersection of two areas
from django.contrib.gis.geos import Polygon
from myapp.models import Area

area1 = Area.objects.get(pk=1)
area2 = Area.objects.get(pk=2)
intersection = area1.boundary.intersection(area2.boundary)

# Enable spatial indexing for faster querying
from django.contrib.gis.db import models

class Location(models.Model):
    name = models.CharField(max_length=100)
    coordinates = models.PointField(spatial_index=True)
```

# 36. **Filtering with `gt`, `gte`, `lt`, `lte`**: 

These are query operators used for greater than (`gt`), greater than or equal to (`gte`), less than (`lt`), and less than or equal to (`lte`) comparisons. They are particularly useful for numerical and date/time fields

**Greater Than (`gt`)**:
```python
# Select objects where the value of 'field_name' is greater than 10
MyModel.objects.filter(field_name__gt=10)
```

**Greater Than or Equal To (`gte`)**:
```python
# Select objects where the value of 'field_name' is greater than or equal to 10
MyModel.objects.filter(field_name__gte=10)
```

**Less Than (`lt`)**:
```python
# Select objects where the value of 'field_name' is less than 5
MyModel.objects.filter(field_name__lt=5)
```

**Less Than or Equal To (`lte`)**:
```python
# Select objects where the value of 'field_name' is less than or equal to 5
MyModel.objects.filter(field_name__lte=5)
```

# 37. **Using `distinct()` to Remove Duplicates**

The `distinct()` method is used to eliminate duplicate rows from query results. It's especially useful when your query might return the same instance more than once.

**Removing Duplicates**:
```python
# Returns a QuerySet that contains each object only once
MyModel.objects.filter(field_name='some_value').distinct()
```

**Using with Related Fields**:
```python
# When querying across related models, 'distinct()' can be used to remove duplicates
MyModel.objects.select_related('related_model').filter(related_model__field_name='some_value').distinct()
```
