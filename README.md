# OpenLDAP server for Galaxy testing

Based on: https://github.com/osixia/docker-openldap

Use this image to test your ldap configuration.

``docker run --name my-openldap-container --detach -p 389:389 -p 636:636 jelle/galaxy-openldap``

By default, the following users are available:
- **dn**: uid=galaxy,dc=example,dc=org
  **password**: galaxy
- **dn**: uid=dummy,dc=example,dc=org
  **password**: dummy

## Ldapsearch

Examples:

``ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin -x uid=galaxy``

Same but from the container:
``docker exec my-openldap-container ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin -x uid=galaxy``

## Docker-compose

With `docker-compose up` you get a phpLDAPadmin page at http://localhost:8080 to which you can
login with `cn=admin,dc=example,dc=org` as username and `admin` as password.

## Galaxy

Relevant galaxy configuration in `config/auth_conf.xml`:

```
<authenticator>
  <type>ldap</type>
  <options>
    (..)
    <server>ldap://localhost</server>
    <ldap-options>OPT_X_TLS_REQUIRE_CERT=OPT_X_TLS_NEVER</ldap-options>
    <login-use-username>True</login-use-username>
    <continue-on-failure>False</continue-on-failure>
    <!-- For OpenLDAP: -->
    <search-base>dc=example,dc=org</search-base>
    <search-fields>uid,mail</search-fields>
    <search-filter>(mail={email})</search-filter>
    <search-filter>(uid={username})</search-filter>
    <bind-user>{dn}</bind-user>
    <bind-password>{password}</bind-password>
    <auto-register-username>{uid}</auto-register-username>
    <auto-register-email>{mail}</auto-register-email>
    <search-user>cn=admin,dc=example,dc=org</search-user>
    <search-password>admin</search-password>
    <auto-create-roles>True</auto-create-roles>
    <auto-create-groups>True</auto-create-groups>
    <auto-assign-roles-to-groups-only>True</auto-assign-roles-to-groups-only>
    <auto-register-roles>gidNumber</auto-register-roles>
  </options>
</authenticator>
```	
