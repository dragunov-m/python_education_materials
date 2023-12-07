
Source: https://djangocentral.com/django-rest-framework-cheat-sheet/

# 1. Serializers  

Serializers play a pivotal role in converting complex data types, such as Django models, into Python data types that can be easily rendered into JSON, XML, or other content types. Here's a quick guide to DRF serializers:

- Define a serializer class by inheriting from `serializers.Serializer` or `serializers.ModelSerializer` (for model-based serializers).
- Specify fields using class attributes like `CharField`, `IntegerField`, etc.
- Implement validation logic using methods like `validate_<field_name>()`.
- Use serializers to handle both input data validation and output data rendering.

```python
class MyModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = MyModel
        fields = '__all__'
```

# 2. Basic View Types##

Views in DRF are analogous to Django's views, but tailored specifically for handling API requests. They're responsible for processing incoming requests and returning appropriate responses.

DRF provides different types of views to handle various use cases:

- `APIView`: The base class for all views. It provides basic request/response handling.
- `ViewSet`: Combines multiple views (list, create, retrieve, update, delete) into a single class.
- `GenericAPIView`: Provides common behavior for CRUD operations.
- `ModelViewSet`: A combination of `GenericAPIView` and `ViewSet` tailored for model-backed APIs.

# 3. HTTP Methods and Corresponding Views

DRF maps HTTP methods to view methods:

- `GET`: `list()`, `retrieve()`
- `POST`: `create()`
- `PUT`: `update()`
- `PATCH`: `partial_update()`
- `DELETE`: `destroy()`

# 4. Authentication and PermissionsAuthentication and Permissions

DRF provides authentication and permission classes to control access to views:

```python
from rest_framework.authentication import BasicAuthentication
from rest_framework.permissions import IsAuthenticated

class MyView(APIView):
    authentication_classes = [BasicAuthentication]
    permission_classes = [IsAuthenticated]
```

# 5. Custom Permissions

Define custom permissions to control access:

```python
from rest_framework import permissions

class IsOwnerOrReadOnly(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        if request.method in permissions.SAFE_METHODS:
            return True
        return obj.owner == request.user
```

# 6. ViewSets and Routers

DRF offers ViewSets for a more concise way of defining views:

```python
from rest_framework import viewsets

class MyModelViewSet(viewsets.ModelViewSet):
    queryset = MyModel.objects.all()
    serializer_class = MyModelSerializer
```

`ViewSets` can be registered with `routers` to generate URLs automatically:

```python
from rest_framework.routers import DefaultRouter

router = DefaultRouter()
router.register(r'mymodels', MyModelViewSet)
urlpatterns += router.urls
```

# 7. Pagination

DRF offers built-in pagination classes for handling large data sets:

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10,
}
```

# 8. Filtering and Ordering

Filter and order querysets using URL query parameters:

```python
class MyModelViewSet(viewsets.ModelViewSet):
    queryset = MyModel.objects.all()
    serializer_class = MyModelSerializer
    filter_backends = [filters.SearchFilter, filters.OrderingFilter]
    search_fields = ['name']
    ordering_fields = ['name']
```

# 9. API Versioning

You can version your API to avoid breaking changes:

```python
urlpatterns = [
    path('v1/', include('myapp.urls')),  # Use API versioning in URLs
]
```

# 10. Versioning

Version your APIs using DRF's versioning classes:

```python
REST_FRAMEWORK = {
    'DEFAULT_VERSIONING_CLASS': 'rest_framework.versioning.NamespaceVersioning',
    'ALLOWED_VERSIONS': ['v1', 'v2'],
}
```

# 11. Throttling and Rate Limiting

Protect your API using throttling and rate limiting:

```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',
        'user': '1000/day',
    },
}
```

# 12. Content Negotiation

DRF supports content negotiation for handling different media types (JSON, XML, etc.):

```python
REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES': [
        'rest_framework.renderers.JSONRenderer',
        'rest_framework.renderers.XMLRenderer',
    ],
}
```

# 13. Exception Handling

DRF provides built-in exception handling and error responses:

```python
from rest_framework.views import exception_handler

def custom_exception_handler(exc, context):
    response = exception_handler(exc, context)
    if response is not None:
        response.data['custom_message'] = 'An error occurred'
    return response
```

# 14. Overriding Generic Views

You can customize the behavior of generic views by overriding methods:

```python
class MyModelListCreateView(generics.ListCreateAPIView):
    queryset = MyModel.objects.all()
    serializer_class = MyModelSerializer

    def perform_create(self, serializer):
        serializer.save(user=self.request.user)

```

# 15. View Function DecoratorsView Function Decorators

Use decorators to add behavior to views, such as authentication and permission checks:

```python
from rest_framework.decorators import authentication_classes, permission_classes

