# To add:

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
