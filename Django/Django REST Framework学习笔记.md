# Django REST Framework学习笔记

环境：

- python 3.8
- Django 3.0
- djangorestframework 3.11.0
- Markdown 3.2.1
- django-filter 2.2.0

### 配置

`settings.py`中

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

```python
REST_FRAMEWORK = {
    # Use Django's standard `django.contrib.auth` permissions,
    # or allow read-only access for unauthenticated users.
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly'
    ]
}
```