@authentication_classes([TokenAuthentication])
@permission_classes([IsAuthenticated])
def my_view(request):
    # View logic here

```

# 16. Serializer Context

Pass additional context to serializers:

```python
serializer = MyModelSerializer(instance, context={'request': request})
```

Rendering Custom Data

Render custom data using `Response` and `status`:

```python
from rest_framework.response import Response
from rest_framework import status

class MyView(APIView):
    def get(self, request):
        data = {'message': 'Hello, world!'}
        return Response(data, status=status.HTTP_200_OK)
```

# 17. File Uploads

Handle file uploads in views using serializers:

```python
class MyModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = MyModel
        fields = ['file']

class MyModelView(APIView):
    def post(self, request):
        serializer = MyModelSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

# 18. Testing Views

DRF provides testing tools to test views and API functionality:

```python
from rest_framework.test import APITestCase

class MyViewTest(APITestCase):
    def test_my_view(self):
        response = self.client.get('/my-view/')
        self.assertEqual(response.status_code, status.HTTP_200_OK)

```

Serializer Validation

Add custom validation to serializers using `validate_<field_name>` methods:

```python
class MySerializer(serializers.ModelSerializer):
    def validate_my_field(self, value):
        if value < 0:
            raise serializers.ValidationError('Value cannot be negative')
        return value
```

# 19. DRF with Function-Based Views

You can use DRF features with function-based views:

```python
from rest_framework.decorators import api_view
from rest_framework.response import Response

@api_view(['GET'])
def my_function_view(request):
    data = {'message': 'Hello, function view!'}
    return Response(data)
```

Serializing Relationships

Handle related data using serializers:

```python
class AuthorSerializer(serializers.ModelSerializer):
    class Meta:
        model = Author
        fields = ['name', 'books']

class BookSerializer(serializers.ModelSerializer):
    author = AuthorSerializer()
    class Meta:
        model = Book
        fields = ['title', 'author']
```

# 20. Combining Views

Combine multiple views into one using `ViewSets` and `mixins`:

```python
from rest_framework import mixins, viewsets

class MyViewSet(mixins.ListModelMixin,
                mixins.RetrieveModelMixin,
                viewsets.GenericViewSet):
    queryset = MyModel.objects.all()
    serializer_class = MyModelSerializer
```

# 21. Caching

Cache responses using DRF's caching decorators:

```python
from rest_framework.decorators import cache_page

class MyView(APIView):
    @cache_page(60 * 15)  # Cache for 15 minutes
    def get(self, request):
        # ...
```

# 22. DRF's Mixins

Leverage DRF's mixins for common CRUD operations:

```python
from rest_framework import mixins, viewsets

class MyViewSet(mixins.ListModelMixin,
                mixins.CreateModelMixin,
                mixins.RetrieveModelMixin,
                mixins.UpdateModelMixin,
                mixins.DestroyModelMixin,
                viewsets.GenericViewSet):
    queryset = MyModel.objects.all()
    serializer_class = MySerializer
```

# 23. Custom Action

Create custom actions in viewsets:

```python
class MyViewSet(viewsets.ModelViewSet):
    @action(detail=True, methods=['post'])
    def do_something(self, request, pk=None):
        # Custom action logic
```

# 24. Query Parameters

Retrieve query parameters in views:

```python
class MyView(APIView):
    def get(self, request):
        param_value = request.query_params.get('param_name')
        # ...
```

# 25. Custom Nested Relationships

You can use serializer methods to create custom nested relationships:

```python
class AuthorSerializer(serializers.ModelSerializer):
    class Meta:
        model = Author
        fields = '__all__'

class BookSerializer(serializers.ModelSerializer):
    author_data = serializers.SerializerMethodField()

    class Meta:
        model = Book
        fields = '__all__'

    def get_author_data(self, obj):
        author = obj.author
        return AuthorSerializer(author, context=self.context).data
```

# 26. Multiple Serializers for a Single View
## Use different serializers for different request methods:

```python
class MyModelAPIView(APIView):
    serializer_class_read = MyModelReadSerializer
    serializer_class_write = MyModelWriteSerializer

    def get_serializer_class(self):
        if self.request.method in ['GET', 'HEAD']:
            return self.serializer_class_read
        return self.serializer_class_write

    def get(self, request):
        # ...
    
    def post(self, request):
        # ...
```

# 27. Related Data Creation

Override `perform_create` in your view to handle related data creation:

```python
class OrderCreateView(generics.CreateAPIView):
    serializer_class = OrderSerializer

    def perform_create(self, serializer):
        order = serializer.save()
        products_data = self.request.data.get('products')
        if products_data:
            for product_data in products_data:
                product = Product.objects.get(id=product_data['id'])
                OrderItem.objects.create(order=order, product=product, quantity=product_data['quantity'])
```