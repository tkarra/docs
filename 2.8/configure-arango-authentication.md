---
layout: default
description: ArangoDB only provides a very simple authentication interface and noauthorization
---
Authentication and Authorization
================================

### Authentication and Authorization

ArangoDB only provides a very simple authentication interface and no
authorization. We plan to add authorization features in later releases, which
will allow the administrator to restrict access to collections and queries to
certain users, given them either read or write access.

Currently, you can only secure the access to ArangoDB in an all-or-nothing
fashion. The collection *_users* contains all users and a salted SHA256 hash
of their passwords. A user can be active or inactive. A typical document of this
collection is
{% arangoshexample examplevar="examplevar" script="script" result="result" %}
    @startDocuBlockInline USER_01_authFetch
    @EXAMPLE_ARANGOSH_OUTPUT{USER_01_authFetch}
    db._users.toArray()
    @END_EXAMPLE_ARANGOSH_OUTPUT
    @endDocuBlock USER_01_authFetch
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}
### Command-Line Options for the Authentication and Authorization

<!-- arangod/RestServer/ArangoServer.h -->
{% docublock server_authentication %}

<!-- arangod/RestServer/ArangoServer.h -->
{% docublock serverAuthenticateSystemOnly %}

Introduction to User Management
-------------------------------

ArangoDB provides basic functionality to add, modify and remove database users
programmatically. The following functionality is provided by the *users* module
and can be used from inside arangosh and arangod.

**Note**: This functionality is not available from within the web
interface.

### Save

`users.save(user, passwd, active, extra, changePassword)`

This will create a new ArangoDB user. The username must be specified in
*user* and must not be empty.

The password must be given as a string, too, but can be left empty if required.
If you pass the special value *ARANGODB_DEFAULT_ROOT_PASSWORD*, the password
will be set the value stored in the environment variable
`ARANGODB_DEFAULT_ROOT_PASSWORD`. This can be used to pass an instance variable
into ArangoDB. For example, the instance identifier from Amazon.

If the *active* attribute is not specified, it defaults to *true*. The
*extra* attribute can be used to save custom data with the user.

If the *changePassword* attribute is not specified, it defaults to *false*.
The *changePassword* attribute can be used to indicate that the user must
change has password before logging in.

This method will fail if either the username or the passwords are not specified
or given in a wrong format, or there already exists a user with the specified
name.

The new user account can only be used after the server is either restarted or
the server authentication cache is [reloaded](#reload).

**Note**: this function will not work from within the web interface

*Examples*
{% arangoshexample examplevar="examplevar" script="script" result="result" %}
    @startDocuBlockInline USER_02_saveUser
    @EXAMPLE_ARANGOSH_OUTPUT{USER_02_saveUser}
    require("org/arangodb/users").save("my-user", "my-secret-password");
    @END_EXAMPLE_ARANGOSH_OUTPUT
    @endDocuBlock USER_02_saveUser
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}
### Reload

`users.reload()`

Reloads the user authentication data on the server

All user authentication data is loaded by the server once on startup only and is
cached after that. When users get added or deleted, a cache flush is required,
and this can be performed by called this method.

*Examples*
{% arangoshexample examplevar="examplevar" script="script" result="result" %}
    @startDocuBlockInline USER_03_reloadUser
    @EXAMPLE_ARANGOSH_OUTPUT{USER_03_reloadUser}
    require("org/arangodb/users").reload();
    @END_EXAMPLE_ARANGOSH_OUTPUT
    @endDocuBlock USER_03_reloadUser
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}
**Note**: this function will not work from within the web interface


### Document

`users.document(user)`

Fetches an existing ArangoDB user from the database.

The username must be specified in *user*.

This method will fail if the user cannot be found in the database.

*Examples*
{% arangoshexample examplevar="examplevar" script="script" result="result" %}
    @startDocuBlockInline USER_04_documentUser
    @EXAMPLE_ARANGOSH_OUTPUT{USER_04_documentUser}
    require("org/arangodb/users").document("my-user");
    @END_EXAMPLE_ARANGOSH_OUTPUT
    @endDocuBlock USER_04_documentUser
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}
**Note**: this function will not work from within the web interface

