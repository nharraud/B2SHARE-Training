# Special requests
In this guide more specific API calls are explained, like reporting abuse and getting access to restricted data. As the B2SHARE REST API continuously expands with new functionality, please check back later to find out new options.

The guide currently covers:
- Report a record as an abuse record
- Send a request to get access to restricted data in a record

### Setup your connection
Please make sure your machine has been properly set up to use Python and required packages. Follow [this](A_Setup_and_install.md) guide in order to do so.

This guide assumes you have successfully registered your account on the [B2SHARE website](https://trng-b2share.eudat.eu) using your institutional credentials or social ID through B2ACCESS. In addition, the loading of the token, importing Python packages and checking request responses will not be covered here.

## Report a record as an abuse record
If you find anything wrong with an existing published record that is not your own, you can report the record using the API. When the request is successfully done, the administrator of the community will receive an automatic e-mail in which the abuse is reported.

Currently, four different reasons for abuse are defined of which one should be included in the report (see table below).

Tag | Label | Description
--- | ----- | -----------
`noresearch` | Abuse or Inappropriate content |  The record contains material that is abusively used by the creator of the record, or the record contains inappropriate material
`copyright` | Copyrighted material | The record contains copyrighted material not owned by the record's creator
`noresearch` | Not research data | The record does not contain any research data
`illegalcontent` | Illegal content | The record contains other illegal content

Furthermore, the reporter and an additional message are included in the API request. In this example, the record `b43a0e6914e34de8bd19613bcdc0d364` is reported for abuse because it does not contain research data. The reason is indicated by setting the corresponding tag to `true` in the data structure. Multiple reasons can be set at the same time. The reporter's personal and address information are included to allow any contact after the report.

```python
>>> header = {"Content-Type": 'application/json'}
>>> data = {"noresearch": True,
            "abusecontent": True,
            "copyright": False,
            "illegalcontent": False,
            "message": "This record does not contain any research data...",
            "name": "John Smith",
            "affiliation": "Some University",
            "email": "j.smith@example.com",
            "address": "Example street",
            "city": "ExampleCity",
            "country": "ExampleCountry",
            "zipcode": "12345",
            "phone": "07364017452"
           }
>>> payload = {"access_token": token}
```

The header is used to indicate that the payload is in JSON format. To make the request, the `abuse` indicator is added to the URL, and the data structure is provided as a serialized string. The POST request then looks as follows:

```python
>>> url = "https://trng-b2share.eudat.eu/api/records/b43a0e6914e34de8bd19613bcdc0d364/abuse"
>>> r = requests.get(url, params=payload, data=json.dumps(data), headers=header)
```

If the request is successfull, the response looks as follows:

```python
>>> print r
<Response [200]>
>>> print r.text
{
  "message": "The record is reported."
}
```

Please note that in all cases you have to provide all abuse reasons at the same time, but only one can be set to `true`. If you have two or more set to `true` the following response will be returned:

```python
>>> print r
<Response [400]>
>>> print r.text
{
  "Error": "From 'noresearch', 'abusecontent', 'copyright', 'illegalcontent' (only) one should be True"
}
```

## Send a request to get access to restricted data in a record
In some cases the files in a dataset published in B2SHARE that you want to use for your own research is currently not publicly accessibe. In that case you can request access to the owner of the record by providing a message and your personal details in a request using the API. An automatic email will be sent to the person that owns and/or administers the record. In this example, access is requested for record `b43a0e6914e34de8bd19613bcdc0d364`.

```python
>>> header = {"Content-Type": 'application/json'}
>>> data = {"message": "I would like to have access to your record",
            "name": "John Smith",
            "affiliation": "Some University",
            "email": "j.smith@example.com",
            "address": "Example street",
            "city": "ExampleCity",
            "country": "ExampleCountry",
            "zipcode": "12345",
            "phone": "7364017452"
           }
>>> payload = {"access_token": token}
```

The header is again included to indicate that the payload is in JSON format. Similarly to the request in the previous section, the `accessrequests` indicator is added to the URL, and the data structure is provided as a serialized string. The request is done through the POST method and looks as follows:

```python
>>> url = "https://trng-b2share.eudat.eu/api/records/b43a0e6914e34de8bd19613bcdc0d364/accessrequests"
>>> r = requests.get(url, params=payload, data=json.dumps(data), headers=header)
```

On a successfull request, the response looks as follows:

```python
>>> print r
<Response [200]>
>>> print r.text
{
  "message": "An email was sent to the record owner."
}
```
