# drf-nested-serializer [![Build Status](https://travis-ci.org/theonion/drf-nested-serializer.svg?branch=master)](https://travis-ci.org/theonion/drf-nested-serializer) [![Coverage Status](https://coveralls.io/repos/theonion/drf-nested-serializer/badge.svg?branch=master)](https://coveralls.io/r/theonion/drf-nested-serializer?branch=master)

This project is an attempt to provide new DRF Serializer base classes that work for the *majority* of use cases.


## Development Setup

```bash
$ pip install -e .
$ pip install "file://$(pwd)#egg=drf-nested-serializer[dev]"
$ python setup.py test
```


## Simple ForeignKey and ManyToManyField serialization

For example, given these simple related models, and a single serializer:

```python
from django.db import models
from nested_serializers import NestedSerializer

class Tag(models.Model):
	name = models.CharField(max_length=255)


class FeatureType(models.Model):
	name = models.CharField(max_length=255)


class Article(models.Model):
	title = models.CharField(max_length=255)

	tags = models.ManyToManyField(Tag)
	feature_type = models.ForeignKey(FeatureType)


class ArticleSerializer(NestedSerializer):

    class Meta:
        model = Article
```

We can add new feature types while creating new articles:

```python
serializer = ArticleSerializer(data={
	'title': 'testing',
	'feature_type': {
		'name': 'testing'
	}
})
assert serializer.is_valid()
instance = serializer.save()
```

We can add add existing feature types to new articles:

```python
serializer = ArticleSerializer(data={
	'title': 'testing',
	'feature_type': {
		'id': 1
		'name': 'testing'
	}
})
assert serializer.is_valid()
instance = serializer.save()
```