### Replace

`users.replace(user, passwd, active, extra, changePassword)`

This will look up an existing ArangoDB user and replace its user data.

The username must be specified in *user*, and a user with the specified name
must already exist in the database.

The password must be given as a string, too, but can be left empty if required.

If the *active* attribute is not specified, it defaults to *true*.  The
*extra* attribute can be used to save custom data with the user.

If the *changePassword* attribute is not specified, it defaults to *false*.
The *changePassword* attribute can be used to indicate that the user must
change has password before logging in.

This method will fail if either the username or the passwords are not specified
or given in a wrong format, or if the specified user cannot be found in the
database.

**Note**: this function will not work from within the web interface

*Examples*
{% arangoshexample examplevar="examplevar" script="script" result="result" %}
    @startDocuBlockInline USER_03_replaceUser
    @EXAMPLE_ARANGOSH_OUTPUT{USER_03_replaceUser}
    require("org/arangodb/users").replace("my-user", "my-changed-password");
    @END_EXAMPLE_ARANGOSH_OUTPUT
    @endDocuBlock USER_03_replaceUser
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}
### Update

`users.update(user, passwd, active, extra, changePassword)`

This will update an existing ArangoDB user with a new password and other data.

The username must be specified in *user* and the user must already exist in
the database.

The password must be given as a string, too, but can be left empty if required.

If the *active* attribute is not specified, the current value saved for the
user will not be changed. The same is true for the *extra* and the
*changePassword* attribute.

This method will fail if either the username or the passwords are not specified
or given in a wrong format, or if the specified user cannot be found in the
database.

**Note**: this function will not work from within the web interface

*Examples*
{% arangoshexample examplevar="examplevar" script="script" result="result" %}
    @startDocuBlockInline USER_04_updateUser
    @EXAMPLE_ARANGOSH_OUTPUT{USER_04_updateUser}
    require("org/arangodb/users").update("my-user", "my-secret-password");
    @END_EXAMPLE_ARANGOSH_OUTPUT
    @endDocuBlock USER_04_updateUser
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}
### isValid

`users.isValid(user, password)`

Checks whether the given combination of username and password is valid.  The
function will return a boolean value if the combination of username and password
is valid.

Each call to this function is penalized by the server sleeping a random
amount of time.

*Examples*
{% arangoshexample examplevar="examplevar" script="script" result="result" %}
    @startDocuBlockInline USER_05_isValidUser
    @EXAMPLE_ARANGOSH_OUTPUT{USER_05_isValidUser}
    require("org/arangodb/users").isValid("my-user", "my-secret-password");
    @END_EXAMPLE_ARANGOSH_OUTPUT
    @endDocuBlock USER_05_isValidUser
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}
**Note**: this function will not work from within the web interface

### all()

`users.all()`

Fetches all existing ArangoDB users from the database.

*Examples*
{% arangoshexample examplevar="examplevar" script="script" result="result" %}
    @startDocuBlockInline USER_06_AllUsers
    @EXAMPLE_ARANGOSH_OUTPUT{USER_06_AllUsers}
    require("org/arangodb/users").all();
    @END_EXAMPLE_ARANGOSH_OUTPUT
    @endDocuBlock USER_06_AllUsers
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}
### Remove

`users.remove(user)`

Removes an existing ArangoDB user from the database.

The username must be specified in *User* and the specified user must exist in
the database.

This method will fail if the user cannot be found in the database.

**Note**: this function will not work from within the web interface

*Examples*
{% arangoshexample examplevar="examplevar" script="script" result="result" %}
    @startDocuBlockInline USER_07_removeUser
    @EXAMPLE_ARANGOSH_OUTPUT{USER_07_removeUser}
    require("org/arangodb/users").remove("my-user");
    @END_EXAMPLE_ARANGOSH_OUTPUT
    @endDocuBlock USER_07_removeUser
{% endarangoshexample %}
{% include arangoshexample.html id=examplevar script=script result=result %}
