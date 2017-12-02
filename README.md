# Django Bougie REST API

This is a proposal for creating a new CMS on top of the Django `django.contrib.admin` module. By having the resources and UI logic seperated the CMS can be better coutured to websites, but also gives us the possibility to have a better UX experience over all.

## Contribution

Are you looking to enhance the overall Django Admin experience, I'd love to collaborate. This right now can be a start of a new overhal of how CMS architectures will look like as this can be used as a blueprint for other systems.


### Todos

- Add List Search
- Add optional Filtering
- Inline Model Admin



## Get Site info

`/site/`

```json
{
    "site_title": "Colly",
    "site_header": "Colly Administration",
    "site_url": "/",
    "modules_url": "/site/apps/"
}
```

## Get Apps

`GET /site/apps` **Auth Required**
```json
[
    {
        "name": "Users",
        "app_url": "/users/",
        "app_label": "Users",
        "models": [
            {
                "name": "User",
                "actions": [
                    "add", "change", "delete"
                ]
            }
        ]
    },
    {
        "name": "Companies",
        "app_url": "/companies/",
        "app_label": "Users",
        "models": [
            {
                "name": "User",
                "actions": [
                    "add", "change", "delete"
                ]
            }
        ]
    }
]
```

`OPTIONS /forms/:app/:model/` **Auth Required**
```json
{
    "name": "UserAdminForm",
    "list_display_fields": ["first_name", "last_name", "email"],
    "model": {
        "name": "User",
        "labels": {
            "singular": "User",
            "plural": "Users"
        }
    }
}
```

## Get Form by Action

`OPTIONS /forms/:app/:model/:action`


```json
{
    "name": "UserAdminForm",
    "resource": "/users/user",
    "fieldsets": [
        {
            "name": null,
            "fields": ["first_name", "last_name", "email"]
        }
    ],
    "fields": {
        "first_name": {
            "name": "CharField",
            "required": true,
            "label": "First Name",
            "initial": null,
            "help_text": "This is your django username",
            "error_messages": {
                "required": "This field is required.",
                "invalid": "Enter a valid value."
            },
            "widget": {
                "name": "TextInput",
                "is_hidden": false,
                "needs_multipart_form": false,
                "is_localized": false,
                "is_required": true,
                "attrs": {
                    "maxlength": "30"
                },
                "input_type": "text"
            },
            "min_length": 6,
            "max_length": 30
        },
        "last_name": {
            "name": "CharField",
            "required": true,
            "label": "Last Name",
            "initial": null,
            "help_text": "This is your Last Name",
            "error_messages": {
                "required": "This field is required.",
                "invalid": "Enter a valid value."
            },
            "widget": {
                "name": "TextInput",
                "is_hidden": false,
                "needs_multipart_form": false,
                "is_localized": false,
                "is_required": true,
                "attrs": {
                    "maxlength": "30"
                },
                "input_type": "text"
            },
            "min_length": 6,
            "max_length": 30
        },
        "email": {
            "name": "EmailField",
            "required": true,
            "label": "Email",
            "initial": null,
            "help_text": "Your daily email",
            "error_messages": {
                "required": "This Field is required.",
                "invalid": "Enter a valid value"
            }
        }
        "password": {
            "name": "CharField",
            "required": true,
            "label": "Password",
            "initial": null,
            "help_text": "",
            "error_messages": {
                "required": "This field is required.",
                "invalid": "Enter a valid value."
            },
            "widget": {
                "name": "PasswordInput",
                "is_hidden": false,
                "needs_multipart_form": false,
                "is_localized": false,
                "is_required": true,
                "attrs": {
                    "maxlength": "128"
                },
                "input_type": "password"
            },
            "min_length": 6,
            "max_length": 128
        }

    },
    "inlines": [
        {
            "name": "UserProfileInlineAdmin",
            "form_url": "/forms/users/profile"
        }
    ]

}
```

# Objects

## Field object
Below is the representation of a model object
```json
{
    "name": "CharField",
    "label": "Password",
    "initial": null,
    "help_text": "",
    "required": true,
    "error_messages": {
        "required": "This field is required.",
        "invalid": "Enter a valid value."
    },
    "widget": {
        "name": "PasswordInput",
        "is_hidden": false,
        "needs_multipart_form": false,
        "is_localized": false,
        "is_required": true,
        "attrs": {
            "maxlength": "128"
        },
        "input_type": "password"
    },
    "min_length": 6,
    "max_length": 128
}
```

## Widget object


# REST Persistence

The Bougie Rest API works on a usual REST structure, e.g.:

- `POST /resources/` Create resource
- `GET /resources/` List resources
- `GET /resources/:id/` Get detail user
- `PUT /resources/:id/` **Fully** update user
- `DELETE /resources/:id/` Delete given user

There is one less RESTful endpoint and that is for batch delete:
- `DELETE /resources/` Delete in batch with `ids` array


## Create Endpoint

`POST /forms/:app/:model/`

```json
{
    "email": "paul@glemma.nl",
    "first_name": "Paul",
    "last_name": "Oostenrijk"
}
```


## Get List Endpoint

`GET /apps/:app/:model/`
```json
{
    "next": "",
    "prev": "",
    "count": 1,
    "results": [
        {
            "id": 1,
            "email": "paul@glemma.nl",
            "first_name": "Paul",
            "last_name": "Oostenrijk"
        }
    ]
}
```


## Update Resource 

`PUT /apps/:app/:model/:id`

```json
{
    "email": "paul@glemma.nl",
    "first_name": "Henk"
}
```

## Validate Resource

Validate one or more fields before updating the resource, this is good for on edit validation.

```json
{
    "email": "paul@glemma.nl",
    "first_name": "Henk"
}
```

## Delete Resource

`DELETE /apps/:app/:model/:id`

`204 No Content` is returned


## Errors

```json
{
  "errors": [
    {
      "field": "email",
      "type": "ValidationError",
      "message": "Email already taken"
    }
  ]
}
```
