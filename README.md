# Additionals:

Role based extended auth class:
```
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
