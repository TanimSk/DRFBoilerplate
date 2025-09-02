# Additionals:
create an app ````python manage.py startapp app_name```

Custom paginator
```py
class StandardResultsSetPagination(PageNumberPagination):
    page_size = 10
    page_size_query_param = "page_size"
    max_page_size = 500
    page_query_param = "p"

    def get_paginated_response(self, data):
        return Response(
            {
                "count": self.page.paginator.count,
                "page_size": self.get_page_size(self.request),
                "next": self.get_next_link(),
                "previous": self.get_previous_link(),
                "num_pages": self.page.paginator.num_pages,
                "current_page": self.page.number,
                "results": data,
            }
        )
```


Role based extended auth class:

```py
# Authenticate User Only Class
class AuthenticateOnlyStudent(BasePermission):
    def has_permission(self, request, view):
        if not request.user or not request.user.is_authenticated:
            raise PermissionDenied("User is not authenticated.")

        if not getattr(request.user, "is_student", False):
            raise PermissionDenied("User is not a Student.")

        if not getattr(request.user.student_profile, "is_verified", False):
            raise PermissionDenied("Student is not verified.")

        return True
```

serializer with hidding fields:

```python
class StudentProfileSerializer(serializers.ModelSerializer):
    class Meta:
        model = Student
        fields = [
            "id",
            ...
            "promo_code",
        ]
        read_only_fields = [
            "id",
            ...
            "promo_code",
        ]

    def __init__(self, *args, **kwargs):
        # Accept an optional context variable to exclude `promo_code`
        hidden_keys = kwargs.pop("hide_fields", False)
        super().__init__(*args, **kwargs)

        if hidden_keys:
            for key in hidden_keys:
                if key in self.fields:
                    self.fields.pop(key, None)

# usage
serializer = StudentProfileSerializer(student, hide_fields=["promo_code"])
```


Gzip response:
```py
from django.views.decorators.gzip import gzip_page
from django.utils.decorators import method_decorator
from rest_framework.views import APIView
from rest_framework.response import Response

class MyDataView(APIView):
    @method_decorator(gzip_page)
    def get(self, request, *args, **kwargs):
        data = {"message": "This response is GZipped only for GET requests"}
        return Response(data)

    def post(self, request, *args, **kwargs):
        return Response({"message": "POST response (not gzipped)"})
```
